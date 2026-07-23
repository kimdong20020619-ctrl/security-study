---
tags:
  - claude-code
  - 도구설정
작성일: 2026-07-23
---

# Claude Code 환경설정 — harness-100 & find-skills

Claude Code(cmd) 작업 환경에 추가한 도구 두 가지와 사용 방법을 정리한 문서. 특정 프로젝트 전용이 아니라 Claude Code 자체 설정에 대한 내용이라, TryHackMe 학습노트와는 분리해서 이 독립 폴더(`클로드코드-환경설정/`)에 별도로 둠.

## 1. harness-100 — 논문/아이디어/보안감사용 에이전트 팀

**저장소**: https://github.com/revfactory/harness-100 (개인 개발자 revfactory, Apache 2.0)
**정체**: Claude Code용 "에이전트 팀 하네스" 100종. 각 하네스는 전문가 에이전트 4~5개 + 오케스트레이터 스킬 + 확장 스킬 2~3개로 구성됨.

### 후보 4개 (내 용도에 맞춰 선정)

| 하네스 | 용도 | 구성 에이전트 |
|--------|------|----------------|
| `98-academic-paper` | 논문 프로젝트 | 연구설계자·실험관리자·통계분석가·논문작성자·투고준비자 (IMRaD 구조) |
| `43-startup-launcher` | 아이디어/피칭형 프로젝트 | 시장분석가·비즈니스모델러·MVP설계자·피치덱작성자 |
| `45-gov-funding-plan` | 정부지원사업/공모전 사업계획서형 | 공고분석가·기술성작성자·사업성작성자·예산편성자 |
| `28-security-audit` | 보안 포트폴리오 레포 자체 감사 | 취약점스캐너·코드보안분석·침투테스트보고·보안컨설턴트·감사리뷰어 (OWASP/CVE/SAST) |

### 설치 스크립트

`C:\Users\kimdo\scripts\install-harness.cmd` — 실행할 때마다 GitHub에서 최신 버전을 새로 clone해서 설치하므로 항상 최신 상태가 유지됨(사전 설치 불필요, 실행 시점에 자동 갱신).

```cmd
install-harness.cmd <하네스이름> [대상폴더]
```

예:
```cmd
install-harness.cmd 98-academic-paper "논문 작업 폴더"
install-harness.cmd 28-security-audit "C:\Users\kimdo\Documents\GitHub\log-analyzer"
```

### 자동 적용 워크플로우

논문/아이디어/보안감사 작업을 시작한다고 말하면:
1. Claude가 대상 폴더를 먼저 확인 (고정 폴더가 없어서 매번 물어봄)
2. 폴더가 확정되면 Claude가 직접 `install-harness.cmd`를 실행해서 적용
3. 이건 "설치는 URL만 안내, 직접 하지 않기"라는 기존 원칙을 **이 스크립트에 한해서만** 완화한 것 — git commit/push 같은 다른 작업은 여전히 직접 승인 필요

### 왜 프로젝트별 설치이고 전역이 아닌가

Anthropic 공식 문서([Create plugins](https://code.claude.com/docs/en/plugins))는 "개인 워크플로우·프로젝트 한정 커스터마이징·실험 단계"는 standalone(`.claude/`) 방식을, "팀 공유·여러 프로젝트 재사용"은 정식 plugin/marketplace 방식을 권장함. harness-100은 marketplace 형식이 아니라 순수 파일 복사 방식이라, standalone처럼 프로젝트별로 필요할 때 넣는 게 공식 가이드와도 맞음.

## 2. find-skills (Vercel Labs) — 전역 설치됨

**정체**: "이거 어떻게 하지?"라는 질문이 나오면 처음부터 새로 만들지 않고, 기존에 검증된 스킬(skills.sh)이 있는지 먼저 찾아주는 메타 검색 스킬.
**운영 주체**: Vercel Labs 공식 (⭐26k, 250만 설치)
**설치 위치**: `C:\Users\kimdo\.claude\skills\find-skills` (전역, 모든 프로젝트에 자동 적용)
**설치일**: 2026-07-23
**설치 명령**: `npx -y skills add vercel-labs/skills --skill find-skills --agent claude-code`

**보안 스캔 결과** (설치 시 자동 표시됨): Gen "Safe", Socket "0 alerts", **Snyk "Medium Risk"**. 상세: https://skills.sh/vercel-labs/skills — 사용하다 이상하면 스킬 폴더 삭제로 되돌릴 수 있음.

전역 설치가 맞는 이유: harness-100과 달리 논문/아이디어/프로그래밍/보안 모든 활동에 공통으로 쓰이는 범용 검색 기능이라, 활동별로 나눌 필요가 없음.

## 3. Anthropic 공식 가이드 대조 검증 (2026-07-23)

공식 문서([Best practices](https://code.claude.com/docs/en/best-practices), [Discover plugins](https://code.claude.com/docs/en/discover-plugins))와 대조한 결과:

- ✅ harness-100을 marketplace 등록 없이 프로젝트별 standalone으로 복사하는 방식 — 공식 권장과 일치
- ✅ 전역 설치 최소화, 필요 시점에만 적용 — 헤비유저 관행("설치 개수보다 '뭐가 설치됐는지 기억하는' 인지 부하가 진짜 한계")과 일치
- ⚠️ 공식 보안 경고: "Plugins and marketplaces are highly trusted components that can execute arbitrary code on your machine... Only install from sources you trust." → harness-100·find-skills 둘 다 Anthropic 공식/커뮤니티 마켓플레이스가 아닌 서드파티 소스라서, 이 경고가 그대로 적용됨. Apache 2.0으로 코드가 공개돼 있다는 것과 Anthropic이 검증했다는 것은 별개.
- 참고: 공식 문서는 CLAUDE.md를 "짧고 사람이 읽기 쉽게, 필요한 규칙만" 유지하라고 권장함(`~/.claude/CLAUDE.md`가 현재 상당히 긺 — 오늘 논의 대상은 아니었지만 참고로 기록).

## 관련 문서

- 상세 후보 목록·설치 근거는 Claude Code 메모리 `reference_harness100_candidates.md`에도 저장돼 있음(세션 간 자동 상기용)
- GitHub: [kimdong20020619-ctrl/claude-code-setup](https://github.com/kimdong20020619-ctrl/claude-code-setup) — 이 문서의 미러 + install-harness.cmd 사본, 공개 완료
