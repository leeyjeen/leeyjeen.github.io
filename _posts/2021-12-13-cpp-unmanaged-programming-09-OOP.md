---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 9: 개체 지향 프로그래밍 (Object-Oriented Programming) 3"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 9: 개체 지향 프로그래밍 (Object-Oriented Programming) 3

## 상속

```cpp
// Animal.h
class Animal
{
public:
    Animal(int age);
private:
    int mAge;
};

// Cat.h
class Cat : public Animal
{
public:
    Cat(int age, const char* name);
private:
    char* mName;
};
```

```cpp
// Cat.cpp
Cat::Cat(int age, const char* name)
    : Animal(age)
{
    size_t size = strlen(name) + 1;
    mName = new char[size];
    strcpy(mName, name);
}
```

## 상속이란?
* 다른 클래스의 특성들을 내려 받음
    * 베이스(base) 클래스 = 부모 클래스
    * 파생(derived) 클래스 = 자식 클래스
* 파생 클래스의 개체는 다음의 것들을 가짐
    * 베이스 클래스의 멤버 변수
    * 베이스 클래스의 멤버 메서드
    * 자신의 생성자와 소멸자
* 파생 클래스는 멤버 변수 및 메서드 추가 가능

## Java와 C++의 차이
* C++

```cpp
class Cat : public Animal
{
public:
    Cat(int age, const char* name);
private:
    char* mName;
};

Cat::Cat(int age, const char* name)
    : Animal(age)
{
    size_t size = strlen(name) + 1;
    mName = new char[size];
    strcpy(mName, name);
}
```

* Java

```java
public class Cat extends Animal
{
    private String Name;
    public Cat(int age, String name)
    {
        super(age); // Animal(int age)을 호출
        Name = name;
    }
}
```

## 파생 클래스를 정의하는 법

```
class <derived class name> : <access - modifier> <base class name> {};
```

```cpp
class Cat : public Animal {};
class Honda : private Car {};
class AndroidPhone : protected Phone {};
```

## 파생 클래스의 접근 제어자
* Java와 달리 상속시 베이스 클래스 멤버의 접근 수준을 결정할 수 있다
    * public 상속
        ```cpp
        // Animal.h
        class Animal
        {
        public:
            Animal(int age);
            void Move(); // myCat.Move(); OK
        private:
            int mAge;
        };

        // Cat.h
        class Cat : public Animal // public
        {
        public:
            Cat(int age, const char* name);
        private:
            char* mName;
        };
        ```
    * private 상속
        ```cpp
        // Animal.h
        class Animal
        {
        public:
            Animal(int age);
            void Move(); // myCat.Move(); NOT OK
        private:
            int mAge;
        };

        // Cat.h
        class Cat : private Animal // private
        {
        public:
            Cat(int age, const char* name);
        private:
            char* mName;
        };
        ```
    * protected 상속

## 생성자 호출 순서
* 베이스 클래스의 생성자가 먼저 호출됨
    * 명시적 또는 암시적으로
* 그 다음으로 파생 클래스의 생성자가 호출됨
* 부모 클래스의 특정 생성자를 호출할 때는 **초기화 리스트**를 사용해야 함
    
    ```cpp
    Cat::Cat(int legs, int age, const string& callingName)
        : Animal(legs, age) // 명시적 호출
        , mCallingName(callingName)
    {
    }
    ```

## 암시적으로 부모 클래스의 생성자 호출하기
* 사례1: 매개변수 없는 생성자가 있는 베이스 클래스

```cpp
// Animal.h
class Animal
{
public:
    Animal();
private:
    int mAge;
};
```

```cpp
// Animal.cpp
Animal::Animal()
    : mAge(0)
{
}

// Cat.cpp
Cat::Cat(int age, const string& name)
    // 컴파일러가 암시적으로 Animal()을 호출!! OK
{
    size_t size = strlen(name) + 1;
    mName = new char[size];
    strcpy(mName, name);
}

// main.cpp
Cat* myCat = new Cat(2, "Mew");
```

* 사례2: 매개변수 없는 생성자가 없는 베이스 클래스

```cpp
// Animal.h
class Animal
{
public:
    Animal(int age);
private:
    int mAge;
};
```

