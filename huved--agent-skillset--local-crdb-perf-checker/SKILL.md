---
name: local-crdb-perf-checker
description: Local-only CockroachDB 성능 점검 자동화 스킬. 로컬 CockroachDB 단일 노드를 띄우고(선택), 프로젝트 DDL을 로컬 DB에 적용한 뒤(seed SQL 실행 포함), 실행된 쿼리(Statement Statistics)와 선택된 쿼리의 EXPLAIN ANALYZE를 기반으로 성능 진단을 수행한다. 결과는 “개선 필요 항목 + 개선 방안”이 포함된 CSV로 출력한다. 원격/클라우드 DB 연결 문자열이면 즉시 실패해야 한다(로컬 DB만 허용). Use when this capability is needed.
metadata:
  author: huved
---

# local-crdb-perf-checker

## 목표

로컬 CockroachDB만 사용해서 다음을 반복 가능하게 수행한다(언어/프레임워크 무관).

1) 로컬 perf DB 초기화 + 프로젝트 DDL 적용  
2) 시드 SQL 실행(프로젝트가 제공하는 seed/migration SQL)  
3) 실행된 쿼리 리스트 분석(Statement Statistics) 및 CSV 리포트 생성(개선 방안 포함)  
4) (선택) 특정 쿼리에 대해 `EXPLAIN ANALYZE` 측정 후 CSV 리포트 생성  

## 핵심 안전 규칙 (반드시 지킬 것)

- 로컬 DB만 허용한다. `postgresql://...@localhost/...`, `127.0.0.1`, `::1` 이외의 호스트면 **무조건 실패**한다.
- `DROP DATABASE` 같은 파괴적 작업이 포함되므로, 로컬이 아닌 URL은 절대 실행하지 않는다(스크립트가 강제해야 한다).

## 빠른 시작(범용)

아래 예시는 “현재 작업 디렉터리 = 프로젝트 루트”를 가정한다.

### ✅ 무잔여 모드(권장): 최종 CSV 리포트만 남기기

- 원칙: **임시 작업 디렉터리(`mktemp`)에 DB store/벤치/아티팩트 등 “작업물”을 모두 저장**하고, 최종 산출물인 CSV 리포트만 원하는 경로에 남긴다.

```bash
# 작업용 임시 디렉터리(종료 시 자동 정리)
WORKDIR="$(mktemp -d)"
trap 'rm -rf "$WORKDIR"' EXIT

STORE="$WORKDIR/cockroach-store"
export CONNECTION_STRING='postgresql://root@127.0.0.1:26257/perf_db?sslmode=disable'

# 최종 산출물(리포트) 경로만 프로젝트 내/외 원하는 곳으로 지정
OUT_SQL_STATS="$(pwd)/sql_stats.csv"

python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" start-crdb \
  --listen-addr 127.0.0.1:26257 \
  --http-addr 127.0.0.1:8081 \
  --store "$STORE"

python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" reset-db \
  --url "$CONNECTION_STRING" \
  --ddl ./path/to/ddl.sql

python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" apply-sql \
  --url "$CONNECTION_STRING" \
  --file ./path/to/seed.sql

python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" reset-sql-stats \
  --url "$CONNECTION_STRING"

# 워크로드 실행(직접 수행): 서버 실행+curl / E2E / 배치 등

python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" export-sql-stats \
  --url "$CONNECTION_STRING" \
  --since-hours 24 \
  --out "$OUT_SQL_STATS"

python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" stop-crdb \
  --host 127.0.0.1:26257

echo "OK: report=$OUT_SQL_STATS"
```

#### (선택) EXPLAIN ANALYZE도 “무잔여”로 실행하기

- `bench.json`와 `artifacts/`는 **임시 디렉터리**에 두고, 최종 결과 CSV만 원하는 경로에 남긴다.

