---
tags: [web-vuln-scanner, implementation, done]
---

# 구현: Task6 — reporter.py (HTML 리포트)

허브: [[허브]] · 관련: [[설계-3단계파이프라인]]

`HtmlReporter` — 4가지 판정 상태(확정 위험/위험 추정/오탐 추정/판정 불가)를 배지로 표시. **설계 단계부터 계획된 대로** alert.name/risk/url, LLM reasoning/fix_suggestion 5개 필드 전부 `html.escape()` 처리. 최종 리뷰에서도 "1번 프로젝트가 실수했던 부분을 이번엔 처음부터 제대로 반영했다"고 명시적으로 확인됨 — 사후 수정 없이 1차 구현에서 바로 Approved.
