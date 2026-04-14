# CLAUDE.md

이 리포지토리는 AI Harness 방법론을 설계·문서화하는 공간이다.
Claude는 이 파일을 읽고 작업 방식, 구조 규칙, 문서 작성 컨벤션을 파악한다.

---

## 프로젝트 목적

**AI Harness**는 어떤 AI 모델을 사용하든 동일한 품질의 아웃풋이 나오도록 AI를 구조적으로 제어하는 체계다.
핵심 가치는 **멱등성(Idempotency)** — 입력이 달라도 결과가 수렴된다.

---

## 디렉토리 구조

```
AI_Harness/
├── README.md                    # 프로젝트 전체 개요
├── framework/
│   ├── workflow.md              # 6단계 전체 워크플로우 상세 설명
│   └── skills_and_agents.md    # 단계별 스킬·에이전트 전체 목록
├── cps/
│   └── cps_evolution_example.md  # CPS 점진적 구체화 예시
├── prd/
│   ├── prd_methodology.md      # PRD 방법론 설명
│   ├── prd_template.md         # PRD 작성 템플릿
│   ├── prd_example.md          # PRD 작성 예시
│   └── prd_skills.md           # PRD 파이프라인 스킬 명세
├── mia/
│   ├── meeting_agent.md        # MIA 개념 설계 및 작동 구조
│   ├── meeting_room_visualization.md  # 회의실 ASCII 시각화
│   └── meeting_agent_visualization.html  # 인터랙티브 시각화
├── docs/
│   └── ai-instruction-writing-guide.md  # AI 지침 작성 가이드
└── .claude/
    ├── skills/                  # 커스텀 Claude 스킬
    └── commands/                # Claude 커스텀 명령어
```

---

## 작업 언어

- **모든 문서는 한국어**로 작성한다
- 기술 용어(스킬명, 에이전트명, 코드 식별자)는 영어 원문 유지
- 응답도 한국어로 한다

---

## 문서 작성 컨벤션

### 마크다운 규칙
- 제목 계층: `#` → `##` → `###` 순서를 지킨다
- 섹션 구분선(`---`)을 적극 활용한다
- 코드 블록은 언어 명시 또는 인라인 인용(`` ` ``)으로 표기한다
- 표(table)로 정리 가능한 항목은 반드시 표로 작성한다

### 스킬·에이전트 명명 규칙
- 스킬명: `kebab-case` (예: `requirement-parser`, `cps-writer`)
- 에이전트명: `kebab-case` + `-agent` 접미사 (예: `requirements-analyst`, `meeting-log-agent`)
- 파일명: `snake_case.md` (예: `prd_template.md`, `meeting_agent.md`)

### 신규 스킬·에이전트 추가 시
1. `framework/skills_and_agents.md`의 해당 단계 표에 항목 추가
2. 스킬명 / 입력 / 출력 / 설명 네 열을 모두 채운다
3. 하단 **전체 스킬·에이전트 맵** 코드 블록도 함께 업데이트한다

---

## 핵심 개념 용어 정의

| 용어 | 정의 |
|------|------|
| **AI Harness** | AI 능력을 억제하지 않고 올바른 방향으로 집중시키는 구조적 제어 체계 |
| **멱등성** | 어떤 AI 모델, 어떤 표현의 입력을 쓰든 최종 아웃풋이 동일한 구조로 수렴하는 성질 |
| **FDE** | Forward Deployed Engineer. 고객 현장에 배치되어 AI 시스템을 구축하는 엔지니어 |
| **CPS** | Context · Problem · Solution. 비정형 요구사항을 구조화하는 기획 프레임워크 |
| **PRD** | Product Requirements Document. CPS 이후 이행하는 전통적 기획 문서 |
| **MIA** | Meeting Intelligence Agent. 미팅 전·중·후 전 과정에 참여하는 AI 에이전트 구조 |
| **스킬** | 단일 목적의 프롬프트 지시사항 단위. 재사용 가능한 작업 템플릿 |
| **에이전트** | 여러 스킬을 조합하거나 외부 도구를 사용해 자율적으로 목표를 달성하는 단위 |

---

## 6단계 워크플로우 요약

```
1단계  요구사항 입력   — 비정형 요구사항 → 시스템 구성 요소 정의
2단계  플랜 수립       — 미팅 로그 → CPS → PRD
3단계  설계 분석       — 원칙 정의 → 개념 정의 → 아키텍처
4단계  코드 구현       — 아키텍처 명세 → AI 코드 생성
5단계  린터 강제화     — 커밋 시 자동 실행 → 멱등성 확보
6단계  이밸루에이션    — RAG 지표 기반 품질 지속 모니터링
```

상세 내용: [`framework/workflow.md`](./framework/workflow.md)

---

## 작업 지침

- 기존 문서를 수정할 때는 반드시 먼저 파일을 읽고 현재 구조를 파악한 뒤 편집한다
- 새 파일은 꼭 필요할 때만 만들고, 가능하면 기존 파일을 편집한다
- 링크를 추가할 때는 상대 경로를 사용한다 (예: `../framework/workflow.md`)
- 코드 또는 스킬 예시를 만들 때 실제 프로젝트 도메인(배달 플랫폼, 엔터프라이즈 AX)을 활용하면 좋다
- 이 리포지토리에는 실행 가능한 코드가 없으며, 모든 산출물은 문서다
