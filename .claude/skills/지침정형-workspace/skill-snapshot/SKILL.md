---
name: aiwg
description: Reviews and rewrites AI instructions, prompts, system messages, guidelines, or rules to follow AIWG (AI Instruction Writing Guide) principles. Use this skill whenever the user wants to write, improve, review, or evaluate any instruction, directive, prompt, system message, guideline, or rule meant for an AI — even if they don't explicitly mention AIWG. Trigger on phrases like "AI한테 줄 지침", "프롬프트 작성", "시스템 메시지", "AI 규칙", "지침 개선", "프롬프트 리뷰", "instruction 써줘", "write a prompt", "system prompt", "write instructions for AI".
---

# AIWG (AI Instruction Writing Guide) Reviewer

목적: 사용자가 AI에게 전달할 지침을 AIWG 원칙에 맞게 진단하고 개선한다.

## AIWG 핵심 원칙

| 원칙 | 규칙 |
|------|------|
| 간결성 | 미사여구·강조 중첩·배경 서술 제거. 핵심만 기술. |
| 구조성 | 산문 금지. 번호 목록·불릿·표·if-else 블록 사용. |
| 구체성 | 주어·목적어 명시. 동의어 혼용 금지. 약어는 첫 등장 시 1회 정의. |

## 작업 절차

사용자 입력을 받으면 다음 순서로 처리한다.

### 1. 입력 분류

- 입력이 초안(draft)이면 → 진단 + 개선안 제시
- 입력이 요청사항(요구사항 설명)이면 → AIWG 형식으로 직접 작성
- 입력이 기존 지침 리뷰 요청이면 → 진단만 제시 후 개선 여부 묻기

### 2. 진단 (초안이 있을 때)

아래 체크리스트를 기준으로 위반 항목을 표로 출력한다.

| 체크 항목 | 상태 | 위반 내용 |
|-----------|------|-----------|
| 주어 명시 | ✅/❌ | (위반 시 해당 문장) |
| 모호한 지시어 없음 | ✅/❌ | |
| 동의어 혼용 없음 | ✅/❌ | |
| 감탄사·수식어 없음 | ✅/❌ | |
| 산문 단락 없음 | ✅/❌ | |
| 불필요한 맥락 제거 | ✅/❌ | |

### 3. 개선안 출력

아래 AIWG 템플릿을 적용하여 재작성한다.

```
# [지침 이름]

목적: [대상]이 [행동]하기 위한 지침.

## 입력
- [항목]: [타입/형식]

## 규칙
1. [규칙]

## 출력
- [항목]: [타입/형식]

## 예외
- [조건]: [처리 방법]
```

불필요한 섹션(예외가 없으면 예외 섹션 등)은 생략한다.

### 4. 변경 요약

개선안 아래에 한 줄로 주요 변경 사항을 요약한다.

**예:** `산문 3단락 → 규칙 목록 5항, 모호한 지시어 "이것" 2개 → "API 응답 JSON"으로 교체`

## 안티패턴 참조

| 패턴 | 처리 |
|------|------|
| 강조 중첩 ("매우 중요하므로 반드시") | "필수:" 레이블로 교체 |
| 암묵적 주어 ("확인 후 처리한다") | 주어 명시 ("AI가 입력값을 확인한 후 변환한다") |
| 과도한 예시 (유사 예시 3개 이상) | 대표 예시 1개로 축소 |
| 중의적 조건 ("필요한 경우") | 구체적 조건으로 교체 ("입력값이 null이면") |
| 배경 서술 (히스토리·이유 포함) | 행동 지침만 유지 |
