# 모듈 4 — 네트워크 기초 (Network Fundamentals)

---

## 섹션 1 — 데이터 표현 (Data Representation)

### 컴퓨터가 색상을 표현하는 방법

컴퓨터는 **RGB (Red · Green · Blue)** 세 가지 색상의 조합으로 색을 표현한다.  
각 색을 켜짐(1) / 꺼짐(0) 두 가지 상태로만 표현하면 2³ = **8가지 색상**을 만들 수 있다.

| 이진 표현 (R G B) | 켜진 색상 | 색상 이름 |
|:-----------------:|----------|----------|
| 000 | 없음 | 검은색 (Black) |
| 001 | 파란색 | 파란색 (Blue) |
| 010 | 초록색 | 초록색 (Green) |
| 011 | 초록색 + 파란색 | 시안 (Cyan, 청록색) |
| 100 | 빨간색 | 빨간색 (Red) |
| 101 | 빨간색 + 파란색 | 마젠타 (Magenta) |
| 110 | 빨간색 + 초록색 | 노란색 (Yellow) |
| 111 | 모두 | 하얀색 (White) |

> 011이 **시안(청록색)**, 101이 **마젠타**임에 주의 — 혼동하기 쉬운 부분

---

### 8가지 → 16,000,000가지로 확장

켜짐/꺼짐 2단계 대신, 각 색상에 **256가지 밝기 단계(0~255)** 를 부여하면 훨씬 많은 색을 표현할 수 있다.

- 256가지 상태를 표현하려면 **8비트 (1바이트)** 가 필요하다.
- R, G, B 각각에 1바이트씩 → 총 **24비트 (3바이트)**
- 256 × 256 × 256 = **약 1,677만 가지 (16,000,000+)** 색상 표현 가능

> "24비트 컬러" 또는 "True Color"라고 부르는 이유가 여기서 나온다.

---

### 진법 체계 (Number Systems)

컴퓨터가 이진법(0과 1)을 사용하는 이유는 두 가지 물리적 상태만 구분하면 되기 때문이다.

| 물리적 구현 | 0 (낮음) | 1 (높음) |
|-----------|----------|----------|
| 트랜지스터 논리 회로 (TTL) | 낮은 전압 | 높은 전압 |
| 하드 디스크 드라이브 | 자기 남극 | 자기 북극 |
| 광섬유 | 빛 없음 | 빛 있음 |

인간이 십진법(10진법)을 사용하는 가장 타당한 이유는 손가락이 10개이기 때문이다.

---

### 이진수 (Binary, 2진법)

- 컴퓨터가 사용하는 기본 수 체계
- 0과 1, 두 가지 숫자만 사용
- **비트(Bit)**: 이진 숫자(Binary Digit)의 줄임말, 0 또는 1
- **바이트(Byte)**: 8비트 묶음. **옥텟(Octet)** 이라고도 한다.

---

### 16진수 (Hexadecimal, 16진법)

4개의 이진수(비트)를 하나의 16진수 숫자로 표현한다.  
16진수는 0~9 + A~F (총 16개 기호)를 사용한다.

| 십진수 | 16진수 | 이진 표현 |
|:------:|:------:|:---------:|
| 0 | 0 | 0000 |
| 1 | 1 | 0001 |
| 2 | 2 | 0010 |
| 3 | 3 | 0011 |
| 4 | 4 | 0100 |
| 5 | 5 | 0101 |
| 6 | 6 | 0110 |
| 7 | 7 | 0111 |
| 8 | 8 | 1000 |
| 9 | 9 | 1001 |
| 10 | A | 1010 |
| 11 | B | 1011 |
| 12 | C | 1100 |
| 13 | D | 1101 |
| 14 | E | 1110 |
| 15 | F | 1111 |

**16진수 → 10진수 변환 예시**

`9BDF` (16진수) → 10진수 계산:

```
9 × 16³ + B(11) × 16² + D(13) × 16¹ + F(15) × 16⁰
= 9 × 4096 + 11 × 256 + 13 × 16 + 15 × 1
= 36,864 + 2,816 + 208 + 15
= 39,903
```

