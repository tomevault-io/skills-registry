---
name: kb-strands-agentcore
description: Strands Agents フレームワークのナレッジ。Agent作成、ツール定義、イベント処理、会話履歴管理等（CDKは /kb-agentcore-cdk、Observabilityは /kb-agentcore-observability、Identity認証は /kb-agentcore-identity） Use when this capability is needed.
metadata:
  author: minorun365
---

# Strands Agents ナレッジ

AWS が提供する AI エージェントフレームワーク「Strands Agents」に関する学びを記録する。
CDK/デプロイ/ランタイムは `/kb-agentcore-cdk`、Observabilityは `/kb-agentcore-observability` を参照。

## 基本情報

### Strands Agents
- 公式: https://strandsagents.com/
- GitHub: https://github.com/strands-agents/strands-agents
- Python 3.10以上が必要

### Bedrock AgentCore
- 15リージョンで利用可能（us-east-1, us-west-2, ap-northeast-1 等）
- Evaluations機能のみ一部リージョン限定（東京は非対応）

## インストール

```bash
# pip
pip install strands-agents bedrock-agentcore

# uv
uv add strands-agents bedrock-agentcore
```

### AWS CLI login 認証を使う場合
```bash
uv add 'botocore[crt]'
```
`aws login` で認証した場合、botocore[crt] が必要。これがないと認証エラーになる。

---

## Agent作成

### 基本構造
```python
from strands import Agent

agent = Agent(
    model="us.anthropic.claude-sonnet-4-5-20250929-v1:0",
    system_prompt="あなたはアシスタントです",
)
```

### 利用可能なモデル（Bedrock）

クロスリージョン推論のプレフィックスはリージョンによって異なる：

| リージョン | プレフィックス |
|-----------|--------------|
| us-east-1, us-west-2 | `us.` |
| ap-northeast-1（東京） | `jp.` |

```python
# リージョンに応じてプレフィックスを自動判定
import os

def _get_model_id() -> str:
    region = os.environ.get("AWS_REGION", "us-east-1")
    prefix = "jp" if region == "ap-northeast-1" else "us"
    return f"{prefix}.anthropic.claude-sonnet-4-5-20250929-v1:0"
```

---

## 実行方法

### 同期実行
```python
result = agent(prompt)
print(result)
```

### 非同期実行
```python
result = await agent.invoke_async(prompt)
```

### ストリーミング（同期）
```python
for event in agent.stream(prompt):
    if "data" in event:
        print(event["data"], end="", flush=True)
```

### ストリーミング（非同期）
```python
async for event in agent.stream_async(prompt):
    if "data" in event:
        print(event["data"], end="", flush=True)
```

---

## イベントタイプ

ストリーミング時に受け取るイベント：

| イベント | 説明 |
|---------|------|
| `data` | テキストチャンク（LLMの出力） |
| `current_tool_use` | ツール使用情報 |
| `result` | 最終結果 |

```python
async for event in agent.stream_async(prompt):
    if "data" in event:
        # テキストチャンク
        print(event["data"], end="")
    elif "current_tool_use" in event:
        # ツール使用中
        tool_info = event["current_tool_use"]
        print(f"Using tool: {tool_info['name']}")
    elif "result" in event:
        # 完了
        final_result = event["result"]
```

### current_tool_use の input はストリーミング中は文字列型

`current_tool_use` イベントの `input` フィールドは、ストリーミング中は**不完全なJSON文字列**として徐々に構築される。辞書型を期待している場合はJSONパースが必要：

```python
elif "current_tool_use" in event:
    tool_info = event["current_tool_use"]
    tool_name = tool_info.get("name", "unknown")
    tool_input = tool_info.get("input", {})

    # inputが文字列の場合はJSONパースを試みる
    if isinstance(tool_input, str):
        try:
            import json
            tool_input = json.loads(tool_input)
        except json.JSONDecodeError:
            pass  # パースできない場合はそのまま（不完全なJSON）

    # パース成功時のみ辞書として扱える
    if isinstance(tool_input, dict) and "query" in tool_input:
        print(f"Search query: {tool_input['query']}")
```

**ポイント**: ストリーミング中はイベントが複数回発火し、`{"query"` -> `{"query": "検索` -> `{"query": "検索ワード"}` のように徐々に完成する。完全なJSONになったタイミングでのみパースが成功する。