```cpp
// Animal.cpp
Animal::Animal(int age)
    : mAge(age)
{
}

// Cat.cpp
Cat::Cat(int age, const string& name)
    // 컴파일러가 암시적으로 Animal()을 호출!! -> 컴파일 에러
{
    size_t size = strlen(name) + 1;
    mName = new char[size];
    strcpy(mName, name);
}

// main.cpp
Cat* myCat = new Cat(2, "Mew");
```

## 자식 개체 지우기

```cpp
// Animal.cpp
Animal::~Animal()
{
}

// Cat.cpp
Cat::~Cat()
{
    delete mName;
    // ~Animal()을 호출
}

// main.cpp
delete myNeighboursCat;
// ...
```

* 자식 소멸자 호출 -> 부모 소멸자 호출

## 소멸자 호출 순서
* 생성자 호출 순서와 정반대 (자식 소멸자 먼저 호출)
* 파생 클래스 소멸자의 마지막에서 베이스 클래스의 소멸자가 **자동적으로** 호출됨

## 다형성(Polymorphism)을 배우기 전에...
* 멤버 함수에 대해 말해 보자

```cpp
class Animal
{
public:
    // ...
    int GetAge();
private:
    int mAge;
};
```

```cpp
class Cat : public Animal
{
public:
    // ...
    const char* GetName();
private:
    char* mName;
};
```

```cpp
// 각 값은 스택 또는 힙에 위치해 있음
Cat* myCat = new Cat(5, "Coco");
Cat* yourCat = new Cat(2, "Mocha");
```

```cpp
// 그렇다면 멤버 함수는 어떨까?
myCat->GetName();
yourCat->GetName();
```

## 멤버 함수의 메모리
* 멤버 함수도 메모리 어딘가에 위치해 있음
    * 모든 것이 메모리 어딘가에 위치해 있어야 함
* 그런데 각 개체마다 멤버 함수의 메모리가 잡혀 있을까?
    ```cpp
    myCat->GetName(); // 이 둘의 동작은
    yourCat->GetName(); // 완전히 일치
    ```
* 그 대신 각 **멤버 함수**는 컴파일 시에 **딱 한 번만** 메모리에 "할당"됨
    * 저수준에서는 멤버 함수는 전역 함수와 그다지 다르지 않음

## 함수 오버라이딩

```cpp
// Animal.h
class Animal
{
public:
    void Speak();
};

// Animal.cpp
void Animal::Speak()
{
    std::cout << "An Animal is speaking" << std::endl;
}
```

```cpp
// Cat.h
class Cat : public Animal
{
public:
    void Speak();
};

// Cat.cpp
void Cat::Speak()
{
    std::cout << "Meow" << std::endl;
}
```

```cpp
// Main.cpp
Cat* myCat = new Cat();
myCat->Speak(); // Meow?

Animal* yourCat = new Cat();
yourCat->Speak();   // Meow?
```

## Java & C++ 상속 비교
* Java

```java
public class Animal
{
    public void Speak() {} // "An animal is speaking" 출력
}

public class Cat extends Animal
{
    public void Speak() {} // "Meow" 출력
}

Cat myCat = new Cat(2, "Coco");
myCat.Speak(); // Meow
Animal yourCat = new Cat(5, "Mocha");
yourCat.Speak(); // Meow
```

* C++

```cpp
class Animal
{
public:
    void Speak(); // "An animal is speaking" 출력
}

class Cat : public Animal
{
public:
    void Speak(); // "Meow" 출력
}

Cat* myCat = new Cat();
myCat->Speak(); // Meow
Animal* yourCat = new Cat();
yourCat->Speak(); // An animal is speaking
```

Java는 실체를 따라 가고, C++은 무늬를 따라 간다.

## 정적 바인딩 - 멤버 변수

```cpp
Cat* myCat = new Cat();
myCat->Speak(); // Cat 클래스의 Speak() 호출

Animal* yourCat = new Cat();
yourCat->Speak(); // Animal 클래스의 Speak() 호출
```

## 가상(virtual) 함수 (다형성의 핵심)

```cpp
// Animal.h
class Animal
{
public:
    virtual void Move(); // virtual 키워드: 가상함수 -> 나중에 실행할 때 실체를 찾아서 호출
    virtual void Speak();
};

// Animal.cpp
void Animal::Move()
{
}

void Animal::Speak()
{
    std::cout << "An animal is speaking" << std::endl;
}
```