> 각 자리의 가중치: 16⁰=1, 16¹=16, 16²=256, 16³=4096

---

### 8진수 (Octal, 8진법)

3개의 이진수(비트)를 하나의 8진수 숫자로 표현한다.  
0~7 범위의 숫자만 사용하며, 현대 컴퓨터 시스템에서 자주 접하기는 어렵다.

| 십진수 | 8진수 | 이진 표현 |
|:------:|:-----:|:---------:|
| 0 | 0 | 000 |
| 1 | 1 | 001 |
| 2 | 2 | 010 |
| 3 | 3 | 011 |
| 4 | 4 | 100 |
| 5 | 5 | 101 |
| 6 | 6 | 110 |
| 7 | 7 | 111 |

**8진수 → 10진수 변환 예시**

`357` (8진수) → 10진수 계산:

```
3 × 8² + 5 × 8¹ + 7 × 8⁰
= 3 × 64 + 5 × 8 + 7 × 1
= 192 + 40 + 7
= 239
```

---

### 섹션 1 요약

| 진법 | 사용 기호 | 비트 묶음 | 특징 |
|------|----------|----------|------|
| **2진법 (Binary)** | 0, 1 | 1비트 | 컴퓨터 기본 수 체계 |
| **16진법 (Hexadecimal)** | 0~9, A~F | 4비트 → 1자리 | 색상 코드, 메모리 주소 등에 사용 |
| **8진법 (Octal)** | 0~7 | 3비트 → 1자리 | 현대 시스템에서 드물게 사용 |
| **10진법 (Decimal)** | 0~9 | — | 인간이 일상에서 사용 |

- **비트(Bit)**: 0 또는 1, 가장 작은 데이터 단위
- **바이트(Byte) = 옥텟(Octet)**: 8비트 묶음
- **16진수 색상**: R·G·B 각 1바이트(2자리 16진수) = 총 6자리 (예: `#FF5733`)
- 1바이트 = 256가지 값 → R·G·B 3바이트 = 약 **1,677만 가지 색상**

---

## 섹션 2 — 데이터 인코딩 (Data Encoding)

### 표현 vs 인코딩

| 개념 | 설명 |
|------|------|
| **표현 (Representation)** | 데이터가 메모리에 비트와 숫자로 저장된다는 개념 |
| **인코딩 (Encoding)** | 숫자와 의미 사이의 합의된 대응 관계. 예: 문자 "A"에 어떤 숫자가 대응하는지 정의 |

문자열은 각 문자에 숫자 코드가 할당된 일련의 숫자로, 컴퓨터는 이를 다른 데이터처럼 저장하고 처리한다.  
파일을 열 때 이상한 문자가 보이는 이유 중 하나는 **저장한 인코딩과 열 때 사용한 인코딩이 다르기 때문**이다.

---

### ASCII

- **ASCII** = American Standard Code for Information Interchange (미국 정보 교환 표준 코드)
- 1963년 제정된 초기 문자 인코딩 방식
- **7비트**, 0~127 범위의 **128개 문자** 정의
- 영어 알파벳 (대·소문자), 숫자, 구두점, 제어 문자 포함

**주요 ASCII 코드표**

