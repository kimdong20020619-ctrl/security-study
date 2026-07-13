---
tags:
  - 보안포트폴리오
  - 사용법
---
# 📜 log-analyzer 사용법

SSH(auth.log)·웹(access.log) 로그에서 공격 징후를 탐지하는 관제 도구.

## 준비물
- Python 3.10+ (표준 라이브러리만)
- 레포: `C:\Users\kimdo\Documents\GitHub\log-analyzer`

## 실행 방법 (PowerShell)
```powershell
cd C:\Users\kimdo\Documents\GitHub\log-analyzer
$env:PYTHONPATH="src"
python -m log_analyzer --auth samples\sample_auth.log --access samples\sample_access.log
python -m log_analyzer --auth samples\sample_auth.log --json
```

## 옵션
- `--auth <파일>`   : SSH 인증 로그
- `--access <파일>` : 웹 접근 로그(Common Log Format)
- `--json`          : JSON 출력
- 둘 중 하나만 줘도 됨

## 실데이터로 활용
- **본인 소유** 리눅스 서버/클라우드 VM의 `/var/log/auth.log`, nginx `access.log`
- 또는 공개 로그셋(SecRepo 등)
  ```powershell
  python -m log_analyzer --auth C:\logs\auth.log
  ```
- 실제 브루트포스 탐지 결과를 캡처

## 결과 해석
- 심각도순 정렬: 🔴CRITICAL(실패 다발 후 성공=침해 의심) → 🟠HIGH(브루트포스·주입) → 🟡MEDIUM(스캐닝·민감경로)
- 각 항목: 출처 IP, 횟수, 근거
- 임계값 조정: `src/log_analyzer/detectors.py`의 `BRUTEFORCE_FAILS`, `WEB_SCAN_404`

## 관련
- [[포트폴리오-로드맵]] · [[보안-개인프로젝트-기획]]
