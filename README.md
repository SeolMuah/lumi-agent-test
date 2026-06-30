# Day 2 Mission — Docker 컨테이너화 및 GCE VM 배포

## 환경 설정

```bash
# 의존성 설치
uv sync

# 환경변수 설정
cp .env.example .env
```

## 실행 (로컬)

```bash
# Docker 이미지 빌드
docker build -t lumi-agent .

# Docker Compose로 실행
docker-compose up --build

# 백그라운드 실행
docker-compose up -d

# 로그 확인
docker-compose logs -f

# 종료
docker-compose down
```

## 미션

### 1. `app/api/routes/health.py` - 헬스체크 엔드포인트

| TODO | 내용 |
|------|------|
| TODO 1 | APIRouter 인스턴스 생성 |
| TODO 2 | 헬스체크 엔드포인트 구현 |

### 2. `Dockerfile` - 컨테이너 이미지 구성

| TODO | 내용 |
|------|------|
| TODO 1 | 의존성 설치 명령어 작성 |
| TODO 2 | 보안 설정 - non-root 유저 생성 및 권한 설정 |
| TODO 3 | 헬스체크 설정 |
| TODO 4 | 서버 실행 명령어 작성 (uv run) |

### 3. `docker-compose.yml` - 로컬 실행 환경

| TODO | 내용 |
|------|------|
| TODO 1 | 이미지 및 빌드 설정 |
| TODO 2 | 포트 매핑 (호스트:컨테이너) |
| TODO 3 | 환경변수 설정 |
| TODO 4 | 헬스체크 및 재시작 정책 설정 |

### 4. `.github/workflows/cd.yml` - CD 파이프라인

| TODO | 내용 |
|------|------|
| TODO 1 | 트리거 설정 |
| TODO 2 | GHCR Push 권한 설정 |
| TODO 3 | GHCR 로그인 |
| TODO 4 | 이미지 빌드 & Push |


---

## 📓 강의 노트 (참고용)

> 아래는 강의 진행 노트입니다 (미션과 별개 · 참고용).

# Service Deployment 1강
## 코드 강의
- 바닥부터 구현하는 과정을 보여줄 예정
  - 단, 일부 코드는 구현된 것을 가져와서 사용
- 추천하는 학습 방식
  - 구현하는 흐름을 보기. 처음에 어떤 폴더를 만들고, 파일을 만들고, 어떻게 수정했는가? 그 다음에 어떤 것을 수정했는가?
  - 노트북 베이스 => 스크립트

## 도구
- VSCode, Terminal
- Extension : TODO Tree

## 오늘 할 것
- 큰 뼈대를 잡을 예정
- 강의 자료에서 Supabase 설정은 이미 완료했다고 가정
- 프로젝트 구조를 잡을 예정
  - app/main.py
  - app/core/config.py
  - app/schemas/chat.py
- .env 설정 -> config.py -> chat.py -> main.py
  - 사용할 때 : main.py를 실행 -> config.py, chat.py

## TODO 정리
- [v] config.py 구현
- [v] schemas/chat.py 구현
  - [v] ChatRequest 클래스 정의
  - [v] ChatResponse 클래스 정의
- [v] main.py 구현
  - [v] FastAPI 앱 인스턴스 생성
  - [v] lifespan 함수 정의
  - [v] CORS 미들웨어 추가
  - [v] 루트 엔드포인트 정의
  - [v] __main__ 실행 블록

# Service Deployment 2강
- LangGraph MVP 구현
  
## 오늘 할 것
- 노트북 파일을 스크립트 파일로 변환
  - 그 과정에서 필요한 것들 추가(DB 연결)

## TODO 정리
- [v] graph 구현 : notebook to py
    - [v] state.py 구현
    - [v] nodes.py 구현
        - [v] router : 메시지 의도 분류
            - [v] core/prompts.py 구현
        - [v] rag : 문서 검색. 사서
            - [v] repositories 구현
        - [v] tool : 툴 실행
            - [v] tools/executor.py 구현
        - [v] response 노드 구현
            - [v] core/prompts.py 구현
    - [v] edges.py 구현
    - [v] graph.py 구현
- [v] API 서버 구현
    - [v] chat.py 구현
- [v] ui.py 구현
- [v] main.py 구현

## 오늘 강의 핵심
- 바닥부터 다 구현을 해야 한다가 아님
- 구현하는 과정을 익히기 위해 보여드린 것
  - 노트북 파일에서 스크립트로 변환할 때 이렇게 하면 되는구나 감
  - 데일리미션에 있는 TODO, 코드를 읽어보기! 모르는 표현이 있으면 찾아보고 아 이런거구나!
  - print
  - 바닥부터 한번 시도해볼까?


### 프로젝트 구조

```
app/
├── core/
│   ├── config.py          ← 설정 관리 (pydantic-settings)
│   └── prompts.py         ← 프롬프트 분리
├── schemas/
│   └── chat.py            ← API 요청/응답 모델
├── graph/                  ← 노트북에서 만든 에이전트
│   ├── state.py           ← State 정의
│   ├── nodes.py           ← 노드 구현
│   ├── edges.py           ← 라우팅 로직
│   └── graph.py           ← 그래프 조립 + 싱글톤
├── repositories/           ← DB 접근 계층 (Mock → Real)
│   ├── rag.py             ← RAG 검색 (Supabase pgvector)
│   ├── schedule.py        ← 스케줄 조회
│   └── fan_letter.py      ← 팬레터 저장
├── tools/
│   └── executor.py        ← Tool 실행 (Repository 활용)
├── api/routes/
│   └── chat.py            ← REST API 엔드포인트
├── ui.py                  ← Gradio UI (/ui)
└── main.py                ← FastAPI 앱 (lifespan, CORS)
```


# Service Deployment 3강
## 오늘 할 일(목표)
- 2강에서 만든 MVP -> 개선
- 스트리밍을 구현할 예정. 노드 상태 + 토큰 스트리밍을 동시에 보여주기
- 실시간 스트리밍!

## TODO
- [v] app/schemas/chat.py : StreamEvent, to_sse()
- [v] app/api/routes/chat.py : SSE 구현. stream_with_status 함수
  - [v] SSE 엔드포인트 추가
- [v] app/ui.py : 스트리밍 데이터를 받아서 처리할 수 있도록 함수 
- [v] UI에서 확인을 할 예정
- [v] router쪽의 이슈 해결을 위한 코드

## 정리
- SSE 구현을 위해서 어떻게 하는가?
- yield 이벤트 발생 -> 이벤트 형태 정의 -> 그거에 맞게 로직
- stream_with_status 로직 : 읽어보기
- 바닥부터 다 구현이 아니라, 일단 구현된 것을 읽을 수 있는지? -> 이해가 되는지?

# Service Deployment 5강
## 오늘 할 일
- docker에 대한 이해(기본 명령어)
- Dockerfile, docker-compose.yml
- api/routes/health.py

## Docker 사용 흐름
- 1) Docker Image를 가져다가 바로 쓴다 -> Docker Image가 저장된 Registry에서 검색 후 활용
  - Docker hub, GitHub Registry
- 2) 특정 도커 이미지를 기반으로 나만의 이미지를 만들어서 활용 -> docker images 보이도록 docker image build
  - 컨테이너를 실행할 때는 docker run
- 3) Docker Compose를 사용해서 캠핑 풀세트를 만들어서 활용