```cpp
// Cat.h
class Cat : public Animal
{
public:
    void Speak();
};

// Cat.cpp
void Cat::Speak()
{
    std::cout << "Meow" << std::endl;
}
```

```cpp
// Main.cpp
Cat* myCat = new Cat(2, "Coco");
myCat->Speak(); // Meow?

Animal* yourCat = new Cat(5, "Mocha");
yourCat->Speak(); // Meow?
```

* virtual 키워드를 붙여 주면, Java처럼 실체를 호출한다!

## Java는 모든 것이 기본적으로 가상 함수
* Java 프로그래머는 final 키워드를 쓸 수 있음 (이걸 까먹으면 비 가상함수보다 언제나 느림)
* C++에서는 virtual 키워드를 생략하면 큰일 날 수 있음... (나중에 배울 내용)

## 가상 함수
* 자식 클래스의 멤버함수가 언제나 호출됨
    * 부모의 포인터 또는 참조를 사용 중이더라도
* 동적(dynamic) 바인딩 / 늦은(late) 바인딩
    * 실행 중에 어떤 함수를 호출할지 결정한다
    * 당연히 정적 바인딩보다 느림
* 이를 위해 **가상 테이블**이 생성됨
    * 모든 가상 멤버함수의 주소를 포함
    * Q: 클래스마다 하나? vs 개체마다 하나?
        * A: 클래스마다 하다
    * 개체를 생성할 때, 해당 클래스의 가상 테이블 주소가 함께 저장됨

## 가상 테이블
```
Speak()이 어디 있지?

가상 테이블이 0x009ED8에 있고
Speak()은 두 번째 함수니 
0x009EDC에 저장된 주소에 있겠군
```

점프 테이블 또는 룩업(lookup) 테이블이라고도 불린다.

## 비 가상 소멸자

```cpp
// Animal.h
class Animal
{
public:
    ~Animal()
private:
    int mAge;
};

// Cat.h
class Cat : public Animal
{
public:
    ~Cat();
private:
    char* mName;
};
```

```cpp
Cat* myCat = new Cat(2, "Coco");
delete myCat; // ~Cat() 호출 후 ~Animal() 호출
```

```cpp
Animal* yourCat = new Cat(5, "Mocha");
delete yourCat; // ~Animal() 호출. ~Cat()은 호출 안됨 (~Animal()이 가상함수가 아니기 때문)
```

## 가상 소멸자

```cpp
// Animal.h
class Animal
{
public:
    virtual ~Animal();
private:
    int mAge;
};
```

```cpp
// Cat.h
class Cat : public Animal
{
public:
    // 여기는 virtual 키워드 생략 가능
    virtual ~Cat();
private:
    char* mName;
};

// Cat.cpp
Cat::~Cat()
{
    delete mName;
}
```

* 매우 중요!! 모든 클래스마다 가상 소멸자를 사용할 것!
* 모든 소멸자에 언제나 `virtual` 키워드를 붙일 것

## 가상 함수에 대한 한 가지 유의점
* 멤버 함수의 가상성은 상속됨
* 베이스 클래스에서 가상으로 선언된 멤버 함수가 있다면  
    * 파생 클래스에서 동일한 시그니처를 가진 함수 역시 가상 함수!
    * virtual 키워드가 안 붙어 있어도!

## 다중(multiple) 상속

```cpp
// Faculty.h
class Faculty
{
};

// Student.h
class Student
{
};

// TA.h
class TA : public Student, public Faculty // 학생, 교수 모두 상속
{
};

// Main.cpp
TA* myTA = new TA(); // 조교
```

### 어느 부모의 생성자가 먼저 호출될까?
* 파생 클래스에서 등장한 부모 클래스 순서대로
    * 초기화 리스트의 순서는 상관 없음

```cpp
// Student(), Faculty() 순으로 호출 (상속 받은 순서대로 생성자 호출)
class TA : public Student, public Faculty
{
};

// Faculty(), Student() 순으로 호출 (상속 받은 순서대로 생성자 호출)
class TA : public Faculty, public Student
{
};

// Student(), Faculty() 순으로 호출 (상속 받은 순서대로 생성자 호출)
class TA : public Student, public Faculty
    : Faculty()
    , Student()
{
};
```

