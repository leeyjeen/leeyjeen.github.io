---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 16: 표준 템플릿 라이브러리(STL, Standard Template Library) 3"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 16: 표준 템플릿 라이브러리(STL, Standard Template Library) 3

## 큐(Queue)란?
* 선입 선출(First-in First-out, FIFO) 자료 구조

## 예시: 큐 만들기

```cpp
#include <queue>

int main()
{
    std::queue<std::string> studentNameQueue;
    studentNameQueue.push("Coco");
    studentNameQueue.push("Mocha");

    while (!studentNameQueue.empty())
    {
        std::cout << "Waiting student: " << studentNameQueue.front() << std::endl;
        studentNameQueue.pop();
    }

    return 0;
}
```

## 큐 만들기

```cpp
queue<<type>><name>
```

    ```cpp
    std::queue<int> studentIDQueue;
    std::queue<std::string> studentNameQueue;
    std::queue<StudentInfo> studentInfoQueue;
    ```

## 요소 삽입하기

```cpp
void push(const value_type& val);
```

* queue의 맨 뒤에 새 요소를 삽입한다

    ```cpp
    studentIDQueue.push(1234);
    stduentNameQueue.push("Lulu");
    studentInfoQueue.push(StudentInfo("Coco", 1234));
    ```

## 요소 제거하기

```cpp
void pop();
```

* 가장 먼저 삽입되었던 요소를 제거한다

    ```cpp
    std::queue<std::string> studentNameQueue;
    // "Coco"와 "Mocha"를 넣는다

    studentNameQueue.pop(); // studentNameQueue: "Mocha"
    ```

## `front()`, `back()`

```cpp
value_type& front();
```

* 가장 먼저 삽입되었던 요소를 참조로 반환한다

```cpp
value_type& back();
```

* 가장 마지막에 삽입되었던 요소를 참조로 반환한다

    ```cpp
    // "Coco"와 "Mocha"를 넣는다

    std::string front = studentNameQueue.front(); // "Coco"
    std::string back = studentNameQueue.back(); // "Mocha"
    ```

## `size()`, `empty()`

```cpp
size_type size();
```

* queue에 들어 있는 요소의 수를 반환한다

```cpp
bool empty();
```

* queue가 비어 있으면 true, 그렇지 않으면 false를 반환

    ```cpp
    int size = studentNameQueue.size();
    bool bEmpty = studentNameQueue.empty();
    ```

## 스택(Stack)이란?
* 후입 선출(Last-in-first-out, LIFO) 자료구조

## 예시: 스택 만들기

```cpp
#include <stack>

int main()
{
    std::stack<std::string> studentNameStack;
    studentNameStack.push("Coco");
    studentNameStack.push("Mocha");

    while (!studentNameStack.empty())
    {
        std::cout << studentNameStack.top() << std::endl;
        studentNameStack.pop();
    }

    return 0;
}
```

## 스택 만들기

```cpp
stack<<type>><name>
```

    ```cpp
    std::stack<int> studentIDStack;
    std::stack<std::string> studentNameStack;
    std::stack<StudentInfo> studentInfoStack;
    ```

## 요소 삽입하기

```cpp
void push(const value_type& val);
```

* stack의 맨 뒤에 새 요소를 삽입한다

    ```cpp
    studentIDStack.push(1234);
    studentNameStack.push("Coco");
    studentInfoStack.push(StudentInfo("Coco", 1234));
    ```

## 요소 제거하기

```cpp
void pop();
```

* stack에 가장 마지막에 저장된 요소를 제거한다

    ```cpp
    std::stack<std::string> studentNameStack;
    // "Coco"와 "Mocha"를 넣는다

    studentNameStack.pop(); // studentNameStack: "Coco"
    ```

## `top()`

```cpp
value_type& top();
```

* stack에 가장 마지막에 저장된 요소를 참조로 반환한다

    ```cpp
    // "Coco"와 "Mocha"를 넣는다
    std::string top = studentNameStack.top();   // "Mocha"
    ```

## `size()`, `empty()`

```cpp
size_type size();
```

* stack에 들어 있는 요소의 수를 반환한다

```cpp
bool empty();
```

* stack이 비어 있으면 true를, 그렇지 않으면 false를 반환한다

    ```cpp
    int size = studentNameStack.size();
    bool bIsEmpty = studentNameStack.empty();
    ```

## 리스트(List)란?
* 양방향 연결 리스트(이중 연결 리스트)
* operator[]가 없음
* 양쪽 끝에서 삽입/제거 가능

## 예시: 리스트 만들기

```cpp
#include <list>

int main()
{
    std::list<int> scores;
    scores.push_front(10); // 10
    scores.push_front(20); // 20, 10
    scores.push_back(30); // 20, 10, 30

    return 0;
}
```

