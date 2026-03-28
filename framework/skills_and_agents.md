# 단계별 클로드 스킬 및 에이전트 목록

각 워크플로우 단계에서 필요한 Claude 스킬과 에이전트를 정의한다.
- **스킬**: 단일 목적의 프롬프트 지시사항 단위. 재사용 가능한 작업 템플릿.
- **에이전트**: 여러 스킬을 조합하거나 외부 도구를 사용해 자율적으로 목표를 달성하는 단위.

---

## 1단계 — 요구사항 입력

> 목표: 비정형 고객 요구사항을 분석하여 시스템 구성 요소와 회의 안건으로 구조화한다.

### 스킬

| 스킬명 | 입력 | 출력 | 설명 |
|--------|------|------|------|
| `requirement-parser` | 자유형식 고객 요구사항 텍스트 | 구조화된 요구사항 목록 | 비정형 문장에서 기능 요구사항과 비기능 요구사항을 분리·추출 |
| `ambiguity-detector` | 요구사항 목록 | 불명확 항목 + 확인 질문 목록 | 정의되지 않은 용어, 모순, 누락된 전제를 감지하여 회의 안건화 |
| `component-identifier` | 요구사항 목록 | 시스템 구성 요소 목록 + 근거 | 요구사항에서 필요한 소프트웨어 단위를 도출 (예: 고객 앱, 기사 앱, 어드민) |
| `feasibility-reporter` | 요구사항 + 도메인 문서 | 실현 가능성 리포트 | 기술적 타당성 검토 및 난이도 평가 |
| `cost-estimator` | 요구사항 + 기술 스택 | 예산 및 API 비용 예측 | AX 도입 비용 및 예상 API 사용량 산출 |
| `timeline-estimator` | 요구사항 + 기술 난이도 | 소요 기간 산출표 | 구성 요소별 구현 기간 추정 |

### 에이전트

| 에이전트명 | 사용 스킬 | 설명 |
|------------|-----------|------|
| `requirements-analyst` | `requirement-parser` → `ambiguity-detector` → `component-identifier` | 원시 요구사항을 받아 구성 요소 정의까지 한 번에 수행 |
| `poc-briefing-agent` | `feasibility-reporter` + `cost-estimator` + `timeline-estimator` | PoC 제안 전 고객사에 전달할 사전 분석 브리핑 문서 생성 |

---

## 2단계 — 플랜 수립

> 목표: 미팅 내용을 구조적으로 기록하고, CPS 프레임워크로 정리한 뒤 PRD로 이행한다.

### 스킬

| 스킬명 | 입력 | 출력 | 설명 |
|--------|------|------|------|
| `meeting-transcriber` | 미팅 녹취 또는 메모 | 전사된 미팅 로그 (발화자 구분) | 미팅 내용을 구조화된 텍스트로 전환 |
| `meeting-summarizer` | 미팅 로그 | 결론 요약 + 잘된 것/부족한 것/다음 안건 | 발산된 대화를 수렴하여 핵심만 추출 |
| `cps-writer` | 미팅 로그 누적본 | CPS 문서 (Context / Problem / Solution) | 누적된 미팅 내용을 CPS 프레임워크로 정리 |
| `cps-updater` | 기존 CPS 문서 + 신규 미팅 로그 | 업데이트된 CPS 문서 | 새 미팅 내용을 기존 CPS에 반영하여 점진적으로 구체화 |
| `prd-writer` | 확정된 CPS 문서 | PRD 초안 | CPS에서 PRD로 이행하는 기획 문서 작성 |
| `build-vs-buy-analyzer` | 기능 목록 | 자체 구축 / 외부 솔루션 구매 분류표 | 각 기능을 직접 만들지 구매할지 분석 |

### 에이전트

| 에이전트명 | 사용 스킬 | 설명 |
|------------|-----------|------|
| `meeting-log-agent` | `meeting-transcriber` → `meeting-summarizer` → `cps-updater` | 미팅 종료 후 로그 생성부터 CPS 업데이트까지 자동화 |
| `plan-document-agent` | `cps-writer` + `prd-writer` + `build-vs-buy-analyzer` | 확정 시점에 CPS → PRD → 산출물 일괄 생성 |

---

## 3단계 — 설계 분석

> 목표: 원칙을 정의하고 도메인 개념을 구조화하여 AI가 구현할 수 있는 아키텍처 명세를 만든다.

