---
tags:
  - 보안포트폴리오
  - 사용법
---
# 🔒 pii-scanner 사용법

문서·CSV에서 개인정보(PII)를 탐지·마스킹하는 도구 (CPPG 관점).

## 준비물
- Python 3.10+ (표준 라이브러리만)
- 레포: `C:\Users\kimdo\Documents\GitHub\pii-scanner`

## 🔰 처음이라면 (공통 준비, 1분)
1. 시작 메뉴 → **PowerShell** 실행 → `python --version` 입력.
   - `Python 3.10` 이상 나오면 OK. 안 나오면 `python.org`에서 3.12 설치(설치 첫 화면 **"Add python.exe to PATH" 체크**) → PowerShell 새로 열고 다시 확인.
2. 아래 "실행 방법" 명령을 **위→아래 순서대로 복붙**. (`$env:PYTHONPATH="src"`는 창 새로 열 때마다 한 번)

## 실행 방법 (PowerShell)
```powershell
cd C:\Users\kimdo\Documents\GitHub\pii-scanner
$env:PYTHONPATH="src"
python -m pii_scanner scan samples\sample.txt              # 탐지 리포트
python -m pii_scanner mask samples\sample.txt -o out.txt   # 텍스트 마스킹본 저장
python -m pii_scanner csv  samples\sample.csv -o out.csv   # CSV 셀 단위 마스킹
```

## 명령·옵션
- `scan <파일>` : PII 탐지 목록 출력 (원본 → 마스킹 미리보기)
- `mask <파일> [-o 출력]` : 텍스트 마스킹 (미지정 시 화면 출력)
- `csv <파일> [-o 출력]` : CSV 각 셀 마스킹 (미지정 시 `<원본>.masked.csv`)

## 탐지 대상 & 검증
- 주민등록번호(체크섬 검증), 신용카드(Luhn 검증), 휴대전화, 이메일
- 검증 덕분에 형식만 맞고 실제로는 아닌 번호는 걸러짐(오탐↓)

## 실데이터로 활용
- 가상 기업의 고객 CSV(합성)를 만들어 `csv` 명령으로 마스킹 → before/after 캡처
- `docs/` 의 관리체계 문서 3종(처리방침·접근통제·점검 체크리스트)을 가상 기업 시나리오로 채우면 컨설팅 포트폴리오로 강화

## 결과 해석
- `scan`: `[종류] 원본 → 마스킹` 목록
- `csv`/`mask`: 종류별 검출 수 요약 (예: RRN=2, PHONE=2, EMAIL=2)
- ⚠️ 실제 개인정보 결과물은 저장·공유 금지

## 관련
- [[포트폴리오-로드맵]] · [[보안-개인프로젝트-기획]]
