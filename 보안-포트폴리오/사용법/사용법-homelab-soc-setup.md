---
tags:
  - 보안포트폴리오
  - 사용법
---
# ⚙️ homelab-soc-setup 사용법 (자동화 설치)

VirtualBox VM 생성 + Wazuh 자동 설치 스크립트. `mini-soc-homelab` 실습의 **인프라 구축 단계**.

> 🔰 **완전 초보자는 이 노트 하나로 따라 하세요**: [[진행가이드-홈랩SOC-초보자용]] (클라우드 회원가입 → 탐지 스크린샷까지 클릭·명령 단위).

## 준비물
- ⚠️ **호스트 RAM 16GB+** 또는 클라우드 (8GB에서는 Wazuh 구동 불가 — `SETUP-LOG.md` 참고)
- VirtualBox (설치돼 있음)
- 레포: `C:\Users\kimdo\Documents\GitHub\homelab-soc-setup`

## 실행 방법
```powershell
cd C:\Users\kimdo\Documents\GitHub\homelab-soc-setup
# 1) VM 생성 + Ubuntu 무인설치 (호스트에서)
powershell -ExecutionPolicy Bypass -File scripts\01-create-vm.ps1
# 2) 설치 완료 후, 게스트에서 Wazuh 설치
bash scripts\03-install-wazuh.sh
```

## 스크립트 구성
- `scripts/01-create-vm.ps1` : VBoxManage로 VM 생성 + ISO 다운로드 + 무인설치
- `scripts/03-install-wazuh.sh` : 게스트에서 Wazuh 올인원 설치(`wazuh-install.sh -a`)
- `scripts/user-data.yaml` : Ubuntu autoinstall 참고 설정
- `SETUP-LOG.md` : 실제 실행 로그·RAM 제약 판단 근거

## 클라우드로 하는 경우 (권장, 8GB PC일 때)
- Ubuntu 인스턴스(4vCPU/8GB) 생성 → `scripts/03-install-wazuh.sh`만 실행
- 대시보드 접속: `https://<인스턴스 IP>` (admin / 설치 시 출력된 비밀번호)

## 완료 후
→ [[사용법-mini-soc-homelab]]로 탐지 시나리오 진행

## 관련
- [[사용법-mini-soc-homelab]] · [[포트폴리오-로드맵]] · [[보안-개인프로젝트-기획]]
