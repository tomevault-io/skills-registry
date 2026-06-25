---
name: kb-agentcore-observability
description: AgentCore OpenTelemetry・ログ・トレース・デバッグのナレッジ Use when this capability is needed.
metadata:
  author: minorun365
---

# AgentCore Observability ナレッジ

Bedrock AgentCore の Observability（OpenTelemetry、ログ、メトリクス、トレース）と運用・デバッグに関する学びを記録する。
Strands Agents フレームワーク自体は `/kb-strands-agentcore`、CDK/デプロイは `/kb-agentcore-cdk` を参照。

## Observability（トレース）セットアップ

AgentCore Observability でトレースを出力する場合、以下の4点が**すべて**必要：

### 1. requirements.txt
```
strands-agents[otel]
aws-opentelemetry-distro
```

### 2. Dockerfile（`opentelemetry-instrument` で起動）
```dockerfile
CMD ["opentelemetry-instrument", "python", "agent.py"]
```

### 3. CDK環境変数
```typescript
environmentVariables: {
  AGENT_OBSERVABILITY_ENABLED: 'true',
  OTEL_PYTHON_DISTRO: 'aws_distro',
  OTEL_PYTHON_CONFIGURATOR: 'aws_configurator',
  OTEL_EXPORTER_OTLP_PROTOCOL: 'http/protobuf',
}
```

### 4. import パス（トップレベルから import すること）
```python
# OK: トレースが出力される
from bedrock_agentcore import BedrockAgentCoreApp

# NG: トレースが出力されない（ログ・メトリクスは出るがトレースだけ欠落）
from bedrock_agentcore.runtime import BedrockAgentCoreApp
```

**注意**: 上記4つすべてが必要。1つでも欠けるとトレースが出力されない。

### import パスの罠: runtime サブモジュール経由だとトレースが出ない

`from bedrock_agentcore.runtime import BedrockAgentCoreApp` を使うと、内部的には同じクラスが動くにもかかわらず、GenAI Observability の Traces View にトレースが一切表示されない。OTel のログ・メトリクスは正常に出力されるため、影響を受けるのはトレース（X-Ray スパン）のエクスポートのみ。SDK のトップレベル `__init__.py` での Observability 初期化フックに乗らないことが原因と推測される。

---

## OTELログ形式

OTEL有効時、ログは `otel-rt-logs` ストリームにJSON形式で出力される。各セッションは `session.id` フィールドで識別される。

```json
{
  "resource": { ... },
  "scope": { "name": "strands.telemetry.tracer" },
  "timeUnixNano": 1769681571307833653,
  "body": {
    "input": { "messages": [...] },
    "output": { "messages": [...] }
  },
  "attributes": {
    "session.id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  }
}
```

### CloudWatch Logs Insightsでのセッションカウント

OTELログからセッション数をカウントするクエリ：

```
parse @message /"session\.id":\s*"(?<sid>[^"]+)"/
| filter ispresent(sid)
| stats count_distinct(sid) as sessions by datefloor(@timestamp, 1h) as hour_utc
| sort hour_utc asc
```

**注意**: `datefloor(@timestamp + 9h, ...)` を使うと挙動が不安定。UTCで集計してからスクリプト側でJSTに変換する。

```bash
# UTCの時刻をJSTに変換
JST_HOUR=$(( (10#$UTC_HOUR + 9) % 24 ))
```

---

## EMF（Embedded Metric Format）メトリクス

Strands Agentsは自動的にEMF形式でトークンメトリクスを出力する。ログストリーム `otel-rt-logs` にJSON形式で記録される。

**メトリクス名一覧:**
- `strands.event_loop.input.tokens` / `strands.event_loop.output.tokens`
- `strands.event_loop.cache_read.input.tokens` / `strands.event_loop.cache_write.input.tokens`
- `strands.event_loop.latency` / `strands.model.time_to_first_token`

**EMFのデータ形式はヒストグラム:**

```json
{
  "strands.event_loop.input.tokens": {
    "Values": [576.81, 2571.17, 7272.37],
    "Counts": [1.0, 1.0, 1.0],
    "Count": 3,
    "Sum": 10344,
    "Max": 7197,
    "Min": 582
  }
}
```

