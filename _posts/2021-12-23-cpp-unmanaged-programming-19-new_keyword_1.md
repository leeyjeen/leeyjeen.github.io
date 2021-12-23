---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 19: 새로운 키워드(C++11~) (1/2)"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 19: 새로운 키워드(C++11~) (1/2)

## 새로운 키워드들
* auto
* static_assert
* default/delete
* final/override

## `auto` 키워드
* 자료형을 추론
* JavaScript 등의 언어에 있는 동적인 형(type)과는 다름
* 실제 자료형은 컴파일하는 동안 결정됨
* 따라서, 반드시 auto 변수를 초기화해야 됨

    ```cpp
    auto x; // 에러
    auto x = "Chris"; // 올바름
    ```

## JavaScript vs C++
* JavaScript

    ```js
    var x;  // OK. x가 정의되지 않음
    x = "Chris";    // OK. x는 문자열
    x = 50; // OK. x는 숫자
    ```

* C++

    ```cpp
    auto x = "Chris";   // x는 const char*
    x = 50; // 에러
    ```

## `auto`로 포인터와 참조 받기
* `auto`를 사용하여 포인터와 참조를 받을 수 있음
    * 포인터를 받을 때: `auto` 또는 `auto*`
    * 참조를 받을 때: `auto&`

## 예시: `auto`로 포인터 받기

```cpp
// Main.cpp

Cat* myCat = new Cat("Coco", 2);
auto myCatPtr = myCat; // myCat과 동일한 포인터이다!
```

## 왜 포인터와 값 둘 다 `auto`로 받을까?
* 컴파일러가 어느 형인지 알아낼 수 있음
* 그러나 이건 포인터일까 아닐까?

    ```cpp
    auto name = object.GetName(); // 무엇을 반환하는지 코드만 봐서는 모른다
    ```

* 가독성에 좋지 않음 -> 나쁜 생각
* 포인터형을 받을 때는 `auto*`를 쓰자

    ```cpp
    auto* name = object.GetName();
    ```

## 예시: `auto`로 참조 받기?..

```cpp
// Main.cpp
Cat myCat("Coco", 2);
Cat& myCatRef = myCat;
// auto anotherMyCatRef = myCatRef; // myCat에 대한 참조일까? 아니다! 
auto& anotherMyCatRef = myCatRef;   // myCat에 대한 참조이다!
```

## 당연히 `auto&`여야 한다
* auto로 참조도 받을 수 있다면 받는 게 참조인지 아닌지 컴파일러가 어떻게 구분하겠는가?

    ```cpp
    auto a = b; // 참조일까 복사일까? 컴파일러가 구분 불가능
    ```

* 그래서 당연히 &를 붙여야 함

## 예시: `auto`로 `const` 받기

    ```cpp
    const int b = 1;
    auto& a = b;    // const를 이어 받을까? 받는다!
    ```

## 왜 `const`와 비`const` 모두 `auto`로 받을까?
* 다시 말하지만, 컴파일러가 알아낼 수 있기 때문

    ```cpp
    auto& a = constInt; // constInt는 const int
    auto& b = nonConstInt;  // nonConstInt는 int
    ```

* 하지만, 이건 `const`일까 아닐까?

    ```cpp
    auto& name = object.GetName();
    ```

* 아까도 말했지만, 가독성에 좋지 않음 -> 나쁘고 게으른 생각
* `const` 참조를 받을 때는 `const auto&`를 쓰자

    ```cpp
    const auto& name = object.GetName();
    ```

## `auto`와 함수 반환형
* `auto` 키워드는 함수가 반환하는 걸 저장하는 데 때론 유용
* 함수 반환형이 변해도 `auto`는 그대로
* 허나 이런 일이 자주 발생할까?

## 예시: `auto`로 함수 반환 값 저장하기

```cpp
// MyMath.h
float Add(float a, float b) // 이게 double Add(double a, double b) 로 바뀌더라도 main은 바뀔 필요 없음
{
    return a + b;
}
```

```cpp
// Main.cpp
int main()
{
    auto result = Add(10.0, 30.0);

    return 0;
}
```