| 십진수 | 16진수 | 이진수 | 문자 | 설명 |
|:------:|:------:|:------:|:----:|------|
| 48 | 30 | 00110000 | `0` | 숫자 0 |
| 57 | 39 | 00111001 | `9` | 숫자 9 |
| 64 | 40 | 01000000 | `@` | 골뱅이 |
| 65 | 41 | 01000001 | `A` | 대문자 A |
| 90 | 5A | 01011010 | `Z` | 대문자 Z |
| 91 | 5B | 01011011 | `[` | 여는 대괄호 |
| 92 | 5C | 01011100 | `\` | 역슬래시 |
| 93 | 5D | 01011101 | `]` | 닫는 대괄호 |
| 94 | 5E | 01011110 | `^` | 캐럿 |
| 95 | 5F | 01011111 | `_` | 밑줄 |
| 97 | 61 | 01100001 | `a` | 소문자 a |
| 122 | 7A | 01111010 | `z` | 소문자 z |
| 127 | 7F | 01111111 | DEL | 삭제 문자 |

> **패턴**: 대문자 A~Z는 65~90 (0x41~0x5A), 소문자 a~z는 97~122 (0x61~0x7A). 대·소문자 차이는 **32 (0x20)**.

**"TryHackMe"를 ASCII로 표현하면**

```
이진수:   01010100 01110010 01111001 01001000 01100001 01100011 01101011 01001101 01100101 00001010
16진수:   54       72       79       48       61       63       6b       4d       65       0a
문자:     T        r        y        H        a        c        k        M        e        \n(줄바꿈)
```

> 비트를 직접 읽는 것은 불편하기 때문에 실무에서는 **16진수**를 주로 사용한다.

---

### ASCII의 한계와 확장 (ISO-8859 시리즈)

ASCII는 7비트로 영어만 표현 가능하여 다른 언어를 지원하지 못했다.  
8비트로 확장하면 128개 문자를 추가할 수 있지만, 모든 언어를 담기에는 여전히 부족했다.  
이 문제를 해결하기 위해 **ISO/IEC 8859** 시리즈가 등장했다.

| 표준 | 지원 언어 | 예시 문자 |
|------|----------|----------|
| **ISO-8859-1 (Latin-1)** | 서유럽어 (독일어, 프랑스어, 스페인어, 이탈리아어 등) | ß, ü, é, ç, ñ |
| **ISO-8859-2 (Latin-2)** | 중동부유럽어 (폴란드어, 체코어, 헝가리어, 루마니아어 등) | ł, ń, č, ř, ő |

**문제점**: ISO-8859-1로 저장한 `Ø`를 ISO-8859-2로 열면 `Ř`로 잘못 표시된다.  
→ 저장할 때와 **동일한 인코딩**으로 열어야 한다.

---

### 유니코드 (Unicode)

ASCII와 ISO-8859의 한계를 극복하기 위해 등장한 **범용 문자 인코딩 표준**.

- 전 세계 모든 현대·역사적 문자 체계에 **고유한 코드 포인트(U+XXXX)** 를 부여
- 하나의 파일에서 **여러 언어를 동시에** 사용 가능
- 어떤 인코딩을 사용했는지 걱정할 필요 없음
- **유니코드 17.0**: 약 **15만 7천 개** 문자 정의 (이모지 약 4,000개 포함)

**유니코드 코드 포인트 예시**

| 코드 포인트 | 문자 | 설명 |
|------------|:----:|------|
| U+0041 | A | 라틴 대문자 A |
| U+03A9 | Ω | 그리스어 오메가 |
| U+3042 | あ | 일본어 히라가나 |
| U+9F8D | ⿓ | 중국어 "용(龍)" |
| U+30C4 | ツ | 일본어 "츠" |
| U+062A | ت | 아랍어 "타아" |
| U+265E | ♞ | 체스 검은 나이트 |
| U+2615 | ☕ | 커피 |
| U+1F525 | 🔥 | 불꽃 이모지 |
| U+1F60A | 😊 | 웃는 얼굴 이모지 |
| U+1F60C | 😌 | 안도한 얼굴 이모지 |

---

### UTF-8 / UTF-16 / UTF-32 비교

유니코드는 문자 집합(어떤 문자에 어떤 번호인지)을 정의하고,  
**UTF (Unicode Transformation Format)** 는 그 번호를 실제 비트로 저장하는 방식을 정의한다.

| 항목 | **UTF-8** | **UTF-16** | **UTF-32** |
|------|-----------|------------|------------|
| **바이트 크기** | 1~4바이트 (가변) | 2 또는 4바이트 (가변) | 항상 4바이트 (고정) |
| **ASCII 호환** | ✅ 완벽 호환 (1바이트) | ❌ 비호환 | ❌ 비호환 |
| **일반 문자** | 1~2바이트 | 2바이트 | 4바이트 |
| **이모지·희귀 문자** | 4바이트 | 4바이트 (서로게이트 쌍) | 4바이트 |
| **효율성** | 높음 (영어·ASCII에 최적) | 중간 | 낮음 (항상 4바이트) |
| **주요 사용처** | 🌐 현대 웹 표준 | Windows 내부, 일부 API | 내부 처리용 |

**문자별 인코딩 크기 비교**

| 문자 | UTF-8 | UTF-16 | UTF-32 |
|:----:|:-----:|:------:|:------:|
| `A` (U+0041) | 1바이트 | 2바이트 | 4바이트 |
| `Ω` (U+03A9) | 2바이트 | 2바이트 | 4바이트 |
| `あ` (U+3042) | 3바이트 | 2바이트 | 4바이트 |
| `🔥` (U+1F525) | 4바이트 | 4바이트 | 4바이트 |

> **실무 포인트**: 웹과 리눅스 환경에서는 **UTF-8**이 사실상 표준이다.  
> 보안 분야에서 인코딩 문제는 취약점으로 이어질 수 있다 (예: URL 인코딩, Base64).

---

### 섹션 2 요약

| 항목 | 핵심 내용 |
|------|----------|
| **인코딩** | 숫자와 문자 의미 사이의 합의된 대응 관계 |
| **ASCII** | 7비트, 128문자, 영어 전용, 1963년 제정 |
| **ISO-8859** | 8비트 확장, 지역별 언어 표준 (서로 다른 표준끼리 깨짐 현상 발생) |
| **유니코드** | 전 세계 모든 문자에 고유 코드 포인트 부여, 약 15만 7천 자 |
| **UTF-8** | 1~4바이트 가변, ASCII 하위 호환, 현대 웹 표준 |
| **UTF-16** | 2~4바이트 가변, Windows 내부 사용 |
| **UTF-32** | 항상 4바이트 고정, 단순하지만 비효율적 |

---

## 섹션 3 — Python 기초: 숫자 맞추기 게임 (Python: Simple Demo)

이 섹션은 Python의 핵심 개념 3가지를 간단한 "숫자 맞추기 게임"을 통해 소개한다.

**핵심 개념 3가지**: 변수 (Variables) / 조건문 (Conditional Statements) / 반복문 (Iterations)

---

### Task 2 — 변수 (Variables)

변수를 사용하면 값을 저장하고 나중에 이름으로 참조할 수 있다.

#### 주요 함수

| 함수 | 설명 | 반환값 |
|------|------|--------|
| `random.randint(a, b)` | a 이상 b 이하의 무작위 정수 반환 | 정수 |
| `input(prompt)` | 사용자 입력을 받음 | 문자열 (string) |
| `int(text)` | 문자열을 정수로 변환 | 정수 |
| `print(...)` | 화면에 텍스트 출력 | 없음 |

#### 초기 변수 설정 코드

```python
import random  # 난수 생성 도구