- `Count`: 1回のエージェント呼び出し内のモデルコール回数（≒ターン数）
- `Sum`: セッション合計トークン
- `Max`/`Min`: 単一ターンの最大/最小値
- `Values`: 近似値（ヒストグラムバケット境界）。**正確な値は Sum/Max/Min を使う**

**CloudWatch Log Insights での分析:**

```
# EMFログの検索
filter @message like /strands.event_loop.input.tokens/
| fields @message, @timestamp
| limit 200
```

**注意**: Log Insights の `parse` + `stats pct()` では EMF ヒストグラムの値を正しく集計できない。Python等で JSON をパースして `Sum`/`Max`/`Min` を直接抽出する方が正確。

---

## トレースの確認

1. CloudWatch Console -> **Bedrock AgentCore GenAI Observability**
2. Agents View / Sessions View / Traces View で確認可能
3. トレースの確認画面は **CloudWatchコンソール側**。AgentCoreコンソールではない点に注意

---

## ローカル開発でのトレース確認（コンソールエクスポーター）

Runtime にデプロイせずにローカルでトレースを確認するには、`StrandsTelemetry` のコンソールエクスポーターを使う。

```python
from strands import Agent
from strands.telemetry import StrandsTelemetry

# トレースのコンソール出力を有効化（1行）
StrandsTelemetry().setup_console_exporter()

agent = Agent()
response = agent("こんにちは")
```

出力されるスパン階層:
```
invoke_agent Strands Agents    ← Agent Span（エージェント全体）
  └─ execute_event_loop_cycle  ← Cycle Span（推論サイクル）
       ├─ chat                 ← Model Invoke Span（LLM呼び出し）
       └─ execute_tool xxx     ← Tool Span（ツール呼び出し）
```

各スパンに付与される主要属性:
- `gen_ai.usage.prompt_tokens` / `gen_ai.usage.completion_tokens`
- `gen_ai.server.time_to_first_token`（ms）
- `gen_ai.request.model`

**注意**: ローカルから CloudWatch X-Ray への直接送信は非推奨。ADOT 自動計装で SigV4 署名付き POST が成功（200 OK）しても、Runtime 環境外からのトレースは `aws/spans` に記録されない。ローカル開発ではコンソールエクスポーターを使うこと。

---

## StrandsTelemetry API

```python
from strands.telemetry import StrandsTelemetry

t = StrandsTelemetry()
t.setup_console_exporter()     # コンソール出力（ローカル開発用）
t.setup_otlp_exporter(**kw)    # OTLPエンドポイントに送信（Langfuse等）
t.setup_meter(                 # メトリクス有効化
    enable_console_exporter=True,
    enable_otlp_exporter=True,
)
# メソッドチェーン可: t.setup_otlp_exporter().setup_console_exporter()
```

`setup_otlp_exporter` の kwargs は `OTLPSpanExporter` にそのまま渡される:
- `endpoint`: OTLPエンドポイントURL
- `headers`: 認証ヘッダー等

---

## Langfuse連携（サードパーティOTEL送信）

OTELベースなので、CloudWatch以外のバックエンドにもトレースを送信可能。Langfuseとの連携例:

```python
import base64
import os

from dotenv import load_dotenv
from strands import Agent
from strands.telemetry import StrandsTelemetry

load_dotenv()

LANGFUSE_PUBLIC_KEY = os.environ["LANGFUSE_PUBLIC_KEY"]
LANGFUSE_SECRET_KEY = os.environ["LANGFUSE_SECRET_KEY"]
LANGFUSE_HOST = os.environ.get("LANGFUSE_HOST", "https://cloud.langfuse.com")

# OTLPエクスポーターの認証ヘッダーを生成（HTTP Basic認証）
auth = base64.b64encode(
    f"{LANGFUSE_PUBLIC_KEY}:{LANGFUSE_SECRET_KEY}".encode()
).decode()

# Langfuseにトレースを送信
StrandsTelemetry().setup_otlp_exporter(
    endpoint=f"{LANGFUSE_HOST}/api/public/otel/v1/traces",
    headers={"Authorization": f"Basic {auth}"},
)

agent = Agent()
response = agent("こんにちは")
```

