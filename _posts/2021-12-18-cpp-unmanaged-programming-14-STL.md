---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 14: 표준 템플릿 라이브러리(STL, Standard Template Library) 1"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 14: 표준 템플릿 라이브러리(STL, Standard Template Library) 1

## STL 컨테이너 목록
* 벡터(vector)
* 맵(map)
* 셋(set)
* 스택(stack)
* 큐(queue)
* 리스트(list)
* 덱(디큐, deque)
* ...

## STL 컨테이너의 목적
* 템플릿 기반
* 모든 컨테이너에 적용되는 표준 인터페이스
* 메모리 자동 관리

## 벡터
* **어떤 자료형도** 넣을 수 있는 **동적 배열** (메모리를 자동으로 늘려줌)
    * 기본(primitive) 데이터
    * 클래스(class)
    * 포인터(pointer)
* 그 안에 저장된 모든 요소들이 **연속된 메모리 공간**에 위치
* 요소 수가 증가함에 따라 자동으로 메모리를 관리해 줌
* 어떤 요소에도 임의로 접근(random access) 가능

## 예시: 벡터 만들기

```cpp
#include <iostream>
#include <vector>

int main()
{
    std::vector<int> scores;
    scores.reserve(2);

    scores.push_back(30);
    scores.push_back(50);

    scores.pop_back();

    std::cout << "Current capacity : " << scores.capacity() << std::endl;
    std::cout << "Current size : " << scores.size() << std::endl;
}
```

## vector 변수를 만드는 법 1

```
std::vector<<type>><name>;
```

* 빈 vector를 생성한다

    ```cpp
    std::vector<int> scores;
    std::vector<string> names;

    std::vector<Cat> myCats;
    std::vector<Cat*> myCats;
    ```

## vector 변수를 만드는 법 2

```
std::vector<<type>><name>(const vector& x);
```

* x라는 vector와 같은 크기(size) 및 데이터를 갖는 vector를 생성한다

    ```cpp
    std::vector<int> scores;

    scores.push_back(1);
    scores.push_back(2);

    std::vector<int> scores1(scores); // scores의 사본
    ```

## vector 변수를 만드는 법 3

```
std::vector<<type>><name>(<size>);
```

* 크기(size)를 지정하여 vector를 생성한다 (모든 요소의 값은 0)

    ```cpp
    std::vector<int> scores(10);

    std::vector<Cat> myCats(4);
    ```

## 제일 마지막에 요소 추가하기

```
push_back(<data>);
```

* vector 맨 뒤에 요소를 추가한다

    ```cpp
    scores.push_back(30);

    names.push_back("Coco");

    myCats.push_back(myNewCat);
    ```

## 제일 마지막에 있는 요소 제거하기

```
pop_back();
```

* vector의 맨 뒤에 있는 요소를 제거한다

    ```cpp
    scores.pop_back();
    ```

## 용량(capacity)과 크기(size)

* `capacity();`
    * vector에 **할당된** 요소 공간 수
* `size();`
    * vector에 **실제로 들어 있는** 요소 수

```cpp
scores.capacity();
scores.size();
```

## vector의 용량 늘리기

```
reserve(<size>);
```

* vector의 용량을 늘린다
* 용량이 증가해야 하면 새로운 저장 공간을 재할당하고 기존 요소들을 모두 새 공간으로 복사

```cpp
scores.reserve(10);
```

!! 불필요한 재할당을 막기 위해 vector를 생성한 직후에 이 함수를 호출하자 !!

## 요소 하나에 접근하기

```
operator[](size_t n);
```

* 지정된 위치(n)의 요소를 **참조로 반환**한다

```cpp
scores[i] = 3;

std::cout << names[i] << " ";

std::cout << myCats[i].GetScore() << " ";
```

## 예시: 모든 요소 출력하기 1