## `auto`는 엄청나게 좋다?
* `auto` 키워드가 타이핑을 좀 줄여 줌
* 허나 가독성을 떨어뜨림
    * 아래에서 minValue는 음수가 될 수 있을까?

        ```cpp
        auto minValue = object.GetMinValue();
        if (minValue < -3)
        {
            // ...
        }
        ```

* 아직 `auto`에 관해 모두가 동의하는 코딩 표준이 없음
* 우리 코딩 표준에서는 `auto`보다 실제 자료형을 선호
* 단, 몇 가지 예외!

## `auto`와 반복자
* 반복자에는 `auto` 키워드가 매우 유용
    * 이전에 쓰던 코드

        ```cpp
        for (std::vector<int>::const_iterator it = v.begin(); it != v.end(); ++it)
        {
            // ...
        }
        ```

    * 이제 이렇게 간단함!

        ```cpp
        for (auto it = v.begin(); it != v.end(); ++it)
        {
            // ...
        }
        ```

## `auto`로 템플릿형 받기
* 일부 템플릿형의 경우에도 마찬가지

    ```cpp
    MyArray<int>* a = new MyArray<int>(10);

    auto* a = new MyArray<int>(10); // 우측에 이미 어떤 타입인지 보이는 경우, auto를 써도 상관 없다
    ```

## 베스트 프랙티스
* 이 수업의 코딩 표준. 업계 전반의 코딩 표준은 아님
* 명시적이어야 함
    * `auto`보다 실제 자료형 사용을 권장
    
        ```cpp
        Class* myClass = new Class("COMP3200");
        // ...
        const auto* name = myClass->GetStudentName(id);
        const char* name = myClass->GetStudentName(id); // 더 나음
        ```

    * 예외: 템플릿 매개변수와 반복자에는 `auto` 사용

        ```cpp
        std::vector<int> scores;
        int sum = 0;
        for (auto it = scores.begin(); it != scores.end(); ++it)
        {
            sum += *it;
        }
        ```

    * 포인터의 경우 `auto`보다 `auto*` 사용 (명확함)

        ```cpp
        Cat* myCat = new Cat("Coco", 2);
        // ...
        auto myCat2 = myCat;
        auto* myCat2 = myCat;   // 더 나음
        ```

    * const 경우 `auto&`보다 `const auto&`를 사용

        ```cpp
        Class* myClass = new Class("COMP3200");
        // ...
        auto& name = myClass->GetStudentName(id);
        const auto& name = myClass->GetStudentName(id); // 더 나음
        ```

* 전부 가독성과 관련된 것
* 런타임 성능은 동일

## `assert`
* 실행 중에 가정(어서션, assertion)이 맞는지 평가
* 오직 디버그 빌드에서만 작동
* 실패한 assert를 보려면 반드시 프로그램을 실행해야 함
    * 모든 코드 경로가 실행되었다고 어떻게 장담할까?
    * 일부 어서션은 프로그램을 실행하기도 전에 알 수 있음
* "모든 곳에 assert를 쓰자"
    * 여전히 유효한 조언

## `static_assert`
* 컴파일 중에 어서션 평가
* 컴파일러가 assert 조건이 참인지 아닌지 앎
* 실패하면 컴파일러는 컴파일 에러를 뱉음
* 많은 경우에 유용함
* C++11에서 정말 유용한 기능

## 예시1: 구조체의 크기

```cpp
// Class.h
struct Student
{
    char name[64];
    char id[10];
    int currentSemester;
};

class Class
{
public:
    // ...
    const Student* GetStudentInfo(const char* name);
};
```

```cpp
// Class.cpp
const Student* Class::GetStudentInfo(const char* name)
{
    static_assert(sizeof(Student)==74,
        "Student structure size mismatch");
    // 이하 코드 생략
}
```

## 예시2: Version 확인하기

```cpp
// Class.h
class Class
{
public:
    const static int Version = 1;
    // ...
};
```

```cpp
// Main.cpp
#include "Class.h"

static_assert(Class::Version > 1,
    "You need higher version than 1.");
```

