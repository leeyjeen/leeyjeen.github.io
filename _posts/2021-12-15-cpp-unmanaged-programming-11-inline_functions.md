---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 11: 인라인 함수 (Inline Functions)"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 11: 인라인 함수 (Inline Functions)

## 함수 호출할 때
* 함수는 메모리 안에 "할당"되어 있음
* 함수를 호출하기 위해 필요한 단계들
    1. 변수들을 스택에 푸시(push)
    2. 함수 주소로 점프
    3. 함수를 실행
    4. 호출자 함수로 다시 점프
    5. 1번 단계에서 넣어뒀던 변수들을 팝(pop)
* 여러 단계가 있다...
    * 그래서 좀 더 느림
    * CPU 캐시에 최적이 아닐 수도 있음
    * 모던 CPU 아키텍처에서는 더 느림
    * 그런데 아직도 이런 잘못된 조언이 떠돌아 다닌다
        * "모든 걸 함수로 만들라!!"
    * 그러나...
        * 가독성을 위해 함수를 쓰면 좋은 경우들이 있음
        * 하지만 연산 그 자체는 매우 단순하기에
        * 함수호출에 필요한 오버헤드를 떠맡기에는 좀 부담이 됨
        * 해법이 없을까? -> **인라인 함수**

## 인라인(inline) 함수

* 인라인 멤버함수

```cpp
// Animal.h
class Animal
{
public:
    Animal(int age);
    inline int GetAge() const; // inline! 
};

int Animal::GetAge() const
{
    return mAge;
}
```

* 멤버 아닌 인라인 함수

```cpp
// Math.h
inline int Square(int number) // inline!
{
    return number * number;
}
```

## 인라인 함수의 동작 원리
* 사실상 복붙과 비슷
    * 함수호출 대신에...

    ```cpp
    // Main.cpp
    Cat* myCat = new Cat(2, "Coco");
    int age = myCat->GetAge();
    ```
    -> 컴파일을 하면 -> 

    ```cpp
    Cat* myCat = new Cat(2, "Coco");
    int age = myCat.mAge; // 컴파일러가 이렇게 바꿔준다! (컴파일 도중에)
    ```

* 매크로(MACRO)와 매우 비슷한 개념
    * `#define`

    ```cpp
    #define SQUARE(x) (x)*(x)

    int main()
    {
        int result = SQUARE(3); // int result = (3)*(3); 으로 복붙해줌. 컴파일하기 전에 사전처리기가 소스코드 돌면서 텍스트 복붙 후 컴파일.
    }
    ```

## 그럼 대신 매크로를 써도 되는가?
* 안 된다...
* 매크로는 디버깅하기 힘듦
    * 콜스택에 함수 이름이 안 보임
    * 중단점(breakpoint)도 설정 불가능
* 매크로는 범위(scope)를 준수하지 않음
* 정말 매크로를 쓸 이유가 있지 않는 한 인라인 함수를 쓰자!

## 인라인 함수의 동작 원리

* 예시1

```cpp
// 멤버 아닌 함수
inline int Square(int number)
{
    return number * number;
}

int main()
{
    int number = 2;
    int result = Square(number); // 
    return 0;
}
```

-> 컴파일러 ->

```cpp
int main()
{
    int number = 2;
    int result = number * number; // 
    return 0;
}
```

* 예시2

```cpp
// 멤버함수
class Animal
{
public:
    inline int GetAge();
};

int Animal::GetAge()
{
    return mAge;
}

// Main.cpp
Cat* myCat = new Cat(2, "Coco");
int age = myCat->GetAge();
```

-> 컴파일러 ->

```cpp
// Main.cpp
Cat* myCat = new Cat(2, "Coco");
int age = myCat->mAge;
```

## 인라인 함수를 쓸 때 주의점
* inline 키워드는 힌트일 뿐
    * **실제로는 인라인 안될 수도 있음**
    * **컴파일러가 자기 맘대로 아무 함수나 인라인할 수도 있음**
* 인라인 함수 구현이 헤더 파일에 위치해야 함
    * 복붙을 하려면 컴파일러가 그 구현체를 볼 수 있어야 하기에...
    * 각 cpp 파일은 따로 컴파일 됨
    * 따라서 b.h를 인클루드하는 a.cpp 파일을 컴파일할 때, 컴파일러는 b.cpp에 뭐가 들어 있는지 모름
* 간단한 함수에 적합
    * 특히 getter나 setter에...
* 실행 파일의 크기가 증가하기가 쉬움
    * 동일한 코드를 여러 번 복붙하니까
    * 남용하지 말 것
    * 실행파일이 작을수록 CPU 캐시하고 잘 작동 -> 속도가 빨라질 수 있음