```cpp
#include <vector>

int main()
{
    std::vector<int> scores;
    scores.reservve(2);

    scores.push_back(30);
    scores.push_back(50);

    for (size_t i = 0; i < scores.size(); ++i)
    {
        std::cout << scores[i] << " ";
    }
    // ...
}
```

## 허나 이 방식은 벡터에만 쓸 수 있음
* 맵(map)에서는 인덱스로 operator[]를 쓸 수 없음
*  그래서, STL 컨테이너를 순회할 때는 **반복자**(iterator)를 쓰는 게 표준 방식

## 예시: 모든 요소 출력하기 2

```cpp
#include <iostream>
#include <vector>

int main()
{
    std::vector<int> scores;
    scores.reserve(2);

    scores.push_back(30);
    scores.push_back(50);

    for (std::vector<int>::iterator iter = scores.begin(); iter != scores.end(); ++iter)
    {
        std::out << *iter << " ";
    }
    // ...
}
```

## 반복자(iterator)

```
vector<<type>>::iterator<name>
```

* vector의 요소들을 순회하는 데 사용한다

```cpp
std::vector<int>::iterator iter = scores.begin();
```

## 반복자 - `begin()` / `end()`

* `begin();`
    * vector의 **첫 번째 요소**를 **가리키는** 반복자를 반환
* `end();`
    * vector의 **마지막 요소 바로 뒤의 요소**를 **가리키는** 반복자를 반환

```cpp
vector<int>::iterator bIter = scores.begin();

vector<int>::iterator eIter = scores.end();
```

## 반복자 - 뒤에서 접근하고 싶은 경우
* end()에서 시작해서 begin()까지?
* 하지만 end()는 마지막 요소 하나 뒤에 있는 건데...
* 이럴 때 쓰는 게 역방향 반복자(reverse iterator)!

## 역방향 반복자 - `rbegin()`, `rend()`
* `reverse_iterator rbegin();`
    * vector의 마지막 요소를 **가리키는** 역방향 반복자를 반환한다
* `reverse_iterator rend();`
    * vector의 첫 번째 요소 앞의 "요소"를 **가리키는** 역방향 반복자를 반환한다

        ```cpp
        std::vector<int>::reverse_iterator reversedBeginIt = scores.rbegin();
        std::vector<int>::reverse_iterator reversedEntIt = scores.rend();
        ```

## 특정 위치에 요소 삽입하기

```cpp
std::vector<int> scores;

scores.reserve(4);
scores.push_back(10); // 10
scores.push_back(50); // 10, 50
scores.push_back(38); // 10, 50, 38
scores.push_back(100); // 10, 50, 38, 100

std::vector<int>::iterator it = scores.begin();

// 특정 위치에 요소 삽입하기
it = scores.insert(it, 80); // 80, 10, 50, 38, 100
```

## 복사 문제

```cpp
std::vector<int> scores;
scores.reserve(4);
// 10, 50, 38을 넣는다

std::vector<int>::iterator it;
it = scores.begin();

it = scores.insert(it, 80); // 이 경우, 10, 50, 38을 뒤로 복사한 후 80을 삽입한다... 데이터를 복사하는 문제가 발생
```

## 재할당 + 복사 문제

```cpp
// 재할당은 더 큰 문제

std::vector<int> scores;
scores.reserve(4); // 공간: 4개
// 10, 50, 38, 100을 넣는다

std::vector<int>::iterator it;
it = scores.begin();

it = scores.insert(it, 80); // 공간이 부족하여 추가적인 메모리에 재할당 & 복사 -> 성능 떨어짐
```

## 특정 위치에 있는 요소 삭제하기

```cpp
std::vector<int> scores;

scores.push_back(10); // 10
scores.push_back(50); // 10, 50
scores.push_back(38); // 10, 50, 38
scores.push_back(100); // 10, 50, 38, 100

std::vector<int>::iterator it;
it = scores.begin();

// 삭제: 앞에걸 지우고 하나씩 당겨오는 복사가 발생함
it = scores.erase(it); // 50, 38, 100
```

