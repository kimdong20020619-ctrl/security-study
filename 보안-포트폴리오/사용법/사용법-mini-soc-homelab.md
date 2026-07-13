---
tags:
  - 보안포트폴리오
  - 사용법
---
# 🏠 mini-soc-homelab 사용법 (실습 진행)

Wazuh SIEM 홈랩 + Atomic Red Team 탐지 실습. **코드 실행이 아니라 홈랩을 구축**하는 프로젝트.

## 준비물
- ⚠️ **호스트 RAM 16GB+** 또는 **클라우드 인스턴스(4vCPU/8GB)** (Wazuh 인덱서가 무거움)
- VirtualBox
- 레포: `C:\Users\kimdo\Documents\GitHub\mini-soc-homelab` (문서·탐지 템플릿·스타터 룰)

## 진행 순서
1. **VM·Wazuh 설치**: `homelab-soc-setup` 레포의 자동화 스크립트 사용 → [[사용법-homelab-soc-setup]]
2. **설정 확인**: `docs/01-setup.md`의 네트워크·에이전트·Sysmon 절차
3. **탐지 룰 반영**: `rules/local_rules.xml`을 Wazuh 서버 `/var/ossec/etc/rules/`에 → `systemctl restart wazuh-manager`
4. **공격 재현**: Windows 타깃에서 Atomic Red Team 5개 기법 실행
   ```powershell
   Invoke-AtomicTest T1059.001    # 각 기법, 실행 후 -Cleanup
   ```
5. **결과 채우기**: `detections/` 템플릿에 실제 탐지 로그·스크린샷 기입, `mitre-mapping.md`의 ⬜ → ✅

## 결과물 (포트폴리오)
- Wazuh 대시보드 탐지 스크린샷
- 기법별 탐지 리포트(`detections/T1059.001-powershell.md` 등)
- MITRE ATT&CK 매핑 표

## 핵심
문서 레포를 **실제 SIEM 운영·탐지 프로젝트**로 격상시키는 게 목표. 관제 대표작.

## 관련
- [[사용법-homelab-soc-setup]] · [[포트폴리오-로드맵]] · [[보안-개인프로젝트-기획]]
