---
tags:
  - 보안포트폴리오
  - 사용법
---
# 🧩 ioc-extractor 사용법

위협 리포트·로그 텍스트에서 침해지표(IOC)를 추출하는 위협 인텔 도구.

## 준비물
- Python 3.10+ (표준 라이브러리만)
- 레포: `C:\Users\kimdo\Documents\GitHub\ioc-extractor`

## 🔰 처음이라면 (공통 준비, 1분)
1. 시작 메뉴 → **PowerShell** 실행 → `python --version` 입력.
   - `Python 3.10` 이상 나오면 OK. 안 나오면 `python.org`에서 3.12 설치(설치 첫 화면 **"Add python.exe to PATH" 체크**) → PowerShell 새로 열고 다시 확인.
2. 아래 "실행 방법" 명령을 **위→아래 순서대로 복붙**. (`$env:PYTHONPATH="src"`는 창 새로 열 때마다 한 번)

## 실행 방법 (PowerShell)
```powershell
cd C:\Users\kimdo\Documents\GitHub\ioc-extractor
$env:PYTHONPATH="src"
python -m ioc_extractor samples\sample_report.txt          # 텍스트 리포트
python -m ioc_extractor samples\sample_report.txt --json   # JSON
python -m ioc_extractor samples\sample_report.txt --defang # 공유 안전 표기(hxxp/[.])
type report.txt | python -m ioc_extractor -                # 표준입력
```

## 추출 항목
IPv4 · 도메인 · URL · 해시(MD5/SHA1/SHA256) · 이메일 · CVE
- `hxxp`, `[.]` 같은 defang 표기를 자동 복원(refang) 후 추출
- `.exe`/`.dll` 같은 파일명은 도메인에서 제외(오탐 필터, 실데이터 검증으로 추가)

## 실데이터로 활용 (검증 완료)
1. **CISA 권고문**(cisa.gov) 페이지를 텍스트로 저장
2. 실행 → 실제 랜섬웨어 해시·IOC 추출
   ```powershell
   python -m ioc_extractor C:\Downloads\cisa_advisory.txt
   ```
- 예: CISA AA25-203A(Interlock)에서 SHA256 35개 추출 → README "실제 데이터 검증" 참고

## 결과 해석
- 종류별 개수 + 목록. `--defang`은 클릭 사고 방지용 표기.
- 정상 도메인도 섞여 나올 수 있음(화이트리스트 없음) → 분석가가 선별

## 관련
- [[포트폴리오-로드맵]] · [[보안-개인프로젝트-기획]]
