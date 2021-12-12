---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 8: 개체 지향 프로그래밍 (Object-Oriented Programming) 2"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 8: 개체 지향 프로그래밍 (Object-Oriented Programming) 2

## 복사 생성자
* 같은 클래스에 속한 다른 개체를 이용하여 새로운 개체를 초기화
    * 같은 크기, 같은 데이터
    
```
<class-name> (const <class-name>&);
```

```
Vector(const Vector& other);

Vector a; // 매개변수 없는 생성자를 호출
Vector b(a); // 복사 생성자를 호출
```

```cpp
// Vector.h
class Vector
{
public:
    Vector(const Vector& other); // 받는 매개변수가 나랑 같은 클래스에 속하는 다른 개체인 경우: 복사 생성자
private:
    int mX;
    int mY;
};

Vector::Vector(const Vector& other)
    : mx(other.mX)
    , mY(other.mY)
{
}
```

## 암시적(implicit) 복사 생성자
* 코드에 복사 생성자가 없는 경우, 컴파일러가 암시적 복사 생성자를 자동생성

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
    Vector() {}
    Vector(const Vector& other) // 암시적 복사 생성자 
        : mX(other.X)
        , mY(other.Y)
    {
    }
private:
    // ...
};
```

* 암시적 복사 생성자는 **얕은 복사**(shallow copy)를 수행
    * 멤버별 복사
    * 각 멤버의 값을 복사함
    * 개체인 멤버변수는 그 개체의 복사 생성자가 호출됨
    ```cpp
    Vector(const Vector& other)
        : mX(other.mX)
        , mY(other.mY)
    {
    }
    ```
* 클래스에 포인터형 변수가 있다면...?
    * 예: ClassRecord 클래스
    ```cpp
    // ClassRecord.h
    class ClassRecord
    {
    public:
        ClassRecord(const int* scores, int count);
        ~ClassRecord();
    private:
        int mCount;
        int* mScores;
    };
    ```

    ```cpp
    // ClassRecord.cpp
    ClassRecord::ClassRecord(const int* scores, int count)
        : mCount(count)
    {
        mScores = new int[mCount];
        memcpy(mScores, scores, mCount * sizeof(int));
    }

    ClassRecord::~ClassRecord()
    {
        delete[] mScores;
    }

    // 암시적 복사 생성자
    ClassRecord::ClassRecord(const ClassRecord& other)
        : mCount(other.mCount)
        , mScores(other.mScores)
    {
    }
    ```

    ```cpp
    // Main.cpp
    ClassRecord classRecord(scores, 5); // (1)
    ClassRecord* classRecordCopy = new ClassRecord(classRecord); // (2)
    delete classRecordCopy; // (3)
    ```
    (1) 스택 메모리에 classRecord 영역(mCount값, mScores 힙메모리 주소) 저장 (ex. 5, 2048), 힙 메모리에 classRecord scores 값 저장
    (2) 복사 생성자 호출: 힙 메모리에 classRecord 복사값 저장 (ex.5, 2048), 스택 메모리에 힙 메모리 주소 저장 (ex. 1024)
    (3) delete를 함으로써 2048이 가리키던 값이 삭제되므로 문제 발생 -> 얕은 복사의 문제점

## 사용자가 만든 복사 생성자
* 클래스 안에서 동적으로 메모리를 할당하고 있다면? 얕은 복사로 인한 문제가 발생할 위험이 크다. 포인터를 복사해오고 한쪽에서 수정하거나 지울 수 있는 가능성이 있기 때문.
* 직접 복사 생성자를 만들어서 **깊은 복사**(deep copy)를 해야 한다
    * 포인터 변수가 가리키는 **실제 데이터까지도 복사**

## 포인터의 깊은 복사
```cpp
// ClassRecord.cpp
ClassRecord::ClassRecord(const ClassRecord& other)
    : mCount(other.mCount)
{
    mScores = new int[mCount];
    memcpy(mScores, other.mScores, mCount * sizeof(int));
}

