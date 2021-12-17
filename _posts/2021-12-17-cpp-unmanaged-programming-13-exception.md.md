---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 13: 예외(Exception)"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 13: 예외(Exception)

## C++에서의 예외
* C++도 예외를 "지원"
* 허나, C++에서는 예외의 중요성이 좀 떨어짐
* (개인적인 의견) 언어를 불문하고 심하게 남용되고 있음
* 따라서 올바른 사용법을 중점적으로 살펴보자

## Java는 어딜 봐도 예외 투성이
* 많아도 너무 많다
    * if문으로 수많은 예외 발생 상황을 처리할 수도 있는데..
    * 예외로부터 안전한(exception-safe) 코드를 작성하기 힘듦
    * 사람의 사고방식은 선형적

## C++의 예외는 어디서 올까?
* 프로그래머가 만든 것
* C++ 표준 라이브러리가 많은 예외를 던지기도 함
* 하지만 Java나 C#에서 당연히 있는 예외가 C++에는 없음

## os "예외" vs C++ 예외

OS "Exception" | std::exception
--- | ---
비동기적 | 동기적
윈도우: 구조적 예외 처리(SEH) <br> 리눅스: POSIX 신호, Faults, Traps, Aborts | C++ STL
플랫폼마다 다름 | 모든 플랫폼에 공통
오버헤드 비교적 많음, 더 느림 | 오버헤드가 비교적 적음

## 대부분의 예외는 불필요
* 이론적으로 말하면 생성자의 경우에는 유용함

```cpp
Inventory::Inventory(int count)
{
    mSlots = new int[count];
}

// mSlots가 NULL이면 어떻게 해야 할까?...
// 생성자의 경우 문제가 있다는 것을 말할 방법이 없다
```

## 생성자 예외 던지기

```cpp
// Exception.h
#include <exception>

struct SlotNullException : public std::exception
{
    const char* what() const throw()
    {
        return "Slot is NULL";
    }
};
```

```cpp
// Inventory.cpp
Inventory::Inventory(int slotCount)
{
    mSlots = new int[slotCount];
    if (mSlots == NULL)
    {
        throw SlotNullException();
    }
}
```

```cpp
// Main.cpp
Inventory* myInventory = nullptr;
try
{
    myInventory = new Inventory(5);
}
catch (const SlotNullException& e)
{
    std::cerr << e.what() << std::endl;
}
catch (const std::exception& e)
{
    //  다른 에러들
}
// 아직 올바른 에러 처리는 아니다. myInventory 생성 실패했을 경우 myInventory는 어떻게 할 것인지...?
```

## 그럼 생성자에서 쓰는 예외는 괜찮은가?
* 괜찮다. 하지만 예외 처리 기능이 꺼져 있는게 대부분 C++ 컴파일러의 기본 옵션이다.
    * 느리다
    * 아직도 C++를 쓰는 업계는 빠른 성능이 중요한 곳
* 그리고 메모리 부족 때문에 예외가 발생하면 어떻게 할까?
    * 프로그램 종료?
        * 크래시랑 무엇이 다른가..
    * 생성자 호출 재시도?
        * 하지만 이미 메모리가 동났다..

## C++ vs 다른 언어들
* C++의 예외 처리를 사용하는 회사에서 근무할지도 모름
* 그래서 C++도 예외 처리를 지원할 수 있다는 정도만 기억하자
* 다른 언어들은 지금까지 예외를 광범위하게 사용해 왔음
* 하지만 큰 변화가 일어나고 있음
    * 왜?
    * 먼저 역사부터

## 누가 그러길...
1. 에러 코드는 가독성이 떨어져서 실수하기 쉽다
2. 예외는 코드의 유지보수를 쉽게 만든다
3. 예외는 코드를 더 안전하고 탄탄하게 만든다

## 진실
* 에러 코드는 예외 처리 못지 않게 가독성이 높음
* 혹은 더 높을 수도 있음

## 누가 그러길...
* 예외처리를 사용하면 예외가 발생하는 경우에도 프로그램이 계속 돌아간다
* 그래서 서비스가 다운되지 않고 계속 실행된다

