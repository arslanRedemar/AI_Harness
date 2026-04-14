# AGENTS.md

AI Harness 방법론에서 사용하는 모든 에이전트와 스킬의 설계 원칙, 구성 규칙, 전체 목록을 정의한다.

---

## 에이전트 vs 스킬

| 구분 | 정의 | 특징 |
|------|------|------|
| **스킬** | 단일 목적의 프롬프트 지시사항 단위 | 재사용 가능한 작업 템플릿. 입력→출력이 명확 |
| **에이전트** | 여러 스킬을 조합하거나 외부 도구를 사용해 자율적으로 목표를 달성하는 단위 | 스킬의 파이프라인 또는 오케스트레이터 |

---

## 설계 원칙

1. **단일 책임**: 스킬 하나는 하나의 변환만 수행한다
2. **명시적 입출력**: 모든 스킬은 입력 형식과 출력 형식이 문서화되어야 한다
3. **할루시네이션 방지**: 스킬은 입력 데이터에 없는 내용을 생성하지 않는다 — 없으면 TBD 처리
4. **CPS 추적성**: PRD 관련 스킬의 모든 출력은 CPS 원문 출처를 태그한다
5. **멱등성 보장**: 동일 입력에 대해 동일 구조의 출력이 나와야 한다

---

## 에이전트 명명 규칙

```
스킬:    kebab-case             예) requirement-parser, cps-writer
에이전트: kebab-case + -agent   예) requirements-analyst, meeting-log-agent
         또는 기능 설명형       예) meeting-intelligence-agent
```

---

## 전체 에이전트·스킬 목록

> 단계별 상세 명세: [`framework/skills_and_agents.md`](./framework/skills_and_agents.md)

### 1단계 — 요구사항 입력

**스킬**

| 스킬명 | 입력 | 출력 |
|--------|------|------|
| `requirement-parser` | 자유형식 요구사항 텍스트 | 구조화된 요구사항 목록 |
| `ambiguity-detector` | 요구사항 목록 | 불명확 항목 + 확인 질문 목록 |
| `component-identifier` | 요구사항 목록 | 시스템 구성 요소 목록 + 근거 |
| `feasibility-reporter` | 요구사항 + 도메인 문서 | 실현 가능성 리포트 |
| `cost-estimator` | 요구사항 + 기술 스택 | 예산 및 API 비용 예측 |
| `timeline-estimator` | 요구사항 + 기술 난이도 | 소요 기간 산출표 |

**에이전트**

| 에이전트명 | 파이프라인 | 역할 |
|------------|-----------|------|
| `requirements-analyst` | `requirement-parser` → `ambiguity-detector` → `component-identifier` | 원시 요구사항을 구성 요소 정의까지 한 번에 처리 |
| `poc-briefing-agent` | `feasibility-reporter` + `cost-estimator` + `timeline-estimator` | PoC 제안 전 사전 분석 브리핑 문서 생성 |

---

### 2단계 — 플랜 수립

**스킬**

| 스킬명 | 입력 | 출력 |
|--------|------|------|
| `meeting-transcriber` | 미팅 녹취 또는 메모 | 전사된 미팅 로그 (발화자 구분) |
| `meeting-summarizer` | 미팅 로그 | 결론 요약 + 잘된 것/부족한 것/다음 안건 |
| `cps-writer` | 미팅 로그 누적본 | CPS 문서 (Context / Problem / Solution) |
| `cps-updater` | 기존 CPS + 신규 미팅 로그 | 업데이트된 CPS 문서 |
| `prd-writer` | 확정된 CPS 문서 | PRD 초안 |
| `build-vs-buy-analyzer` | 기능 목록 | 자체 구축 / 외부 솔루션 구매 분류표 |

**에이전트**

| 에이전트명 | 파이프라인 | 역할 |
|------------|-----------|------|
| `meeting-log-agent` | `meeting-transcriber` → `meeting-summarizer` → `cps-updater` | 미팅 종료 후 로그 생성부터 CPS 업데이트 자동화 |
| `plan-document-agent` | `cps-writer` + `prd-writer` + `build-vs-buy-analyzer` | CPS → PRD → 산출물 일괄 생성 |

---

### 2단계 확장 — PRD 파이프라인

> 상세: [`prd/prd_skills.md`](./prd/prd_skills.md)

**핵심 스킬** (할루시네이션 방지 구조)

| 스킬명 | 역할 |
|--------|------|
| `cps-completeness-checker` | PRD 착수 가능 여부 판단 |
| `prd-drafter` | CPS 출처 없는 항목은 생성 않고 TBD 처리 |
| `requirement-validator` | PRD↔CPS 교차 검증 (VALID / HALLUCINATED / MISSING) |
| `prd-change-detector` | PRD 확정 후 스코프 변경 실시간 감지 |
| `traceability-matrix-builder` | 요구사항 ID ↔ 출처 미팅 ↔ 발화 원문 매핑 |