// Main.cpp
// scores에 5개의 점수가 들어있다고 가정하자
ClassRecord classRecord(scores, count);
ClassRecord* classRecordCopy = new ClassRecord(classRecord);
delete classRecordCopy;
```

## 함수 오버로딩
* 매개변수 목록을 제외하고는 모든게 동일
* 반환형은 상관 없음

```cpp
void Print(int score); // OK
void Print(const char* name); // OK
void Print(float gpa, const char* name); // OK
int Print(int score); // 컴파일 에러
int Print(float gpa); // OK
```

## 함수 오버로딩 매칭하기
* 오버로딩된 함수 중에 어떤 함수를 호출해야 하는지 판단하는 과정
* 함수 매칭 결과는 3개가 있음
    1. 매칭되는 함수를 찾을 수 없음 // 컴파일 에러
    2. 매칭되는 함수를 여러개 찾음 // 컴파일 에러
        * 누굴 호출할지 모호
    3. 가장 적합한 함수를 하나 찾음 // OK 

```cpp
void Print(int score); // (1)
void Print(const char* name); // (2)
void Print(float gpa, const char* name); // (3)

Print(100); // (1)
Print("AA"); // (2)
Print(4.0f, "AA"); // (3)
Print(77.5f); // (1), 컴파일 경고가 나올 수 있음
Print("AA", 4.0f); // 컴파일 에러
```

## 함수 매칭 순서

```cpp
int Max(int, int);
int Max(double, double);
int Max(const int a[], size_t);
int Min(int, int);
int Min(double, double);
int Min(const int a[], size_t);

int main()
{
    std::cout << Max(1, 3.14) << std::endl;
}
```
-> `Max(1, 3.14)`의 경우, `int Max(int, int)`, `int Max(double, double)` 모두 정확한 매치 1개, 표준 변환 1개가 일어나므로 모호한 호출이 된다. 컴파일 에러가 발생한다.

## 연산자
* 함수처럼 작동하는 부호
* C++에서는 프로그래머가 연산자를 오버로딩할 수 있음

## 연산자 오버로딩

```cpp
// Vector.h
class Vector
{
public:
    Vector operator+(const Vector& rhs) const; // 나 자신과 다른 Vector에 '+' 부호를 사용했을 때
private:
    int mX;
    int mY;
};
```

```cpp
// main.cpp
Vector v1(10, 20);
Vector v2(3, 17);
Vector sum = v1 + v2; // 연산자 오버로딩 호출
```

```cpp
// Vector.cpp
Vector Vector::operator+(const Vector& rhs) const
{
    Vector sum;
    sum.mX = mX + rhs.mX;
    sum.mY = mY + rhs.mY;

    return sum;
}
```

* C와 Java는 연산자 오버로딩을 지원하지 않음
* 부호는 같지만 여러가지 연산이 가능
```
int1 = int1 + int2; // 두 int형 변수 더함
float1 = float1 + float2;  // 두 float형 변수 더함
name = firstName + lastName;  // 두 string형 변수 더함
```
* 연산자는 오버로딩하는 방법은 두 가지
    * 멤버 함수
    * 멤버 아닌 함수

## 멤버 함수를 이용한 연산자 오버로딩
* 연산자 역시 메서드
    ```cpp
    Vector sum = v1 + v2;
    Vector sum = v1.operator+(v2); // 위와 동일
    std::cout << number;
    std::cout.operator<<(number); // 위와 동일
    ```
* 특정 연산자들은 멤버 함수를 이용해서만 오버로딩 가능
    * =, (), [], ->

## Vector의 `operator+()` 연산자를 오버로딩해보자
* 우리의 목표
    ```cpp
    Vector result = vector1 + vector2;
    Vector result = vector1.operator+(vector2);
    // 반환형               // 함수 이름 // 인자
    ```

    ```cpp
    Vector operator+(const Vector& rhs) const;
    ```

    ```cpp
    // Vector.cpp
    Vector Vector::operator+(const Vector& rhs) const
    {
        Vector sum;
        sum.mX = mX + rhs.mX;
        sum.mY = mY + rhs.mY;

        return sum;
    }
    ```

## 멤버 연산자를 작성하는 법
```
<return-type> <class-name>::operator<operator-symbol>(<argument-list>)
{
}
```

* 이런 짓을 할 수 있을까?

```cpp
std::cout << vector1.mX << ", " << vector1.mY <<< std:: endl; // private 멤버 변수 -> 컴파일 에러
```

* 첫 번째 시도

```cpp
std::cout << vector1.GetX() << ", " << vector1.GetY() << std::endl;
```

* 하지만 이렇게 하고 싶다!

```cpp
Vector vector1(10, 20);
std::cout << vector1; 
```

-> 10, 20 를 출력하고 싶다면,

```cpp
#include <iostream>
std::cout.operator<<(vector1); // cout이 어디에 들어있지?
void cout::operator<<(const Vector& rhs) const // ostream 클래스에 넣으면 되지 않나? NO.. 안됨
{
    // ...
}
```
 
그 대신 전역 함수를 만들어야 함

## 연산자 오버로딩에 필요한 `friend` 함수
* friend 함수는 멤버 함수가 아님
* 하지만 다른 클래스의 private 멤버에 접근할 수 있음

```cpp
class Vector
{
    frient void operator<<(std::ostream& os, const Vector& rhs);
}