**ポイント**:
- Langfuse Cloud の Hobby プラン（無料）で動作確認済み
- トレース名 `invoke_agent Strands Agents` として記録される
- トークンコストの自動計算まで動作する
- AgentCore Runtime にデプロイする場合は `DISABLE_ADOT_OBSERVABILITY=True` 環境変数で ADOT を無効化する必要あり（競合防止）

---

## LLM にやらせてはいけない計算

### 日付→曜日の変換

LLM は日付から曜日を計算するのが苦手（既知の弱点）。`strands_tools` の `current_time` は ISO 8601 形式（例: `2026-02-09T02:46:56+00:00`）を返すが、曜日情報が含まれないため LLM が誤認識する。

**実例**: 2026年2月9日（月曜日）を LLM が「日曜日」と誤認識

**原則**: LLM に計算させず、ツール側で確定情報を返す。

```python
from datetime import datetime, timezone, timedelta
from strands import tool

JST = timezone(timedelta(hours=9))
WEEKDAY_JA = ["月", "火", "水", "木", "金", "土", "日"]

@tool
def current_time() -> str:
    """現在の日本時間（JST）を曜日付きで取得します。"""
    now = datetime.now(JST)
    weekday = WEEKDAY_JA[now.weekday()]
    return f"{now.year}年{now.month}月{now.day}日({weekday}) {now.strftime('%H:%M')} JST"
```

**ポイント**:
- タイムゾーン変換もツール側で完結させる（システムプロンプトの「+9時間して」は不確実）
- `Python の weekday()` は月曜=0 なので日本語曜日配列のインデックスと一致する
- `strands_tools.current_time` の代わりにカスタムツールを使う

---

## AgentCoreBrowser（ビルトインブラウザツール）

### ツール登録の注意: `.browser` メソッドを渡す

`AgentCoreBrowser()` インスタンスをそのまま `tools` に渡すと「unrecognized tool specification」エラーになる。`@tool` デコレータ付きの `.browser` メソッドを渡す必要がある。

```python
from strands_tools.browser import AgentCoreBrowser

browser = AgentCoreBrowser()

# NG: インスタンスそのまま → unrecognized tool specification
agent = Agent(tools=[browser, ...])

# OK: .browser メソッドを渡す
agent = Agent(tools=[browser.browser, ...])
```

`LocalChromiumBrowser` も同様のパターン（`local.browser`）。

### AgentCoreBrowser はリモートブラウザ（Playwright ローカルドライバー不要）

`AgentCoreBrowser()` は AgentCore Browser Service（リモート）を呼び出すため、ローカルの Playwright ドライバー（node バイナリ）は不要。`shutil.copy2` で 118MB の node バイナリをコピーするワークアラウンドを入れると、モジュール初期化でタイムアウトしてデプロイが失敗する。

```python
# NG: AgentCoreBrowser にはこのワークアラウンドは不要（デプロイ失敗の原因）
import shutil, stat
_node_src = "/var/task/playwright/driver/node"
shutil.copy2(_node_src, "/tmp/playwright_node")  # 118MBコピー → タイムアウト

# OK: AgentCoreBrowser はそのまま使える
from strands_tools.browser import AgentCoreBrowser
browser = AgentCoreBrowser()
agent = Agent(tools=[browser.browser, ...])
```

### CodeZip デプロイ時の Playwright 実行権限問題（LocalChromiumBrowser のみ）

**`LocalChromiumBrowser` を使う場合のみ**、CodeZip パッケージングで Playwright ドライバーの実行ビット（`+x`）が失われる。`AgentCoreBrowser`（リモートブラウザ）ではこの問題は発生しない。

**解決策**: `/tmp` にコピーして `PLAYWRIGHT_NODEJS_PATH` 環境変数で指定（`get_agent()` 内の遅延初期化で実行すること）

**ポイント**:
- `/var/task/` は読み取り専用のため `os.chmod()` は効かない
- Playwright 1.58.0 の `_driver.py` で `PLAYWRIGHT_NODEJS_PATH` 環境変数がサポートされている
- Docker デプロイの場合はこの問題は発生しない（Dockerfile で権限設定可能）
- **モジュールレベルで 118MB のファイルコピーを実行するとデプロイ時のヘルスチェックでタイムアウトする**

### Browser Tool の IAM 権限

`agentcore deploy` のデフォルトロールには Browser Tool の権限がない。以下を手動追加する必要がある:

