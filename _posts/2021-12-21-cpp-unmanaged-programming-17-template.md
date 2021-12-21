---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 17: 템플릿(Template) 프로그래밍"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 17: 템플릿(Template) 프로그래밍

## 다시 만난 벡터

```cpp
// Main.cpp
#include <vector>

int main()
{
    std::vector<int> scores; // -> 템플릿 프로그래밍...
    scores.push_back(10);   // 10
    scores.push_back(50);   // 10, 50

    return 0;
}
```

## 템플릿이란?
* Java와 C#에서의 제너릭(generic) 메서드/클래스와 비슷
* STL 컨테이너 또한 템플릿이다
* 덕분에 코드를 자료형마다 중복으로 작성하지 않아도 된다

## 예시: 두 정수 더하기

```cpp
// MyMath.h
int Add(int a, int b)
{
    return a + b;
}
```

```cpp
// Main.cpp
#include "MyMath.h"
#include <iostream>

int main()
{
    std::cout << Add(3, 10) << std::endl;

    return 0;
}
```

-> 그럼, float 둘을 더하고 싶다면..?

## 예시: 두 실수 더하기

```cpp
// MyMath.h
float Add(float a, float b)
{
    return a + b;
}
```

```cpp
// Main.cpp
#include "MyMath.h"
#include <iostream>

int main()
{
    std::cout << Add(3.1f, 10.1f) << std::endl;

    return 0;
}
```

-> 그럼 double 둘을 더하고 싶다면..?

double용 Add() 함수를 하나 더 만들어야 한다.

이렇게 하지 않기 위하여 템플릿을 사용한다.

## 예시: 두 정수 더하기

```cpp
// MyMath.h
template <typename T> // 또는 template <class T>
T Add(T a, T b)
{
    return a + b;
}
```

```cpp
// Main.cpp
#include "MyMath.h"

int main()
{
    std::cout << Add<int>(3, 10) << std::endl; // int용 Add함수를 컴파일 중에 만들어준다
    std::cout << Add<float>(3.14f, 10.14f) << std::endl; // float용 Add함수를 컴파일 중에 만들어준다

    return 0;
}
```

## 함수 템플릿

```cpp
template <class <type_name>> <function_declaration>;
template <typename <type_name>> <function_declaratino>;
```

    ```cpp
    template <class T>
    T Add(T a, T b)
    {
        // ...
    }

    template <typename T>
    T Add(T a, T b)
    {
        // ...
    }
    ```

* 함수 템플릿을 호출할 때 템플릿 매개변수를 생략할 수 있음

    ```cpp
    Add<int>(3, 10);
    Add(3, 10);
    ```

## `typename` vs `class`
* 차이가 뭘까?
* 사실상 없음
* 그냥 typename을 사용하자

## 템플릿은 어떻게 작동할까?
* 템플릿을 인스턴스화할 때마다 컴파일러가 내부적으로 코드를 생성

    ```cpp
    // MyMath.h
    template <typename T>
    T Add(T a, T b)
    {
        return a + b;
    }
    ```

    ```cpp
    // Main.cpp
    #include "MyMath.h"

    // (1)
    int Add(int a, int b)
    {
        return a + b;
    }

    int main()
    {
        int resultInt = Add<int>(3, 10); // 코드에 이게 있으면 컴파일시 (1)이 생성됨
        // ...
    }
    ```

## 이것은 무엇을 의미할까?
* 템플릿에 넣는 자료형 가짓수에 비례해서 exe 파일 크기 증가
* 컴파일 타임에 어느 정도 다형성을 부여할 수 있음
    * 소개되었을 때 새롭게 뜨는 주제였음
    * 그래서 많이들 잘못 사용했음...
    * 이에 대해서는 나중에...

## 클래스 템플릿 예시: MyIntArray

```cpp
class MyIntArray
{
public:
    bool Add(int data);
    MyIntArray();

private:
    enum { MAX = 3 };
    int mSize;
    int mArray[MAX];
};
```

```cpp
#include "MyIntArray.h"

int main()
{
    MyIntArray scores;

    scores.Add(10); // true 반환
    scores.Add(20); // true 반환
    scores.Add(30); // true 반환
    scores.Add(40); // false 반환

    return 0;
}
```

-> 그럼 float 배열을 만들고 싶으면?

float 클래스 배열을 따로 만들지 말고 template을 이용하자

## 예시1: MyArray

