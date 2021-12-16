---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 12: static 키워드"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 12: static 키워드

## Java vs C++
* Java
    * 정적(static) 변수
        * 멤버 메서드 안
    * 정적 멤버 함수
    * 정적 블록(block)
    * 정적 내포(nested) 클래스

* C++
    * 정적 변수
        * 멤버 함수 안
        * 멤버 아닌 함수 안
    * 정적 멤버 함수

## static
* 범위(scope)의 제한을 받는 전역 변수
* 어떤 범위?
    * 파일 속
    * 네임스페이스 속
    * 클래스 속
    * 함수 속

## `extern` 키워드
* 다른파일의 전역변수에 접근을 가능하게 해 줌

```c
// ExternTest.h
extern int globalValue;

void IncreaseValue();
```

```c
#include "ExternTest.h"

int globalValue = 2;
void IncreaseValue()
{
    globalValue++;
}
```

```c
#include "ExternTest.h"

int main()
{
    printf("%d", globalValue);
    // ...
}
```

## C 스타일 `static`
* 정적 변수가 함수 밖에 있다면 다른 파일에서는 이 정적변수에 접근할 수 없음

```cpp
// StaticTest.h
extern int globalValue; // extern: global로 접근 가능

void IncreaseValue();
```

```cpp
// StaticTest.cpp
#include "StaticTest.h"

static int globalValue = 2; // static: 이 안에서만 접근 가능!

void IncreaseValue()
{
    globalValue++;
}
```

```cpp
// Main.cpp
#include "StaticTest.h"

int main()
{
    // 링커(linker) 에러
    printf("%d", globalValue);
    return 0;
}
```

## 함수 속 정적 변수

* Java
    * 존재하지 않음
* C++
    * 함수로 범위(scope)가 제한된 전역 변수

    ```cpp
    void Accumulate(int number)
    {   // 이 영역은 result에 접근 가능
        static int result = 0;
        result += number;
        std::cout << "result = " << result << std::endl;
    }

    int main()
    {   // 이 영역은 result에 접근 불가
        Accumulate(10); // 10
        Accumulate(20); // 30
        return 0;
    }
    ```

## 정적 멤버 변수
* Java

    ```java
    public class Cat
    {
        private static int count = 0;
        Cat()
        {
            count++;
        }
    }
    ```

* C++

    ```cpp
    // Cat.h
    class Cat
    {
    public:
        Cat();
    private:
        static int mCount;
    };

    // Cat.cpp
    int Cat::mCount = 0;
    Cat::Cat()
    {
        mCount++;
    }
    ```

* **클래스당 하나의 COPY만 존재**
* 개체의 메모리 레이아웃의 일부가 아님
* 클래스 메모리 레이아웃에 포함
* exe파일 안에 필요한 메모리가 잡혀 있음
    * 컴파일러가 이 변수의 인스턴스가 몇 개 존재해야 하는지 알기에...
    * 오직 하나

## 정적 멤버 변수 베스트 프랙티스
* 함수안에서 정적 변수를 넣지 말 것
    * 클래스 안에 넣을 것
* 전역변수 대신 정적 멤버변수를 쓸 것
    * 범위(scope)를 제한하기 위해
* C스타일의 정적변수를 쓸 이유가 이제 없음

## 정적 멤버 함수

* Java

    ```java
    public class Math
    {
        public static int Square(int number) // static 키워드
        {
            return number * number;
        }
    }
    ```

* C++

    ```cpp
    // Math.h
    class Math
    {
    public:
        static int Square(int number); // static 키워드
    };
    
    // Math.cpp
    int Math::Square(int number)
    {
        return number * number;
    }
    ```

* 논리적인 범위(scope)에 제한된 전역 함수
* 해당 클래스의 정적 멤버에만 접근 가능
* 개체가 없이도 정적 함수를 호출할 수 있음
    ```cpp
    Math::Square(10);
    ```

## 정적 메서드에서 비정적 멤버 변수를 쓰려고 하면...

```cpp
// Cat.h
class Cat
{
public:
    Cat(const std::string& name);
    static const char* GetName(); // static 메서드
private:
    char* mName;
    static int mCount;
};
```

```cpp
// Cat.cpp
Cat::Cat(const std::string& name)
{
    ++mCount;
}

const char* Cat::GetName() // 정적 메서드에서
{
    return mName; // 비정적 멤버 변수를 쓰려고 하면... 컴파일 에러
}
```