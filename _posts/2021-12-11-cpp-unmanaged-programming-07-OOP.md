---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 7: 개체 지향 프로그래밍 (Object-Oriented Programming) 1"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 7: 개체 지향 프로그래밍 (Object-Oriented Programming) 1

## Java vs C++
* Java가 OOP에 관해서 더 엄격하다
* C++에서는 OOP와 OOP 아닌 것을 섞어 쓸 수 있다
* C++은 언매니지드 언어이기 때문에 뭔가 특별한 것도 있다

## OOP란
* "사람들이 세상을 바라보는 방식" = OOP의 핵심 개념
* 직관적

## 클래스

### 멤버 변수의 접근권한
* C++의 기본 접근권한은 `private`
```cpp
class Vector
{
    int mX; // private 멤버 변수
    int mY; // private 멤버 변수
};
```

cf. Java에서 접근권한 제어 키워드를 생략하면 public도 private도 아니다. 해당 패키지 내에서 접근 가능...

### 접근 제어자 (Access Modifier)
* `public`
    * 누구나 접근 가능
* `protected`
    * 자식 클래스에서 접근 가능
* `private`
    * 해당 클래스에서만 접근 가능(개체에서가 아님)

## 보통 제어자별로 C++ 멤버들을 그룹 지음
```cpp
class SomeClass
{
public:
    int PublicMemeber;
protected:
    int mProtectedMember;
private:
    int mPrivateMember1;
    int mPrivateMember2;
};
```

### 개체 생성
```cpp
// 스택(stack) 메모리에 만들기 (빠름)
Vector a;

// 힙 메모리에 만들기 (느림)
Vector* b = new Vector();
```

* Vector a; 를 살펴보자
    * 스택에 8byte 메모리 할당
* Vector* b = new Vector(); 를 살펴보자
    * 힙에 8byte 메모리 할당
    * 스택 주소에 힙의 주소를 저장

## 스택
* 예약된 로컬 메모리 공간(작음, 일반적으로 1MB 이하)
* 함수 호출과 반환이 이 메모리에서 일어남
* 단순히 스택 포인터를 옮김
    * 메모리를 할당 및 해제할 필요가 없음
    * 스택에 할당된 메모리는 범위(scope)를 벗어나면 사라짐
    * 변수와 매개변수를 위해 필요한 크기는 컴파일 도중에 알 수 있음
* 하지만 스택에 큰 개체를 많이 넣으면
    * 스택 오버플로우(overflow)가 발생할 수 있음
    * 성능이 느려 질 수도 있음

```cpp
// a,b를 참조로 받는 이유: 데이터 복사를 하지 않겠다
// a,b를 const로 받는 이유: 데이터를 내부에서 변경하면 안된다
Vector AddVector(const Vector& a, const Vector& b)
{
    Vector result; 
    result.mX = a.mX + b.mX;
    result.mY = a.mY + b.mY;

    return result;
}

voic Foo()
{
    Vector c = AddVector(a, b);
}
```
-> 스택의 작동과정: 
1. 스택에 Foo() 영역에서 필요한 메모리들이 잡힌다.
2. AddVector() 호출함으로써 스택에 AddVector() 영역 메모리가 생긴다.
3. AddVector()가 끝나면 사라진다.
4. 원래의 Foo() 영역으로 돌아간다.

## 힙
* 전역 메모리 공간 (큼, ~GBs)
* 비어 있고 연속된 메모리 블록을 찾아야 함
* 프로그래머가 메모리를 직접 할당 및 해제해야 함
    * 그렇지 않으면, 메모리 누수 발생
    * C++은 언매니지드 언어이므로 직접 관리해줘야 함

오브젝트가 충분히 작으면 스택을 사용하는 게 좋다. 어느 범위에서만 쓰는 오브젝트는 더더욱!

```cpp
void PrintVectors(const Vector& a, const Vector& b)
{
    Vector* result = new Vector; // 힙에 메모리 생성. new를 함수안에서 하는 것은 좋지 않은 코드...
    result->mX = a.mX + b.mX;
    result->mY = a.mY + b.mY;

    // 출력...
    delete result; // new로 생성했을 경우 delete 잊지 말기
}

void Foo()
{
    PrintVectors(a, b);
}
```
-> 힙의 작동과정:
1. 스택에 Foo() 영역에서 필요한 메모리들이 잡힌다.
2. PrintVectors() 호출: 스택에 PrintVectors() 영역에서 필요한 메모리들이 잡힌다. 그 중, 힙의 메모리 주소를 저장하는 부분도 있다.
3. result Vector -> 4byte씩 두개 필요하므로 힙에 8byte 메모리가 할당되고, 해당 힙 메모리 주소가 스택에 잡힌다.
4. delete를 통해 할당되었던 메모리가 누수되지 않도록 한다.

