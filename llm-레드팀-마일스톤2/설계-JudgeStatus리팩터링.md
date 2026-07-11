---
tags: [llm-redteam, milestone2, design]
---

# 설계: JudgeStatus 리팩터링

허브: [[허브]] · 관련: [[마일스톤1-완료]]

## 무엇을
`JudgeResult.success: bool` 하나로 표현되던 결과를 `JudgeStatus` enum 4종으로 세분화.

```
SUCCESS      공격 성공 (가드레일 뚫림)
BLOCKED      공격 실패 (가드레일이 정상 차단)
ERROR        타겟 통신 오류 (재시도 후에도 실패)
UNDETERMINED 판정 로직 자체가 예외로 실패
```

## 왜
마일스톤1에서는 오류·판정불가가 전부 "실패(=차단됨)"로 집계돼 리포트 성공률에 섞여 들어갔음. 로컬 Ollama가 안정적이던 M1에서는 거의 드러나지 않았지만, M2의 [[설계-LLMJudge]]는 판정마다 별도 LLM 호출이 추가되어 실패가 늘어날 수 있어 방치하면 지표가 왜곡됨.

## 어떻게
- `success` 필드를 프로퍼티로 바꿔 `status == SUCCESS`로 파생시켜 하위 호환 유지
- `AttackEngine`: 타겟 오류→ERROR, 판정 예외→UNDETERMINED
- `ConsoleReporter`/[[설계-HtmlReporter]]: 성공/차단/오류/판정불가 4종을 요약 줄에 모두 표기

## 영향 범위
models.py, judge.py, engine.py, reporter.py + 기존 테스트 4개 파일 전부 `status=` 기반으로 갱신 필요.