## 리스트 만들기

```cpp
std::list<<type>><name>
```

    ```cpp
    std::list<int> scores;
    std::list<std::string> names;
    std::list<StudentInfo> Students;
    ```

## 요소 삽입하기

```cpp
iterator insert(iterator position, const value_type& value);
```

* position이 가리키는 위치에 새 요소를 삽입한다

```cpp
void push_front(const value_type& value);
```

* 제일 처음에 새 요소를 삽입한다

```cpp
void push_back(const value_type& value);
```

* 제일 마지막에 새 요소를 삽입한다

    ```cpp
    std::list<int> scores;
    std::list<int>::iterator it = scores.begin();

    scores.insert(it, 99);  // 99
    scores.push_front(10);  // 10, 99
    scores.push_back(50);   // 10, 99, 50
    ```

## 처음 또는 마지막 요소 제거하기

```cpp
void pop_front()
```

* 첫 번째 요소를 제거한다

```cpp
void pop_back()
```

* 마지막 요소를 제거한다

    ```cpp
    std::list<int> scores;  // 10, 99, 50
    scores.pop_front(); // 99, 50
    scores.pop_back();  // 99
    ```

## 특정 요소 제거하기

```cpp
iterator erase(iterator position);
```

* position이 가리키는 위치의 요소를 제거한다

```cpp
void remove(const value_type& value);
```

* value와 값이 같은 요소를 전부 제거한다

    ```cpp
    std::list<int> scores = { 20, 30, 40, 30, 25, 30, 70, 96 };
    std::list<int>::iterator it = scores.begin();

    scores.erase(it);   // 30, 40, 30, 25, 30, 70, 96
    scores.remove(30);  // 40, 25, 70, 96
    ```

## 리스트의 다른 메서드들
* 정렬하기
* 두 리스트 합치기
* 한 리스트에서 빼내어 다른 리스트에 넣기
* 중복인 요소들 제거하기
* ...

## 리스트의 장점과 단점
* 장점
    * 삽입과 제거에 걸리는 시간: O(1)
    * 어느 위치든 삽입/제거 가능
* 단점
    * 탐색이 느린 편
    * 임의적으로 접근 불가
    * 메모리가 불연속적

## 기타 STL 컨테이너들
* 멀티셋(multi-set)
    * 중복 키를 허용
    * 요소를 수정하면 안됨
* 멀티맵(multi-map)
    * 중복 키를 허용
* 덱(디큐, deque)
    * Double-ended queue의 약자
    * 양쪽 끝에서 요소 삽입과 삭제가 모두 가능
* 우선순위 큐(priority-queue)
    * 자동 정렬되는 큐

## STL 컨테이너의 목적
* 모든 컨테이너에 적용되는 표준 인터페이스
* std 알고리듬은 많은 컨테이너에서 작동
    * 나중에 배울 내용
* 템플릿 프로그래밍 기반
    * 나중에 배울 내용
* 메모리 자동 관리

## STL 단점 - 모든 컨테이너에 알맞은 표준 인터페이스라는 환상
* 모든 컨테이너에 같은 인터페이스가 적용되는 것은 이상하다
    * 극단적으로 OOP를 추구한 사례

        ```cpp
        std::vector<int> scores;
        scores.push_back(10); // 스택같아 보인다...

        std::list<int> ages;
        ages.push_back(100); // 스택같아 보인다...
        ```

## STL 단점 - 메모리 단편화
* 빈번한 메모리 재할당은 메모리 단편화를 초래함
* 메모리 단편화는 엄청난 문제가 될 수 있음. 특히 가상 메모리를 지원하지 않는 플랫폼에서 프로그램을 실행할 때...
* 메모리 단편화 때문에 애플리케이션이 뻗어 버릴 수 있음
    * 즉, 총 여유 공간은 충분하나 충분히 큰 연속되는 메모리가 없는 경우
* 디버깅 및 고치는 게 쉽지 않음

## 회사내부에서 만드는 STL 대체품들...
* 많은 회사들이 자신만의 컨테이너를 만들어 STL을 대체
    * EA
        * EASTL
        * STL과 호환됨. 메모리 문제 등을 고친 컨테이너
    * Epic Games (언리얼 엔진 4)
        * TArray, TMap, TMultiMap, TSet
        * STL보다 나은 인터페이스로 구연한 언리얼만의 컨테이너
* EA STL과 언리얼 엔진 4는 모두 오픈소스니 한 번 살펴볼 것
* 또한 직접 컨테이너를 만들어보면 메모리 관리에 대한 이해를 높일 수 있을 것임

