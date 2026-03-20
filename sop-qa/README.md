# SOP Q&A Assistant v2

문서 기반 제약·바이오 SOP Q&A 웹앱입니다.
사용자는 SOP를 검색해 근거와 함께 답변을 받고, 피드백을 남길 수 있습니다.
운영자는 대시보드에서 만족도·질문 분석·피드백 리뷰 큐를 확인하고 서비스를 개선할 수 있습니다.

## 바로 보기

- 메인 페이지: [https://jhr122333.github.io/sop-qa-assistant/](https://jhr122333.github.io/sop-qa-assistant/)
- 챗봇: [https://jhr122333.github.io/sop-qa-assistant/sop-qa-demo.html](https://jhr122333.github.io/sop-qa-assistant/sop-qa-demo.html)
- 관리자 대시보드: [https://jhr122333.github.io/sop-qa-assistant/admin-dashboard.html](https://jhr122333.github.io/sop-qa-assistant/admin-dashboard.html)

## 포함된 화면

- `index.html` — 제품 소개와 진입 화면
- `sop-qa-demo.html` — 사용자용 SOP Q&A 챗봇 (피드백 UI 포함)
- `admin-dashboard.html` — 운영자용 KPI / 만족도 차트 / 질문 분석 / 피드백 리뷰 큐

## 기능 (v2)

### 챗봇 (`sop-qa-demo.html`)

- SOP 문서 10종 기반 BM25 검색 + OpenAI API 답변 생성
- 답변마다 출처(Document ID / Section / Version) 표시
- **v2 신규** 👍/👎 피드백 버튼
  - 👎 클릭 시 사유 선택: `부정확한 답변` / `정보 부족` / `출처 불일치` / `기타`
  - `기타` 선택 시 자유 텍스트 입력
  - 로컬 서버: `POST /api/feedback` → `feedback-log.json` 저장
  - GitHub Pages: `localStorage` fallback 자동 적용
- **v2 신규** 채팅 히스토리
  - 사이드바 탭 전환: 문서 목록 / 대화 기록
  - 과거 대화 선택 시 질문·답변 복원
  - 최근 20개 세션 `localStorage` 자동 저장
- **v2 신규** 새 대화 버튼 (+ 아이콘)
  - 헤더 및 사이드바 두 곳에 배치
  - 클릭 시 히스토리를 유지한 채 새 세션 시작
- **UI 개선** 아바타를 이모지에서 SVG 아이콘으로 교체
- **UI 개선** 새 대화 / 전송 버튼 텍스트 제거 — 아이콘만 표시 (전송: 종이비행기 SVG)

### 관리자 대시보드 (`admin-dashboard.html`)

- KPI 카드 (총 질문 수 / 답변 성공률 / 근거 부족 비율 / 부정 피드백)
- 질문량 추이 (최근 7일 바 차트)
- **v2 신규** 응답 만족도 분석 (Chart.js)
  - 전체 긍정/부정 비율 도넛 차트
  - 최근 7일 만족도 추이 라인 차트
  - 불만족 사유 분포 가로 막대 차트
- **v2 신규** 질문 분석
  - 가장 많이 질문된 SOP Top 5
  - 질문 유형 자동 분류 도넛 차트 (6종)
  - SOP 커버리지 히트맵 (10종 참조 빈도)
- **v2 신규** 피드백 리뷰 큐
  - 최신 부정 피드백 카드 목록 (질문 / 사유 / 참조 SOP / 코멘트)
  - 검토 완료 체크박스 (상태 localStorage 저장)
- **v2 신규** 탑재 Q&A 목록
  - 오프라인 키워드 Q&A DB 전체 항목 테이블 (25건)
  - 출처 SOP 배지 / 인식 키워드 태그 / 답변 미리보기
  - 키워드·SOP ID 실시간 검색 필터

## 로컬 실행

```bash
# 의존성 설치
npm install

# 서버 시작 (포트 3000)
npm start
```

브라우저에서 `http://127.0.0.1:3000` 접속

환경 변수 설정 (`.env` 파일 생성):

```env
OPENAI_API_KEY=sk-...
OPENAI_MODEL=gpt-4.1-mini   # 생략 시 기본값
```

## 파일 구조

```text
sop-qa-assistant/
├── index.html
├── sop-qa-demo.html
├── admin-dashboard.html
├── server.mjs
├── package.json
├── .env.example
├── data/
│   ├── documents/sop-documents.json       # SOP 문서 10종
│   ├── dashboard/dashboard-mock.json      # 대시보드 mock 데이터
│   ├── dashboard/feedback-demo.json       # v2: 피드백 샘플 데이터 (28건)
│   └── runtime/
│       ├── chat-log.json                  # 질문 로그
│       └── feedback-log.json              # v2: 피드백 로그 (자동 생성)
└── docs/
    ├── service-planning.md                # v2: 서비스 기획서
    ├── v1-to-v2-changelog.md              # v2: 변경 로그
    ├── PRD.md
    ├── RAG-ARCHITECTURE.md
    ├── DASHBOARD-SPEC.md
    ├── EVAL-PLAN.md
    └── SOURCE-DATA-SPEC.md
```

## API 엔드포인트

| 메서드 | 경로 | 설명 |
|--------|------|------|
| `POST` | `/api/chat` | SOP Q&A 답변 생성 |
| `GET`  | `/api/dashboard` | 대시보드 집계 데이터 |
| `POST` | `/api/feedback` | 피드백 저장 (v2 신규) |
| `GET`  | `/api/feedback` | 피드백 목록 조회 (v2 신규) |

## GitHub Pages 동작 방식

GitHub Pages에서는 서버 기능 없이 정적 파일만 동작합니다.

| 기능 | 로컬 서버 | GitHub Pages |
|------|-----------|--------------|
| 챗봇 답변 생성 | OpenAI API 연동 | 사전 정의 Q&A (키워드 매칭) |
| 피드백 저장 | `feedback-log.json` | `localStorage` |
| 대시보드 데이터 | 실데이터 (`chat-log` + `feedback-log`) | `feedback-demo.json` fallback |

## 관련 문서

- [서비스 기획서](./docs/service-planning.md)
- [v1 → v2 변경 로그](./docs/v1-to-v2-changelog.md)
- [RAG 아키텍처](./docs/RAG-ARCHITECTURE.md)
- [대시보드 스펙](./docs/DASHBOARD-SPEC.md)