secret = random.randint(1, 20)  # 1~20 사이 비밀 숫자
tries = 0    # 시도 횟수 카운터
guess = 0    # 초기값 — 1~20 밖의 값으로 설정 (비밀 숫자가 될 수 없음)

print("I'm thinking of a number between 1 and 20")

text = input("Take a guess: ")   # 사용자 입력 → 문자열
guess = int(text)                 # 문자열 → 정수 변환
tries = tries + 1                 # 시도 횟수 1 증가
```

> `input()`은 항상 **문자열**을 반환한다. 숫자 비교를 하려면 반드시 `int()`로 변환해야 한다.

---

### Task 3 — 조건문 (Conditional Statements)

#### if / elif / else 구조

Python에서 "else if"는 `elif`로 작성한다.

```python
if guess < 1 or guess > 20:
    print("That number is out of range. Try again.")
elif guess < secret:
    print("Too low, try again.")
elif guess > secret:
    print("Too high, try again.")
else:
    print("You got it in", tries, "tries!")
```

#### 동작 원리

| 조건 | 출력 메시지 |
|------|------------|
| guess < 1 또는 guess > 20 | "That number is out of range." |
| guess < secret | "Too low, try again." |
| guess > secret | "Too high, try again." |
| 위 조건 모두 거짓 (= guess == secret) | "You got it in N tries!" |

**핵심**: `if` 조건이 참이면 해당 블록 실행 후 나머지 `elif` / `else`는 건너뜀.  
이전 조건이 모두 거짓일 때만 다음 `elif` 조건을 확인한다.

---

### Task 4 — 반복문 (Iterations)

한 번만 시도하는 게임을 정답을 맞출 때까지 계속 반복되게 만들기 위해 **while 루프**를 사용한다.

- `!=` : "같지 않다 (does not equal)"
- `while 조건:` 형태 — 조건이 참인 동안 블록 내 코드를 반복 실행

```python
while guess != secret:
    text = input("Take a guess: ")
    guess = int(text)
    tries = tries + 1

    if guess < 1 or guess > 20:
        print("That number is out of range. Try again.")
    elif guess < secret:
        print("Too low, try again.")
    elif guess > secret:
        print("Too high, try again.")
    else:
        print("You got it in", tries, "tries!")
