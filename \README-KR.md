<div align="center">

# ARTEX

AI 자율 침투 테스트 시스템 (Go 백엔드 + Next.js 프론트엔드)


🌐 **온라인 데모**: [https://artex-demo.vercel.app/](https://artex-demo.vercel.app/)

</div>

---

## 스크린샷 미리보기

> 전체 상호작용은 [온라인 데모](https://artex-demo.vercel.app/)를 참고하세요.

| 대시보드 (개요 / 토큰 소비 / 활동 스트림) | 작업 목록 |
| :---: | :---: |
| ![대시보드](screenshots/dashboard.png) | ![작업](screenshots/tasks.png) |

| 작업 · 실행 과정 (세션 / 도구 호출) | 탐색 링크 |
| :---: | :---: |
| ![실행 과정](screenshots/sessions.png) | ![링크](screenshots/graph.png) |

| 발견 사항 | 자산 |
| :---: | :---: |
| ![발견](screenshots/findings.png) | ![자산](screenshots/assets.png) |

| 자산 커버리지 맵 (포스 피드백 레이아웃 · 테스트 완료 하이라이트 · 노드 접기/펼치기) |
| :---: |
| ![자산 커버리지 맵](screenshots/assets_test.png) |

| 트래픽 레코딩 | 휴먼 인 더 루프 대화 |
| :---: | :---: |
| ![트래픽](screenshots/traffic.png) | ![대화](screenshots/chat.png) |

| 에이전트 관리 | LLM 설정 |
| :---: | :---: |
| ![에이전트](screenshots/agents.png) | ![LLM](screenshots/llm.png) |

| 인터셉트 승인 | 백엔드 로그 |
| :---: | :---: |
| ![인터셉트](screenshots/intercept.png) | ![로그](screenshots/logs.png) |


---

## 승인 기록 상세

전역 '승인 기록', 작업 내 '인터셉트 승인' 및 대화 내 승인 카드는 모두 상세 내용을 펼쳐 볼 수 있습니다. 표시 구조는 [AegisHook의 CallDetail 컴포넌트](https://github.com/RuoJi6/AegisHook/blob/main/web/src/components/CallDetail.vue)를 참고하였으며, ARTEX의 컴포넌트와 테마를 계승합니다.


## 자산 동기화 (ScopeSentry)

[ScopeSentry](https://github.com/Autumn-27/ScopeSentry)에서 자산 데이터를 직접 동기화하여 중복 수집을 방지할 수 있습니다.

- 「**자산 동기화**」 페이지에서 ScopeSentry의 주소와 API Key를 입력하여 데이터 소스를 연동합니다.
- **프로젝트** 또는 **작업** 단위로 동기화할 타겟과 자산 유형(도메인 / 서브도메인 / IP / 포트 / 사이트 / 엔드포인트 등)을 선택합니다.
- 원클릭으로 가져와 회사 자산 범위에 맞춰 병합한 뒤, ARTEX의 자산 그래프에 바로 진입하여 에이전트 탐색에 활용할 수 있습니다.

---

## 설치

> 데이터베이스 **PostgreSQL** 필수; 탐색 기능 이용 시 **LLM** 설정 필요 (`ANTHROPIC_API_KEY` 또는 `OPENAI_API_KEY`, UI에서도 설정 가능).

### 방법 1: 원클릭 설치 스크립트 (권장)

```bash
git clone [https://github.com/Autumn-27/ARTEX.git](https://github.com/Autumn-27/ARTEX.git)
cd ARTEX
./install.sh

```

스크립트 기능: Postgres 감지 / 자동 설치 → **① 전체 Docker** 또는 **② 로컬 컴파일 실행** 중 선택:

* **① 전체 Docker**: Postgres 비밀번호 입력 (엔터 시 랜덤 생성) → `.env` 자동 작성 → `docker compose up -d`.
* **② 로컬 실행**: 데이터베이스 선택 (기존 연결 / Docker로 구동) → `config.json` 생성 → `go`로 내장 단일 바이너리 컴파일 → 실행.

설치 완료 후 **http://localhost:8787** 접속 (최초 접속 시 `/setup`에서 관리자 비밀번호 설정).

### 방법 2: Docker Compose (수동)

```bash
git clone [https://github.com/Autumn-27/ARTEX.git](https://github.com/Autumn-27/ARTEX.git)
cd ARTEX
cp .env.example .env         # POSTGRES_PASSWORD, 선택적으로 ANTHROPIC_API_KEY 입력
docker compose up -d         # autumn27/artex 이미지 + postgres 풀링
# → http://localhost:8787

```

이미지에는 자주 사용되는 도구(ripgrep/curl/vim/npm/nmap 등)가 포함되어 있으며, `./skills`와 `./data`는 바인드 마운트로 지속 유지됩니다.

원격 MCP는 시스템 설정에서 `http`(Streamable HTTP) 또는 `sse`(구버전 SSE)를 선택할 수 있습니다.
구버전 SSE 서비스는 일반적으로 `GET /sse`로 이벤트 스트림을 생성한 뒤, 서비스가 반환하는 `/message?sessionId=...`를 통해 JSON-RPC 요청을 수신합니다. 설정 시 URL을 `/sse`로 입력하고 요청 헤더에 `Authorization=Bearer <token>`을 작성하세요.

### 방법 3: 사전 컴파일된 바이너리 다운로드 (Releases)

[Releases](https://github.com/Autumn-27/ARTEX/releases)에서 각 플랫폼에 맞는 zip 파일을 다운로드한 후 압축을 풀면 `artex` + `start.sh`(Windows의 경우 `start.bat`) + `skills/` + `config.example.json`이 포함되어 있습니다.

```bash
cp config.example.json config.json   # 데이터베이스 연결 정보 입력
./start.sh                           # → http://localhost:8787

```

> `./artex`를 직접 실행하지 말고 `start.sh` / `start.bat`을 사용하여 실행하세요. 이 스크립트는 종료 코드에 따라 프로세스를 재시작할지 여부를 결정하는 데몬 스크립트이며, **페이지 내의 [원클릭 업데이트](https://www.google.com/search?q=%23%EB%B0%A9%EB%B2%95-1%ED%8E%98%EC%9D%B4%EC%A7%80-%EC%9B%90%ED%81%B4%EB%A6%AD-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8-%EA%B6%8C%EC%9E%A5)가 이를 통해 이루어집니다**. `./artex`를 직접 실행하면 업데이트 후 재시작되지 않습니다.
> 백그라운드 상주 실행: `nohup ./start.sh >artex.log 2>&1 &`

### 방법 4: 소스 코드를 통한 단일 바이너리 컴파일

```bash
# 1) 프론트엔드 정적 내보내기
cd web && npm ci && npm run build:static && cd ..
# 2) 내장 디렉터리로 복사
cp -r web/out server/webui/dist
# 3) 컴파일 (-tags embedui 플래그로 프론트엔드 내장)
CGO_ENABLED=0 go build -tags embedui -o artex ./cmd/artex
./start.sh

```

### 방법 5: 크로스 플랫폼 Release 압축파일 빌드

`build.sh`는 프론트엔드를 먼저 빌드하여 내장한 뒤, Go 링커를 사용해 디버깅 정보를 제거하고 배포 파일을 zip으로 압축합니다. Release 모드는 기본적으로 Linux amd64/arm64, macOS amd64/arm64, Windows amd64용 zip 패키지를 생성합니다.

```bash
./build.sh --release
# 산출물: dist/artex-0.3.3-*.zip

```

UPX 자체 압축 바이너리는 일부 Linux 커널, 가상화 환경 또는 보안 정책과 호환되지 않을 수 있으므로 기본적으로 비활성화되어 있습니다. `ARTEX_TARGETS`를 사용하여 타겟을 사용자 정의할 수 있으며, 타겟 실행 환경의 호환성이 확인된 경우 `--upx`를 명시하여 바이너리 크기를 더 줄일 수 있습니다.

```bash
ARTEX_TARGETS=linux/amd64,windows/amd64 ./build.sh --release
./build.sh --target linux/amd64 --upx

```

---

## 업데이트 및 업그레이드

> 업그레이드는 프로그램만 교체하고 데이터는 유지합니다: Postgres 데이터 볼륨 `pgdata`, `./data`(jwt.key / SQLite 등), `./skills`는 모두 유지됩니다. **데이터베이스 마이그레이션을 수동으로 실행할 필요가 없습니다** — `artex`는 매번 시작할 때마다 `schema.sql`(부속 `ADD COLUMN` / `CREATE INDEX IF NOT EXISTS` 포함)을 멱등성 있게 재실행합니다(즉, "재시작이 곧 마이그레이션"). 업그레이드 전 `./data`와 데이터베이스를 미리 백업하는 것을 권장합니다.

### 방법 1: 페이지 내 원클릭 업데이트 (권장)

**시스템 설정** 페이지 (사이드바 「시스템 설정」 → `/system/settings`)의 **버전 및 업데이트** 카드에서 서버에 로그인할 필요 없이 새 버전을 직접 확인하고 설치할 수 있습니다.

'업데이트' 클릭 시: 현재 플랫폼의 배포 패키지 다운로드 → Release의 `SHA256SUMS` 비교 → `-h`로 새 바이너리 스모크 테스트 → `artex.new`로 임시 저장 → 프로세스 종료 후 `start.sh` / `start.bat`을 통해 재시작 및 교체 완료. 페이지는 새 버전이 온라인 상태가 될 때까지 자동으로 대기한 후 새로고침됩니다.

* **실패 시 손상된 프로그램이 남지 않음**: 검증이나 스모크 테스트 실패 시 임시 파일을 폐기하고 현재 버전을 유지합니다. 교체된 새 버전이 연속 3회 실행에 실패하면 자동으로 `artex.old`로 롤백됩니다 (실패한 버전은 문제 분석을 위해 `artex.failed`로 보관).
* **언제든지 롤백 가능**: 이전 버전이 `artex.old`로 보관되며, 카드에 '이전 버전으로 롤백' 버튼이 있습니다. 단, 데이터베이스 구조는 롤백되지 않습니다.
* **업데이트 시 실행 중인 작업이 중단됨** — 업데이트는 곧 재시작을 의미하므로 유휴 시간에 진행해 주세요.
* **개발 빌드는 업데이트 불가**: 버전 번호가 `dev` 또는 `git describe` 접미사를 포함하는 경우 정식 버전이 로컬 디버깅용 바이너리를 덮어쓰는 것을 방지하기 위해 비활성화됩니다.
* **Docker 환경에서는 프로그램만 교체되고 이미지는 교체되지 않음**: 이미지 내의 playwright, nmap 등의 툴체인은 함께 업그레이드되지 않으며, `docker compose up -d`로 컨테이너를 재생성하면 이미지 내장 버전으로 돌아갑니다. 이미지까지 함께 업그레이드하려면 `docker compose pull artex && docker compose up -d artex`를 사용하세요.
* GitHub 접속 시 프록시가 필요한 경우, 동일 페이지에서 **전역 프록시**를 설정하면 업데이트 경로가 해당 프록시를 거치게 됩니다. 업데이트는 GitHub 도메인에서만 다운로드하며 강제로 HTTPS를 사용합니다.

### 방법 2: 원클릭 업데이트 스크립트

```bash
cd ARTEX
./update.sh

```

스크립트를 통해 `git pull`을 통한 최신 코드 가져오기 여부를 선택한 후, **① Docker 업데이트** 또는 **② 로컬 컴파일 업데이트**(`install.sh`와 대응) 중 선택할 수 있습니다.

* **① Docker**: 타겟 이미지 태그 지정 가능 (엔터 시 `.env`의 `ARTEX_TAG` 유지, 기본값 `latest`) → `docker compose pull` → `docker compose up -d`(새 이미지로 재시작 시 자동 마이그레이션).
* **② 로컬**: 프론트엔드 정적 산출물 재생성 → `./artex` 재컴파일 (완료 후 프로세스 재시작으로 적용).

### 방법 3: Docker Compose (수동)

```bash
cd ARTEX
git pull               # compose / 스크립트 업데이트 (선택사항)
# 특정 버전 지정: .env에 ARTEX_TAG=v0.2.0 설정; 미설정 시 latest 사용
docker compose pull artex
docker compose up -d artex     # 새 이미지로 재시작 → schema 자동 마이그레이션
docker image prune -f          # 구형 이미지 정리 (선택사항)

```

### 방법 4: 사전 컴파일된 바이너리 (Releases)

[Releases](https://github.com/Autumn-27/ARTEX/releases)에서 새 버전 zip을 다운로드하고, 기존 프로세스를 중단한 뒤 `artex`와 `skills/`를 덮어씁니다(`config.json`과 `data/`는 유지). 그 후 재시작합니다.

```bash
cp -r <압축해제디렉터리>/skills ./ && cp <압축해제디렉터리>/artex ./
./start.sh

```

### 방법 5: 소스 코드로 컴파일

```bash
git pull
cd web && npm ci && npm run build:static && cd ..
cp -r web/out server/webui/dist
CGO_ENABLED=0 go build -tags embedui -o artex ./cmd/artex
# ./start.sh 재시작

```

---

## 설정

**데이터베이스** (`config.json` 또는 환경 변수 `ARTEX_PG_DSN`으로 오버라이드):

```json
{
  "database": {
    "host": "127.0.0.1", "port": 5432,
    "user": "artex", "password": "yourpass",
    "dbname": "artex", "sslmode": "disable"
  }
}

```

**LLM**: `export ANTHROPIC_API_KEY=sk-...` (또는 `OPENAI_API_KEY`), 혹은 UI의 「LLM 설정」 페이지에서 입력 가능.
선택 사항: `ARTEX_LLM_PROVIDER` / `ARTEX_LLM_MODEL` / `ARTEX_LLM_BASE_URL` / `ARTEX_LLM_PROXY`.

**동시성**: 각 작업의 work agent 수는 「시스템 설정」에서 설정 가능 (기본값 3).

**기본 매개변수**: `./start.sh -addr :8787 -proxy :8788` (`-addr` 프론트엔드+API, `-proxy` 트래픽 레코딩 프록시). 시작 스크립트는 매개변수를 그대로 `artex`에 전달합니다.

### 리버스 프록시 배포 (HTTPS / 포트 443만 개방)

프론트엔드와 API/SSE 모두 동일한 백엔드 포트(기본값 `:8787`)에서 제공되며, 실시간 활동 스트림은 기본적으로 **동일 출처(Same-Origin)** 주소를 사용하므로 **`NEXT_PUBLIC_SSE_BASE`를 설정할 필요가 없습니다**. 공용 네트워크에는 443 포트만 개방하고 8787은 내부망에 두면 됩니다.

SSE는 지속적인 연결 및 푸시 방식이므로, 리버스 프록시에서 반드시 버퍼링을 비활성화(off)해야 합니다. 그렇지 않으면 브라우저는 연결되지만 이벤트 수신을 받지 못해 활동 스트림이 계속 로딩 상태로 머물게 됩니다. Nginx 설정 예시:

```nginx
server {
    listen 443 ssl;
    server_name your.domain.com;
    # ssl_certificate / ssl_certificate_key ...

    location / {
        proxy_pass [http://127.0.0.1:8787](http://127.0.0.1:8787);
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;

        # SSE 핵심 설정: 버퍼링 비활성화, 긴 타임아웃, HTTP/1.1
        proxy_buffering off;
        proxy_cache off;
        proxy_read_timeout 3600s;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
    }
}

```

> SSE가 페이지와 다른 출처(예: 별도의 서브도메인)를 거쳐야 하는 경우에만 **빌드 시점**에 `NEXT_PUBLIC_SSE_BASE`를 설정하세요(해당 변수는 `next build` 시점에 정적 패키지에 고정되며, 컨테이너 런타임에 설정해도 적용되지 않습니다).

---

## 개발

### 수동 취약점 재테스트

작업 상세의 「재테스트」 탭에서 본 작업의 취약점을 페이징 방식으로 선택하고, 이전 결과와 증거를 확인한 뒤 수동으로 재테스트를 시작할 수 있습니다. 시작 후 현재 탭이 유지되며 회전 아이콘과 「재테스트 중」 상태가 표시됩니다. 수정이 확인되면 취약점 상태가 동기화됩니다.

취약점 목록의 각 행 작업 영역에서 「재테스트」를 클릭하거나, 취약점 상세의 「취약점 재테스트」 영역에서 「재테스트 시작」을 클릭한 후 선택적으로 수정 버전, 테스트 조건 또는 제한 사항을 입력하면 시스템이 독립적인 재테스트 에이전트 세션을 생성합니다. 시작 후 현재 페이지가 유지되며, 목록의 타일형, 작업별 그룹화 및 자산 뷰 모두 이 진입점을 지원합니다. 재테스트 실행 중에는 회전 아이콘과 「재테스트 중」이 표시되며, 확인이 필요한 경우 클릭하여 해당 세션으로 진입할 수 있고 종료 후 「재테스트」로 복구됩니다. 재테스트 시 기존 스캔 작업을 재시작할 필요가 없으며, 결과는 「여전히 재현됨」, 「수정됨」, 「확인 불가」로 분류되고 매회 결과, 증거 및 세션 링크가 취약점 상세에 저장됩니다.

신버전 백엔드는 최초 시작 시 편집 가능한 「취약점 재테스트」(retester) 에이전트를 사전 설정하며, 에이전트 관리에서 프롬프트, LLM, 실행 예산 및 도구를 설정할 수 있습니다. 기본적으로 바인딩된 LLM을 사용하며, 바인딩되지 않은 경우 전역 활성 설정을 사용합니다. 재테스트 세션이 성공적으로 완료되고 결과가 「수정됨」인 경우 시스템은 자동으로 취약점 처리 상태를 「수정됨」으로 변경하며, 실행 중, 실패, 중지 또는 기타 결과는 기존 상태를 유지합니다. 원본 증거와 보고서는 항상 보관됩니다. 상태 드롭다운 메뉴에서 수동으로 「수정됨」을 선택할 수도 있습니다. 동일한 취약점이 재테스트 중일 때는 기존 세션이 재사용되며, 중지, 실패 또는 서비스 재시작 후 다시 시작할 수 있습니다.

본 버전의 이력 기록은 취약점 상세와 세션을 통해 확인하며, 아직 취약점 보고서 내보내기나 작업 아카이브 패키지에 포함되지 않았고 트래픽 패킷과 자동 연동되지도 않았습니다. 데모 모드는 명시적으로 표시된 시뮬레이션 기록만 생성하며 실제 타겟을 요청하지 않습니다.

### 로컬 실행 및 테스트

```bash
./dev.sh    # 백엔드(:8787) + 트래픽 프록시(:8788) + 프론트엔드 next dev(:5173) → http://localhost:5173

```

* 백엔드: `go run ./cmd/artex` (`-tags embedui` 미포함 시 프론트엔드 내장 안 함)
* 프론트엔드: `cd web && npm run dev` (`/api`를 백엔드로 프록시, 핫 리로드 지원)
* 테스트: `go test ./...`
* Mock 미리보기 (백엔드 없음): `cd web && NEXT_PUBLIC_MOCK=1 npm run dev`

---

## 시스템 기술 아키텍처

ARTEX는 **LLM 멀티 에이전트 기반 자율 침투 시스템**입니다: Go 단일 백엔드(Next.js 프론트엔드 내장) + PostgreSQL, 에이전트 기능은 [`norma`](https://github.com/Autumn-27/norma) SDK(`agentcore` / `tool` / `permission` / `harness` / `memory` / `transcript`)로 제공됩니다. 핵심은 **듀얼 그래프 아키텍처**와 이를 둘러싼 두 가지 자율성 메커니즘입니다: **worker 간 프로세스 레벨 정보 교환**, **planner의 다중 라운드 공유 todolist 안정적 공격 체인**.

### 전체 레이어 구조

```mermaid
flowchart TB
  subgraph FE["프론트엔드 Next.js (go:embed 단일 바이너리 내장)"]
    UI["대시보드 · 작업 · 자산 · 커버리지 맵 · 트래픽 · 워크스페이스 · 시스템 설정"]
  end
  subgraph SRV["server (Go net/http)"]
    API["REST /api/* JWT 인증 SSE"]
    ENG["engine 스케줄링 루프"]
    MGR["Manager 작업/엔진/스토어 라이프사이클"]
  end
  subgraph AG["agent (norma SDK)"]
    GO["goals 목표 분해 + 범위 추출"]
    PL["planner 계획자 (유일한 의도 생성자)"]
    WK["worker 실행자 ×N"]
    MA["mainagent 휴먼 인 더 루프"]
  end
  subgraph DB["PostgreSQL"]
    AGRAPH["자산 그래프 assets / companies / task_scope"]
    EGRAPH["탐색 그래프 exploration_nodes / anchors / activity"]
  end
  subgraph SUB["서포트 서브시스템"]
    PROXY["트래픽 레코딩 프록시 MITM + CA 로그"]
    GUARD["guard / intercept 도구 승인 게이트"]
    ENR["enrich DNS / HTTP 비동기 보완"]
    EXT["MCP · skills · memory · report"]
  end

  UI -->|HTTP| API
  API --> MGR --> ENG
  ENG --> PL
  ENG --> WK
  API --> MA
  API --> GO
  PL --> DB
  WK --> DB
  MA --> DB
  GO --> DB
  WK -->|"Bash / HTTP 전체 로그"| PROXY
  WK --> GUARD
  WK --> ENR
  PL -.-> EXT
  WK -.-> EXT
  MA -.-> EXT

```

| 레이어 | 역할 |
| --- | --- |
| **프론트엔드** | Next.js 정적 내보내기, `go:embed`로 단일 바이너리 내장; 작업/자산/탐색 링크/커버리지 맵 시각화, 휴먼 인 더 루프 대화 |
| **server** | `net/http` 라우팅 + JWT 인증 + SSE; `Manager`를 통한 작업, 엔진, DB 스토어 라이프사이클 관리 |
| **engine** | 작업당 1개의 `plannerLoop` + N개의 worker 고루틴; 의도 수집, 타임아웃/정지/drain |
| **agent** | goals / planner / worker / mainagent, `ToolSet`을 통한 듀얼 그래프의 LLM 도구화 |
| **db** | 듀얼 그래프의 PostgreSQL 저장 (pgx); `go:embed`와 함께 시작 시마다 테이블 멱등성 생성 |
| **서포트** | 기록형 MITM 프록시, 승인 게이트, 비동기 보완, MCP/스킬/메모리/보고서 |

### 듀얼 그래프 아키텍처: 탐색 그래프 + 자산 그래프

시스템은 「**목표가 무엇인가**」와 「**어느 정도 수준까지 테스트했는가**」를 서로 독립적이면서도 앵커(Anchor)를 통해 연결된 두 개의 그래프로 분리합니다.

* **자산 그래프 (Asset Graph, 전역 공유)**: 모든 작업에서 공통으로 사용하는 자산 진실(Truth) 저장소. 노드는 `root_domain / subdomain / ip / service / app / endpoint`이며 회사별로 소속됩니다. 도메인→서브도메인→서비스→엔드포인트의 부모-자식 관계 및 중복 제거 키는 모두 프로그램이 계산하며, 에이전트는 원시 정보만 제출합니다.
* **탐색 그래프 (Exploration Graph, 작업별 독립)**: 한 번의 작업이 진행되는 '사고와 추진' 과정. 노드는 `goal(목표) / intent(의도) / fact(사실) / finding(취약점) / hint(힌트)`이며, `spawns / derived_from / yields / proves` 등의 간선으로 연결된 **혈연 체인**을 형성하여 "어떤 방향이 어떤 사실에서 파생되어 무엇을 산출했는지"에 답합니다.
* **두 그래프는 앵커로 연결됨**: `exploration_anchors(node_id, asset_id)`를 통해 의도/사실/취약점을 특정 자산에 고정합니다. 이를 통해 "탐색 방향" 관점에서 어떤 자산을 공격했는지 볼 수 있을 뿐만 아니라, "특정 자산" 관점에서 본 작업 동안 어떤 의도로 테스트되었고 어떤 사실이 도출되었는지 역으로 조회할 수 있습니다. 이는 **자산 테스트 커버리지** 및 **자산 커버리지 맵**(범위 내 자산 + 테스트 완료 하이라이트)을 지원하는 기반이 됩니다.

```mermaid
flowchart LR
  subgraph EG["탐색 그래프 (작업별 독립 · 추진 체인)"]
    direction TB
    G["goal 목표"]
    I1["intent 의도 A"]
    F1["fact 사실"]
    I2["intent 의도 B"]
    FD["finding 취약점"]
    G -->|spawns| I1
    I1 -->|yields| F1
    F1 -->|derived_from| I2
    I2 -->|proves| FD
  end
  subgraph AG["자산 그래프 (전역 공유 · 진실 저장소)"]
    direction TB
    RD["root_domain"]
    SD["subdomain"]
    SV["service"]
    EP["endpoint"]
    RD --> SD --> SV --> EP
  end
  I1 -. anchor .-> SD
  F1 -. anchor .-> SV
  I2 -. anchor .-> EP
  FD -. anchor .-> EP

```

> 분업: **planner**는 탐색 그래프 상태를 읽고 목표를 판단하며, 커버되지 않은 새로운 방향이 있을 때만 **의도(intent)**를 frontier로 보냅니다. **worker**는 **하나의 의도**를 가져와 실제 도구로 실행한 뒤, 새로운 자산/사실/취약점을 두 그래프에 기록하고 종료합니다. 자산 그래프는 공유 사실이며, 탐색 그래프는 작업별 추진 체인입니다.

### 엔진과 의도 라이프사이클 (탐색의 폐루프)

엔진은 **이벤트 기반**의 폐루프입니다. 그래프가 변경되면 planner가 깨어나고, planner가 의도를 발행하며, worker가 의도를 가져와 실행한 뒤 다시 기록하고, 기록이 다음 라운드를 트리거하는 방식 — 목표가 증명될 때까지(`prove_goal`) 반복됩니다.

```mermaid
sequenceDiagram
  autonumber
  participant EV as 그래프 변경 디바운스(debounce)
  participant P as planner
  participant FR as frontier 의도 큐
  participant W as worker
  participant PX as 기록 프록시
  participant DB as 듀얼 그래프 + activity

  EV-->>P: 웨이크업
  P->>DB: 상태 읽기 (graph_overview 프리팃 + coverage/scope)
  P->>FR: 0..N개의 의도 발행 (asset_ids 포함)
  Note over P,FR: 대부분의 웨이크업은 0개를 발행 —새로운 방향이 없으면 종료
  W->>FR: claimNext로 하나의 의도 획득
  W->>DB: 의도 asset_ids의 원시 자산을 초기 정보로 조회
  W->>PX: 실제 도구 실행 (Kali / Bash / HTTP)
  PX-->>W: 응답 (전체 로그 기록 + CA 검증)
  W->>DB: fact / asset / finding + 각 단계 activity 기록
  DB-->>EV: 그래프 변경
  EV-->>P: 다시 웨이크업 (폐루프)

```

### Worker 간의 프로세스 레벨 정보 교환

심층적인 탐색 과정에서 많은 가치 있는 관찰 내용(특정 에러 메시지, 특정 응답, 특정 숨겨진 파라미터)이 하나의 worker의 실행 과정(trace)에서 나타나지만, 정식 fact로 작성되지 않을 수 있습니다. 중복 작업을 방지하고 후속 worker들이 서로의 어깨 위에 올라설 수 있도록, worker는 **다른 work의 실행 과정을 검색할 수 있는 기능**을 갖추고 있습니다.

* `search_all_worker_traces(q)`: **현재 작업 내 다른 work들의 실행 과정**에서 키워드 검색을 수행합니다 (자신이 수행 중인 의도의 단계는 자동으로 제외됨). 검색 결과 항목에는 `intent_id`가 포함됩니다.
* `list_worker_traces` / `get_worker_trace(intent_id, step_ids=[…])`: 먼저 어떤 work들이 실행되었는지 확인한 후, 특정 work의 몇 가지 단계에 대한 전체 내용을 가져와 세부 정보를 교환합니다.

이렇게 함으로써 탐색 그래프에 아직 대응하는 fact가 없더라도, 후속 worker들은 다른 worker의 과정 중 관찰 내용을 재사용할 수 있습니다 — **정보는 worker 간에 "실행 과정" 단위로 흐르며**, 경계는 유지됩니다(각 worker는 여전히 자기가 할당받은 단 하나의 의도만 수행).

```mermaid
flowchart LR
  WA["worker A (의도 #12)"] -->|"매 단계 activity"| ACT[("탐색 그래프 · activity 과정 저장소")]
  WB["worker B (의도 #34)"] -->|"매 단계 activity"| ACT
  WC["worker C (의도 #56)"] ==>|"1) search_all_worker_traces(q)"| ACT
  ACT ==>|"2) A/B의 단계 매칭 (자신 제외)"| WC
  WC ==>|"3) get_worker_trace(id, step_ids)"| ACT
  ACT ==>|"4) 전체 과정 내용 반환"| WC

```

### Planner의 다중 라운드 공유 todolist → 안정적인 공격 체인

실제 공격 체인은 종종 **앞뒤 의존성이 있는 다단계 시퀀스**로 이루어져 있습니다(예: 인젝션 포인트 발견 → 자격 증명 획득 → 횡이동 → 권한 상승). 이를 한 번에 병렬로 밀어 넣으면 혼란만 가중될 뿐입니다. 따라서 planner는 작업별로 유지되고 웨이크업 간에 공유되는 계획 대기열(todolist)을 유지합니다.

* planner는 이벤트 기반이므로 그래프가 바뀔 때마다 깨어나지만, **매번 웨이크업할 때마다 새로운 세션**입니다. 공유된 todolist 덕분에 직렬화된 공격 체인을 **한 번 기록**한 후, 이후 여러 라운드에 걸쳐 **의존성에 따라 단계별로 의도**를 발행할 수 있으며, 한 라운드에서 전체 체인을 한꺼번에 전개하지 않습니다.
* 매 라운드마다 "선행 단계가 완료되었고 의존하는 fact가 존재하는" 다음 단계에 대해서만 의도를 발행하며, 진행 상황에 따라 목록을 업데이트합니다(fact에 의해 충족된 단계를 완료로 표시).

```mermaid
flowchart TB
  subgraph TODO["공유 todolist (작업별 유지 · 웨이크업 간 상주)"]
    direction LR
    T1["1 인젝션 포인트 [완료됨]"]
    T2["2 자격 증명 획득 [진행 중]"]
    T3["3 횡이동 [선행 대기]"]
    T4["4 권한 상승 [선행 대기]"]
    T1 -.선행 충족.-> T2 -.-> T3 -.-> T4
  end
  R1["1차 웨이크업 의도① 발행"] --> T1
  R2["2차 (①이 fact 산출) 의도② 발행"] --> T2
  R3["3차 (②가 fact 산출) 의도③ 발행"] --> T3

```

결과적으로 "이벤트 기반 + 무상태 세션" 환경에서도 공격 체인이 **안정적으로 진행되고 중복되거나 순서가 어긋나지 않음** — 이것이 바로 ARTEX가 다단계 이용 체인을 자율적으로 완수할 수 있는 핵심 이유입니다.

---

## 커뮤니티

QR 코드를 스캔하여 위챗 공식 계정 **SecSentry**를 팔로우하고, 공식 계정 백엔드에서 메시지를 보내 커뮤니티에 참여하세요.

---

## 참고

https://github.com/oritera/Cairn

## 라이선스 및 면책 조항

### 오픈소스 라이선스

본 프로젝트는 GNU Affero General Public License v3.0 (AGPL-3.0)에 따라 라이선스가 부여되며, 전체 약관은 저장소 루트 디렉토리의 [LICENSE](https://www.google.com/search?q=LICENSE) 파일을 참조하세요.

이는 누구나 본 프로젝트를 자유롭게 사용, 수정 및 배포할 수 있음을 의미하지만, **파생 작품 역시 반드시 AGPL-3.0으로 오픈소스화되어야 합니다**; 특히 **본 프로젝트를 수정하여 네트워크(예: 온라인 서비스 배포)를 통해 사용자에게 제공하는 경우, 해당 사용자에게도 대응하는 전체 소스 코드를 반드시 공개해야 합니다**.

> ⚠️ **중요 참고**: 오픈소스 라이선스 자체는 소프트웨어의 사용 용도를 제한하지 않습니다. 아래의 「사용 제한」 및 「면책 조항」은 저자가 사용자에게 제시하는 추가적인 약속이자 엄중한 선언이므로 반드시 준수해 주시기 바랍니다.

**ARTEX는 개인 학습, 코드 연구 및 로컬 기술 검증 용도로만 제공되며, 어떠한 온라인 시스템이나 웹사이트를 대상으로 실제 테스트를 수행하는 데 사용할 수 없습니다.**

### 허용 범위

* 본 프로젝트 소스 코드의 **열람, 학습 및 연구**, 그리고 **로컬 격리 환경**에서의 기술 원리 검증 목적으로만 사용할 수 있습니다.
* 개인 학습, 학술 연구, 코드 리뷰 등 비공격적인 용도에 적합합니다.

### 금지 사항

* **어떤 웹사이트, 온라인 서비스 또는 네트워크 연결 시스템에 대해서도 본 도구를 사용하여 스캔, 탐색, 이용 또는 공격을 시도하는 행위를 엄격히 금지합니다**(승인 여부나 자체 자산 여부와 무관).
* 본 도구를 실제 침투 테스트, 공방 대항전 또는 운영(Production) 환경에 사용하는 것을 엄격히 금지합니다.
* 불법 침입, 데이터 탈취, 랜섬웨어, 서비스 거부 또는 기타 파괴적, 범죄적 행위에 본 도구를 사용하는 것을 엄격히 금지합니다.
* 사용자가 속한 국가/지역의 법령을 위반하는 활동에 본 도구를 사용하는 것을 엄격히 금지합니다.

### 컴플라이언스 책임

사용자는 사이버 보안, 데이터 보호 및 컴퓨터 범죄와 관련하여 자신이 속한 국가/지역의 모든 법령을 스스로 준수해야 합니다(중국 대륙의 경우 「네트워크 보안법」, 「데이터 보안법」, 「개인정보 보호법」 및 관련 사법 해석 등이 포함되나 이에 국한되지 않음). **본 도구 사용으로 인해 발생하는 모든 법적 책임과 결과는 전적으로 사용자가 부담합니다.**

### 면책 조항

본 프로젝트는 어떠한 명시적이거나 묵시적인 보증 없이 "있는 그대로(AS IS)" 제공됩니다. 저자와 기여자들은 본 도구의 사용(사용 방식의 적절성 여부와 무관)으로 인해 발생하는 직간접적 손실, 데이터 손실, 시스템 손상 또는 법적 분쟁에 대해 책임을 지지 않습니다. **본 프로젝트를 다운로드, 설치 또는 사용하는 것은 위의 모든 조항을 읽고 이해하였으며 이에 동의함을 의미합니다.**

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