**에이전트**

| 에이전트명 | 파이프라인 | 역할 |
|------------|-----------|------|
| `prd-readiness-agent` | `cps-completeness-checker` + `prd-gap-detector` | PRD 착수 가능 여부 판단 |
| `prd-drafting-agent` | 증류 스킬 8개 + `prd-drafter` | CPS 전체 → PRD 전체 초안 자동 생성 |
| `prd-validation-agent` | `requirement-validator` + `prd-gap-detector` + `traceability-matrix-builder` | PRD 완결성·일치성 검증 |
| `prd-maintenance-agent` | `prd-change-detector` + `contradiction-detector` | PRD 확정 후 변경 감지 및 영향도 리포트 |

---

### 2단계 확장 — 미팅 인텔리전스 에이전트 (MIA)

> 상세: [`mia/meeting_agent.md`](./mia/meeting_agent.md)

**스킬**

| 스킬명 | 사용 시점 | 역할 |
|--------|-----------|------|
| `pre-meeting-briefer` | 미팅 전 | 과거 로그 기반 브리핑 문서 생성 |
| `gap-alert-generator` | 미팅 전 | 공백 기간 동안 잊기 쉬운 항목 추출 |
| `realtime-cps-tagger` | 미팅 중 | 발언을 Context/Problem/Solution으로 실시간 분류 |
| `suggestion-engine` | 미팅 중 | 놓친 맥락·반복 주제를 감지해 FDE에게 제안 |
| `contradiction-detector` | 미팅 중 | 이전 합의와 어긋나는 발언 실시간 감지 |
| `abstract-request-resolver` | 미팅 중 | 추상적 요구를 측정 가능한 지표로 전환 (웹 검색 활용) |
| `post-meeting-documenter` | 미팅 후 | 결정 사항 + 미결 항목 + 액션 아이템 자동 생성 |

**에이전트**

| 에이전트명 | 파이프라인 | 역할 |
|------------|-----------|------|
| `pre-meeting-agent` | `pre-meeting-briefer` + `gap-alert-generator` | 미팅 전 브리핑 패키지 생성 |
| `live-meeting-agent` | `realtime-cps-tagger` + `suggestion-engine` + `contradiction-detector` + `abstract-request-resolver` | 미팅 중 실시간 동반 |
| `post-meeting-agent` | `post-meeting-documenter` + `cps-updater` | 미팅 후 문서 생성 및 CPS 갱신 |
| `meeting-intelligence-agent` | 위 세 에이전트 통합 | 미팅 전·중·후 전 과정을 하나의 세션으로 관리 |

---

### 3단계 — 설계 분석

**스킬**

| 스킬명 | 입력 | 출력 |
|--------|------|------|
| `principle-definer` | 프로젝트 목표 + 제약 | 설계 원칙 목록 (우선순위 포함) |
| `domain-glossary-builder` | CPS + 고객사 용어 | 도메인 용어 사전 |
| `hierarchy-designer` | 도메인 용어 사전 | 개념 계층 구조 다이어그램 |
| `architecture-specifier` | 원칙 + 계층 구조 + 요구사항 | 아키텍처 명세 문서 (DDD 기반) |
| `mental-model-mapper` | 사용자 인터뷰 / UX 요구사항 | 유저 멘탈 모델 문서 |
| `tech-stack-recommender` | 요구사항 + 원칙 + 팀 역량 | 기술 스택 추천 및 근거 |

**에이전트**

| 에이전트명 | 파이프라인 | 역할 |
|------------|-----------|------|
| `design-analysis-agent` | `principle-definer` → `domain-glossary-builder` → `hierarchy-designer` → `architecture-specifier` | 원칙부터 아키텍처까지 설계 문서 전체 순차 생성 |
| `ux-alignment-agent` | `mental-model-mapper` + `architecture-specifier` | 유저 멘탈 모델이 아키텍처에 반영되었는지 검증 |

---

### 4단계 — 코드 구현

**스킬**

| 스킬명 | 입력 | 출력 |
|--------|------|------|
| `domain-model-generator` | 아키텍처 명세 + 도메인 용어 사전 | 도메인 모델 코드 (Entity, VO, Repository) |
| `feature-scaffolder` | 기능 명세 + 아키텍처 명세 | 기능별 파일 구조 + 보일러플레이트 코드 |
| `interface-definer` | 도메인 모델 + 기능 명세 | 모듈 간 인터페이스 계약 문서 |
| `code-reviewer` | 생성된 코드 + 설계 원칙 | 리뷰 코멘트 + 수정 제안 |
| `module-rewriter` | 삭제 대상 모듈 명세 + 인터페이스 계약 | 재작성된 모듈 코드 |

**에이전트**