```

#### while 루프 흐름

```
조건 확인: guess != secret
    ↓ 참(True)
코드 블록 실행 → 입력 → 힌트 출력 → 다시 조건 확인
    ↓ 거짓(False, guess == secret)
루프 종료
```

#### 완성된 프로그램 (guess_v3.py)

```python
import random

secret = random.randint(1, 20)
tries = 0
guess = 0

print("I'm thinking of a number between 1 and 20")

while guess != secret:
    text = input("Take a guess: ")
    guess = int(text)
    tries = tries + 1

    if guess < 1 or guess > 20:
        print("That number is out of range. Try again.")
    elif guess < secret:
        print("Too low, try again.")
    elif guess > secret:
        print("Too high, try again.")
    else:
        print("You got it in", tries, "tries!")
```

---

### 섹션 3 요약

| 개념 | Python 문법 | 설명 |
|------|------------|------|
| **변수** | `x = 값` | 값을 저장하고 이름으로 참조 |
| **조건문** | `if / elif / else` | 조건에 따라 다른 코드 블록 실행 |
| **else if** | `elif` | Python의 "else if" 표현 |
| **반복문** | `while 조건:` | 조건이 참인 동안 코드 반복 실행 |
| **같지 않다** | `!=` | "does not equal" 비교 연산자 |

---

## 섹션 4 — JavaScript 기초: 숫자 맞추기 게임 (JavaScript: Simple Demo)

섹션 3의 Python 숫자 맞추기 게임과 **동일한 프로그램**을 JavaScript로 구현한다.  
**실행 환경**: Node.js (`node 파일명.js` 명령어로 실행)

---

### Task 2 — 변수와 상수 (Variables & Constants)

JavaScript는 변수(`let`)와 상수(`const`)를 키워드로 구분한다.

| 키워드 | 용도 | 예시 |
|--------|------|------|
| `let` | 값이 변경될 수 있는 변수 | `let tries = 0;` |
| `const` | 값이 변경되지 않는 상수 | `const secret = ...;` |
| `console.log()` | 화면에 텍스트 출력 | `console.log("Hello")` |

**난수 생성 원리: `Math.floor(Math.random() * 20) + 1`**

| 단계 | 연산 | 결과 예시 |
|------|------|----------|
| `Math.random()` | 0 이상 1 미만의 소수 반환 | 0.372 |
| `× 20` | 범위를 0~20으로 확장 | 7.44 |
| `Math.floor()` | 소수점 버림 | 7 |
| `+ 1` | 범위를 1~20으로 이동 | 8 |

```javascript
let tries = 0;
let guess = 0;
const secret = Math.floor(Math.random() * 20) + 1;

