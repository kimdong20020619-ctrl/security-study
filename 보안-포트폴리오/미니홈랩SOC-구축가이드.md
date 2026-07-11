# 🧪 미니 홈랩 SOC 구축 가이드 (재현 가능·따라하기)

> **작성일**: 2026-07-10
> **목적**: 격리된 가상 홈랩에 SIEM(Wazuh)을 세우고, 표준 탐지검증 도구(Atomic Red Team)로 공격 기법을 재현한 뒤 **탐지·MITRE ATT&CK 매핑**까지 하는 방어(Detection Engineering) 실습 → 포트폴리오화.
> **상위**: [[보안-개인프로젝트-기획]] · [[포트폴리오-로드맵]]
> **명령·버전 검증일**: 2026-07-10 (공식 문서 기준, 버전은 아래 링크에서 최신 확인)

#프로젝트 #홈랩 #wazuh #블루팀 #실습

> ⚠️ **윤리·법적 경계 (반드시 읽기)**: 이 실습은 **본인 소유의 격리된 가상 네트워크에서만** 수행한다. Atomic Red Team은 표준 탐지검증용 시뮬레이션이며, 외부·타인 시스템 대상 실행은 불법이다. VM 네트워크는 **Host-Only(외부 인터넷 분리)** 로 구성한다.

---

## 0. 개요

### 성공 기준 (완료 판정)
- [ ] Wazuh 서버(대시보드) 접속 성공
- [ ] Ubuntu·Windows 에이전트 2대 등록 및 Active 표시
- [ ] Sysmon 로그가 Wazuh로 수집됨
- [ ] Atomic 테스트 **5개** 실행 → 각각 Wazuh 알림 발생 확인
- [ ] MITRE ATT&CK 매핑 표 작성
- [ ] GitHub 저장소에 문서·스크린샷·회고 업로드

### 아키텍처
```
[Windows 타깃 VM] --Sysmon--\
   Atomic Red Team 실행       \  (Host-Only Network 192.168.56.0/24)
                               >--- [Wazuh Server VM] (Manager+Indexer+Dashboard)
[Ubuntu 타깃 VM] --auditd-----/         │
   (SSH 브루트포스 대상)               탐지 룰 → 알림 → MITRE 매핑
```

---

## 1. 사전 준비

### 하드웨어 최소 요구
- 호스트 RAM 16GB 권장(최소 12GB), 디스크 여유 80GB+
- CPU 가상화(VT-x/AMD-V) 활성화(BIOS)

### 소프트웨어 다운로드 목록
| 항목 | 용도 | 비고 |
|------|------|------|
| VirtualBox | 가상화 | 무료 |
| Ubuntu Server 22.04 LTS ISO | Wazuh 서버 + Linux 타깃 | |
| Windows 10 평가판 ISO | Windows 타깃 | MS Evaluation Center |
| Sysmon (Sysinternals) | Windows 상세 로그 | |
| Sysmon 설정 XML | 탐지 최적화 | SwiftOnSecurity/olafhartong config |

---

## 2. 네트워크 설계 (VirtualBox)

1. VirtualBox → 파일 → 도구 → **네트워크 관리자** → **Host-Only 네트워크 생성** (예: `vboxnet0`, 192.168.56.1/24).
2. 각 VM의 어댑터1 = **호스트 전용(Host-Only)** 로 설정 → 외부 인터넷과 분리.
3. (설치 패키지 다운로드가 필요한 동안만) 어댑터2 = NAT 임시 활성 → 설치 후 비활성 권장.

**IP 계획(예시)**
| VM | IP |
|----|-----|
| Wazuh Server | 192.168.56.10 |
| Ubuntu 타깃 | 192.168.56.20 |
| Windows 타깃 | 192.168.56.30 |

---

## 3. VM 스펙

| VM | vCPU | RAM | 디스크 |
|----|:---:|:---:|:---:|
| Wazuh Server (Ubuntu 22.04) | 2 | 4~6GB | 50GB |
| Ubuntu 타깃 | 1 | 2GB | 20GB |
| Windows 10 타깃 | 2 | 4GB | 40GB |

---

## 4. Step 1 — Wazuh 서버 설치 (All-in-One)

Ubuntu 22.04 서버(192.168.56.10)에서 루트 권한으로:

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

- `-a` = all-in-one(서버+인덱서+대시보드 한 호스트). 최대 ~100 에이전트/90일 로그에 충분.
- 완료 시 출력되는 접속 정보 기록:
  ```
  INFO: You can access the web interface https://192.168.56.10
        User: admin
        Password: <설치 시 출력된 비밀번호>
  ```
