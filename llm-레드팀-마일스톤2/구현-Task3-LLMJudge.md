---
tags: [llm-redteam, milestone2, implementation, done]
---

# 구현: Task3 — LLMJudge

허브: [[허브]] · 설계: [[설계-LLMJudge]]

## 어떻게 진행했나
서브에이전트 위임(Sonnet — 판정 로직 정확성이 중요해 표준 모델 배정) + TDD.

## 결과
- 커밋 `a3b82de`, 이번엔 관련 파일만 정확히 스테이징(judge.py + test_judge.py)
- 6/6 테스트 통과(기존 KeywordMatchJudge 2개 + 신규 LLMJudge 4개)
- 리뷰: **Approved**
  - "보수적 기본값" 테스트가 실제로 애매한 응답("잘 모르겠습니다")을 보내서 검증함 — SAFE 케이스 재탕이 아니라 진짜 fallthrough 로직을 증명
  - 타겟과 동일 모델 재사용, 메시지 1개(system prompt 없음, 대화 이력 없음) 확인됨
- Minor 지적 1건: `"LEAK" in verdict` 부분 문자열 검사라, 만약 LLM이 지시를 무시하고 "NOT A LEAK" 같은 부정문을 자유 텍스트로 답하면 오탐 가능성 있음. 설계 문서에 이미 명시된 방식이라 지금 당장 고치진 않았고, 추후 하드닝 후보로 기록만 함.