| 에이전트명 | 파이프라인 | 역할 |
|------------|-----------|------|
| `implementation-agent` | `domain-model-generator` → `feature-scaffolder` → `interface-definer` | 아키텍처 명세 → 전체 코드 골격 생성 |
| `code-repair-agent` | `code-reviewer` → `module-rewriter` | 문제 모듈 감지 후 인터페이스 보존한 채 재구현 |

---

### 5단계 — 린터 강제화

**스킬**

| 스킬명 | 입력 | 출력 |
|--------|------|------|
| `linter-rule-definer` | 아키텍처 명세 + 도메인 용어 사전 | 린터 규칙 명세 문서 |
| `linter-config-generator` | 린터 규칙 명세 | `.eslintrc`, `custom-rules/` 등 설정 파일 |
| `commit-hook-installer` | 린터 설정 파일 + 프로젝트 구조 | `pre-commit` 훅 스크립트 |
| `lint-error-analyzer` | 린터 오류 메시지 | 위반 원인 분석 + 수정 지시사항 |
| `lint-autofix-suggester` | 린터 오류 목록 + 코드 | 자동 수정 가능 항목 분류 + 수정 코드 |

**에이전트**

| 에이전트명 | 파이프라인 | 역할 |
|------------|-----------|------|
| `linter-setup-agent` | `linter-rule-definer` → `linter-config-generator` → `commit-hook-installer` | 프로젝트 맞춤 린터 환경 처음부터 완성까지 구성 |
| `lint-fix-agent` | `lint-error-analyzer` → `lint-autofix-suggester` | 린터 실패 원인 분석 후 수정 지시사항 전달 |

---

### 6단계 — 이밸루에이션

**스킬**

| 스킬명 | 입력 | 출력 |
|--------|------|------|
| `eval-criteria-definer` | 조직 목표 + 리스크 허용도 | 평가 기준 문서 (지표 + 임계값) |
| `rag-evaluator` | 질의-응답 쌍 + 소스 문서 | Context Relevance / Faithfulness / Answer Relevance / Recall 점수 |
| `regression-detector` | 현재 평가 결과 + 과거 기록 | 품질 저하 항목 + 알림 |
| `eval-report-generator` | 평가 지표 측정값 | 이밸루에이션 리포트 (에이전트별) |
| `threshold-recommender` | 과거 평가 기록 + 조직 목표 | 임계값 조정 제안 |

**에이전트**

| 에이전트명 | 파이프라인 | 역할 |
|------------|-----------|------|
| `eval-setup-agent` | `eval-criteria-definer` + `rag-evaluator` | 평가 기준 + 측정 파이프라인 초기 구성 |
| `daily-monitor-agent` | `rag-evaluator` → `regression-detector` → `eval-report-generator` | 매일 품질 저하 감지 및 리포트 생성 ("에이전트 도그") |
| `eval-tuning-agent` | `threshold-recommender` + `eval-criteria-definer` | 누적 데이터 기반 평가 기준 주기적 재보정 |

---

## 구현 우선순위

| 우선순위 | 스킬 / 에이전트 | 이유 |
|----------|-----------------|------|
| 즉시 | `meeting-log-agent` | 모든 프로젝트에서 매 미팅마다 사용 |
| 즉시 | `linter-setup-agent` | 멱등성 확보의 핵심, 초기 세팅 필수 |
| 즉시 | `requirements-analyst` | 모든 프로젝트의 시작점 |
| 단기 | `design-analysis-agent` | 설계 품질이 이후 전 단계에 영향 |
| 단기 | `daily-monitor-agent` | 납품 후 지속 품질 보장 |
| 중기 | `poc-briefing-agent` | 수주 경쟁력 강화 |
| 중기 | `code-repair-agent` | 유지보수 단계에서 가치 발휘 |
| 중기 | `prd-drafting-agent` | CPS 충분히 쌓인 후 PRD 자동화 효과 극대화 |
| 중기 | `prd-validation-agent` | PRD 품질 보증, 할루시네이션 방지 |
| 장기 | `eval-tuning-agent` | 충분한 데이터 축적 후 유효 |
| 장기 | `prd-maintenance-agent` | PRD 확정 이후 장기 운영 단계에서 가치 발휘 |

---

## Claude Code 스킬 (.claude/skills/)

이 리포지토리에 실제 구현된 Claude Code 커스텀 스킬 목록이다.

| 스킬 디렉토리 | 용도 |
|---------------|------|
| `skill-creator` | 새 스킬 생성·개선·이밸루에이션 (영문) |
| `skill-creator-ko` | 새 스킬 생성·개선·이밸루에이션 (한국어) |
| `지침정형` | AI 모델용 시스템 프롬프트·지침 문서 작성·검토·수정 |

> 스킬 추가 방법: `.claude/skills/<스킬명>/SKILL.md` 형식으로 작성한다.
