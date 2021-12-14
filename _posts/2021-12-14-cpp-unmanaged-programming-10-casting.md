---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 10: 캐스팅 (형변환, Casting)"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 10: 캐스팅 (형변환, Casting)

## 암시적(Implicit) 캐스팅
* 컴파일러가 형을 변환해 줌
    * 단, 형변환이 허용되고 프로그래머가 명시적으로 형변환을 하지 않았을 경우에만.

    ```cpp
    int number1 = 3;
    long number2 = number1; // 암시적 캐스팅
    ```

## 명시적(Explicit) 캐스팅
* 프로그래머가 형변환을 위한 코드를 직접 작성
* C++ 캐스팅
    1. static_cast
    2. const_cast
    3. dynamic_cast (C++98, 모던 C++)
    4. reinterpret_cast
* 뭐 이렇게 많을까?

## C 스타일의 캐스팅을 기억하는가?

```c
int score = (int)someVariables;
```

* 위 코드는 대체 무얼 할까?
    * C++ 스타일 4개의 캐스팅 중 하나를 함
        * 뭔가 명확하지 못함
    * 명백한 실수를 컴파일러가 캐치하지 못함
        * C++ 캐스팅이 이런 문제를 해결
        * 그러나 기계는 C vs C++를 구분하지 않음
        * 기계는 C에 있는 정도만 안다...

## 정적 캐스팅(Static Casting)
-> 컴파일중에 캐스팅..
* C 스타일

```c
float number1 = 3.f;
int number2 = (int)number1; // (int)
Animal* myPet = new Cat(2, "Coco");
Cat* myCat = (Cat*)myPet;   // (Cat*)
```
-> 정적이므로 컴파일 도중에 모든게 결정된다! 이것을 C++ 스타일로 작성한다면,

* C++ 스타일

```cpp
float number1 = 3.f;
int number2 = static_cast<int>(number1); // static_cast<int>
Animal* myPet = new Cat(2, "Coco");
Cat* myCat = static_cast<Cat*>(myPet); // static_cast<Cat*>
```

## 리인터프리트(Reinterpret) 캐스팅
* C 스타일

```c
Animal* myPet = new Cat(2, "Coco");
unsigned int myPetAddr = (unsigned int)myPet; // (unsigned int) myPet의 주소를 int로 저장
cout << "address: " << hex << myPet;
```

* C++ 스타일

```cpp
Animal* myPet = new Cat(2, "Coco");
unsigned int myPetAddr = reinterpret_cast<unsigned int>(myPet); // reinterpret<unsigned int>
cout << "address: " << hex << myPet;
```

## 컨스트(Const) 캐스팅
* C 스타일

```c
void Foo(const Animal* ptr)
{
    // BAD CODE
    Animal* animal = (Animal*)ptr; // const 시그니쳐를 무시..
    animal->SetAge(5);
}
```

* C++ 스타일

```cpp
void Foo(const Animal* ptr)
{
    // BAD CODE
    Animal* animal = const_cast<Animal*>(ptr); // const_cast<Animal*> 사용
    animal->SetAge(5);
}
```

## 동적(Dynamic) 캐스팅
-> 실행중에 캐스팅..
* C 스타일

```c
Cat* myCat = (Cat*)myPet;
```

* C++ 스타일

```cpp
Cat* myCat = dynamic_cast<Cat*>(myPet);
```

## `static_cast`

1. 값
    * 두 숫자형 간의 변환
        * 값을 유지(단, 반올림 오차는 제외)
        * 이진수 표기는 달라질 수 있음
    * 예시1
        
        ```cpp
        int number1 = 3;
        short number2 = static_cast<short>(number1);
        ```
    * 예시2

        ```cpp
        float number1 = 3.f;
        int number2 = static_cast<int>(number1);
        ```
2. 개체 포인터
    * 변수형 체크 후 베이스 클래스를 파생 클래스로 변환
    * 컴파일시에만 형 체크 가능
    * 실행 도중 여전히 크래시가 날 수 있음
    * 예시1

        ```cpp
        Animal* myPet = new Cat(2, "Coco");
        Cat* myCat = static_cast<Cat*>(myPet); // OK
        Dog* myDog = static_cast<Dog*>(myPet); // 컴파일은 됨. (부모가 Animal인지만 체크함) 그러나 위험...
        myDog->GetDogHouseName(); // Dog 클래스의 멤버를 가지고 있지 않아 크래시가 날 수 있다!
        ```
    * 예시2

        ```cpp
        Animal* myPet = new Cat(2, "Coco");
        House* myHouse = static_cast<House*>(myPet); // 컴파일 에러
        myHouse->GetAddress();
        ```

## `static_cast` 사용법

```
static_cast<<type>>(<variable-name>)
```

```cpp
int number2 = static_cast<int>(number1); // float number1
Dog* myDog = static_cast<Dog*>(myPet); // Animal* myPet;
```
    
## `reinterpret_cast`
* 연관 없는 두 포인터형 사이의 변환을 허용
    * Cat* <-> House*
    * char* <-> int*
* 포인터와 포인터 아닌 변수 사이의 형 변환을 허용
    * Cat* <-> unsigned int
* **이진수 표기는 달라지지 않음**
    * A형의 이진수 표기를 그냥 B형인 것처럼 해석
* 위험해 보인다...

## `static_cast` vs `reinterpret_cast`

```cpp
int* signedNumber = new int(-10);
```