void operator<<(std::ostream& os, const Vector& rhs)
{
    os << rhs.mX << ", " << rhs.mY;
}
```

## 멤버 아닌 연산자 오버로딩을 작성하는 법
```
// header
friend <return-type> operator<operator-symbol>(<argument-list>);

// cpp
<return-type> operator<operator-symbol>(<argument-list>)
{
}
```

## Vector를 출력하는 << 연산자

```cpp
// Vector.h
class Vector
{
    friend std::ostream& operator<<(std::ostream& os, const Vector& rhs);
public:
    // ...
private:
    // ...
};
```

```cpp
// Vector.cpp
std::ostream& operator<<(std::ostream& os, const Vector& rhs)
{
    os << rhs.mX << ", " << rhs.mY;
    return os;
}
```

```cpp
// main.cpp
Vector vector1(10, 20);
std::cout << vector1;
```

## 연산자 오버로딩과 `const`
* `const`를 쓰는 이유?
    * 멤버 변수의 값이 바뀌는 것을 방지
    * 최대한 많은 곳에 const를 붙일 것!
    * 지역(local) 변수에까지도
        * 모든 회사가 이 코딩표준을 따르지는 않음
        * 그러나 우리의 코딩표준에는 포함
* `const &`를 사용하는 이유?
    * 불필요한 개체의 사본이 생기는 것을 방지
    * 멤버 변수가 바뀌는 것도 방지

## 제한사항
* 오버로딩된 연산자는 최소한 하나의 사용자정의 형을 가져야 함
    ```cpp
    Vector operator+(const Vector& rhs) const;
    ```
* 오버로딩된 연산자는 피연산자 수를 동일하게 유지해야 함
    ```cpp
    Vector vector1;
    +vector1; // 불가능. + 연산자는 2개의 피연산자를 가짐.
                // 그러나 단항 + 연산자를 오버로딩할 순 있음.
    ```
* 새로운 연산자 부호를 만들 수 없음
     ```cpp
     Vector operator@(const Vector& rhs) const; // 불가능
     ```
* 오버로딩할 수 없는 연산자가 존재
    * '.', '.*', '::', '?:', 등

## 대입(assignment) 연산자
* `operator=`
* 복사 생성자와 거의 동일
    * 그러나 대입 연산자는 메모리를 해제해 줄 필요가 있을 수도 있다
* 복사 생성자를 구현했다면 대입 연산자도 구현해야 할 것임

## 암시적 `operator=`
* `operator=` 구현이 안되어 있으면 컴파일러가 자동으로 만들어 줌

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
    Vector() {}
    Vector& operator=(const Vector& rhs)
    {
        mX = rhs.mX;
        mY = rhs.mY;

        return *this;
    }
};
```

## 암시적 함수들을 제거하는 법

### 클래스에 딸려오는 기본 함수들
* 매개변수 없는 생성자
* 복사 생성자
* 소멸자
* 대입(=) 연산자

### 기본 생성자를 "지우는" 법
* 방법1

```cpp
// Vector.h
class Vector
{
public:
    Vector(const Vector& other); // 다른 생성자를 정의
private:
    int mX;
    int mY;
};
```

* 방법2

```cpp
// Vector.h
class Vector
{
public:
private:
    Vector() {}; // private에 넣는다

    int mX;
    int mY;
};
```

## 암시적 복사 생성자를 "지우는" 법

```cpp
// Vector.h
class Vector
{
public:
    Vector();
private:
    Vector(const Vector& other) {}; // private에 넣는다

    int mX;
    int mY;
};
```

## 암시적 소멸자를 "지우는" 법

```cpp
// Vector.h
class Vector
{
public:

private:
    ~Vector() {} // private에 넣는다 
    
    int mX;
    int mY;
};
```

## 암시적 operator=를 "지우는" 법

```cpp
// Vector.h
class Vector
{
public:

private:
    const Vector& operator=(const Vector& rhs); // private에 넣는다

    int mX;
    int mY;
};
```