## 예시3: 배열의 길이

```cpp
// Student.h
class Student
{
public:
    static const int MAX_SCORES = 10;
    int GetScore(int index);
    // ...
private:
    int mScores[MAX_SCORES];
};
```

```cpp
// Student.cpp
int Student::GetScore(int index)
{
    static_assert(
        sizeof(mScores) / sizeof(mScores[0]) == MAX_SCORES,
        "The size of scores vector ois not 10");
    // ...
}
```

## 베스트 프랙티스
* 최대한 assert보다 `static_assert`를 사용
    * 첫째 "모든 곳에 Static Assert를 쓰자"
    * 그러고 나면 "모든 곳에 Assert를 쓰자"
* assert를 사용한다면...
    * 여전히 프로그램을 실행시켜야 됨
    * 실행 도중에만 어서션을 포착. 그것도 오직 디버그 빌드에서만...
* `static_assert`를 사용한다면
    * 컴파일 중에 모든 문제를 즉시 알아챔
    * 또한 컴파일러처럼 생각하는 데도 도움이 됨!

## 문제1: 암시적 생성자

```cpp
// Dog.h
class Dog
{
private:
    std::string mName;
    int mAge;
};
// 컴파일러가 암시적 생성자를 만들어 냄
```

```cpp
// Main.cpp
#include "Dog.h"
int main()
{
    Dog myDog;
    Dog copiedMyDog(myDog);
    // ...
}
```

## 문제2: 빈 생성자 만들기

```cpp
// Dog.h
class Dog
{
public:
    Dog();
    Dog(std::string name);
private:
    std::string mName;  
};
```

```cpp
// Dog.cpp
#include "Dog.h"

Dog::Dog()
{
}

Dog::Dog(std::string name)
{
    // ...
}
```

## 어떻게 이 문제들을 해결할까? 

default 키워드!!!

## 예시: `default` 키워드

```cpp
// Dog.h
class Dog
{
public:
    Dog() = default; // 컴파일러가 만들어준 버전으로 쓰겠다는 의미
    Dog(std::string name);
private:
    std::string mName;
};
```

```cpp
// Dog.cpp
#include "Dog.h"

// 컴파일러가 Dog()를 대신 만들어 냄

Dog::Dog(std::string name)
{
    // 이하 코드 생략
}
```

## `default` 키워드
* `default` 키워드를 사용하면, 컴파일러가 특정한 생성자, 연산자 및 소멸자를 만들어낼 수 있음
* 그래서, 비어 있는 생성자나 소멸자를 구체화할 필요가 없음
* 또한 기본 생성자, 연산자 및 소멸자를 더 분명하게 표시할 수 있음
    * 명확하게 표현하는 것은 항상 좋다

## 컴파일러가 만든 생성자를 삭제하는 방법은?
* C++11 전

```cpp
// Dog.h
class Dog
{
public:
    Dog() = default;
    // ...
private:    // private에 넣기
    Dog(const Dog& other);
    // ...
};
```

```cpp
// Main.cpp
#include "Dog.h"

int main()
{
    Dog myDog;
    Dog copiedMyDog(myDog); // 에러
    // ...
}
```

* C++11 이후

```cpp
// Dog.h
class Dog
{
public:
    Dog() = default;
    Dog(const Dog& other) = delete; // 지운다!
    // ...
};
```

```cpp
// Main.cpp
int main()
{
    Dog myDog;
    Dog copiedMyDog(myDog); // 에러 (명확한 이유 존재)
    // ...
}
```

## `delete` 키워드
* 컴파일러가 자동으로 생성자를 만들어 주길 원치 않는다면 `delete` 키워드를 사용
* private 접근 제어자로 빈 생성자를 만드는 ㅌ츠릭은 이제 그만
* 올바른 에러 메시지도 나옴

## `default`/`delete`에 관한 베스트 프랙티스
* 컴파일러가 코드를 생성하는 암시적 방식에 기댈 필요가 없음
* 명확해야 함
* 어디에나 `default`/`delete` 키워드를 넣자!
