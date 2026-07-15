---
tags:
  - 보안포트폴리오
  - 학습
  - 클라우드
---
# ☁️ AWS 방어 입문 계획 (비관제 SE·사내보안 취업 문 확대용)

> 목표: **얕게라도 "클라우드도 되는 보안 신입"** 되기. 방어(운영·설정 보안) 관점 중심. 개발자용 심화 X.
> 배치: 2027 Q3 여력 시(분기별 표대로). 취업 전 기초까지.

## 왜 하나 (냉정)
- 사내보안·보안 SE 채용의 상당수가 **클라우드(특히 AWS)를 건드림**. 국내 수요는 AWS > Azure.
- "클라우드도 되는 신입"은 희소가치 → 지원 범위가 넓어짐.

## 학습 순서 (방어 관점)
1. **클라우드 기본** — AWS Cloud Practitioner Essentials (무료 디지털 과정)
2. **IAM(최소권한)** — 사용자·역할·정책, MFA, 루트계정 보호 → 보안의 핵심
3. **네트워크 보안** — Security Group / NACL / VPC 기초
4. **로깅·모니터링** — **CloudTrail**(감사로그), **GuardDuty**(위협탐지), Config
5. **스토리지 보안** — S3 버킷 공개 차단, 암호화(KMS)

## 무료 학습 리소스
- **AWS Skill Builder** — 무료 디지털 과정(Cloud Practitioner Essentials 등)
- **AWS Free Tier** 계정 — 직접 실습(GuardDuty·CloudTrail 켜보기)
- **TryHackMe "AWS 방어" 모듈** — 구독 내(Security Engineer 경로)

## 자격증 (선택)
- 입문: **AWS Certified Cloud Practitioner** (기초, 취업 어필용 가성비)
- 심화(나중): AWS Security Specialty (취업 후 고려)

## 🔗 홈랩 SOC와 연계 (포트폴리오 강화 아이디어)
- Free Tier에 **GuardDuty·CloudTrail 활성화** → 로그를 **홈랩 Wazuh로 수집·탐지** → "클라우드 로그까지 관제한 SOC 홈랩"으로 격상
- 기존 `mini-soc-homelab` 프로젝트에 **AWS 로그 소스 1개** 추가하면 클라우드 역량 증빙

## 핵심
깊게 X, **IAM·CloudTrail·GuardDuty·S3 보안** 이 4개만 "설명 가능" 수준이면 신입 지원엔 충분.

## 관련
- [[포트폴리오-로드맵]] · [[TryHackMe-학습-노트화계획]] · [[사용법-mini-soc-homelab]]
