# BGP 진단 (BGP Diagnostics)

> **주제**: BGP 세션이 안 붙거나, 붙었는데 경로가 안 올 때 **읽기 전용으로** 원인을 찾는 절차
> **왜 배우나**: 네트워크관리사 2급 라우팅 파트 + 정보보안기사 「네트워크 보안」. 실무 장애 대응의 대표 시나리오.
> **복습용 노트**: 한 번 훑고 와도 이것만 읽으면 바로 이해되게 정리.

---

## 🧠 이 노트 한 눈에 (먼저 이것만 봐도 됨)

- BGP 장애는 **상태(state)를 먼저 읽으면 원인 범위가 절반으로 줄어든다.**
- 핵심 갈림길: **Established 인가 아닌가.**
  - **아니면** → TCP가 안 붙는 문제 (경로·ACL·소스 주소)
  - **맞는데 경로가 없으면** → 정책 문제 (route-map·prefix-list·max-prefix·주소체계)
- 기본은 **전부 읽기 전용.** 세션 리셋·정책 변경은 변경 창에서만.
- **한 문장 요약: "상태를 읽기 전에 손대지 마라."**

---

## 🔎 쉽게 말하면

BGP는 **인터넷의 우편 협정**이다. 통신사끼리 "우리 쪽으로 오는 편지는 이 주소들이야" 하고 서로 알려주는 약속.

- **세션이 안 붙는다** = 두 우체국 사이에 전화선이 안 이어짐
- **붙었는데 경로가 0개** = 전화는 됐는데, 서로 주소 목록을 안 주고받음 (약관 불일치)
- **엉뚱한 경로가 온다** = 주소 목록에 이상한 게 섞여 들어옴

> 💬 **AS (Autonomous System)** = 하나의 관리 주체가 운영하는 네트워크 덩어리. 통신사 하나, 대기업 하나가 각각 AS 번호를 갖는다. BGP는 **AS와 AS 사이**의 라우팅 프로토콜이다.

---

## 🖐️ 읽기 전용 진단 5단계 (순서대로)

### 0단계 — 대상을 정확히 특정 (건너뛰면 헛수고)

진단 전에 **네 가지**를 확정한다:

| 확인 항목 | 왜 |
|---|---|
| 이웃(neighbor) IP | 어느 세션 얘기인가 |
| **주소 체계(AFI/SAFI)** | IPv4 unicast? IPv6? VPNv4? EVPN? — **여기서 대부분 헤맨다** |
| VRF | 글로벌 테이블인가, 특정 VRF인가 |
| 로컬/원격 AS 번호 | 설정 불일치 확인용 |

> ❗ **가장 흔한 실수**: "글로벌 IPv4 unicast겠지" 하고 가정하기. VRF나 IPv6에서 돌아가는 세션인데 글로벌 테이블만 보고 "경로가 없다"고 결론 내린다.

### 1단계 — 요약 상태와 마지막 리셋 사유

```text
show bgp summary
show bgp neighbors <peer>
show logging | include BGP|<peer>
```

**`Last reset` 사유를 반드시 읽는다.** 여기에 답이 적혀 있는 경우가 많다.

### 2단계 — 상태별 최초 점검 항목 ⭐ 핵심 표

| 상태 | 의미 | 먼저 볼 것 |
|---|---|---|
| **Idle** | 아무것도 안 하는 중 | 이웃이 비활성? 설정 누락? 정책으로 차단? **백오프 타이머로 대기 중?** |
| **Connect** | TCP 연결 시도 중 | 경로가 있나, 상대가 179 포트를 열고 있나 |
| **Active** | TCP가 완성 안 됨 | 라우팅·소스 주소·ACL·상대 도달성 |
| **OpenSent / OpenConfirm** | **TCP는 됐다** | ASN 불일치, 인증(MD5), 타이머, capability 협상, 로그 |
| **Established (prefix 수 있음)** | 정상 교환 중 | 정책과 테이블 선택 문제 |
| **Established (prefix 0)** | 붙었는데 경로 없음 | **인바운드 정책, max-prefix, 상대의 광고, AFI/SAFI** |

> ⚠️ **`Active` 를 오해하지 마라.** 이름이 "활성"이라 잘 되는 것처럼 들리지만, **실제로는 TCP 연결에 실패해서 재시도 중인 상태**다. 시험에 자주 나온다.
>
> 그리고 `Active` 라고 해서 반드시 상대방이 죽은 게 아니다. **우리 쪽 소스 주소나 ACL 문제**일 수 있다.

### 3단계 — 전송 계층(TCP) 확인

```text
ping <peer> source <local-source>
traceroute <peer> source <local-source>
show ip route <peer>
show tcp brief | include <peer>|:179
show bgp neighbors <peer> | include BGP state|Last reset|Local host|Foreign host
```

> ❗ **`source` 옵션이 핵심이다.** 그냥 `ping <peer>` 는 성공하는데 BGP는 안 붙는 경우가 흔하다. BGP가 **루프백에서 세션을 맺는데**, ping은 물리 인터페이스 주소로 나가기 때문이다. 양방향 모두 루프백끼리 도달하는지, `update-source` 설정이 맞는지 확인한다.

