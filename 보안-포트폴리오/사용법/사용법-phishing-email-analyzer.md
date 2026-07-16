---
tags:
  - 보안포트폴리오
  - 사용법
---
# 📧 phishing-email-analyzer 사용법

피싱 이메일(`.eml`)을 오프라인으로 트리아지하는 도구.

## 준비물
- Python 3.10+ (설치 불필요, 표준 라이브러리만)
- 레포: `C:\Users\kimdo\Documents\GitHub\phishing-email-analyzer`

## 🔰 처음이라면 (공통 준비, 1분)
1. 시작 메뉴 → **PowerShell** 실행 → `python --version` 입력.
   - `Python 3.10` 이상 나오면 OK. 안 나오면 `python.org`에서 3.12 설치(설치 첫 화면 **"Add python.exe to PATH" 체크**) → PowerShell 새로 열고 다시 확인.
2. 아래 "실행 방법" 명령을 **위→아래 순서대로 복붙**. (`$env:PYTHONPATH="src"`는 창 새로 열 때마다 한 번)

## 실행 방법 (PowerShell)
```powershell
cd C:\Users\kimdo\Documents\GitHub\phishing-email-analyzer
$env:PYTHONPATH="src"
python -m phishing_analyzer samples\sample_phish.eml          # 텍스트 리포트
python -m phishing_analyzer samples\sample_phish.eml --json   # JSON
```
> 또는 `pip install -e .` 하면 `phishing-analyzer <파일>` 로 바로 실행.

## 옵션
- `--json` : 결과를 JSON으로 (다른 도구·SIEM 연동용)
- `--vt`   : VirusTotal 평판 조회 (환경변수 `VT_API_KEY` 필요)
  ```powershell
  $env:VT_API_KEY="<가입 후 발급받은 키>"
  python -m phishing_analyzer samples\sample_phish.eml --vt
  ```

## 내 스팸메일로 돌리는 법 (실데이터)
1. Gmail에서 스팸 메일 열기 → 우측 **⋮ 더보기 → 메시지 원본 보기**
2. **원본 다운로드** 클릭 → `.eml` 파일 저장
3. 그 파일 경로로 실행:
   ```powershell
   python -m phishing_analyzer C:\Users\kimdo\Downloads\스팸.eml
   ```
4. 결과(판정·핵심 근거)를 스크린샷으로 README에 첨부하면 포트폴리오 강화 (개인정보는 가림)

## 결과 해석
- **판정**: `MALICIOUS(60↑) / SUSPICIOUS(30↑) / CLEAN`
- **핵심 근거**: From/Reply-To 불일치, 표시이름 스푸핑, SPF/DKIM/DMARC 실패, 링크 텍스트/실주소 불일치, 위험 첨부(.exe·이중확장자) 등
- **종료 코드**: 악성/의심 `1`, 정상 `0` (배치·파이프라인에서 분기용)

## 관련
- [[포트폴리오-로드맵]] · [[보안-개인프로젝트-기획]]
