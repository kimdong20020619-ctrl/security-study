---
tags: [web-vuln-scanner, implementation, done]
---

# 구현: Task7 — main.py + README

허브: [[허브]] · 관련: [[검증-E2E트러블슈팅]]

CLI 진입점(`main.py`, `--skip-scan` 플래그) + README 작성. 처음 작성 시점엔 이 컴퓨터에 Docker가 없어 코드 완성·시그니처 대조 검증까지만 하고 실제 E2E는 DONE_WITH_CONCERNS로 보고 후 보류. 이후 Docker Desktop 설치·실행 후 실제 E2E를 시도하면서 main.py 자체에도 버그(sys.path 미설정)가 발견되어 수정([[검증-E2E트러블슈팅]] 참고).
