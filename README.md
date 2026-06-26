# Security Study

보안 공부 기록 레포지토리 | 기준일: 2026-06-26

---

## 진로 목표

```
[신입] 보안 솔루션 기술지원 SE
   → 안랩 / SK쉴더스 / 이글루시큐리티 / S2W 등
   → 경력 3~5년

        ↓

[경력직] 기업 보안 담당 또는 인천공항공사 전산직
   → 정보보안기사 + 실무 경험 기반 지원

        ↓

[장기] CERT / 악성코드 분석가 전문직 전환
```

---

## 학습 로드맵

### Phase 0 — 환경 세팅 (완료 ✅)

| 항목 | 상태 |
|------|------|
| GitHub 레포 세팅 | ✅ |
| WSL2 설치 | ✅ |
| TryHackMe 계정 + 연간 구독 | ✅ |
| 토익 준비 시작 | 7월 예정 |

### Phase 1 — 개념 기초 (2026년)

**TryHackMe Pre-Security Path**

| 순서 | 모듈 | 상태 |
|------|------|------|
| 1 | 사이버 보안 소개 (Cyber Security Intro) | ✅ 완료 |
| 2 | 컴퓨터 기초 (Computer Basics) | ✅ 완료 |
| 3 | 운영 체제 기초 (Operating Systems Basics) | 🔄 진행 중 (섹션 1~2 완료) |
| 4 | 네트워크 기초 (Network Fundamentals) | ⬜ 예정 |
| 5 | 웹 동작 원리 (How The Web Works) | ⬜ 예정 |
| 6 | Linux 기초 (Linux Fundamentals) | ⬜ 예정 |
| 7 | Windows 기초 (Windows Fundamentals) | ⬜ 예정 |

**TryHackMe 이후 학습 순서**

```
Pre-Security Path 완료
    ↓
Jr Penetration Tester Path (기초 해킹 실습)
    ↓
SOC Level 1 Path (방어·탐지 기초)
    ↓
Malware Analysis 모듈 (악성코드 분석)
```

### Phase 2 — 네트워크·시스템 심화 (2026년 하반기)

- TCP/IP, OSI 7계층 심화
- Wireshark 패킷 분석 실습
- Linux 명령어·셸 스크립트
- 방화벽·IDS/IPS 개념

### Phase 3 — 자격증 취득 (2027년)

| 자격증 | 시험 일정 | 목적 |
|--------|----------|------|
| **정보처리기사** | 2027년 3~5월 취득 목표 | 채용 기본 조건 충족 |
| **정보보안기사** | 2027년 8월 필기 / 11월 실기 | 보안 전문성 증명 |
| **토익** | 2026년 7월~ 준비 시작 | 대기업 지원 조건 |

### Phase 4 — 실무 포트폴리오 (2027년)

- 악성코드 분석 보고서 3건 이상
- CTF 참가 기록 (Hack The Box / picoCTF)
- 취약점 진단 실습 보고서
- 보안 도구 개발 (Python 자동화)

### Phase 5 — 취업 지원 (2028년)

- 보안 솔루션 기술지원 SE 집중 지원
- 경력 3년+ 후 기업 보안 담당 또는 인천공항공사 전산직 지원

---

## 현재 진행 현황

- **학습 플랫폼**: TryHackMe (연간 구독, 학생 할인 적용)
- **진행 모듈**: Pre-Security Path — 모듈 3 운영 체제 기초 진행 중
- **학습 일정**: 평일 + 일요일 (토요일 제외)
- **다음 세션**: 모듈 3 나머지 섹션 (일요일 재개)

---

## 개발 환경

| 항목 | 내용 |
|------|------|
| OS | Windows 11 |
| 가상환경 | WSL2 (Ubuntu) |
| 버전 관리 | GitHub Desktop + Git |
| 학습 플랫폼 | TryHackMe |
| 에디터 | VS Code |

---

## 폴더 구조

```
security-study/
├── tryhackme/
│   └── pre-security/          # TryHackMe Pre-Security Path 노트
│       ├── 01_cyber-security-intro.md
│       ├── 02_computer-basics.md
│       └── 03_operating-systems-basics.md
├── network/                   # 네트워크 개념 정리
├── malware-analysis/          # 악성코드 분석 실습
└── certifications/            # 자격증 공부 정리
```

---

## 참고 자료

- [TryHackMe](https://tryhackme.com) — 실습 기반 보안 학습 플랫폼
- [Hack The Box](https://hackthebox.com) — CTF·실습 환경
- [정보보안기사 한국인터넷진흥원(KISA)](https://www.kisa.or.kr) — 자격증 정보