### Java에서처럼 super()를 쓸 수 없는 이유
* C++에서는 다중 상속이 가능하기 때문

    ```cpp
    // 불가능!
    class TA : public Student, public Faculty
        : super("a12345678") // super가 누군지 알 수 없음
        , super("Full - time") // super가 누군지 알 수 없음
    {
    };

    // 가능!
    class TA : public Student, public Faculty
        : Faculty("a12345678") // super가 누군지 알 수 없음
        , Student("Full - time") // super가 누군지 알 수 없음
    {
    };
    ```

* 문제점1 - 어떤 함수가 호출되는가?

    ```cpp
    // Faculty.h
    class Faculty
    {
    public:
        void DisplayData();
    };

    // Student.h
    class Student
    {
    public:
        void DisplayData();
    };

    // TA.h
    class TA : public Student, public Faculty
    {
    };

    // Main.cpp
    TA* myTA = new TA();
    myTA->DisplayData(); // 어떤 부모의 함수가 호출될까?
    ```

    * 해결: 어떤 함수가 호출될지 모호할 때는 직접 부모 클래스를 특정해줘야 함

        ```cpp
        myTA->Student::DisplayData();
        ```

* 문제점2 - 다이아몬드 문제

    ```
        Animal
    Tiger   Lion
        Liger
    ```

    ```cpp
    Liger* myLiger = new Liger(); // Liger 안에는 몇 개의 Animal이 있나? 2개...
    ```

    * 해결: 가상 베이스 클래스

        ```cpp
        // Animal.h
        class Animal
        {
        };

        // Tiger.h
        class Tiger : virtual public Animal // virtual로 상속하면, 상속받는 자식이 하나의 Animal만 갖도록 할 수 있다
        {
        };

        // Lion.h
        class Lion : virtual public Animal // virtual로 상속하면, 상속받는 자식이 하나의 Animal만 갖도록 할 수 있다
        {
        };

        // Liger.h
        class Liger : public Tiger, public Lion
        {
        };
        ```

* 다중 상속을 최대한 쓰지 말자.
* 대신 **인터페이스**를 사용할 것 (Java처럼! Java는 다중상속을 허용하지 않음)

## 추상(abstract) 클래스

```cpp
// Animal.h
class Animal
{
public:
    virtual ~Animal();
    virtual void Speak() = 0; // 구현체는 없다!
private:
    int mAge;
};
```

```cpp
// Cat.h
class Cat : public Animal
{
public:
    ~Cat();
    void Speak(); // 구현하겠다! 
private:
    char* mName;
};
```

## 순수(pure) 가상 함수
* 구현체가 없는 멤버 함수
* 파생 클래스가 구현해야 함

```cpp
class Animal
{
public:
    virtual void Speak() = 0; // 순수 가상 함수
private:
    int mAge;
};
```

```cpp
class Cat : public Animal
{
public:
    Cat(int age, const char* name);
private:
    char* name;
};

// 컴파일 에러 발생
// Speak()이 구현 안되어 있음..
```

## 순수 가상 함수를 선언하는 법

```
virtual <return-type> <function-name> (<argument-list>) = 0;
```

```cpp
virtual void Speak() = 0;
virtual float GetArea() = 0;
```

## 추상 클래스
* **순수 가상 함수**를 가지고 있는 베이스 클래스를 추상 클래스라 함
    * 추상 클래스에서 개체를 만들 수 없음
    * 추상 클래스를 포인터나 참조형으로는 사용 가능

```cpp
class Animal
{
public:
    virtual void Speak() = 0;
private:
    int mAge;
};
```

```cpp
Animal myAnimal;    // 개체 생성 불가능 (컴파일 에러)
Animal* myAnimal = new Animal();    // 개체 생성 불가능 (컴파일 에러)
Animal* myCat = new Cat();  // myCat 개체 생성
Animal& myCatRef = *myCat;  // myCatRef는 myCat의 주소를 가리킨다
```

## 인터페이스

* C++은 자체적으로 인터페이스를 지원하지 않음
* 순수 추상 클래스를 사용해서 Java의 인터페이스를 흉내냄
    * 순수 가상 함수만 가짐
    * 멤버 변수는 없음

```cpp
// IFlyable.h
class IFlyable
{
public:
    virtual void Fly() = 0;
};

// IWalkable.h
class IWalkable
{
public:
    virtual void Walk() = 0;
};
```

```cpp
class Bat : public IFlyable, public IWalkable
{
public:
    void Fly();
    void Walk();
};

class Cat : public IWalkable
{
public:
    void Walk();
};
```