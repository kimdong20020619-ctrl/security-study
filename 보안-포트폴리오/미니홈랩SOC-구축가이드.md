---
tags:
  - 보안포트폴리오
  - 홈랩
---
# 🧪 미니 홈랩 SOC 구축 가이드 (→ GitHub 레포로 이전)

> 이 가이드의 **정식·최신 버전은 GitHub 레포**에 있습니다. 이 노트는 포인터(요약)만 남깁니다.

#프로젝트 #홈랩 #wazuh #블루팀

## 어디에 있나
- **탐지 실습·문서**: `mini-soc-homelab` 레포 — `docs/01-setup.md`, `detections/`(기법별 템플릿), `rules/local_rules.xml`(스타터 룰), `mitre-mapping.md`
- **설치 자동화**: `homelab-soc-setup` 레포 — `scripts/01-create-vm.ps1`(VM 생성+무인설치), `scripts/03-install-wazuh.sh`, `SETUP-LOG.md`

## 한 줄 요지
격리 홈랩에 **Wazuh(SIEM)** 구축 → **Atomic Red Team**으로 공격 재현 → **탐지 룰 + MITRE ATT&CK 매핑**.

> ⚠️ 호스트 RAM 8GB로는 Wazuh 올인원 구동 불가(인덱서가 4GB+ 요구). **16GB+ 머신 또는 클라우드**에서 실행.

## 관련
- [[포트폴리오-로드맵]] · [[보안-개인프로젝트-기획]]