```cpp
// 컴파일 에러. 유효하지 않은 형 변환
unsigned int* unsignedNumber1 = static_cast<unsigned int*>(signedNumber);

// 컴파일됨. 하지만 값은 더 이상 -10이 아님
unsigned int* unsignedNumber2 = reinterpret_cast<unsigned int*>(signedNumber);
```

## `reinterpret_cast` 사용법

```
reinterpret_cast<<type>>(<variable-name>)
```

```cpp
// unsigned int myPetAddress;
Cat* myCat = reinterpret_cast<Cat*>(myPetAddress);
```

## `const_cast`
* `const_cast`로 형을 바꿀 수 없음
* const 또는 volatile 애트리뷰트를 제거할 때 사용

```cpp
Animal* myPet = new Cat(2, "Coco");
const Animal* petPtr = myPet;

// C style - 하면 다 된다
Animal* myAnimal1 = (Animal*)petPtr; // OK
Cat* myCat1 = (Cat*)petPtr; // OK 

Animal* myAnimal2 = const_cast<Animal*>(petPtr); // OK - const를 빼는 것 OK
Cat* myCat2 = const_cast<Cat*>(petPtr); // 컴파일 에러 - const Animal* -> Cat*으로 형 변환할 수 없음
```

* 포인터 형에 사용할 때만 말이 됨
    * 값 형은 언제나 복사되니까...
* 코딩 스타일
    * const_cast를 코드에서 쓰려고 한다면
    * 무언가 잘못하고 있는 것이다!
        
        ```cpp
        void Foo(const char* name) // 애초에 name을 변경하지 않겠다고 const로 선언했는데
        {
            char* str = const_cast<char*>(name); // 바꾸려는게 말이 안됨
            *str = 'p';
        }
        ```

## `const_cast`는 언제 사용할까?
* 써드파티 라이브러리가 const를 제대로 사용하지 않을 때

    ```cpp
    void WriteLine(char* ptr);  // 뭔가 별로인 외부 라이브러리

    void MyWriteLine(const char* ptr) // 우리 프로그램에 있는 함수
    {
        WriteLine(const_cast<char*>(ptr));
    }
    ```

## `const_cast` 사용법

```
const_cast<<type>>(<variable-name>)
```

```cpp
// const Animal* petPtr;
Animal* animal = const_cast<Animal*>(petPtr);
```

## C 스타일 vs `dynamic_cast`
* C 스타일

```c
Animal* myPet = new Cat();

// Compiles
Dog* myDog = (Dog*)myPet; 

// Compiles, but crashes
myDog->GetHouseName();
```

* `dynamic_cast`

```cpp
Animal* myPet = new Cat();

// Compiles, and returns NULL
Dog* myDog = dynamic_cast<Dog*>(myPet); // 사실은 Dog가 아니었으므로 NULL포인터가 들어온다!

// Compiles, GetHouseName() will not execute
if (myDog) // 진짜 Dog일때만 호출하겠어!
{
    myDog->GetHouseName();
}
```

## `static_cast` vs `dynamic_cast`
* `static_cast`

```cpp
Animal* myPet = new Cat();

// 컴파일 됨
Dog* myDog = static_cast<Dog*>(myPet);

// 컴파일 됨. 허나 실행 중 어떻게 동작할지 모름. 크래시?
myDog->GetHouseName();
```

* `dynamic_cast`

```cpp
Animal* myPet = new Cat();

// 컴파일 됨. 그리고 항상 NULL을 반환
Dog* myDog = dynamic_cast<Dog*>(myPet); 

// 컴파일 됨. GetHouseName()은 실행되지 않음
if (myDog) 
{
    myDog->GetHouseName();
}
```

## `dynamic_cast`
* 실행중에 형을 판단
* 포인터 또는 참조형을 캐스팅할 때만 쓸 수 있음
* 호환되지 않는 자식형으로 캐스팅하려 하면 NULL을 반환
    * 따라서, `dynamic_cast`가 `static_cast`보다 안전
* 그러나 이걸 쓰려면 컴파일 중에 RTTI(실시간 타입 정보, Real-Time Type Information)를 켜야 함
* C++ 프로젝트에서 기본적으로는 RTTI를 끄는 것이 보통

## 동적 캐스팅
* 실제로는 `dynamic_cast`과 `static_cast`이 동일하게 동작
* RTTI가 켜져 있으면 다름
* RTTI가 왜 안 켜져 있을까?
    * 성능을 중요시하는 업계에서 C++을 사용하기 때문에 RTTI를 켜서 오는 과부하를 버틸 수 없기 때문

## `dynamic_cast` 사용법

```
dynamic_cast<<type>>(<variable-name>)
```

```cpp
// Animal* myPet;
Dog* myDog = dynamic_cast<Dog*>(myPet);
```

## 캐스팅 규칙

* 규칙: 제일 안전한 것 -> 가장 위험한 것

1. 기본적으로 `static_cast`를 쓸 것
    * `reinterpret_cast<Cat*>` 대신 `static_cast<Cat*>`
        * 만약 Cat이 Animal이 아니라면 컴파일러가 에러를 뱉음
2. `reinterpret_cast`를 쓸 것
    * 포인터와 비포인터 사이의 변환
        * 이걸 정말 해야 할 때가 있음
    * 서로 연관이 없는 포인터 사이의 변환은 그 데이터형이 맞다고 정말 확신할 때만 할 것
3. 내가 변경권한이 없는 외부 라이브러리를 호출할 때만 `const_cast`를 쓸 것