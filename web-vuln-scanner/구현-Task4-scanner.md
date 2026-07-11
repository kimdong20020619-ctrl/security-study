---
tags: [web-vuln-scanner, implementation, done, security-fix]
---

# 구현: Task4 — scanner.py (ZAP API)

허브: [[허브]] · 관련: [[검증-E2E트러블슈팅]]

## 최초 구현
`ZapScanner`가 ZAP API로 spider+active scan을 실행. `zap_client` 의존성 주입 패턴(기본값은 실제 `ZAPv2`)으로 실제 ZAP 서버 없이도 가짜 클라이언트로 테스트 가능하게 설계. 서브에이전트(Sonnet) + 리뷰어 서브에이전트, Approved.

## 이후 실제 E2E 실행 중 발견되어 추가된 수정 (설계 당시엔 몰랐던 실환경 문제)
- 기본 `api_url`을 `localhost`→`127.0.0.1`로 변경(IPv6 우선 해석 문제)
- 모든 ZAP API 호출에 재시도(기본 5회, 3초 간격) 래퍼 추가 — Docker Desktop의 장시간 폴링 중 간헐적 연결 리셋 대응
- 최종 리뷰에서 재시도 시 로깅 누락 지적받아 추가(조용히 느려지는 것 방지)

상세: [[검증-E2E트러블슈팅]]
