# CPS → PRD 변환 방법론

> 누적된 CPS(Context / Problem / Solution)를 기반으로 PRD(Product Requirements Document)를 구체화하는 방법론과 에이전트/스킬 설계.

---

## 핵심 전제

**PRD는 CPS의 압축된 계약서다.**

- 모든 PRD 항목은 CPS에 출처가 있어야 한다 — 할루시네이션 방지
- PRD 완성도는 CPS 완성도에 정비례한다
- PRD 작성 후에도 에이전트는 미팅을 계속 모니터링하며 PRD 변경을 감지한다

```
CPS (누적) ──▶ PRD (구조화) ──▶ 개발 스펙 (실행)
     │               │
     └── 출처 추적 ──┘  ← 모든 요구사항이 어느 미팅 발화에서 왔는지 연결
```

---

## 5단계 방법론

### Phase 1 — PRD 착수 준비: CPS 완성도 평가

PRD 작성을 시작하기 전에 CPS가 충분히 성숙했는지 판단한다.

```
[CPS 완성도 체크리스트]

Context 충분성
  ☑ 고객사 비즈니스 모델이 명확한가?
  ☑ 주요 사용자 유형이 식별되었는가?
  ☑ 기술적 제약 조건이 파악되었는가?
  ☑ 규모 수치가 정의되었는가? (MAU, 트래픽, 데이터량 등)
  ☐ 외부 시스템 연동 범위가 확정되었는가?

Problem 충분성
  ☑ 핵심 문제가 측정 가능한 형태로 정의되었는가?
  ☑ 사용자별 Pain Point가 분리되었는가?
  ☐ 비즈니스 임팩트(손실/기회)가 수치화되었는가?

Solution 충분성
  ☑ 주요 기술 결정이 확정되었는가? (보류 항목 아님)
  ☑ 핵심 기능 목록의 윤곽이 잡혔는가?
  ☐ 우선순위 기준이 합의되었는가? (중요도 / 긴급도)
  ☐ 1차 스코프와 이후 스코프가 분리되었는가?
```

**출력**: CPS 완성도 점수 + 미흡 항목 → 다음 미팅 보완 안건으로 전환

---

### Phase 2 — 요구사항 증류: CPS → 구조화된 요구사항

CPS의 각 차원을 PRD 섹션으로 변환한다.

```
Context  ──▶  배경, 페르소나, 비기능 요구사항, 제약 조건
Problem  ──▶  문제 정의, 비즈니스 목표, 성공 지표
Solution ──▶  기능 요구사항, 수락 기준, 스코프 경계
```

#### 변환 규칙

| CPS 데이터 | PRD 섹션 | 변환 방식 |
|-----------|---------|---------|
| Context: 사용자 유형 | 페르소나 | 역할별 목표·행동 패턴으로 구체화 |
| Context: 기술 제약 | 비기능 요구사항 | 측정 가능한 지표로 전환 (예: "빠르게" → "p99 < 200ms") |
| Context: 규모 수치 | 용량 요구사항 | 최대값 기준 + 성장 여유율 반영 |
| Problem: 핵심 문제 | 문제 정의 + 목표 | "현재 상태 → 목표 상태" 형식으로 서술 |
| Problem: 측정 지표 | 성공 지표(KPI) | 베이스라인 + 목표값 + 측정 방법 |
| Solution: 확정 결정 | 기능 요구사항 | 사용자 스토리 형식으로 재서술 |
| Solution: 보류 항목 | 미결 사항 섹션 | PRD 내 명시적 TBD로 표기 |
| 미팅 간 scope 제외 | Out of Scope | 합의된 제외 근거와 함께 명시 |

---

### Phase 3 — PRD 초안 생성

아래 구조로 PRD를 자동 생성한다. 각 항목에 CPS 출처를 태깅한다.