**重要: バックエンドで重複スキップしてはいけない**

`current_tool_use` の重複イベントをバックエンドで `continue` してはいけない。理由：
- 最初のチャンクの `input` は不完全なJSON文字列（例: `"{\"qu"`）
- JSONパースが失敗し、`query` 等の必要なパラメータが取得できない
- 後続チャンク（パラメータが完成したもの）がスキップされ、イベントが一切フロントに送信されなくなる

重複の吸収はフロントエンド側（`hasInProgress` チェック等）で行うのが正しい。

```python
# NG: バックエンドで重複スキップ -> 最初のチャンク（input不完全）のみ処理される
if tool_name == last_tool_name:
    continue  # 2回目以降のチャンク（inputが完全）がスキップされる！
last_tool_name = tool_name

# OK: 重複スキップせず、条件に合うときだけyield（フロント側で重複吸収）
if tool_name == "web_search":
    if isinstance(tool_input, dict) and "query" in tool_input:
        yield {"type": "tool_use", "data": tool_name, "query": tool_input["query"]}
    # queryが不完全なチャンクではyieldしない -> 完成したチャンクでyieldされる
else:
    yield {"type": "tool_use", "data": tool_name}
```

---

## ツールの定義

### 関数デコレータ方式
```python
from strands import Agent, tool

@tool
def get_weather(city: str) -> str:
    """指定した都市の天気を取得します。

    Args:
        city: 都市名

    Returns:
        天気情報
    """
    return f"{city}の天気は晴れです"

agent = Agent(
    model="us.anthropic.claude-sonnet-4-5-20250929-v1:0",
    tools=[get_weather],
)
```

### クラス方式
```python
from strands import Agent, Tool

class WeatherTool(Tool):
    name = "get_weather"
    description = "指定した都市の天気を取得します"

    def run(self, city: str) -> str:
        return f"{city}の天気は晴れです"

agent = Agent(
    model="us.anthropic.claude-sonnet-4-5-20250929-v1:0",
    tools=[WeatherTool()],
)
```

### ツール駆動型の出力パターン

LLMの出力をフロントエンドでフィルタリングするのが難しい場合、出力専用のツールを作成してツール経由で出力させる方式が有効。

```python
# グローバル変数で出力を保持
_generated_markdown: str | None = None

@tool
def output_slide(markdown: str) -> str:
    """生成したスライドのマークダウンを出力します。

    Args:
        markdown: Marp形式のマークダウン全文

    Returns:
        出力完了メッセージ
    """
    global _generated_markdown
    _generated_markdown = markdown
    return "スライドを出力しました。"

agent = Agent(
    model="us.anthropic.claude-sonnet-4-5-20250929-v1:0",
    system_prompt="スライドを作成したら、必ず output_slide ツールを使って出力してください。",
    tools=[output_slide],
)
```

**メリット**:
- フロントエンドでのテキスト除去処理が不要
- ツール使用中のステータス表示が容易
- マークダウンがテキストストリームに混入しない

### 外部APIカスタムツール（追加パッケージ不要）

外部REST APIを呼ぶカスタムツールは `urllib.request`（標準ライブラリ）で実装すると、requirements.txtに追加パッケージ不要で済む。

```python
import json
import os
import urllib.request

from strands import tool

TAVILY_API_KEY = os.environ.get("TAVILY_API_KEY", "")

@tool
def web_search(query: str) -> str:
    """ウェブ検索を行い、最新の情報を取得します。

    Args:
        query: 検索クエリ

    Returns:
        検索結果のテキスト
    """
    req = urllib.request.Request(
        "https://api.tavily.com/search",
        data=json.dumps({
            "query": query,
            "max_results": 5,
            "search_depth": "basic",
            "include_answer": True,
        }).encode("utf-8"),
        headers={
            "Authorization": f"Bearer {TAVILY_API_KEY}",
            "Content-Type": "application/json",
        },
        method="POST",
    )

    with urllib.request.urlopen(req, timeout=30) as resp:
        result = json.loads(resp.read().decode("utf-8"))

    parts = []
    if result.get("answer"):
        parts.append(f"【要約】\n{result['answer']}")
    for item in result.get("results", []):
        title = item.get("title", "")
        url = item.get("url", "")
        content = item.get("content", "")
        parts.append(f"■ {title}\n{url}\n{content}")

    return "\n\n".join(parts) if parts else "検索結果が見つかりませんでした。"
```