## 개체 배열 (Array)
```cpp
// 10개의 벡터 개체를 힙에 만듦
Vector* list = new Vector[10];

// 10개의 포인터를 힙에 만듦
Vector** list = new Vector*[10];
```

## 개체 소멸
```cpp
Vector* a = new Vector;
Vector* list = new Vector[10];
// ...

delete a; // 메모리가 즉시 해제됨
a = NULL; // 안해도 됨

delete[] list; // 주의: []를 반드시 넣어야 한다!
list = NULL; // 안해도 됨
```

## `new`를 사용한 뒤에는 꼭 `delete`를 쓰자
* 메모리 누수 방지를 위해 `delete`가 필수
* Java나 C#은?
    * 마법적인 가비지 컬렉터
    * 메모리는 **나중에** 해제될 수도 있음
    * 메모리 직접관리 vs 편의
    * 속도는 C++ vs 안전은 Java, C#

## 멤버 변수 초기화
```cpp
class Vector
{
private:
    int mX; 
    int mY;
};

Vector a;
Vector* b = new Vector(); // mX, mY에는 가비지값이 들어 있음
```

### 기본값
* Java, C#
    * x, y는 모두 0으로 초기화 됨
* C++
    * 값이 초기화되지 않음
    * 전에 메모리 공간에 저장되어 있던 값을 사용
    * 왜? 성능때문에!

## 생성자 (Constructor)
```cpp
class Vector
{
public:
    // 매개변수 없는 생성자
    Vector()
    {
        mX = 0; 
        mY = 0; 
    }
private:
    int mX;
    int mY;
};
```
-> C++은 멤버변수는 거의 private이라서 밑에 놓고, public을 위에 놓는다.

## 초기화 리스트 (Initializer List)
* 대입
    * 초기화가 된 이후에 실행된다

```cpp
class Vector
{
public:
    Vector()
    {
        mX = 0; // 대입
        mY = 0; // 대입
    }
private:
    int mX;
    int mY;
};
```

```cpp
class X
{
    const int mNameSize;
    AnotherClass& mAnother;

    X(AnotherClass& another)
    {
        mNameSize = 20; // 에러 (const는 초기화시 정의되어야 함)
        mAnother = another; // 에러 (참조 변수는 초기화시 정의되어야 함)
    }
};
```

* 초기화 리스트
    * 오브젝트가 만들어질 때 초기화된다
    * 멤버 변수를 대입 없이 초기화
    * 상수나 참조 변수도 초기화 가능

```cpp
class Vector
{
public:
    Vector()
        : mX(0) // 초기화 리스트
        , mY(0) // 초기화 리스트
    {
    }
private:
    int mX;
    int mY;
};
```

```cpp
class X
{
    const int mNameSize;
    AnotherClass& mAnother;

    X(AnotherClass& another)
        : mNameSize(20)
        , mAnother(another)
    {
    }
};
```

## 더 나은 Vector 클래스를 만들어 보자
* 헤더 파일
```cpp
// vector.h
class Vector // 클래스 정의
{
public:
    Vector();
    Vector(int x, int y);
private:
    int mX;
    int mY;
};
```

* Cpp 파일

```cpp
// vector.cpp
Vector::Vector() // 클래스 구현
    : mX(0)
    , mY(0)
{    
}

Vector::Vector(int x, int y)
    : mX(x)
    , mY(y)
{
}
```

## 기본 생성자
* 기본 생성자는 매개변수를 받지 않음
* 클래스에 생성자가 없으면 컴파일러가 기본 생성자를 자동적으로 만들어 줌
* 이렇게 자동적으로 만들어진 생성자는
    * 멤버 변수를 초기화하지 않음
    * 하지만 **모든 포함된 개체의 생성자를 호출**

```cpp
Vector() {}
```

