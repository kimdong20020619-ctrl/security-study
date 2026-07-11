---
tags: [llm-redteam, milestone2, implementation, done]
---

# 구현: Task5 — config.py 팩토리 확장

허브: [[허브]]

## 어떻게 진행했나
서브에이전트 위임(Sonnet) + TDD. `build_target`/`build_judge`/`build_reporter`에 각각 `policy`/`llm`/`html` 분기 추가.

## 핵심 확인 사항
`build_judge`가 `llm` 타입일 때 `config["target"]["confidential_keywords"]`를 참조하도록(마일스톤1이 `keyword` 타입일 때 `config["target"]["secret"]`을 참조하던 것과 동일 패턴) 정확히 구현됐는지가 이 태스크의 핵심 검증 포인트였음 — 리뷰에서 확인됨.

## 결과
- 커밋 `f4e8e67`
- test_config.py 12/12, 전체 스위트 34/34
- 리뷰: **Approved**. 기존 game/keyword/console 분기는 동작 변화 없음(변수 추출만), 3개 분기 모두 ValueError 폴백 정상 작동