```bash
BENCH_JSON="$WORKDIR/bench.json"
ARTIFACTS_DIR="$WORKDIR/artifacts"
OUT_EXPLAIN="$(pwd)/explain_results.csv"

python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" make-bench \
  --out "$BENCH_JSON"

# BENCH_JSON을 프로젝트 쿼리로 수정한 뒤 실행
python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" benchmark \
  --url "$CONNECTION_STRING" \
  --bench "$BENCH_JSON" \
  --out "$OUT_EXPLAIN" \
  --artifacts "$ARTIFACTS_DIR"
```

---

### 🧪 디버그 모드(작업물 유지): 로그/아티팩트 보존

- 아래 방식은 `./.cockroach-data-perf`, `./scripts/perf/*` 등 작업 파일이 남을 수 있다(원인 추적/재현에는 유용).

1) 로컬 CockroachDB 실행(이미 떠 있으면 생략, 로컬 바인딩 강제)

    python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" start-crdb \\
      --listen-addr 127.0.0.1:26257 \\
      --http-addr 127.0.0.1:8081 \\
      --store ./.cockroach-data-perf

2) perf DB 초기화 + DDL 적용(DDL 파일은 반복 지정 가능)

    export CONNECTION_STRING='postgresql://root@127.0.0.1:26257/perf_db?sslmode=disable'
    python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" reset-db \\
      --url "$CONNECTION_STRING" \\
      --ddl ./path/to/ddl.sql

3) 시드 SQL 실행(프로젝트가 제공하는 seed SQL을 그대로 실행)

    python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" apply-sql \\
      --url "$CONNECTION_STRING" \\
      --file ./path/to/seed.sql

4) SQL 통계 리셋(이후 실행되는 “프로젝트 쿼리”만 수집하기 위함)

    python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" reset-sql-stats \\
      --url "$CONNECTION_STRING"

5) 워크로드 실행(직접 수행)

- 예: 서버 실행 후 curl로 주요 API 호출 / E2E 테스트 수행 / 배치 잡 실행 등
- 가능하면 DB 접속 시 `application_name`을 설정하여(DSN 파라미터) 쿼리 출처를 구분한다.

6) 실행된 쿼리 성능 리포트(CSV) 출력(개선 방안 포함)

    mkdir -p ./scripts/perf
    python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" export-sql-stats \\
      --url "$CONNECTION_STRING" \\
      --since-hours 24 \\
      --out ./scripts/perf/sql_stats.csv

7) (선택) 정적 쿼리 발견(코드베이스 내 raw SQL 문자열 기반, best-effort)

    python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" discover-queries \\
      --repo . \\
      --out ./scripts/perf/queries.csv

8) (선택) `EXPLAIN ANALYZE` 벤치: 템플릿 생성 → 수정 후 실행

    mkdir -p ./scripts/perf
    python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" make-bench \\
      --out ./scripts/perf/bench.json

9) (선택) `EXPLAIN ANALYZE` 실행 → CSV 출력(+ explain 원본 아티팩트 저장)

    python3 "$CODEX_HOME/skills/local-crdb-perf-checker/scripts/perfcheck.py" benchmark \\
      --url "$CONNECTION_STRING" \\
      --bench ./scripts/perf/bench.json \\
      --out ./scripts/perf/results.csv \\
      --artifacts ./scripts/perf/artifacts

## 출력 규칙

- 최종 결과물은 CSV로 남긴다(= “리포트”).
  - 실행 쿼리 기반: `export-sql-stats --out <path>.csv`
  - EXPLAIN 기반: `benchmark --out <path>.csv`
- CSV에는 “성능 개선 필요 항목(issues)”과 “개선 방안(suggestions)”이 반드시 포함되어야 한다.
- EXPLAIN 원본(`--artifacts`)은 필요할 때만 남긴다. **무잔여 모드에서는 임시 디렉터리에 저장 후 자동 삭제**하는 방식을 우선한다.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/huved) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