**ポイント**: `tavily-python` パッケージを使う方法もあるが、`urllib.request` なら追加依存なし。Docker/AgentCoreのビルド時間短縮にも有効。

---

## 会話履歴の管理

```python
from strands import Agent

agent = Agent(model="us.anthropic.claude-sonnet-4-5-20250929-v1:0")

# 会話を継続
response1 = agent("私の名前は太郎です")
response2 = agent("私の名前は何ですか？")  # 「太郎」と答える

# 履歴をクリア
agent.clear_history()
```

### SlidingWindowConversationManager（履歴トリミング）

トークンコスト削減のため、古いメッセージを自動削除する組み込み機能。

```python
from strands import Agent
from strands.agent.conversation_manager import SlidingWindowConversationManager

agent = Agent(
    model=model,
    system_prompt=SYSTEM_PROMPT,
    tools=tools,
    conversation_manager=SlidingWindowConversationManager(window_size=6),
)
```

#### パラメータ

| パラメータ | デフォルト | 説明 |
|-----------|----------|------|
| `window_size` | 40 | 保持する最大**メッセージ数**（ターン数ではない） |
| `should_truncate_results` | True | ツール結果の圧縮を有効化 |
| `per_turn` | False | `False`: 完了後のみ / `True`: 毎回 / `int(N)`: N回ごとにトリミング |

#### window_size の意味

`window_size` は **Bedrock APIのメッセージ配列の要素数**。1つのメッセージが複数の content block（text, toolUse, toolResult）を持ちうるため、実際のテキスト量は window_size だけでは決まらない。

**典型的な1リクエストのメッセージ数（ツール2つ使用時）:**

```
[user]      ユーザーメッセージ          ... 1
[assistant] toolUse: web_search         ... 2
[user]      toolResult: 検索結果        ... 3
[assistant] toolUse: output_slide       ... 4
[user]      toolResult: スライド出力    ... 5
[assistant] 最終テキスト応答           ... 6
```

-> 1リクエスト = 6メッセージ -> `window_size=6` で約1リクエスト分を保持

#### トリミングアルゴリズム（2段階）

1. **フェーズ1: ツール結果の圧縮（優先）**
   - 古い toolResult の内容を `"The tool result was too large!"` に置換
   - メッセージ数は減らさない
   - 同じ toolResult を2回圧縮しない

2. **フェーズ2: メッセージの削除（最終手段）**
   - フェーズ1で不十分な場合、古いメッセージを削除
   - **toolUse/toolResult ペアの整合性を保持**（ペアが壊れない位置で削除）

#### per_turn パラメータの使い分け

```python
# デフォルト: エージェントループ完了後にのみトリミング
# -> 処理中は全メッセージが利用可能（品質重視）
SlidingWindowConversationManager(window_size=6)

# 毎回のモデル呼び出し前にトリミング
# -> ループが多いユースケースでトークン爆発を防止
SlidingWindowConversationManager(window_size=10, per_turn=True)

# N回ごとにトリミング（パフォーマンスバランス）
SlidingWindowConversationManager(window_size=10, per_turn=5)
```

#### per_turn=True と並列ツール実行の非互換性

**`per_turn=True` は、エージェントが複数ツールを並列発行するユースケースでは使用禁止。**

Strands Agents は並列ツール（例: `web_search` x2）の結果を1件ずつ個別にセッション履歴に追加する。`per_turn=True` だと各LLMコール前にトリミングが走り、以下の正のフィードバックループが発生する：

1. LLMが `web_search` x2 を並列発行
2. ツール結果1件目が履歴に追加 -> トリミング発生
3. 「検索結果が1件しかない」状態でLLMが呼ばれる
4. LLMは「情報不足」と判断して追加の `web_search` を発行
5. 1-4が連鎖 -> 1セッションで web_search が16-20回に増殖

さらに `output_slide` 等のツール結果が "The tool result was too large!" に圧縮され、ツールの正常動作も阻害される。

