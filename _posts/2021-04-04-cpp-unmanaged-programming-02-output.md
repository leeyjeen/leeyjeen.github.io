---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 2: 출력 (Output)"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 2: 출력 (Output)

## Hello World 출력하기, 네임스페이스(namespace)

### "Hello World"

- OLD (C)
    - 사용하고 읽기가 더 어렵다.

    ```c
    printf("Hello, %s%d\n", "world", 123);
    ```

- NEW (C++)
    - 사용하고 읽기가 더 쉽다.

    ```cpp
    std::cout << "Hello, " << "world" << 123 << std::endl; // string, int를 함께 사용할 수 있다.
    ```

### std::cout, std::endl

```cpp
#include <iostream>

int main() {
	std::cout << "hello, world!" << std::endl;
	return 0;
}
```

### 네임스페이스(namespace)

- Java의 `package`, C#의 `namespace`와 비슷한 개념
- 함수, 클래스, 기타 등등의 이름 충돌을 피하기 위함

    ```cpp
    namespace hello { // 일반적으로 소문자를 사용한다!
    	void PrintHelloWorld();
    }
    ```

### namespace 예제

- OLD

    ```cpp
    // Hello1.h
    void SayHello();

    // Hello2.h
    void SayHello(); // 똑같은 이름의 함수

    // Main.cpp
    #include "Hello1.h"
    #include "Hello2.h"

    // ...

    SayHello(); // 컴파일 오류 발생
    ```

    → Hello1, Hello2에 동일한 이름의 함수가 들어가 있으므로, 중복된 함수 정의가 있다고 생각하여 **컴파일 오류**가 발생한다.

- NEW

    ```cpp
    namespace hello {
        void SayHello();
    }

    namespace hi {
        void SayHello();
    }

    // ...

    hello::SayHello();
    hi::SayHello();
    ```

    → namespace가 다르므로 호출 가능하다.

### using 지시문

- Java의 `import`나 C#의 `using`과 비슷한 개념

### using 예제

- `using` 사용하지 않는 경우

```cpp
#include <iostream>

int main() {
	std::cout << "hello, world!" << std::endl;
	return 0;
}
```

- `using` 사용하는 경우

```cpp
#include <iostream>

using namespace std;

int main() {
	cout << "hello, world!" << endl;
	return 0;
}
```

## << 연산자(operator)

```cpp
std::cout << "hello, world!" << std::endl;
```

- 밀어 넣는 연산자..!
- `+` 혹은 `-` 등과 같은 연산자 중 하나이다.
- C++에서는 프로그래머가 **연산자의** **동작을 바꿀 수 있다.**

## 출력형식 지정(Output Formatting)

## 16진수 출력 - printf()

- C

    ```c
    int number = 10;
    printf("%#x\n", number);
    ```

    → 쉽게 읽히지 않는다.

→ 좀 더 나은 방법이 없을까? → **Manipulator** (조정자)

## 16진수 출력 - 조정자 (Manipulator)

- C++

    ```cpp
    int number = 10;
    cout << showbase << hex << number << endl;
    ```

    - `showbase` << `hex`
    - 읽기 쉽다!

## 조정자

- `showpos`/`noshowpos` (양수일때 +를 보여줄지 말지)
    
    ```cpp
    cout << showpos << number; // +123
    cout << noshowpos << number; // 123
    ```

- `dec`/`hex`/`oct` (10진수, 16진수, 8진수)

    ```cpp
    cout << dec << number; // 123
    cout << hex << number; // 7b
    cout << oct << number; // 173
    ```

- `uppercase`/`nouppercase` (대문자로 바꾼다, 대문자로 바꾸지 않는다)

    ```cpp
    cout << uppercase << hex << number; // 7B
    cout << nouppercase << hex << number; // 7b
    ```

- `showbase`/`noshowbase` (몇진법인지 표기한다, 표기하지 않는다)

    ```cpp
    cout << showbase << hex << number << endl; // 0x7b
    cout << noshowbase << hex << number << endl; // 7b
    ```

- `left`/`internal`/`right` (왼쪽 정렬, 내부 정렬, 오른쪽 정렬)

    ```cpp
    cout << setw(6) << left << number; // setw는 컬럼수 셋팅, // |-123   |
    cout << setw(6) << internal << number; // internal: 부호는 제일 왼쪽, 숫자는 제일 오른쪽 |-   123|
    cout << setw(6) << right << number; // |   -123|
    ```

- `showpoint`/`noshowpoint` (소수점 이하를 보여준다, 안보여줄 수 있으면 보여주지 않는다)

    ```cpp
    cout << noshowpoint << decimal1 << " " << decimal2; // 100 100.12
    cout << showpoint << decimal1 << " " << decimal2; // 100.000 100.120
    ```

- `fixed`/`scientific` (실수 표기법: 고정적 실수 표기법, 유효한 값 하나를 정수로 두고 나머지를 다 소수점으로 만든 후 몇 승을 해야하는 지)

    ```cpp
    cout << fixed << number; // 123.456789
    cout << scientific << number; // 1.234568E+02 가장 유효한 값 하나에...
    ```

- `boolalpha`/`noboolalpha` (bool값을 알파벳으로 표기해준다, 아니다)

    ```cpp
    cout << boolalpha << bReady; // true
    cout << noboolalpha << bReady; // 1
    ```

### #include <iomanip> 안에 있는 조정자

- `setw()`

    ```cpp
    cout << setw(5) << number; // |  123|
    ```

- `setfill()`

    ```cpp
    cout << setfill('*') << setw(5) << number; // **123
    ```

- `setprecision()`

    ```cpp
    cout << setprecision(7) << number; // 123.4568
    ```

### cout 멤버 메서드

- 조정자 ver.

    ```cpp
    cout << showpos << number;
    ```

- cout 멤버 메서드 ver.

    ```cpp
    cout.setf(ios_base::showpos);
    cout << number;
    ```

- 조정자 ver.

    ```cpp
    cout << setw(5) << number;
    ```

- cout 멤버 메서드 ver.

    ```cpp
    cout.width(5);
    cout << number;
    ```

- 네임스페이스
    - `ios_base`
- `setf()`, `unsetf()`
- `width()`, `fill()`, `precision()`
- 그리 많이 사용되지는 않는다.

### setf(), unsetf()

- `setf(flag)` / `unsetf(flag)`

    - 인자:
        - `boolalpha`, `showbase`, `uppercase`, `showpos`

- `setf(flag, flag)`

    - 인자
        - `dec`, `oct`, `hex` / `basefield`
        - `fixed`, `scientific` / `floatfield`
        - `left`, `right`, `internal` / `adjustfield`

### 참고

- [https://www.udemy.com/share/101ZmQAEYfcltbRXkH/](https://www.udemy.com/share/101ZmQAEYfcltbRXkH/)