```cpp
// MyArray.h
#pragma once
template<typename T>
class MyArray
{
public:
    bool Add(T data);
    MyArray();
private:
    enum { MAX = 3 };
    int mSize;
    T mArray[MAX];
};
```

```cpp
// MyArray.cpp
#include "MyArray.h"

template<typename T>
bool MyArray<T>::Add(T data)
{
    if (mSize >= MAX)
    {
        return false;
    }
    mArray[mSize++] = data;
    return true;
}

template<typename T>
MyArray<T>::MyArray()
    : mSize(0)
{    
}
```

```cpp
// Main.cpp
#include "MyArray.h"

int main()
{
    MyArray<int> scores;

    scores.Add(10); // true 반환
    scores.Add(20); // true 반환
    scores.Add(30); // true 반환
    scores.Add(40); // false 반환

    return 0;
}
```

-> 그러나 컴파일시 에러 발생

## 무엇이 문제일까?
* 컴파일러가 "Main.cpp"를 컴파일할 때, "MyArray.cpp"를 못 찾음
    * "MyArray.h"를 통해서 오직 MyArray 클래스 선언만 볼 수 있음
* 따라서, 컴파일러가 MyArray<int>를 만들어줄 수 없음

## 해결책
* 헤더파일 안에 구현체를 옮긴다

```cpp
// MyArray.h
template<typename T>
class MyArray
{
public:
    bool Add(T data);
    MyArray();

private:
    enum { MAX = 3 };
    int mSize;
    T mArray[MAX];
};

template<typename T>
inline bool MyArray<T>::Add(T data)
{
    return false;
}

template<typename T>
inline MyArray<T>::MyArray()
    : mSize(0)
{
}
```

## 클래스 템플릿
* 개체를 선언할 때 템플릿 매개변수를 명시해야 함

    ```cpp
    MyArray<int> scores;    // OK
    MyArray scores; // 에러 발생
    ```

## 괜찮은 트릭

```cpp
// FixedVector.h
template<typename T, size_t N>
class FixedVector
{
public:
    // public 메서드들
private:
    T mArray[N];
};
```

```cpp
// main.cpp
FixedVector<int, 16> numbers;
```

## 예시2: MyPair

```cpp
// MyPair.h
template<typename T>
class MyPair
{
public:
    const T& GetFirst() const;
    const T& GetSecond() const;
    MyPair(const T& first, const T& second);

private:
    T mFirst;
    T mSecond;
};

template<typename T>
const T& MyPair<T>::GetFirst() const
{
    return mFirst;
}

template<typename T>
const T& MyPair<T>::GetSecond() const
{
    return mSecond;
}

template<typename T>
MyPair<T>::MyPair(const T& first, const T& second)
    : mFirst(first)
    , mSecond(second)
{
}
```

```cpp
// Main.cpp
std::vector<MyPair<std::string>> students;

students.emplace_back(MyPair<std::string>("a1234", "Coco"));
students.emplace_back(MyPair<std::string>("a5678", "Mocha"));

for (std::vector<MyPair<std::string> >::iterator it = students.begin(); it != students.end(); ++it)
{
    std::cout << it->GetFirst() << " : " << it->GetSecond() << std::endl;
}
```

* 템플릿 매개변수를 하나 더 만들 수 있을까?

## 더 좋은 예시2: MyPair

```cpp
// MyPair.h
template<typename T, typename U>
class MyPair
{
public:
    const T& GetFirst() const;
    const T& GetSecond() const;

    MyPair(const T& first, const U& second);
private:
    T mFirst;
    U mSecond;
};

template<typename T, typename U>
const T& MyPair<T, U>::GetFirst() const
{
    return mFirst;
}

template<typename T, typename U>
const U& MyPair<T, U>::GetSecond() const
{
    return mSecond;
}

template<typename T, typename U>
MyPair<T, U>::MyPair(const T& first, const U& second)
    : mFirst(first)
    , mSecond(second)
{
}
```

```cpp
// Main.cpp
std::vector<MyPair<std::string, int> > scores;

scores.emplace_back(MyPair<std::string, int>("Coco", 10));
scores.emplace_back(MyPair<std::string, int>("Mocha", 100));

for (std::vector<MyPair<std::string, int> >::iterator it = scores.begin(); it != scores.end(); ++it)
{
    std::cout << it->GetFirst() << " : " << it->GetSecond() << std::endl;
}
```