**推奨**: `per_turn=False`（デフォルト）のまま使用し、コスト削減はツール結果のサイズ制限（要約等）で対応する。

#### window_size チューニングの考え方

- フロントエンドが毎回最新コンテキスト（例: 生成済みMarkdown）を送信する設計なら、古い履歴は不要 -> 小さい window_size でOK
- `per_turn=False`（デフォルト）の場合、処理中のメッセージはトリミングされないため品質に影響しない
- トリミングの効果は **多ターンセッション** で最大化される

---

## Bedrock AgentCore との統合

### 基本構造
```python
from bedrock_agentcore import BedrockAgentCoreApp
from strands import Agent

app = BedrockAgentCoreApp()
agent = Agent(model="us.anthropic.claude-sonnet-4-5-20250929-v1:0")

@app.entrypoint
async def invoke(payload):
    prompt = payload.get("prompt", "")
    stream = agent.stream_async(prompt)
    async for event in stream:
        yield event

if __name__ == "__main__":
    app.run()  # ポート8080でリッスン
```

### エンドポイント
- `POST /invocations` - エージェント実行
- `GET /ping` - ヘルスチェック

### 必要な依存関係
```
# requirements.txt
bedrock-agentcore
strands-agents
tavily-python  # Web検索が必要な場合
```

**注意**: fastapi/uvicorn は不要（bedrock-agentcore SDKに内包）

### セッションIDでAgentを管理（複数ユーザー対応）

AgentCoreで複数ユーザーの会話履歴を保持する場合、セッションIDごとにAgentインスタンスを管理する：

```python
from strands import Agent

# セッションごとのAgentインスタンスを管理
_agent_sessions: dict[str, Agent] = {}

def get_or_create_agent(session_id: str | None) -> Agent:
    """セッションIDに対応するAgentを取得または作成"""
    # セッションIDがない場合は新規Agentを作成（履歴なし）
    if not session_id:
        return Agent(
            model="us.anthropic.claude-sonnet-4-5-20250929-v1:0",
            system_prompt="...",
            tools=[...],
        )

    # 既存のセッションがあればそのAgentを返す
    if session_id in _agent_sessions:
        return _agent_sessions[session_id]

    # 新規セッションの場合はAgentを作成して保存
    agent = Agent(
        model="us.anthropic.claude-sonnet-4-5-20250929-v1:0",
        system_prompt="...",
        tools=[...],
    )
    _agent_sessions[session_id] = agent
    return agent

@app.entrypoint
async def invoke(payload):
    session_id = payload.get("session_id")
    agent = get_or_create_agent(session_id)
    # ...
```

**注意**: コンテナ再起動でセッションは消える（メモリ管理）。永続化が必要な場合はDynamoDB等を検討。

### SSE keep-alive パターン（長時間処理のコネクション維持）

同期的な重い処理（ファイル変換、外部API呼び出し等）をSSEで返す場合、処理中にkeep-aliveイベントを送信してコネクションを維持する。`asyncio.run_in_executor` + `asyncio.shield` + タイムアウトの組み合わせ。

```python
import asyncio

async def _wait_with_keepalive(task, format_name):
    """タスク完了を待ちつつ、5秒ごとにSSE keep-aliveイベントをyield"""
    while not task.done():
        try:
            await asyncio.wait_for(asyncio.shield(task), timeout=5.0)
        except asyncio.TimeoutError:
            yield {"type": "progress", "message": f"{format_name}変換中..."}

@app.entrypoint
async def invoke(payload, context=None):
    if action == "export_pptx" and markdown:
        try:
            print(f"[INFO] PPTX export started")
            loop = asyncio.get_event_loop()
            task = loop.run_in_executor(None, generate_pptx, markdown, theme)
            async for event in _wait_with_keepalive(task, "PPTX"):
                yield event  # 5秒ごとにprogressイベント送信
            result_bytes = task.result()
            yield {"type": "pptx", "data": base64.b64encode(result_bytes).decode()}
        except Exception as e:
            print(f"[ERROR] PPTX export failed: {e}")
            yield {"type": "error", "message": str(e)}
        return
```

