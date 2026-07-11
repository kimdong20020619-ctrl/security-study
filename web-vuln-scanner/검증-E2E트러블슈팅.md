---
tags: [web-vuln-scanner, verification, troubleshooting]
---

# 검증: 실제 E2E 시도와 트러블슈팅 (2026-07-10 밤 ~ 2026-07-11 새벽)

허브: [[허브]]

Task 1~7 코드가 전부 완성된 후, 사용자 지시로 컨트롤러가 **Docker Desktop을 직접 다운로드·설치·실행**(바탕화면에 설치기 다운로드 → 조용히 설치 → 앱 실행 → 엔진 준비 대기)한 뒤 실제 스캔을 여러 차례 시도하며 발견·수정한 문제들을 시간순으로 기록.

## 1. ModuleNotFoundError
`main.py`를 직접 실행하면 `pytest.ini`의 `pythonpath=src`가 적용 안 됨 → 1번 프로젝트 Task9와 똑같은 문제. `sys.path.insert(...)` 추가로 해결.

## 2. ZAP이 호스트 요청을 거부
ZAP 컨테이너 내부에서는 API가 정상 응답하는데, Windows 호스트(main.py가 실행되는 곳)에서 오는 요청은 "not permitted"로 거부됨. 원인: ZAP의 기본 허용 주소 목록이 Docker 브릿지 게이트웨이 IP(172.18.0.1, 즉 호스트에서 온 요청)를 막음. `docker-compose.yml`의 zap 커맨드에 `-config api.addrs.addr.name=.* -config api.addrs.addr.regex=true` 추가로 해결.

## 3. 간헐적 ConnectionResetError (IPv6 우선 해석)
2번 수정 후에도 폴링 중 연결이 간헐적으로 끊김. `curl`로 직접 테스트해보니 `localhost`(IPv6 `::1`로 해석)는 실패하고 `127.0.0.1`(IPv4)은 항상 성공 — Docker Desktop의 Windows 포트포워딩이 IPv6 경로에서 불안정. `scanner.py` 기본 `api_url`을 `127.0.0.1`로 변경.

## 4. 장시간 폴링에서도 여전히 간헐적 리셋
IPv4 고정 후에도 20분 넘는 스캔 동안 폴링(2~5초 간격, 수백 회 호출) 중 여전히 가끔 리셋 발생. ZAP API 호출 전체를 감싸는 재시도 래퍼(`_call_with_retry`, 기본 5회·3초 간격) 추가. 신규 테스트로 "2번 실패 후 3번째 성공" 시나리오 검증.

## 5. Docker 엔진 자체가 완전히 죽음 (근본 원인)
재시도 로직까지 넣었는데도 스캔 도중 Docker Desktop 엔진이 500 에러/응답 없음 상태로 빠지는 일이 **3번 반복**됨(2번은 Docker Desktop 강제 재시작으로 복구, 3번째는 재시도 중단하고 원인 진단으로 전환).

**진단**: 이 컴퓨터 전체 RAM이 **7.8GB**뿐. Docker Desktop(WSL2 VM) + ZAP(Java, active scan 중 메모리 많이 씀) + Juice Shop(Node.js) + Ollama(llama3 로드 시 수GB) + Windows 자체를 동시 실행하면 메모리 부족으로 엔진이 불안정해지는 것으로 추정. 소프트웨어 버그가 아니라 **하드웨어 리소스 제약**일 가능성이 높다고 판단해, WSL2 메모리 설정 같은 시스템 전역 변경은 사용자 승인 없이 하지 않고 진단만 남김.

## 결론
코드 레벨에서 고칠 수 있는 문제(1~4)는 전부 수정하고 테스트까지 추가했음. 5번(Docker 엔진 자체 불안정)은 코드로 해결 불가능한 환경 제약으로 판단, README에 알려진 제약 사항으로 명시. **실제 스캔이 끝까지 완주해 `report.html`이 실제 Juice Shop 결과로 채워지는 것은 아직 확인 못함** — 최종 리뷰어도 이 점을 명확히 지적함([[검증-최종리뷰]] 참고).

## 사용자가 할 일
리소스 여유 있을 때(Ollama·다른 무거운 프로그램 종료 후) `docker compose up -d && python main.py` 1회 실행해서 실제로 끝까지 도는지 확인 필요.