## 두 개의 템플릿 매개변수

```cpp
template <class <type_name>, class <type_name>>
template <typename <type_name>, typename <type_name>>
```

```cpp
template <typename T, typename U>
void Print(const T& a, const U& b)
{
    std::cout << a << " / " << b << std::endl;
}
```

```cpp
template <typename T, typename U>
class MyClass
{
private:
    T mX;
    U mY;
};
```

## 템플릿 특수화(Specialization)
* 특정한 템플릿 매개변수를 받도록 템플릿 코드를 커스터마이즈할 수 있음
* `std::vector`에 좋은 예가 있음
    
    ```cpp
    template <class T, class Allocator>
    class std::vector<T, Allocator> {}  // 모든 형을 받는 제네릭 vector
    
    template <class Allocator>
    class std::vector<bool, Allocator>  // bool을 받도록 특수화된 vector
    ```

* bool은 왜 다를까?
* 이렇게 생각해보자
    * 제네릭 템플릿이 어느 형에나 적용됨
    * 단 특정형에 특수화된 템플릿이 있다면 그 특수화된 템플릿이 적용됨
* 그렇게 자주 쓰이지는 않음
* 하지만 `std::vector<bool>` 특수화는 가치가 좀 있음
    * 메모리가 쪼들리는 플랫폼이라면

## 예시: `Power()`

```cpp
// MyMath.h
template<typename V, typename EXP>
V Power(const V value, EXP exponent)
{
    V result = 1;
    while (exponent-- > 0)
    {
        result *= value;
    }

    return result;
}
```

```cpp
// Main.cpp
template<>
float Power(float value, float exp)
{
    return std::powf(value, exp);
}

int main()
{
    int powerResultInt = Power(10, 2);
    float powerResultFloat = Power(10.f, 2.5f);
}
```

### 전체 템플릿 특수화
* 템플릿 매개변수 리스트가 비어 있음

    ```cpp
    template<typename VAL, typename EXP>
    VAL Power(const VAL value, EXP exponent) {} // 모든 형을 받는 제네릭 power()

    template <>
    float Power(float value, float exp) // float을 받도록 특수화된 power()
    ```

### 부분 템플릿 특수화

    ```cpp
    template <class T, class Allocator>
    class std::vector<T, Allocator> {}  // 모든 형을 받는 제네릭 vector
    
    template <class Allocator>
    class std::vector<bool, Allocator>  // bool을 받도록 특수화된 vector
    ```

## 장점과 단점
* 컴파일러가 컴파일 도중에 각 템플릿 인스턴스에 대한 코드를 만들어 줌
    * 컴파일 타임은 비교적 느림
    * 템플릿 매개변수를 추가할수록 더 느려짐

        ```cpp
        std::pair<std::string, int>
        std::pair<std::string, std::string>
        std::pair<float, std::string>
        ...
        ```
    
    * 하지만 런타임 속도는 더 빠를지도 모름
    * 실행 파일 크기가 커지기 때문에 항상 그런 것은 아님
    * C#과 Java도 어느 정도 해당되는 말 (그래서 ArrayList를 쓰지 말라는 것)
* 자료형만 다른 중복 코드를 없애는 훌륭한 방법
* 하지만 쓸모없는 템플릿 변형을 막을 방법이 없음
    * 최대한 제네릭 함수를 짧게 유지할 것
    * 제네릭이 아니어도 되는 부분은 별도의 함수로 옮기는 것도 방법. 이 함수가 인라인될 수도 있음
* 컴파일 도중에 다형성을 부여할 수 있음
    * C++ 프로그래머가 너무 많이 잘못 썼던 기능
    * 가상 테이블이 없어서 프로그램이 더 빠름
    * 하지만 exe 파일이 커지면 느려질 수 있음
* 코드 읽기가 더 힘듦
* 디버깅이 좀 이상할 수 있음

## 템플릿 프로그래밍 베스트 프랙티스
* 컨테이너의 경우 매우 적합
    * 아주 다양한 형들을 저장할 수 있음
    * 그런 이유로 Java와 C# 제네릭이 주로 컨테이너에 쓰이는 것
* 컨테이너가 아닌 경우
    * 각기 다른 서넛 이상의 자료형을 다룬다면 템플릿을 쓰자
    * 두 가지 정도라면 그냥 클래스를 2개 만들자

        ```cpp
        class Math;
        class FloatMath;
        ```