> ⛔ **ACL을 꺼서 테스트하지 마라.** 진단하려다 보안 구멍을 낸다. hit counter, 로그, 경로 상태를 먼저 읽는다.

### 4단계 — 경로 정책 확인

```text
show bgp neighbors <peer> advertised-routes    # 내가 상대에게 보낸 것
show bgp neighbors <peer> routes               # 상대에게 받아서 통과한 것
show ip prefix-list <name>
show route-map <name>
show bgp <prefix>
```

**세 가지를 구분해야 한다:**

| 구분 | 의미 |
|---|---|
| advertised (광고) | 내가 상대에게 보낸 경로 |
| received (수신) | 상대가 보낸 **원본** — 필터링 전 |
| routes / installed | 필터를 통과해 내 테이블에 들어온 것 |

> ⚠️ **함정**: `received-routes` 를 보려면 장비에 **추가 설정(soft-reconfiguration inbound 등)이 필요한 플랫폼**이 있다. 명령이 안 먹힌다고 "경로가 안 왔다"고 결론 내리면 오진이다. 그리고 **장애 대응 중에 그 설정을 몰래 넣지 마라** — 메모리를 더 쓰고, 승인 없는 변경이다.

### 5단계 — AS 경로 검사

```text
show bgp regexp _65001_
show bgp regexp ^65001$
show bgp <prefix>
```

> 💬 **AS-path 정규식의 `_`(밑줄)** = AS 번호의 경계. `_65001_` 은 AS 65001을 **하나의 토큰으로** 매치한다.
> 그냥 `65001` 로 쓰면 **`650012`, `165001` 같은 다른 ASN까지 걸린다.** 반드시 경계를 붙인다.
>
> - `^65001$` → AS 경로가 정확히 65001 하나뿐 (직접 연결된 이웃이 원조)
> - `_65001_` → 경로 어딘가에 65001이 있음

---

## 🐍 BGP summary 파싱 패턴

자동화로 상태를 수집할 때 쓰는 정규식. **핵심은 마지막 컬럼 처리다.**

```python
import re

BGP_SUMMARY_RE = re.compile(
    r"^(?P<neighbor>\d{1,3}(?:\.\d{1,3}){3})\s+"
    r"(?P<version>\d+)\s+"
    r"(?P<remote_as>\d+)\s+"
    r"(?P<msg_rcvd>\d+)\s+"
    r"(?P<msg_sent>\d+)\s+"
    r"(?P<table_version>\d+)\s+"
    r"(?P<input_queue>\d+)\s+"
    r"(?P<output_queue>\d+)\s+"
    r"(?P<uptime>\S+)\s+"
    r"(?P<state_or_prefixes>\S+)$",
    re.M,
)

def parse_bgp_summary(raw):
    rows = []
    for match in BGP_SUMMARY_RE.finditer(raw):
        state_or_prefixes = match.group("state_or_prefixes")
        if state_or_prefixes.isdigit():          # 숫자면 = Established + 수신 prefix 수
            state = "Established"
            prefixes_received = int(state_or_prefixes)
        else:                                     # 문자면 = 상태 이름 (Idle, Active...)
            state = state_or_prefixes
            prefixes_received = None
        rows.append({
            "neighbor": match.group("neighbor"),
            "remote_as": int(match.group("remote_as")),
            "state": state,
            "prefixes_received": prefixes_received,
            "uptime": match.group("uptime"),
        })
    return rows
```

> 💡 **왜 마지막 컬럼이 두 가지 의미인가**: Cisco `show bgp summary` 의 마지막 열은 **Established면 수신 prefix 개수(숫자)**, 아니면 **상태 이름(문자)** 를 출력한다. 같은 자리에 다른 타입이 온다. `isdigit()` 로 갈라내는 게 정석이다.
>
> 💬 **`(?P<이름>...)`** = 명명 그룹(named group). 나중에 `match.group("neighbor")` 로 꺼낸다. 숫자 인덱스보다 읽기 쉽고 순서가 바뀌어도 안 깨진다.
>
> 💬 **`re.M`** = MULTILINE. `^`·`$` 가 문자열 전체가 아니라 **각 줄**의 시작·끝에 매치된다. 여러 줄 출력을 파싱할 때 필수.

> ⚠️ 파싱 결과만 저장하지 말고 **원본 출력을 함께 보관**한다. BGP summary 형식은 플랫폼·주소체계마다 다르다.

---

## ⛔ 변경 창에서만 해야 하는 것 (진단으로 제안 금지)

- BGP 세션 clear (하드 리셋)
- 이웃 인증·타이머·update-source·route-map·prefix-list 변경
- 수신 경로 추가 저장 활성화 (soft-reconfiguration)
- 방화벽·ACL·control-plane 정책 완화

> 리셋이 승인되면 **가장 덜 파괴적인 방법**을 고른다. 하드 리셋보다 **soft reset / route-refresh** 가 우선이다. 하드 리셋은 세션을 끊었다 다시 맺으므로 그동안 경로가 사라진다.