console.log("I'm thinking of a number between 1 and 20");
```

---

### Task 3 — 사용자 입력 (User Input)

```javascript
const text = await rl.question("Take a guess: ");  // 사용자 입력 → 문자열
guess = parseInt(text, 10);                          // 문자열 → 정수(10진수) 변환
tries = tries + 1;
```

> `parseInt(text, 10)` 의 두 번째 인자 `10`은 **10진수**로 변환하겠다는 의미이다.

---

### Task 4 — 조건문 (Conditional Statements)

JavaScript는 `else if`를 사용한다 (Python의 `elif`와 다름). 코드 블록은 `{}` 중괄호로 묶는다.

```javascript
if (guess < 1 || guess > 20) {
    console.log("That number is out of range. Try again.");
} else if (guess < secret) {
    console.log("Too low, try again.");
} else if (guess > secret) {
    console.log("Too high, try again.");
} else {
    console.log("You got it in", tries, "tries!");
}
```

> `||` 는 JavaScript에서 "또는 (OR)"을 의미한다.

---

### Task 5 — 반복문 (Iterations)

JavaScript에서 "같지 않음"은 `!==`으로 표현한다 (Python의 `!=`와 다름).

```javascript
while (guess !== secret) {
    const text = await rl.question("Take a guess: ");
    guess = parseInt(text, 10);
    tries = tries + 1;

    if (guess < 1 || guess > 20) {
        console.log("That number is out of range. Try again.");
    } else if (guess < secret) {
        console.log("Too low, try again.");
    } else if (guess > secret) {
        console.log("Too high, try again.");
    } else {
        console.log("You got it in", tries, "tries!");
    }
}
```

#### 완성된 프로그램 (guess_v3.js)

```javascript
import * as readline from "node:readline/promises";
import { stdin as input, stdout as output } from "node:process";
const rl = readline.createInterface({ input, output });