**ポイント**:
- `asyncio.shield(task)` で TimeoutError 時もタスクがキャンセルされない
- `task.done()` でループ脱出を判定、`task.result()` で結果取得
- フロントエンドのSSEパーサーは未知の `type` を無視するため、既存コードの変更不要

### ツール使用イベント送信
```python
@app.entrypoint
async def invoke(payload):
    global _generated_markdown
    _generated_markdown = None

    stream = agent.stream_async(payload.get("prompt", ""))
    async for event in stream:
        if "data" in event:
            yield {"type": "text", "data": event["data"]}
        elif "current_tool_use" in event:
            tool_name = event["current_tool_use"].get("name", "unknown")
            yield {"type": "tool_use", "data": tool_name}

    if _generated_markdown:
        yield {"type": "markdown", "data": _generated_markdown}
```

---

## Bedrockプロンプトキャッシュが突然停止する

**症状**: Cost ExplorerのCacheWrite/CacheRead費用が特定のコミット以降ずっと0になる

**原因**: Bedrockのprompt cachingは**ツール定義の合計トークンが1024以上**必要。ツールのdocstringを短くしたり、リッチなビルトインツール（`strands_tools.http_request` 等）をシンプルなカスタムツールに置き換えると、合計が1024を割り込んでキャッシュが機能停止する。

| 状態 | http_requestのトークン数 | ツール合計 | キャッシュ |
|------|------------------------|-----------|----------|
| strands_tools版（21パラメータ） | ~884 tokens | ~1096 | 動作 |
| カスタム版（2パラメータ、docstring短い） | ~90 tokens | ~302 | 停止 |

**診断方法**:
1. Cost Explorerでモデル別にCacheWrite/CacheReadを確認し、0になり始めた日付を特定
2. その日付のコミットで `@tool` 付き関数のdocstringが短くなっていないか確認

**解決策**: docstringを拡充して合計1024トークン以上に戻す（パラメータの詳細説明・使用例・注意事項を追加すると大幅に増やせる）

**`@tool` デコレータとトークン数の関係**:
- Strands Agentsの `@tool` デコレータはPython関数のdocstringをBedrockへのツール説明（tool spec）として送信する
- パラメータ数・docstringの文字数がそのまま毎リクエストのトークンコストになる
- docstringが増えてもキャッシュリード時は90%オフになるため、キャッシュが有効な状態では長いdocstringでもコスト増にはならない

**Bedrockキャッシュの課金構造（Sonnet 4.6）**:
| 種別 | 単価 | 備考 |
|------|------|------|
| 通常インプット | $3.00/MTok | キャッシュなし時 |
| キャッシュライト | $3.75/MTok | 初回（25%高い） |
| キャッシュリード | $0.30/MTok | 2回目以降（**90%オフ**） |

---

## トラブルシューティング

### AWS認証エラー
`aws login` で認証した場合、`botocore[crt]` が必要：
```bash
uv add 'botocore[crt]'
```

### モデルが見つからない
クロスリージョン推論のモデルID（`us.` プレフィックス）を使用しているか確認。
リージョンによって利用可能なモデルが異なる。

### ストリーミングが動かない
`stream()` と `stream_async()` を環境に合わせて使い分ける：
- 同期コンテキスト -> `stream()`
- 非同期コンテキスト（async/await） -> `stream_async()`

### Kimi K2関連

Kimi K2（Moonshot AI）特有の問題は `/kb-kimi` スキルを参照してください。

---

## 関連スキル

- `/kb-agentcore-cdk` - AgentCore CDK、デプロイ、ランタイム統合、コンテナ構成
- `/kb-agentcore-observability` - OpenTelemetry、ログ、メトリクス、トレース
- `/kb-agentcore-identity` - アウトバウンド認証（3LO/M2M/デコレータ分離/callback等）
- `/kb-bidi-agent` - BidiAgent（双方向ストリーミング / 音声対話 / Nova Sonic）
- `/kb-kimi` - Kimi K2（Moonshot AI）特有の問題・ワークアラウンド

## 参考リンク

- [Strands Agents 公式ドキュメント](https://strandsagents.com/)
- [GitHub リポジトリ](https://github.com/strands-agents/strands-agents)
- [Bedrock AgentCore 統合ガイド](https://docs.aws.amazon.com/bedrock/latest/userguide/agents-agentcore.html)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/minorun365) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