## 요소 대입하기

```cpp
assign(size_t n, <data>);
```

* n개의 <data>값을 벡터에 넣는다

    ```cpp
    vector<int> anotherScores;
    anotherScores.assign(7, 100); // 100, 100, 100, 100, 100, 100, 100
    ```

## 두 벡터 교환하기

```cpp
swap(vector& other);
```

* 같은 자료형의 두 벡터를 맞바꾼다

    ```cpp
    vector<int> scores; // 10, 20, 30, 100
    vector<int> anotherScores;  // 40, 50, 60

    scores.swap(anotherScores)    // scores: 100, 100, 100, 100, 100, 100, 100
    // anotherScores: 85, 73
    ```

## 예시: 벡터 축소하기

```cpp
std::vector<int> scores;

scores.reserve(3);

scores.push_back(30); // 30
scores.push_back(100); // 30, 100
scores.push_back(70); // 30, 100, 70

scores.resize(2); // 30, 100

for (int i = 0; i < scores.size(); ++i)
{
    std::cout << scores[i] << " "; // "30 100"
}
```

## 벡터의 크기 변경하기

```cpp
resize(size_t n);
```

* vector의 **크기**를 바꾼다
* 새 크기가 vector의 기존 크기보다 작으면, 초과분이 제거됨
* 새 크기가 vector의 용량보다 크면 재할당이 일어남

    ```cpp
    scores.resize(10);
    ```

## vector에서 모든 요소 제거하기

```cpp
clear();
```

* vector를 싹 지운다
* 크기(size)는 0이 되고 용량은 변하지 않음

    ```cpp
    scores.clear();
    ```

## 예시: 개체(object) 벡터

```cpp
// Score.h
class Score
{
public:
    // ...
private:
    int mScore;
    string mClassName;
};

// Main.cpp
int main()
{
    vector<Score> scores;
    scores.reserve(4);

    scores.push_back(Score(30, "C++"));
    scores.push_back(Score(59, "Algorithm"));
    scores.push_back(Score(87, "Java"));
    scores.push_back(Score(41, "Android"));
    // ...
}
```

## 개체를 직접 보관하는 벡터의 문제점 1

```cpp
vector<Score> scores;
scores.reserve(1);
scores.push_back(Score(10, "C++"));

scores.push_back(Score(41, "Android"));
```

* 증가할 때마다 메모리 재할당 및 복사가 일어난다

## 개체를 직접 보관하는 벡터의 문제점 2

```cpp
vector<Score> scores;
scores.reserve(1);
scores.push_back(Score(10, "C++"));
scores.push_back(Score(41, "Android"));

vector<Score> scores2 = scores;
```

* 사본 만들 때 다 복사해야 한다

## 예시 - 포인터를 저장하는 벡터

```cpp
int main()
{
    vector<Score*> scores;

    scores.reserve(2);

    scores.push_back(new Score(30, "C++"));
    scores.push_back(new Score(87, "Java"));
    scores.push_back(new Score(41, "Android"));

    scores.clear();

    return 0;
}
```

## 예시 - 포인터 벡터 삭제하기

```cpp
int main()
{
    vector<Score*> scores;

    scores.push_back(new Score(30, "C++"));
    scores.push_back(new Score(87, "Java"));

    // 모든 요소에 대해 delete를 꼭 호출할 것.
    // 또한 지운 후에 이 포인터들을 계속 사용하면 안 됨.
    for (vector<Score*>::iterator iter = scores.begin(); iter != scores.end(); ++iter)
    {
        delete *iter;
    }

    scores.clear();

    return 0;
}
```

## 벡터의 장점
* 순서에 상관 없이 요소에 임의적으로 접근 가능
* 제일 마지막 위치에 요소를 빠르게 삽입 및 삭제

## 벡터의 단점
* 중간에 요소 삽입 및 삭제는 느림
* 재할당 및 요소 복사에 드는 비용