```markdown
# PRD: [프로젝트명]

## 1. 배경 및 목적
  - 비즈니스 배경          ← Context 출처
  - 해결하려는 핵심 문제   ← Problem 출처

## 2. 목표 및 성공 지표
  - 비즈니스 목표          ← Problem 출처
  - KPI / 성공 지표        ← Problem + Context 출처

## 3. 사용자 페르소나
  - 페르소나별 목표·Pain Point ← Context 출처

## 4. 기능 요구사항
  - Epic / Feature / Story 계층 ← Solution 출처
  - 각 기능별 수락 기준(AC)

## 5. 비기능 요구사항 (NFR)
  - 성능 / 보안 / 확장성 / 가용성 ← Context 출처

## 6. 스코프 경계
  - In Scope
  - Out of Scope              ← 미팅에서 명시적으로 제외된 항목
  - 미결 사항 (TBD)           ← 보류 중인 Solution 항목

## 7. 의존성 및 제약 조건
  - 외부 시스템 연동
  - 기술 스택 제약            ← 확정된 기술 결정

## 8. 요구사항 추적성 매트릭스
  - 요구사항 ID ↔ CPS 출처 미팅 ↔ 발화 원문
```

**할루시네이션 방지 원칙**: 에이전트는 CPS에 없는 요구사항을 임의로 추가하지 않는다. 없는 항목은 TBD로 표기하고 보완 질문을 생성한다.

---

### Phase 4 — 검증 및 정제

초안을 CPS와 대조하여 일치성을 검증하고 누락을 감지한다.

```
[검증 체크]

1. 역방향 검증: PRD의 모든 요구사항이 CPS에 출처가 있는가?
   → 없으면: 할루시네이션으로 플래그

2. 순방향 검증: CPS의 모든 확정 결정이 PRD에 반영되었는가?
   → 없으면: 누락으로 플래그

3. TBD 검증: 보류 항목이 TBD로 명시되었는가?
   → TBD 없이 확정된 것처럼 기술되면 경고

4. 수락 기준 검증: 각 기능에 테스트 가능한 AC가 있는가?
   → AC가 모호하면 ("빠르게", "쉽게") 구체화 요청
```

**출력**: 검증 리포트 + FDE 검토 요청 항목 목록

---

### Phase 5 — PRD 유지관리: 미팅 이후 변경 감지

PRD 확정 이후에도 에이전트는 미팅을 모니터링하며 PRD에 영향을 주는 변경을 감지한다.

```
[PRD 변경 감지 예시]

미팅 중 감지:
  고객: "아, 그리고 앱 내 결제도 넣어야 할 것 같아요."

  ⚠️ 에이전트:
  "앱 내 결제는 Meeting 1에서 1차 스코프 제외로 합의된 항목입니다.
   PRD Out of Scope 항목 변경이 발생합니다.
   스코프 변경으로 처리할지 확인이 필요합니다."

   → PRD 변경 영향도:
     - 일정: PG사 연동 개발 +3주 예상
     - 비용: 결제 모듈 라이선스 비용 추가
     - 의존성: 사업자 등록 + 전자금융업 신고 필요
```

---

## 스킬 정의

| 스킬명 | 설명 | 입력 | 출력 |
|--------|------|------|------|
| `cps-completeness-checker` | 현재 누적 CPS가 PRD 작성에 충분한지 평가 | CPS 전체 | 완성도 점수 + 미흡 항목 목록 |
| `persona-extractor` | CPS Context에서 사용자 유형별 페르소나 추출 | Context 누적 | 페르소나 문서 (역할·목표·Pain Point) |
| `goal-metric-definer` | Problem에서 측정 가능한 목표·KPI 도출 | Problem 누적 | 목표 + 성공 지표 (베이스라인·목표값) |
| `feature-distiller` | 확정된 Solution에서 기능 요구사항 목록 추출 | Solution 누적 | Epic / Feature / Story 계층 구조 |
| `acceptance-criteria-generator` | 각 기능에 대해 테스트 가능한 수락 기준 생성 | Feature 목록 | Feature별 AC 목록 |
| `nfr-extractor` | Context 제약 조건에서 비기능 요구사항 추출 | Context 누적 | NFR 목록 (성능·보안·확장성 지표) |
| `scope-boundary-detector` | 미팅에서 명시·암묵적으로 제외된 범위 감지 | CPS 전체 | In/Out Scope 목록 + 근거 |
| `prd-drafter` | CPS 전체를 읽고 PRD 전체 초안 생성 | CPS 전체 | PRD 초안 (출처 태그 포함) |
| `requirement-validator` | PRD ↔ CPS 일치성 검증 (할루시네이션 감지) | PRD 초안 + CPS | 검증 리포트 (할루시네이션·누락 플래그) |
| `prd-gap-detector` | PRD 초안에서 TBD·누락 섹션 감지 | PRD 초안 | 보완 질문 목록 + 다음 미팅 안건 |
| `dependency-mapper` | 기능 간 의존성 및 외부 시스템 의존성 매핑 | Feature 목록 | 의존성 다이어그램 + 제약 목록 |
| `prd-change-detector` | 미팅 발화에서 PRD 변경 사항 실시간 감지 | 현재 발화 + PRD | 변경 경보 + 영향도 분석 |
| `traceability-matrix-builder` | 요구사항 ↔ CPS 출처 추적성 매트릭스 생성 | PRD + CPS | 추적성 매트릭스 문서 |