### 스킬

| 스킬명 | 입력 | 출력 | 설명 |
|--------|------|------|------|
| `principle-definer` | 프로젝트 목표 + 제약 조건 | 설계 원칙 목록 (우선순위 포함) | 할루시네이션 허용 여부, 감사 로그, Human-in-the-Loop 등 대전제 정의 |
| `domain-glossary-builder` | CPS 문서 + 고객사 용어 | 도메인 용어 사전 | 개념 정의 및 동의어/혼용 방지 규칙 작성 |
| `hierarchy-designer` | 도메인 용어 사전 | 개념 계층 구조 다이어그램 + 설명 | 도메인 개념 간 포함·참조 관계 정의 |
| `architecture-specifier` | 원칙 + 계층 구조 + 요구사항 | 아키텍처 명세 문서 (DDD 기반) | 레이어 구성, 도메인 모델 목록, 데이터 인터페이스 정의 |
| `mental-model-mapper` | 사용자 인터뷰 또는 UX 요구사항 | 유저 멘탈 모델 문서 | 사용자가 시스템을 어떻게 인식하는지 정리하여 설계에 반영 |
| `tech-stack-recommender` | 요구사항 + 원칙 + 팀 역량 | 기술 스택 추천 및 근거 | 프레임워크, 라이브러리, 인프라 선택 근거 문서화 |

### 에이전트

| 에이전트명 | 사용 스킬 | 설명 |
|------------|-----------|------|
| `design-analysis-agent` | `principle-definer` → `domain-glossary-builder` → `hierarchy-designer` → `architecture-specifier` | 원칙부터 아키텍처까지 설계 문서 전체를 순차 생성 |
| `ux-alignment-agent` | `mental-model-mapper` + `architecture-specifier` | 유저 멘탈 모델이 아키텍처 결정에 반영되었는지 검증 및 조정 |

---

## 4단계 — 코드 구현

> 목표: 아키텍처 명세를 기반으로 AI가 일관된 코드를 생성하도록 요청 구조를 정의한다.

### 스킬

| 스킬명 | 입력 | 출력 | 설명 |
|--------|------|------|------|
| `domain-model-generator` | 아키텍처 명세 + 도메인 용어 사전 | 도메인 모델 코드 (Entity, VO, Repository 인터페이스) | DDD 패턴 기반 도메인 레이어 코드 생성 |
| `feature-scaffolder` | 기능 명세 + 아키텍처 명세 | 기능별 파일 구조 + 보일러플레이트 코드 | 기능 단위로 폴더 구조와 빈 구현체 일괄 생성 |
| `interface-definer` | 도메인 모델 + 기능 명세 | 모듈 간 인터페이스 계약 문서 | 각 모듈의 입출력 인터페이스를 명시해 교체 가능성 확보 |
| `code-reviewer` | 생성된 코드 + 설계 원칙 | 리뷰 코멘트 + 수정 제안 | 원칙 위반, 네이밍 오류, 레이어 침범 여부 검토 |
| `module-rewriter` | 삭제 대상 모듈 명세 + 인터페이스 계약 | 재작성된 모듈 코드 | 버그 발생 시 해당 모듈만 삭제 후 인터페이스 기반으로 재생성 |

### 에이전트

| 에이전트명 | 사용 스킬 | 설명 |
|------------|-----------|------|
| `implementation-agent` | `domain-model-generator` → `feature-scaffolder` → `interface-definer` | 아키텍처 명세를 받아 전체 코드 골격을 생성 |
| `code-repair-agent` | `code-reviewer` → `module-rewriter` | 문제 모듈을 감지하고 인터페이스를 보존한 채 재구현 |

---

## 5단계 — 린터 적용

> 목표: 커밋 시점에 코드 구조 규칙을 자동 강제하여 어떤 AI 모델이 생성하든 동일한 아웃풋을 보장한다.

### 스킬