## 예외를 쓰면 소프트웨어가 더 탄탄해지나?
* 뒷받침할 증거나 데이터가 없음
* 대부분의 탄탄한 프로그램들은 C와 같은 언어와 에러 코드를 합친 작품

## 하지만 그 말이 맞을 수도 있잖아?
* 증거가 없다고 해서 틀린 것은 아님
* 그래서 프로그래머들이 20년 넘게 예외를 써 본 뒤에야 깨달은 점을 제시할 예정
* 한 줄 요약: 대부분의 프로그래머들은 예외를 제대로 처리하지 못함

## 예외 안전성 - Java
* 안전하지 않은 코드

    ```java
    public class CoffeeShop
    {
        // ...
        void SellWithPoint(Customer customer, int itemID, int points)
            throws EmptyItemException {
            deductPoint(customer, points); // 포인트는 이미 뺌

            if (isEmpty(itemID)) {
                throw new EmptyItemException();
            }
        }
    }
    ```
* 안전한 코드

    ```java
    public class CoffeeShop
    {
        // ...
        void SellWithPoint(Customer customer, int itemID, int points)
            throws EmptyItemException {
            if (isEmpty(itemID)) {
                throw new EmptyItemException();
            }
            deductPoint(customer, points);
        }
    }
    ```

## 어떻게든 프로그램은 돌고 있다...
* 예외는 프로그램을 좀비로 만듦
* 100% 예외 안전성을 가지는 프로그램을 짜는 것은 쉬운 일이 아니다
    * 사람은 무엇을 읽어도 위에서부터 아래로 차례대로 읽음
    * 100줄짜리 함수에서 빠져나가는 곳이 100개라면 그걸 과연 사람이 따라갈 수 있을까?

## 어떤 함수가 예외를 처리하지?
* 수많은 언어들에서 어떤 함수가 무슨 예외를 던지는지 알기 힘듦
* 함수 헤더에 그 함수에서 던지는 예외를 표기하지 않기 때문
* 예외: Java

## 예외 처리 - C++

```cpp
// ExceptionExample.h
struct SampleException : public std::exception
{
    const char* what() const throw()
    {
        return "Print message here";
    }
};

// ExceptionExample.cpp
void ExceptionExample::Function1()
{
    try
    {
        Function2();
    }
    catch (const SampleException& e) // 이 exception을 잡기 위해 Function2 안에서 호출하는 모든 함수들을 찾아봐야 한다...
    {
        cerr << e.what() << endl;
    }
}

void ExceptionExample::Function2()
{
    Function3();
}

void ExceptionExample::Function3()
{
    Function4();
}

void ExceptionExample::Function4()
{
    // ...
    throw SampleException(); //...
    // ...
}
```

### 다 기억할 수 있는가?
* 예외를 발생시키는 코드를 찾기 위해선 트리에 있는 모든 함수를 하나하나 읽어봐야 함
* Java는 함수 시그니처 바로 옆에 그 함수에서 나오는 예외 목록을 적어주기에 그나마 나은 편

## Java의 최근 예외 처리 동향
* 요즘 Java 프로그래머들은 catch 블록에서 그냥 catch(Exception)을 사용

    ```java
    try
    {
        Cat myCat = new Cat(5, "Coco");
    }
    catch (Exception e) // 모든 RuntimeException을 잡아냄
    {
        // 생략
    }
    ```

## 정말 괜찮은 해결책이 나올 뻔 했다
* WCF(Windows Communication Foundation)
    * 공식: 서비스 지향 애플리케이션 개발을 위한 프레임워크
    * 비공식: .NET 프레임워크의 계승자(더 이상 일어나지 않을 일)
    * 언어 단에서 트랜잭션(transaction) 지원
* 일단 예외를 던진 개체를 사용하거나 그 개체에 접근할 수 없음
    * 접근을 시도하면 또 다른 예외 발생

## 웹은 에러코드로 돌아갔다
* 웹 요청은 상태코드(status code)와 바디(body)를 반환
* 만약 상태코드가 20x라면,
    * 바디가 있음
* 만약 상태코드가 에러코드라면 (4xx, 5xx),
    * 바디가 비어 있을 수 있음