## 컴파일러가 하는 일?
* 사례1 - Vector 클래스에서 생성자가 없는 경우
```cpp
// Vector.h
class Vector
{
private:
    int mX;
    int mY;
};
```
-> 
컴파일러
->
```cpp
// Vector.obj
class Vector 
{
public:
    Vector() {} // 컴파일러가 기본 생성자를 만들어 준다
private:
    int mX;
    int mY;
};
```
* 사례2 - Vector 클래스에 생성자가 있는 경우
```cpp
// Vector.h
class Vector
{
public:
    Vector(int x, int y); // 매개변수가 있는 생성자
private:
    int mX;
    int mY;
}
```
->
컴파일러
->
```cpp
// Vector.obj
class Vector
{
public:
    Vector(int x, int y); // 기본 생성자 없음
private:
    int mX;
    int mY;
}
```

## 생성자 오버로딩(Overloading)
* 여러 개의 생성자를 만들 수 있음
    * 같은 이름
    * 인자의 개수나 자료형은 다름
* 기본 생성자
    ```cpp
    Vector() : mX(0), mY(0) {}
    Vector a; // 기본 생성자 호출
    ```
* 매개변수를 가지는 생성자
    ```cpp
    // 2개의 매개변수를 가지는 생성자
    Vector(int x, int y)
        : mX(x)
        , mY(y)
    {
    }

    Vector a(1, 3); // 매개변수 목록이 일치하는 생성자 호출
    ```

## 소멸자 (Destructor)
```cpp
// vector.h
class Vector
{
public:
    ~Vector(); // 클래스 이름 앞에 '~'를 붙여서 표기
private:
    int mX;
    int mY;
};

// vector.cpp
Vector::~Vector()
{
}
```
* 개체가 지워질 때 호출됨
* 가상 소멸자에 대해선 나중에 배워볼 것임
* Java
    * 자동으로 가비지를 수집하기 때문에 소멸자 없음
* C++
    * C++ 클래스는 그 안에서 동적으로 메모리를 할당할 수 있음
    * 그런 경우 필히 소멸자에서 메모리를 직접 해제해줘야 한다

## 클래스 안에서의 동적 메모리 할당
```cpp
// MyString.h
class MyString
{
public:
    MyString();
private:
    char* mChars; // 포인터가 있을 땐 대부분 메모리 할당이 일어날 것이라고 생각하면 된다
    int mLength;
    int mCapacity;
};
```
```cpp
// MyString.cpp
MyString::MyString()
    : mLength(0)
    , mCapacity(15) // 클래스 안에서 추가로 할당한 메모리
{
    mChars = new char[mCapacity + 1];
}
```
```cpp
// Main.cpp
void Foo() // main이 호출한다고 가정
{
    MyString name;
    // ...
}
```
이렇게 소멸자가 없을 경우, 실행하면 어떤 일이 일어나는지 보자.
1. 스택에 main() 영역
2. Foo() 호출 -> 스택에 Foo() 영역
3. Foo()에서 `MyString name;` -> name은 스택에 들어감
4. name 스택에서 mLength, mCapacity 는 값 저장, mChars는 힙에 메모리 할당 후 힙 메모리 주소 값을 저장
5. Foo()가 끝난 후... 스택에 있던 Foo() 영역 메모리는 사라지고, 소멸자를 호출하지 않았기 때문에 힙에는 그대로 남아 있음 -> 메모리 누수 발생!

## 메모리 해제를 꼭 기억하자!

```cpp
// MyString.h
class MyString
{
public:
    MyString();
    ~MyString(); // 소멸자
private:
    char* mChars;
    int mLength;
    int mCapacity;
};
```
```cpp
// MyString.cpp
MyString::~MyString()
{
    delete[] mChars;
}
```

## Vector 클래스의 멤버 함수
```cpp
// Vector.h
class Vector
{
public:
    void SetX(int x);
    void SetY(int y);
    int GetX() const; 
    int GetY() const;
    void AdD(const Vector& other);
private:
    int mX;
    int mY;
};
```

## `const`란
* 바꿀 수 없는 것을 말함
    ```cpp
    // const 변수
    const int LINE_SIZE = 20;
    LINE_SIZE = 10; // 컴파일 에러

    // const 메서드. 해당 개체 안의 어떠한 것도 바꾸지 않음
    int GetX() const;
    ```

## Const 멤버 함수
* 멤버 변수가 변하는 것을 방지
    ```cpp
    int GetX() const
    {
        return mX; // OK
    }

    void AddConst(const Vector& other) const
    {
        mX = mX + other.mX; // 컴파일 에러
        mY = mY + other.mY; // 컴파일 에러
    }
    ```