| 스킬명 | 입력 | 출력 | 설명 |
|--------|------|------|------|
| `linter-rule-definer` | 아키텍처 명세 + 도메인 용어 사전 | 린터 규칙 명세 문서 | 파일 명명, 임포트 순서, 배럴 파일 규칙 등 정의 |
| `linter-config-generator` | 린터 규칙 명세 | `.eslintrc`, `custom-rules/` 등 설정 파일 | 규칙 명세를 실제 린터 설정 코드로 변환 |
| `commit-hook-installer` | 린터 설정 파일 + 프로젝트 구조 | `pre-commit` 훅 스크립트 | 커밋 시 린터 자동 실행 환경 구성 |
| `lint-error-analyzer` | 린터 오류 메시지 | 위반 원인 분석 + 수정 지시사항 | 린터 실패 원인을 파악하고 AI에게 수정 방향 제시 |
| `lint-autofix-suggester` | 린터 오류 목록 + 코드 | 자동 수정 가능 항목 분류 + 수정 코드 | 자동 수정 가능한 항목과 수동 검토 필요 항목을 분리 |

### 에이전트

| 에이전트명 | 사용 스킬 | 설명 |
|------------|-----------|------|
| `linter-setup-agent` | `linter-rule-definer` → `linter-config-generator` → `commit-hook-installer` | 프로젝트 특성에 맞는 린터 환경을 처음부터 완성까지 구성 |
| `lint-fix-agent` | `lint-error-analyzer` → `lint-autofix-suggester` | 린터 실패 시 원인 분석 후 수정 지시사항을 AI에 전달해 재시도 |

---

## 6단계 — 이밸루에이션

> 목표: AI 에이전트의 응답 품질을 지속적으로 측정하고, 조직 맞춤형 기준으로 모니터링한다.

### 스킬

| 스킬명 | 입력 | 출력 | 설명 |
|--------|------|------|------|
| `eval-criteria-definer` | 조직 목표 + 리스크 허용도 | 평가 기준 문서 (지표 + 임계값) | "100번 중 몇 번 틀려도 되는가" 등 조직 맞춤 기준 정의 |
| `rag-evaluator` | 질의-응답 쌍 + 소스 문서 | Context Relevance / Faithfulness / Answer Relevance / Recall 점수 | 표준 RAG 4개 지표 측정 |
| `regression-detector` | 현재 평가 결과 + 과거 기록 | 품질 저하 항목 + 알림 | 이전 버전 대비 품질이 하락한 지표 감지 |
| `eval-report-generator` | 평가 지표 측정값 | 이밸루에이션 리포트 (에이전트별) | 에이전트별 품질 현황 요약 문서 생성 |
| `threshold-recommender` | 과거 평가 기록 + 조직 목표 | 임계값 조정 제안 | 축적된 데이터를 바탕으로 적정 임계값 재설정 제안 |

### 에이전트

| 에이전트명 | 사용 스킬 | 설명 |
|------------|-----------|------|
| `eval-setup-agent` | `eval-criteria-definer` + `rag-evaluator` | 프로젝트 초기에 평가 기준과 측정 파이프라인을 함께 구성 |
| `daily-monitor-agent` | `rag-evaluator` → `regression-detector` → `eval-report-generator` | 매일 실행되어 품질 저하를 감지하고 리포트 생성 ("에이전트 도그") |
| `eval-tuning-agent` | `threshold-recommender` + `eval-criteria-definer` | 누적 데이터를 분석해 평가 기준을 주기적으로 재보정 |

---

---

## 2단계 확장 — PRD 파이프라인

> 상세 스킬 명세는 [prd_skills.md](../prd/prd_skills.md) / 방법론은 [prd_methodology.md](../prd/prd_methodology.md) 참고

CPS가 충분히 구체화된 이후 PRD로 전환하는 전 과정을 자동화하는 스킬·에이전트 구조.

### 추가 스킬