---

## 💡 그래서 핵심

1. **상태 이름이 진단의 절반이다.** Idle/Active/OpenSent 각각이 가리키는 방향이 다르다.
2. **Established ≠ 정상.** prefix 0개면 정책 문제다.
3. **`source` 를 붙여서 도달성을 확인**한다. 루프백 소싱이면 그냥 ping은 무의미하다.
4. **AS-path 정규식엔 `_` 경계를 붙인다.** 안 붙이면 다른 ASN이 걸린다.
5. **읽기 전에 손대지 않는다.** 특히 하드 리셋 — `Last reset` 사유와 로그를 먼저 읽는다.

---

## ⚠️ 하지 말아야 할 것 (Anti-Patterns)

- `Active` 를 "상대가 죽었다"로 단정하기
- VRF·주소체계(AFI/SAFI)·update-source 차이를 무시하고 글로벌 IPv4만 보기
- AS-path 정규식을 토큰 경계 없이 쓰기
- `Last reset` 사유와 로그를 안 읽고 하드 리셋부터 때리기
- `received-routes` 출력이 없다고 "경로가 안 왔다"고 결론 내리기
- 진단한다고 ACL·방화벽 끄기

---

## 📝 복습 셀프 체크 (답 가리고 맞혀보기)

1. `Active` 상태의 진짜 의미는? → *TCP 연결이 완성되지 않아 재시도 중 (정상 아님)*
2. `Established` 인데 prefix가 0개면 어디를 보나? → *인바운드 정책, max-prefix, 상대 광고, AFI/SAFI*
3. `OpenSent`/`OpenConfirm` 이면 최소한 무엇이 확인된 것? → *TCP는 붙었다. ASN·인증·타이머·capability를 본다*
4. ping은 되는데 BGP가 안 붙는 대표 원인? → *루프백 소싱. `source` 옵션 없이 테스트해서 놓침*
5. `_65001_` 에서 밑줄의 역할? → *AS 번호 토큰 경계. 없으면 650012 같은 다른 ASN도 매치*
6. advertised / received / installed 차이는? → *내가 보낸 것 / 상대가 보낸 원본 / 필터 통과해 테이블에 들어온 것*
7. BGP summary 마지막 컬럼이 숫자면? → *Established 상태이며 그 숫자가 수신 prefix 개수*
8. 리셋이 승인됐을 때 먼저 시도할 것? → *soft reset / route-refresh (하드 리셋은 최후)*
9. 진단 시작 전 확정해야 할 4가지? → *이웃 IP, 주소체계(AFI/SAFI), VRF, 로컬·원격 AS*

> 위 9개를 막힘없이 답하면 이 노트는 이해 완료.

---

## 주요 용어 정리

| 용어 | 정의 |
|------|------|
| BGP | AS 간 경로 정보를 교환하는 라우팅 프로토콜. TCP 179 포트 사용 |
| AS (Autonomous System) | 단일 관리 주체가 운영하는 네트워크 집합. 고유 AS 번호를 가짐 |
| AFI/SAFI | 주소 체계 식별자. IPv4 unicast, IPv6, VPNv4, EVPN 등을 구분 |
| VRF | 하나의 장비 안에서 라우팅 테이블을 논리적으로 분리하는 기술 |
| Established | BGP 세션이 정상 수립되어 경로를 교환하는 상태 |
| Active | TCP 연결이 완성되지 않아 재시도 중인 상태 (정상 아님) |
| Idle | 세션을 시도하지 않는 상태. 설정 누락·비활성·백오프 대기 |
| update-source | BGP 세션을 맺을 때 출발지로 쓸 인터페이스(주로 루프백) |
| prefix-list | 특정 네트워크 대역을 허용/차단하는 필터 |
| route-map | 조건에 따라 경로를 걸러내거나 속성을 바꾸는 정책 도구 |
| max-prefix | 이웃에게 받을 최대 경로 수 제한. 초과 시 세션 차단 |
| AS-path 정규식 | AS 경로를 패턴으로 검색. `_`가 AS 번호 경계 |
| soft reset / route-refresh | 세션을 끊지 않고 경로만 다시 받는 방식 |
| 하드 리셋 (clear) | 세션을 끊고 재수립. 그동안 경로 소실 |
| 백오프 타이머 | 연속 실패 시 재시도 간격을 늘리는 대기 시간 |

## 관련 노트

- 🔗 [[01_네트워크-장비-설정-검증]] — 설정을 넣기 전 사전 점검
- 🔗 [[05_what-is-networking]] — 네트워크 기초
- 🏠 [[tryhackme/README|Security Study 홈]]

---

> **출처**: ECC(Everything Claude Code) `skills/network-bgp-diagnostics/SKILL.md` (MIT, github.com/affaan-m/ECC, v2.2.0 / commit c9de8f5) 를 읽고 재구성. 명령어·코드는 원문 인용, 설명·비유·셀프체크는 자체 작성.
> **정리일**: 2026-08-15
