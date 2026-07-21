---
tags:
  - 보안포트폴리오
  - 사용법
---
# 🔎 web-vuln-scanner 사용법

OWASP ZAP 스캔 경고를 **로컬 LLM(Ollama)이 오탐 재판정 + 심각도·수정법 요약** → HTML 리포트로 정리하는 **AI 보조 방어 트리아지** 도구. 스캔 대상은 항상 로컬 Docker의 OWASP Juice Shop(연습용 취약 웹앱).

> ⚠️ **로컬 학습 환경 전용.** ZAP은 API 키 없이 열려 있으니 **8080 포트를 외부에 노출 금지**. 외부·타인 사이트 스캔 금지.

## 준비물
- Docker Desktop · Ollama(`ollama pull llama3`) · Python(venv)
- 레포: `C:\Users\kimdo\Documents\GitHub\web-vuln-scanner`

## 실행 방법 (PowerShell)
```powershell
cd C:\Users\kimdo\Documents\GitHub\web-vuln-scanner
python -m venv .venv; .\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
docker compose up -d          # Juice Shop(3000) + ZAP(8080)
python main.py                # 스캔 → LLM 재판정 → report.html (수 분)
python main.py --skip-scan    # 스캔 재실행 없이 분석·리포트만 (scan_results.json 재사용)
docker compose down           # 종료
```
`report.html`을 브라우저로 열어 결과 확인.

## 동작 구조
- **ZapScanner**: ZAP API로 Juice Shop을 spider + active scan
- **Analyzer**: 경고 신뢰도별 처리 — High/Confirmed는 그대로, **Low/Medium/False Positive만 LLM에 재판정** 요청(오탐 걸러내기)
- **HtmlReporter**: 결과를 정적 HTML로 (공격자 제어 텍스트는 `html.escape()` 처리)
- **persistence**: 스캔·분석 결과를 JSON 저장 → 단계별 재실행

## 포트폴리오 포인트 (면접용)
- "스캐너가 뱉는 **저신뢰 경고의 오탐을 LLM으로 2차 판정**해 분석가 피로를 줄이는 **트리아지 자동화**" = 방어(블루팀) + AI 결합.
- 정직한 한계: 규칙/LLM 판정 모두 **1차 보조**, 최종 확인은 사람. RAM 7.8GB에선 Docker+ZAP+Ollama 동시 구동이 불안정(README에 기록).

## 관련
- [[포트폴리오-로드맵]] · [[보안-개인프로젝트-기획]] · [[사용법-prompt-injection-guard]]