| 스킬명 | 입력 | 출력 | 설명 |
|--------|------|------|------|
| `cps-completeness-checker` | CPS 누적 전체 | 완성도 점수 + 미흡 항목 목록 | PRD 착수 가능 여부 판단, 미흡 항목은 다음 미팅 안건으로 전환 |
| `persona-extractor` | CPS Context 누적 | 페르소나 카드 목록 | 사용자 유형별 목표·행동·Pain Point·민감 조건 구조화 |
| `goal-metric-definer` | CPS Problem 누적 | 목표-KPI-측정방법 테이블 | "현재 상태 → 목표 상태 → KPI → 측정 방법" 체인 변환 |
| `feature-distiller` | CPS Solution 확정 항목 | Epic / Feature / Story 계층 | 확정된 Solution만 골라 기능 계층 구조로 분해 |
| `acceptance-criteria-generator` | Feature 목록 | Feature별 AC (Given-When-Then) | 각 기능에 테스트 가능한 수락 기준 생성 |
| `nfr-extractor` | CPS Context 누적 | NFR 목록 (카테고리-지표-목표값) | 성능·가용성·보안·확장성·접근성 비기능 요구사항 추출 |
| `scope-boundary-detector` | CPS 전체 | In / Out / TBD 경계 목록 | 명시·암묵적 제외 항목 감지, 제외 근거 출처 태깅 |
| `prd-drafter` | 증류 스킬 출력 전체 + CPS | PRD 초안 (출처 태그 포함) | CPS 출처 없는 항목은 생성 않고 TBD 처리 (할루시네이션 방지) |
| `requirement-validator` | PRD 초안 + CPS | 검증 리포트 (VALID / HALLUCINATED / MISSING) | PRD↔CPS 역방향·순방향 교차 검증, 수치 불일치 감지 |
| `prd-gap-detector` | PRD 초안 | 보완 질문 목록 + 다음 미팅 안건 | 섹션 누락·모호 표현·AC 없는 기능 감지 |
| `dependency-mapper` | Feature 목록 + 기술 스택 | 의존성 맵 + 크리티컬 패스 | 기능 간 선행 관계 및 외부 시스템 연동 의존성 매핑 |
| `prd-change-detector` | 현재 발화 + 확정 PRD | 변경 경보 + 영향도 분석 | PRD 확정 이후 미팅에서 스코프 변경·신규 요구 실시간 감지 |
| `traceability-matrix-builder` | PRD 확정본 + CPS | 요구사항 추적성 매트릭스 | 요구사항 ID ↔ 출처 미팅 ↔ 발화 원문 매핑 |

### 추가 에이전트

| 에이전트명 | 구성 스킬 | 설명 |
|------------|-----------|------|
| `prd-readiness-agent` | `cps-completeness-checker` + `prd-gap-detector` | PRD 착수 가능 여부 판단, 부족한 부분을 다음 미팅 안건으로 전환 |
| `prd-drafting-agent` | `persona-extractor` + `goal-metric-definer` + `feature-distiller` + `acceptance-criteria-generator` + `nfr-extractor` + `scope-boundary-detector` + `dependency-mapper` + `prd-drafter` | CPS 전체를 읽고 PRD 전체 초안 자동 생성 |
| `prd-validation-agent` | `requirement-validator` + `prd-gap-detector` + `traceability-matrix-builder` | PRD 초안의 완결성·일치성 검증 및 추적성 매트릭스 생성 |
| `prd-maintenance-agent` | `prd-change-detector` + `contradiction-detector` | PRD 확정 이후 미팅에서 변경 사항 실시간 감지 및 영향도 리포트 |

---

## 2단계 확장 — 미팅 인텔리전스 에이전트

> 상세 설계는 [meeting_agent.md](../mia/meeting_agent.md) 참고

미팅 전·중·후 전 과정에 AI가 실시간으로 개입하는 전용 에이전트 구조.

### 추가 스킬

| 스킬명 | 입력 | 출력 | 설명 |
|--------|------|------|------|
| `pre-meeting-briefer` | 과거 미팅 로그 전체 | 브리핑 문서 | 결정 사항·보류 항목·고객 성향을 요약하여 미팅 전 제공 |
| `gap-alert-generator` | 마지막 미팅 날짜 + 미결 항목 | 재확인 항목 목록 | 공백 기간이 길수록 잊기 쉬운 항목을 우선 추출 |
| `realtime-cps-tagger` | 실시간 발화 텍스트 | CPS 분류 태그 + 요약 | 대화 중 발언을 Context / Problem / Solution으로 즉시 분류 |
| `suggestion-engine` | 현재 대화 + 과거 미팅 로그 | FDE용 제안 메시지 | 놓친 맥락·반복 주제·결정 기회를 감지해 FDE에게 제안 |
| `contradiction-detector` | 현재 발언 + 기존 결정 사항 | 충돌 경보 + 근거 | 이전 합의와 어긋나는 발언을 실시간 감지 |
| `abstract-request-resolver` | 추상적 고객 발언 | 구체화된 요구사항 + 레퍼런스 | 웹 검색으로 추상적 요구를 측정 가능한 지표로 전환 |
| `post-meeting-documenter` | 전체 미팅 로그 | 결정 사항 + 미결 항목 + 액션 아이템 | 미팅 종료 후 산출물 자동 생성 |