try {
    const secret = Math.floor(Math.random() * 20) + 1;
    let tries = 0;
    let guess = 0;

    console.log("I'm thinking of a number between 1 and 20");

    while (guess !== secret) {
        const text = await rl.question("Take a guess: ");
        guess = parseInt(text, 10);
        tries = tries + 1;

        if (guess < 1 || guess > 20) {
            console.log("That number is out of range. Try again.");
        } else if (guess < secret) {
            console.log("Too low, try again.");
        } else if (guess > secret) {
            console.log("Too high, try again.");
        } else {
            console.log("You got it in", tries, "tries!");
        }
    }
} finally {
    rl.close();
}
```

---

### Python vs JavaScript 비교표

| 개념 | Python | JavaScript |
|------|--------|------------|
| **변수 선언** | `x = 값` | `let x = 값;` |
| **상수 선언** | 관례로 대문자 사용 | `const x = 값;` |
| **출력** | `print(...)` | `console.log(...)` |
| **사용자 입력** | `input("...")` | `await rl.question("...")` |
| **문자열 → 정수** | `int(text)` | `parseInt(text, 10)` |
| **난수 생성** | `random.randint(1, 20)` | `Math.floor(Math.random() * 20) + 1` |
| **else if** | `elif` | `else if` |
| **OR 연산자** | `or` | `\|\|` |
| **같지 않음** | `!=` | `!==` |
| **코드 블록** | 들여쓰기 (indentation) | `{}` 중괄호 |
| **문장 끝** | 없음 | `;` 세미콜론 |

---

### 섹션 4 요약

| 개념 | JavaScript 문법 |
|------|----------------|
| **변수** | `let x = 값;` |
| **상수** | `const x = 값;` |
| **출력** | `console.log(...)` |
| **OR 연산자** | `\|\|` |
| **조건문** | `if / else if / else` + `{}` |
| **같지 않음** | `!==` |
| **반복문** | `while (조건) {}` |

> 다음 섹션: **SQL 기초** (관계형 데이터베이스 쿼리 언어)

---

## 섹션 5 — SQL 기초 (Database SQL Basics)

---

### Task 2 — 테이블, 행, 열 이해하기

#### 데이터베이스란?

데이터베이스는 컴퓨터가 정보를 **체계적으로 저장**하는 디지털 저장소이다.  
종이 노트와 달리 데이터베이스는 수천 건의 데이터에서도 몇 초 만에 검색·계산·정렬이 가능하다.

#### 테이블 (Table)

데이터베이스 내부에서 정보는 **테이블**에 저장된다. 테이블은 행과 열로 구성된 스프레드시트와 같다.

| 구성 요소 | 설명 |
|----------|------|
| **열 (Column)** | 테이블 맨 위의 제목. 한 가지 유형의 정보를 저장 (예: 가격, 음료명) |
| **행 (Row)** | 가로 방향의 한 줄. 하나의 완전한 정보 세트 (예: 주문 1건) |

**카페 예시 테이블**

| id | drink | price | time |
|:--:|-------|:-----:|------|
| 1 | Coffee | 2.00 | 11:00 AM |
| 2 | Tea | 1.50 | 11:10 AM |
| 3 | Muffin | 2.50 | 11:15 AM |

- 열(Column): id, drink, price, time — 각 정보의 종류
- 행(Row): 카페 주문 1건 = 1행
- 주문 1건 추가 → 행 1개 추가 / 주문 삭제 → 해당 행만 삭제

#### SQL이란?

- **SQL** (Structured Query Language): 데이터베이스에 질문하기 위해 사용하는 언어
- SQL로 작성된 질문을 **쿼리 (Query)** 라고 한다
- 쿼리는 데이터를 **변경하지 않는다** — 단지 요청한 정보를 표시할 뿐

---

### Task 3 — 첫 SQL 쿼리 작성하기

실습 테이블 구조:
- `Orders` (id, drink, price, time) — 주문 내역
- `Menu` (drink, price) — 메뉴 목록

#### 1단계: 모든 데이터 조회 (SELECT + FROM)

```sql
SELECT * FROM Orders;
```

- `SELECT *` : 모든 열(`*`)을 선택
- `FROM Orders` : Orders 테이블에서 데이터를 가져옴
- Orders 테이블의 **모든 행과 열**이 표시됨

#### 2단계: 특정 열만 표시

```sql
SELECT drink, price FROM Orders;
```

- `SELECT` 뒤에 원하는 열 이름을 나열
- `drink`와 `price` 열만 표시됨 (id, time 제외)

#### 3단계: 결과 필터링 (WHERE)

```sql
SELECT * FROM Orders WHERE drink = 'Coffee';
```

- `WHERE` 조건에 맞는 행만 필터링
- Coffee 주문 행만 표시됨
- 메뉴 이름 확인이 필요할 때: `SELECT * FROM Menu;`

#### 4단계: 결과 정렬 (ORDER BY)

```sql
-- 가격 오름차순 (낮은 → 높은)
SELECT * FROM Orders ORDER BY price;

-- 가격 내림차순 (높은 → 낮은)
SELECT * FROM Orders ORDER BY price DESC;
```

- `ORDER BY 열이름` : 오름차순 정렬 (기본값)
- `ORDER BY 열이름 DESC` : 내림차순 정렬 (`DESC` = descending)

---

### 섹션 5 요약

| SQL 명령어 | 역할 | 예시 |
|-----------|------|------|
| `SELECT` | 표시할 열 선택 (`*`=전체) | `SELECT drink, price` |
| `FROM` | 데이터를 가져올 테이블 지정 | `FROM Orders` |
| `WHERE` | 조건에 맞는 행만 필터링 | `WHERE drink = 'Coffee'` |
| `ORDER BY` | 특정 열 기준으로 정렬 | `ORDER BY price DESC` |

**핵심 개념**

| 용어 | 설명 |
|------|------|
| **데이터베이스** | 정보를 체계적으로 저장하는 디지털 저장소 |
| **테이블** | 행과 열로 구성된 데이터 저장 구조 |
| **열 (Column)** | 한 가지 유형의 정보 (예: 가격) |
| **행 (Row)** | 하나의 완전한 레코드 (예: 주문 1건) |
| **쿼리 (Query)** | SQL로 작성된 데이터베이스 질문 |

> **보안 관점**: 허가 없이 주문을 변경·삭제할 수 있다면? → SQL Injection 취약점의 시작