```json
{
  "Effect": "Allow",
  "Action": "bedrock-agentcore:*",
  "Resource": "*"
}
```

**注意**: `GetBrowserSession` + `StartBrowserSession` だけでは不十分。WebSocket automation stream 接続に追加の隠しアクションが必要なため、テスト時は `bedrock-agentcore:*` のフルアクセスが確実。本番では最小権限に絞り込むこと。

---

## FPDF2でPDF生成（日本語対応）

### 日本語フォント（NotoSansCJKjp）

日本語PDFを生成する場合、CJKフォントが必要。NotoSansCJKjpを使用：

```dockerfile
# Dockerfile: フォントをプロジェクトからコピー
COPY fonts/ /app/fonts/
```

**フォントの入手先**:
- https://github.com/minoryorg/Noto-Sans-CJK-JP
- `fonts/NotoSansCJKjp-Regular.ttf`
- `fonts/NotoSansCJKjp-Bold.ttf`

```python
# agent.py: FPDF2でフォント登録
from fpdf import FPDF

class MyPDF(FPDF):
    def __init__(self):
        super().__init__()
        self.add_font("NotoSansCJKjp", fname="/app/fonts/NotoSansCJKjp-Regular.ttf")
        self.add_font("NotoSansCJKjp", style="B", fname="/app/fonts/NotoSansCJKjp-Bold.ttf")
```

### S3への保存と署名付きURL

```python
from botocore.config import Config

# 署名付きURL用のクライアント（s3v4必須）
s3_presigned = boto3.client(
    "s3",
    region_name=AWS_REGION,
    config=Config(signature_version="s3v4"),
)

# PDFをS3にアップロード
pdf_bytes = pdf.output()
s3_client.put_object(
    Bucket=UPLOAD_BUCKET,
    Key=f"estimates/{estimate_no}.pdf",
    Body=pdf_bytes,
    ContentType="application/pdf",
)

# 署名付きURL生成（1時間有効）
download_url = s3_presigned.generate_presigned_url(
    ClientMethod="get_object",
    Params={"Bucket": UPLOAD_BUCKET, "Key": s3_key},
    ExpiresIn=3600,
)
```

### 注意点
- GitHubからフォントをcurlでダウンロードする場合、`-L`オプション必須（リダイレクト対応）
- apt-getでfonts-noto-cjkをインストールするとTTC形式になりFPDF2で追加設定が必要
- **プロジェクトにフォントファイルを含めてCOPYするのが最も確実**

---

## トラブルシューティング

### AgentCore Runtime を CLI から手動呼び出しする方法

**payload は base64 エンコードが必要**:

```bash
PAYLOAD=$(echo -n '{"prompt": "Check emails and notify Slack"}' | base64)
aws bedrock-agentcore invoke-agent-runtime \
  --agent-runtime-arn "arn:aws:bedrock-agentcore:REGION:ACCOUNT:runtime/RUNTIME_ID" \
  --payload "$PAYLOAD" \
  --content-type "application/json" \
  --profile <profile> \
  --region us-east-1 \
  /tmp/response.txt
```

**日本語の payload は使えない**:

```
string argument should contain only ASCII characters
```

AWS CLI がマルチバイト文字を受け付けない。システムプロンプトが日本語なら英語プロンプトでも問題なく動く。

**レスポンスはファイルに保存**:
最後の引数がレスポンスを書き出すファイルパス（省略不可）。

---

## 関連スキル

- `/kb-strands-agentcore` - Strands Agents フレームワーク（Agent作成、ツール定義、イベント処理）
- `/kb-agentcore-cdk` - AgentCore CDK、デプロイ、ランタイム統合、コンテナ構成
- `/kb-agentcore-identity` - アウトバウンド認証（3LO/M2M/デコレータ分離/callback等）

## 参考リンク

- [Strands Agents 公式ドキュメント](https://strandsagents.com/)
- [GitHub リポジトリ](https://github.com/strands-agents/strands-agents)
- [Bedrock AgentCore 統合ガイド](https://docs.aws.amazon.com/bedrock/latest/userguide/agents-agentcore.html)

---
> Source: [minorun365/my-claude-code-settings](https://github.com/minorun365/my-claude-code-settings) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