### 추가 에이전트

| 에이전트명 | 구성 스킬 | 설명 |
|------------|-----------|------|
| `pre-meeting-agent` | `pre-meeting-briefer` + `gap-alert-generator` | 미팅 시작 전 브리핑 패키지 자동 생성 |
| `live-meeting-agent` | `realtime-cps-tagger` + `suggestion-engine` + `contradiction-detector` + `abstract-request-resolver` | 미팅 중 실시간 동반 |
| `post-meeting-agent` | `post-meeting-documenter` + `cps-updater` | 미팅 후 문서 생성 및 CPS 갱신 |
| `meeting-intelligence-agent` | 위 세 에이전트 통합 | 전·중·후 전 과정을 하나의 세션으로 관리 |

---

## 전체 스킬·에이전트 맵

```
1단계  requirement-parser, ambiguity-detector, component-identifier
       feasibility-reporter, cost-estimator, timeline-estimator
       → requirements-analyst, poc-briefing-agent

2단계  meeting-transcriber, meeting-summarizer, cps-writer, cps-updater
       prd-writer, build-vs-buy-analyzer
       → meeting-log-agent, plan-document-agent

2단계+ pre-meeting-briefer, gap-alert-generator, realtime-cps-tagger
(미팅  suggestion-engine, contradiction-detector, abstract-request-resolver
전용)  post-meeting-documenter
       → pre-meeting-agent, live-meeting-agent, post-meeting-agent
       → meeting-intelligence-agent (통합)

2단계+ cps-completeness-checker, persona-extractor, goal-metric-definer
(PRD   feature-distiller, acceptance-criteria-generator, nfr-extractor
파이프 scope-boundary-detector, prd-drafter, requirement-validator
라인)  prd-gap-detector, dependency-mapper, prd-change-detector
       traceability-matrix-builder
       → prd-readiness-agent, prd-drafting-agent
       → prd-validation-agent, prd-maintenance-agent

3단계  principle-definer, domain-glossary-builder, hierarchy-designer
       architecture-specifier, mental-model-mapper, tech-stack-recommender
       → design-analysis-agent, ux-alignment-agent

4단계  domain-model-generator, feature-scaffolder, interface-definer
       code-reviewer, module-rewriter
       → implementation-agent, code-repair-agent

5단계  linter-rule-definer, linter-config-generator, commit-hook-installer
       lint-error-analyzer, lint-autofix-suggester
       → linter-setup-agent, lint-fix-agent

6단계  eval-criteria-definer, rag-evaluator, regression-detector
       eval-report-generator, threshold-recommender
       → eval-setup-agent, daily-monitor-agent, eval-tuning-agent
```

---

## 스킬 구현 우선순위

엔터프라이즈 현장 적용 관점에서 임팩트가 크고 반복 사용 빈도가 높은 순서로 정렬했다.

| 우선순위 | 스킬 / 에이전트 | 이유 |
|----------|-----------------|------|
| 🔴 즉시 | `meeting-log-agent` | 모든 프로젝트에서 매 미팅마다 사용 |
| 🔴 즉시 | `linter-setup-agent` | 멱등성 확보의 핵심, 초기 세팅 필수 |
| 🔴 즉시 | `requirements-analyst` | 모든 프로젝트의 시작점 |
| 🟡 단기 | `design-analysis-agent` | 설계 품질이 이후 전 단계에 영향 |
| 🟡 단기 | `daily-monitor-agent` | 납품 후 지속 품질 보장 |
| 🟢 중기 | `poc-briefing-agent` | 수주 경쟁력 강화 |
| 🟢 중기 | `code-repair-agent` | 유지보수 단계에서 가치 발휘 |
| 🟢 중기 | `prd-drafting-agent` | CPS 충분히 쌓인 후 PRD 자동화 효과 극대화 |
| 🟢 중기 | `prd-validation-agent` | PRD 품질 보증, 할루시네이션 방지 |
| 🔵 장기 | `eval-tuning-agent` | 충분한 데이터 축적 후 유효 |
| 🔵 장기 | `prd-maintenance-agent` | PRD 확정 이후 장기 운영 단계에서 가치 발휘 |
