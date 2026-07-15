---
tags:
  - 보안포트폴리오
  - 사용법
  - 홈랩
---
# 🧭 홈랩 SOC 진행 가이드 (완전 초보자용, 처음부터 끝까지)

> 클라우드·리눅스·SSH가 처음이어도 따라 할 수 있게 **클릭·명령 단위**로 설명.
> 흐름: (1) 클라우드에 Wazuh SOC 세우기 → (2) 공격 흉내 내고 탐지 확인 → (3) 스크린샷=포트폴리오 → (4) AWS 로그 연계(나중).

## 이걸 왜 하나 (1분 이해)
- **SIEM(Wazuh)** = 여러 컴퓨터의 로그를 한곳에 모아 "공격 징후"를 알려주는 보안 관제 시스템.
- 우리가 할 일: 클라우드에 리눅스 서버 1대 빌려 Wazuh를 깔고, **일부러 공격을 흉내** 내서 Wazuh가 잡아내는지 확인 → 그 화면이 **포트폴리오**가 된다.

## 준비물
- 신용/체크카드(클라우드 결제, 하루 1~2천원 수준)
- 이메일 계정
- 이 문서 + 메모장(비밀번호·IP 적어둘 곳)

---

## 1단계 — 클라우드 서버 빌리기 (Vultr 예시)

### 1-1. 회원가입
1. `vultr.com` 접속 → **Sign Up** → 이메일/비번 입력 → 카드 등록.

### 1-2. 서버(인스턴스) 생성
1. 로그인 → 우상단 **Deploy(+)** → **Deploy New Server**
2. 종류: **Cloud Compute – Shared CPU**
3. 위치(Location): **Seoul**
4. 이미지(OS): **Ubuntu 22.04 LTS x64**
5. 크기(Plan): **8 GB RAM** 플랜 (월요금처럼 보여도 시간당 과금 → 하루 쓰고 지우면 저렴)
6. 맨 아래 **Deploy Now**
7. 몇 분 뒤 상태가 **Running** → 서버 클릭하면 **IP Address / Username(root) / Password** 표시 → **메모장에 복사**.

> 💡 용어: **IP 주소**=서버의 인터넷 주소(예 `141.164.x.x`). **포트**=서버의 "문 번호"(22=원격접속 SSH, 443=웹 대시보드). **방화벽**=어떤 문을 누구에게 열지 규칙.

### 1-3. 방화벽 — 내 IP만 열기 (보안 핵심)
1. 내 공인 IP 확인: 브라우저에 **"내 아이피"** 검색 → 나온 숫자 메모(예 `118.x.x.x`).
2. Vultr 좌측 **Network → Firewall → Add Firewall Group**
3. 규칙 2개 추가:
   - Protocol **SSH (22)**, Source = **내 IP** (또는 `내IP/32`)
   - Protocol **HTTPS (443)**, Source = **내 IP**
4. 서버 설정(Settings)에서 이 방화벽 그룹을 **연결**.
> ⚠️ 문을 "전체(`0.0.0.0/0`)"로 열면 전 세계가 접속 시도 → **반드시 내 IP만**.

---

## 2단계 — 서버에 접속하기 (SSH)

### Windows (PowerShell, 추가 설치 불필요)
1. 시작 메뉴 → **PowerShell** 실행
2. 입력(님 서버 IP로 교체):
   ```powershell
   ssh root@141.164.x.x
   ```
3. 처음이면 `Are you sure...` → **yes** 입력 → 비밀번호(1-2에서 메모) **붙여넣기**(화면에 안 보여도 입력되는 중) → Enter
4. `root@서버이름:~#` 가 뜨면 **접속 성공**.

> 오류 해결: `Connection timed out/refused` → 방화벽 22포트·내 IP 확인, IP 오타 확인. `Permission denied` → 비번 틀림(Vultr 콘솔에서 재확인).

---

## 3단계 — Wazuh 설치 (명령 2줄)
접속된 상태(`root@...#`)에서 그대로 붙여넣기:
```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```
- 5~10분 소요. 끝나면 이런 문구가 나옴:
  ```
  User: admin
  Password: XXXXXXXXXX
  ```
  → **이 비밀번호를 메모장에 저장!** (대시보드 로그인에 필요)

