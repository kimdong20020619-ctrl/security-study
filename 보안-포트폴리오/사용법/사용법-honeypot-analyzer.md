---
tags:
  - 보안포트폴리오
  - 사용법
---
# 🍯 honeypot-analyzer 사용법

Cowrie SSH/Telnet 허니팟 JSON 로그로 공격자 행동을 요약하는 도구.

## 준비물
- Python 3.10+ (표준 라이브러리만)
- 레포: `C:\Users\kimdo\Documents\GitHub\honeypot-analyzer`
- Cowrie JSON 로그 (`cowrie.json`)

## 실행 방법 (PowerShell)
```powershell
cd C:\Users\kimdo\Documents\GitHub\honeypot-analyzer
$env:PYTHONPATH="src"
python -m honeypot_analyzer samples\cowrie.json
python -m honeypot_analyzer samples\cowrie.json --json --top 20
```

## 옵션
- `--json` : JSON 출력
- `--top N` : 상위 N개(기본 10)

## 실데이터 얻는 법 (허니팟 운영)
1. **저가 클라우드 VM**(예: Ubuntu)에 Cowrie 설치·기동
2. 며칠 두면 실제 공격자 로그가 `var/log/cowrie/cowrie.json`에 쌓임
3. 그 파일을 내려받아 분석
   ```powershell
   python -m honeypot_analyzer C:\Downloads\cowrie.json
   ```
- **실제 공격 데이터**라 위협 인텔 포트폴리오로 임팩트 큼
- 시간 없으면 공개 Cowrie 데이터셋으로 대체

## 결과 해석
- 상위 공격 IP / 상위 시도 자격증명(user/pass) / 로그인 성공 세션 / 실행 명령 / 다운로드 URL
- 다운로드 URL은 `ioc-extractor`로 넘겨 IOC화하면 연계 데모

## 관련
- [[포트폴리오-로드맵]] · [[보안-개인프로젝트-기획]]
