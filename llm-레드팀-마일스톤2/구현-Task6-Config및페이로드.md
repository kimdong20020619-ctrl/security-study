---
tags: [llm-redteam, milestone2, implementation, done]
---

# 구현: Task6 — 마일스톤2 config·페이로드·README

허브: [[허브]] · 관련: [[설계-PolicyTarget]]

## 어떻게 진행했나
서브에이전트 위임(Haiku — 사전 작성된 내용을 그대로 파일화하는 단순 작업). 코드 변경 없음, 문서/데이터만.

## 결과
- 커밋 `c76d9d6`
- `config-milestone2.yaml`(정책봇 시나리오), `payloads/policy_bot_payloads.yaml`(8개 페이로드, 5개 카테고리 — 신제품 코드명·임원 성과급 인상률을 캐는 문구로 재구성), README에 마일스톤1/2 실행법 모두 명시
- 컨트롤러(나)가 직접 diff 대조로 검증 — 계획서 내용과 정확히 일치