---

## 4단계 — 대시보드(관제 화면) 열기
1. 내 PC 브라우저 주소창에 `https://141.164.x.x` (서버 IP, **https**)
2. "연결이 비공개가 아닙니다/안전하지 않음" 경고 → **고급 → 계속 진행** (자체 서명 인증서라 정상)
3. **admin / (3단계 비번)** 로그인 → Wazuh 화면 등장 🎉

---

## 5단계 — 공격 흉내 내고 탐지 확인 (SSH 무차별 대입)
가장 쉬운 실습: 일부러 비번 틀리게 반복 접속 → Wazuh가 "brute force" 탐지.
1. 내 PC PowerShell에서 **틀린 계정으로** 여러 번 시도:
   ```powershell
   ssh baduser@141.164.x.x
   ```
   아무 비번 입력 → 실패 → **5~6번 반복**.
2. 대시보드 좌측 메뉴 **Threat Hunting**(또는 Security events) → 최근 알림에 `sshd ... authentication failed` / `Multiple authentication failures`(brute force) 계열 알림 확인 → **스크린샷 찍기!** 📸

---

## 6단계 — 포트폴리오로 남기기
- 대시보드·탐지 알림 **스크린샷**을 `mini-soc-homelab` 레포의 `screenshots/`에 저장, `detections/` 템플릿(예: `T1110-ssh-bruteforce.md`)의 빈칸(관찰 이벤트·룰ID·배운 점)을 채움.
- `mitre-mapping.md`의 해당 기법 ⬜ → ✅.
- **GitHub Desktop으로 커밋/푸시** → 깃허브에 "직접 구축한 홈랩 SOC" 증빙 완성.

---

## 7단계 — 정리 (과금 중지, 꼭!)
- 스크린샷·문서 다 저장했으면 Vultr → 서버 → **Destroy(삭제)** → 과금 종료.
- 다시 실습하려면 재생성(위 1단계부터).

---

## 8단계 — (다음) AWS 로그 연계
5단계까지 성공했으면, 다음 목표는 **AWS CloudTrail·GuardDuty 로그를 Wazuh로 수집**(클라우드+온프렘 통합 SOC).
- 상세: `mini-soc-homelab/docs/03-aws-integration.md`
- 요지: AWS 콘솔에서 CloudTrail(→S3)·GuardDuty 켜기 → Wazuh `ossec.conf`에 `aws-s3` wodle 추가 → 루트 로그인·S3 공개 등 탐지.
- 초보자는 **온프렘 홈랩(1~7단계)을 먼저 완성**한 뒤 도전 권장.

---

## ⚠️ 자주 나오는 오류 & 해결
| 증상 | 해결 |
|------|------|
| SSH 접속 안 됨 | 방화벽 22포트+내 IP 확인 · IP 오타 · 내 공인 IP 바뀌었는지 재확인 |
| 대시보드 안 열림 | 443포트+내 IP · `https` 확인 · 설치 완료됐는지(3단계 끝까지) |
| 비번 틀림 | Vultr 서버 → **View Console**에서 비번 재확인 |
| 설치 중 멈춤/실패 | 플랜이 **8GB RAM** 맞는지 · `sudo bash ./wazuh-install.sh -a` 재시도 |
| 요금 걱정 | 하루 실습이면 소액 · **끝나면 Destroy** 필수 |

## ✅ 체크리스트
- [ ] 서버 생성 (Ubuntu 22.04, 8GB, Seoul)
- [ ] 방화벽 내 IP만 (22, 443)
- [ ] SSH 접속 성공
- [ ] Wazuh 설치 + admin 비번 저장
- [ ] 대시보드 로그인
- [ ] 브루트포스 탐지 스크린샷
- [ ] detections/·screenshots/ 채우고 커밋
- [ ] 서버 Destroy(정리)

## 관련
- [[사용법-mini-soc-homelab]] · [[사용법-homelab-soc-setup]] · [[포트폴리오-로드맵]]