- 호스트 브라우저에서 `https://192.168.56.10` 접속 → 로그인 확인.

> 최신 버전·상세: [Wazuh Quickstart 공식문서](https://documentation.wazuh.com/current/quickstart.html)

**포트 참고**: 에이전트 통신 TCP **1514**, 최초 등록 TCP **1515**, 대시보드 **443**.

---

## 5. Step 2 — Ubuntu 타깃에 에이전트 설치

방법 A) 대시보드 → **Agents management → Deploy new agent** 에서 OS 선택 후 안내 명령 복사 실행(권장, 가장 정확).

방법 B) 수동(요지):
```bash
# Wazuh APT 저장소 등록 후
sudo WAZUH_MANAGER="192.168.56.10" apt-get install wazuh-agent
sudo systemctl daemon-reload
sudo systemctl enable --now wazuh-agent
```
- 대시보드 Agents에 Ubuntu 타깃이 **Active** 로 뜨는지 확인.
- auditd 설치로 명령 실행 로깅 강화:
```bash
sudo apt-get install -y auditd
```

---

## 6. Step 3 — Windows 타깃에 에이전트 설치

관리자 PowerShell에서(대시보드 Deploy new agent의 명령을 쓰는 것이 가장 정확):
```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.x.msi -OutFile $env:tmp\wazuh-agent.msi
msiexec.exe /i $env:tmp\wazuh-agent.msi /q `
  WAZUH_MANAGER="192.168.56.10" WAZUH_REGISTRATION_SERVER="192.168.56.10"
