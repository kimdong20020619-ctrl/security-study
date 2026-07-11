---
tags: [web-vuln-scanner, implementation, done]
---

# 구현: Task1 — 스캐폴딩, Docker 확인, docker-compose.yml

허브: [[허브]]

venv, requirements.txt(python-owasp-zap-v2.4, ollama, pytest), .gitignore, pytest.ini, docker-compose.yml(juice-shop+zap 컨테이너) 작성. 컨트롤러가 직접 diff 대조로 검증(간단한 스캐폴딩이라 리뷰어 서브에이전트 없이 진행).

**이 시점엔 이 컴퓨터에 Docker Desktop이 설치되어 있지 않았음** — 이후 사용자 지시로 컨트롤러가 직접 Docker Desktop을 다운로드·설치·실행까지 진행([[검증-E2E트러블슈팅]] 참고).