---

## 에이전트 정의

| 에이전트명 | 구성 스킬 | 역할 |
|------------|-----------|------|
| `prd-readiness-agent` | `cps-completeness-checker` + `prd-gap-detector` | PRD 작성 가능 여부 판단 + 부족한 부분을 다음 미팅 안건으로 전환 |
| `prd-drafting-agent` | `persona-extractor` + `goal-metric-definer` + `feature-distiller` + `acceptance-criteria-generator` + `nfr-extractor` + `scope-boundary-detector` + `dependency-mapper` + `prd-drafter` | CPS 전체를 읽고 PRD 전체 초안 자동 생성 |
| `prd-validation-agent` | `requirement-validator` + `prd-gap-detector` + `traceability-matrix-builder` | PRD 초안의 완결성·일치성 검증 및 추적성 매트릭스 생성 |
| `prd-maintenance-agent` | `prd-change-detector` + `contradiction-detector` + `prd-gap-detector` | PRD 확정 이후 미팅에서 변경 사항 실시간 감지 + 영향도 리포트 |

---

## 전체 흐름

```
미팅 누적 (MIA)
    │
    ▼
[prd-readiness-agent]
    CPS 완성도 평가
    미흡 → 다음 미팅 안건
    충분 ▼
    │
    ▼
[prd-drafting-agent]
    CPS → PRD 초안 자동 생성
    (출처 태그 포함)
    │
    ▼
[prd-validation-agent]
    할루시네이션 감지
    누락 항목 감지
    추적성 매트릭스 생성
    │
    ├── 보완 필요 → 다음 미팅 안건으로 환류
    │
    └── 검증 통과 → PRD 확정
                        │
                        ▼
               [prd-maintenance-agent]
                   이후 미팅 계속 모니터링
                   PRD 변경 감지 시 경보
```

---

## MIA 전체 에이전트 체계 통합

기존 MIA 에이전트에 PRD 에이전트가 추가된 전체 구조.

```
meeting-intelligence-agent
    │
    ├── pre-meeting-agent       (미팅 전 브리핑)
    ├── live-meeting-agent      (미팅 중 실시간)
    ├── post-meeting-agent      (미팅 후 문서화·CPS 갱신)
    ├── kb-management-agent     (도메인 KB 관리)
    │
    └── prd-pipeline-agent      (CPS → PRD 전환 파이프라인)
            ├── prd-readiness-agent
            ├── prd-drafting-agent
            ├── prd-validation-agent
            └── prd-maintenance-agent
```

---

## PRD 전환 시점 판단 기준

CPS 완성도 평가만으로는 부족하다. 아래 세 조건이 함께 충족되어야 PRD 착수를 권장한다.

| 조건 | 판단 기준 |
|------|---------|
| **CPS 밀도** | 확정 결정이 보류 항목보다 많다 |
| **스코프 합의** | 1차 스코프 범위에 대해 고객과 FDE가 명시적으로 동의했다 |
| **수치 기준선** | 핵심 NFR이 측정 가능한 숫자로 정의되었다 (최소 3개 이상) |

---

*관련 문서*
- *[meeting_agent.md](../mia/meeting_agent.md) — MIA 전체 구조 및 스킬/에이전트 정의*
- *[cps_evolution_example.md](../cps/cps_evolution_example.md) — CPS 정밀화 과정 예시*