NET START WazuhSvc
```
- 대시보드에서 Windows 타깃 **Active** 확인.
> 버전 문자열(4.x)은 [Wazuh Agent 설치문서](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html)에서 최신으로 대체.

---

## 7. Step 4 — Sysmon 설치 + Wazuh 연동

1. Windows 타깃에서 Sysmon 설치(관리자 PowerShell):
```powershell
# Sysmon과 설정 XML을 받은 폴더에서
.\Sysmon64.exe -accepteula -i sysmonconfig.xml
```
2. `C:\Program Files (x86)\ossec-agent\ossec.conf` 에 Sysmon 채널 추가:
```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```
3. 에이전트 재시작:
```powershell
Restart-Service WazuhSvc
```
> 참고: [Wazuh 로그수집 설정](https://documentation.wazuh.com/current/user-manual/capabilities/log-data-collection/configuration.html)

---

## 8. Step 5 — Atomic Red Team 설치 (Windows 타깃)

관리자 PowerShell:
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)
Install-AtomicRedTeam -getAtomics -Force
Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force
$PSDefaultParameterValues = @{"Invoke-AtomicTest:PathToAtomicsFolder"="C:\AtomicRedTeam\atomics"}
```
- 각 테스트는 실행 후 **반드시 Cleanup** 으로 흔적 정리.
> 공식: [Atomic Red Team Getting Started](https://www.atomicredteam.io/docs/invoke-atomicredteam/getting-started)

---

## 9. Step 6 — 탐지 시나리오 5개 (실행 → 탐지 → 매핑)

각 시나리오: **① Atomic 실행 → ② Wazuh 대시보드(Threat Hunting/Security events)에서 알림 확인 → ③ 스크린샷 → ④ 완화책 기록**.

### 시나리오 1 — SSH 브루트포스 (Linux, T1110)
- 실행: 호스트나 Windows에서 Ubuntu 타깃(192.168.56.20) SSH에 hydra/실패 로그인 반복 (또는 Ubuntu에서 Atomic `T1110.001`).
- 탐지: Wazuh 기본 룰(예: 5710 등 authentication_failed / 반복 실패) 알림.
- 완화: fail2ban, 키 기반 인증, 로그인 시도 제한.

### 시나리오 2 — PowerShell 실행 (Windows, T1059.001)
```powershell
Invoke-AtomicTest T1059.001
# 정리
Invoke-AtomicTest T1059.001 -Cleanup
```
- 탐지: Sysmon EventID 1(프로세스 생성) → Wazuh 알림.
- 완화: PowerShell 로깅·제약 언어 모드, AMSI.

### 시나리오 3 — 스케줄 작업 지속성 (Windows, T1053.005)
```powershell
Invoke-AtomicTest T1053.005
Invoke-AtomicTest T1053.005 -Cleanup
```
- 탐지: schtasks 생성 이벤트 → Wazuh 룰.
- 완화: 스케줄 작업 변경 감사, 최소권한.

### 시나리오 4 — 파일 삭제/흔적 제거 (T1070.004)
```powershell
Invoke-AtomicTest T1070.004
Invoke-AtomicTest T1070.004 -Cleanup
```
- 탐지: 삭제/로그 정리 이벤트 → Wazuh 룰.
- 완화: 로그 원격 전송(중앙집중), 무결성 모니터링(FIM).

### 시나리오 5 — 도구 다운로드/반입 (T1105)
```powershell
Invoke-AtomicTest T1105
Invoke-AtomicTest T1105 -Cleanup
```
- 탐지: 의심 다운로드(certutil/curl) 프로세스 → Wazuh 룰.
- 완화: 이그레스 필터링, 애플리케이션 허용목록.

> ⚠️ 모든 테스트는 **격리 홈랩에서만**. 실행 전 스냅샷 저장 권장.

---

## 10. Step 7 — MITRE ATT&CK 매핑 표 (산출물)

| 시나리오 | Tactic | Technique ID | Wazuh 탐지 | 완화 |
|---------|--------|:---:|:---:|------|
| SSH 브루트포스 | Credential Access | T1110 | ✅ | fail2ban·키인증 |
| PowerShell 실행 | Execution | T1059.001 | ✅ | PS 로깅·AMSI |
| 스케줄 작업 | Persistence | T1053.005 | ✅ | 작업 감사 |
| 파일 삭제 | Defense Evasion | T1070.004 | ✅ | 원격 로그·FIM |
| 도구 반입 | Command and Control | T1105 | ✅ | 이그레스 필터 |

---

## 11. Step 8 — GitHub 문서화

### 저장소 구조
```
mini-soc-homelab/
├── README.md
├── docs/
│   ├── 01-network-setup.md
│   ├── 02-wazuh-server.md
│   ├── 03-agents.md
│   ├── 04-sysmon.md
│   └── detections/
│       ├── T1110-ssh-bruteforce.md
│       ├── T1059.001-powershell.md
│       ├── T1053.005-scheduled-task.md
│       ├── T1070.004-file-deletion.md
│       └── T1105-ingress-tool.md
├── screenshots/
└── mitre-mapping.md
```

### README 템플릿 (뼈대)
```markdown
# Mini Home Lab SOC — Detection Engineering with Wazuh

## 개요
격리 홈랩에 Wazuh SIEM을 구축하고 Atomic Red Team으로 5개 ATT&CK 기법을
재현·탐지·매핑한 방어 실습 프로젝트.

## 아키텍처
(다이어그램 이미지)

## 기술 스택
VirtualBox · Ubuntu 22.04 · Windows 10 · Wazuh 4.x · Sysmon · Atomic Red Team

## 탐지 시나리오 (요약 표 + 각 detections/ 링크)

## 트러블슈팅
- (막힌 점 → 원인 → 해결)

## 배운 점
- (SIEM·로그·MITRE 관점 3가지)

## 윤리
격리 환경 전용. 외부 시스템 대상 금지.
```

> README 필수: 제목·주제 / 기간 / 기술스택 / 아키텍처 / 탐지 시나리오 / **트러블슈팅** / **배운 점** / MITRE 매핑. (커밋·푸시는 본인이 직접)

---

## 12. 트러블슈팅 체크

| 증상 | 확인 |
|------|------|
| 에이전트 Active 안 됨 | 방화벽/포트 1514·1515, Manager IP 오타, 시간 동기화 |
| Sysmon 로그 안 옴 | ossec.conf localfile 채널명, 에이전트 재시작 |
| Atomic 실행 오류 | ExecutionPolicy Bypass, 관리자 권한, PathToAtomicsFolder |
| 대시보드 접속 불가 | https/443, 인증서 경고 예외, 서버 서비스 상태 |

---

## 13. 참고 링크 (최신 버전 확인)

- [Wazuh Quickstart](https://documentation.wazuh.com/current/quickstart.html)
- [Wazuh Agent 설치](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html)
- [Wazuh 로그수집 설정](https://documentation.wazuh.com/current/user-manual/capabilities/log-data-collection/configuration.html)
- [Atomic Red Team](https://www.atomicredteam.io/docs/invoke-atomicredteam/getting-started)
- [MITRE ATT&CK](https://attack.mitre.org/)

---

## 진행 체크리스트

- [ ] Step 1 Wazuh 서버
- [ ] Step 2 Ubuntu 에이전트
- [ ] Step 3 Windows 에이전트
- [ ] Step 4 Sysmon 연동
- [ ] Step 5 Atomic 설치
- [ ] Step 6 시나리오 5개 탐지
- [ ] Step 7 MITRE 매핑 표
- [ ] Step 8 GitHub 문서화
