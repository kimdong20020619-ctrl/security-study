---
tags: [web-vuln-scanner, implementation, done]
---

# 구현: Task5 — analyzer.py (LLM 재판정)

허브: [[허브]]

## 최초 구현
`Analyzer` — ZAP 경고의 confidence가 High/Confirmed면 재판정 생략(그대로 확정 위험), 그 외(Low/Medium/False Positive)는 로컬 Ollama에게 JSON 형식으로 재판정 요청(REAL_RISK/LIKELY_FALSE_POSITIVE). 파싱 실패·예상 밖 값은 UNDETERMINED로 안전 처리. 개별 alert 예외가 배치 전체를 막지 않도록 구조적으로 격리됨(리뷰에서 확인). 서브에이전트(Sonnet) + 리뷰어, Approved.

## 최종 전체 리뷰에서 발견되어 수정
설계 스펙이 "파싱 실패 시 원본 LLM 응답을 보존해 사후 확인 가능하게 한다"고 명시했는데, 실제로는 예외 메시지만 남기고 원본 응답을 버리고 있었음(Important 발견). `content` 변수로 원본 응답을 캡처해 판정 불가 사유에 포함하도록 수정 — 단, Ollama 호출 자체가 실패한 경우(애초에 응답이 없음)엔 "원본 응답 없음"을 지어내지 않고 표시 안 함. 신규 테스트로 두 케이스를 구분해서 검증.