## 웹 애플리케이션에서의 에러 처리
* 다음을 통해 C++에도 적용 가능
    * Struct
    * Class

### 예시: 웹 요청 방식

```cpp
// Error.h
enum EError
{
    SUCCESS,
    ERROR,
};

struct ErrorCode
{
    EError Status;
    int Code;
};

template <typename T> struct Result
{
    ErrorCode Error;
    T Value;
};

// Main.cpp
Result<const char*> result;

result = record.GetStudentIDByName("Pope Kim");

if (result.Error.Status == ERROR)
{
    cout << "Error Code " << result.Error.Code << endl;
}
else
{
    cout << "Student ID is " << result.Value << endl;
}
```

## 적절한 예외 처리 전략
1. 유효성 검사/예외는 오직 경계에서만
    * 밖에서 오는 데이터를 제어할 수 없기 때문
    * 예) 외부에서 들어오는 웹 요청, 파일 읽기/쓰기, 외부 라이브러리
2. 일단 시스템에 들어온 데이터는 다 올바르다고 간주할 것
    * assert를 사용하여 개발 중 문제를 잡아내고 고칠 것
3. 예외 상황이 발생할 때는 NULL을 능동적으로 사용할 것
    * 하지만 기본적으로 함수가 NULL을 반환하거나 받는 일은 없어야 함
    * 코딩 표준: 만약 NULL을 반환하거나 받는다면 함수의 이름을 잘 지을 것

## 경계에서의 예외 처리

```cs
// C# 방식
string ReadFileOrNull(string filename)
{
    if (!File.Exists(filename))
    {
        return null;
    }

    try
    {
        return File.LoadAllText(filename);
    }
    catch (Exception e)
    {
        return null;
    }
}
```

## 일단 시스템에 들어온 데이터는 다 올바르다고 간주
* assert를 사용하여 개발 중 문제를 잡아내고 고칠 것

    ```cpp
    int ConvertToHumanAge(const Animal* pet)
    {
        Assert(pet != NULL);
        // ...
    }
    ```

## 코딩 표준1
* 매개 변수가 NULL이 될 수 있으면 매개 변수 이름 뒤에 'OrNull'을 붙인다

    ```cpp
    const char* GetCoolName(const char* startWithOrNull) const // 매개변수 이름을 startWithOrNull로: Null값이 들어와도 안에서 알아서 처리하겠다는 의미
    ```

## 코딩 표준2
* 함수가 NULL을 반환할 수 있으면, 함수 이름 뒤에 'OrNull'을 붙인다

    ```cpp
    const char* GetHobbyOrNull() const;
    ```

## 예외는 만병통치약이 아니다
* 동일한 프로그래머가 로직과 예외를 모두 작성
    * 로직이 잘못돼 있으면 예외도 틀렸을 가능성이 높음
    * 동일한 프로그래머가 작성한 유닛 테스트가 한계를 갖는 이유
* 양질의 소프트웨어는 예외가 아니라 철저한 테스트 계획에서 만들어진다

## 개발 중 버그 잡기
* 이걸 라이브 서버에서 해서는 안됨
    * 하지만 예외 때문에 그런 유혹을 받음
* 개발 중 코드에서 버그를 잡기 위해서는 assert를 사용
    * 전제 조건, 사후 조건, 그리고 불변값 확인
    * 또한 assert가 실패하면 올바른 호출 스택을 볼 수 있음
* 품질 관리(QA)가 제대로 이루어지지 않았다면 차라리 소프트웨어가 뻗어 버리게 만드는 게 좋다
    * 문제가 바로 드러나기에 바로 고칠 수 있기 때문
    * 하지만, 안전과 생명에 관련되는 소프트웨어라면 예외

## 시체보다는 좀비가 낫지 않나요?
* 예외가 없는 경우
    * 프로그램에서 크래시 발생
    * 크래시에서 메모리 덤프(dump)를 얻을 수 있음
    * 개발자는 덤프 파일을 열어 디버깅할 수 있음
* 예외가 있는 경우
    * 왼쪽에 언급한 어떠한 것도 안 할 가능성이 높음
    * 아마 어딘가에서 printf로 찍은 로그(log)나 보며 디버깅 할 것...

