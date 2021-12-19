---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 15: 표준 템플릿 라이브러리(STL, Standard Template Library) 2"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 15: 표준 템플릿 라이브러리(STL, Standard Template Library) 2

## 맵(Map)이란?
* 키(key)와 값(value)의 쌍들을 저장
* 키는 중복될 수 없음
* C++ 맵은 자동 정렬되는 컨테이너... (bad..)
* 이진 탐색 트리(binary search tree) 기반
    * 오름차순

## 예시: 맵 만들기

```cpp
#include <map>

int main()
{
    std::map<std::string, int> simpleScoreMap;

    simpleScoreMap.insert(std::pair<std::string, int>("Mocha", 100));
    simpleScoreMap.insert(std::pair<std::string, int>("Coco", 50));

    simpleScoreMap["Mocha"] = 0;

    std::cout << "Current size: " << simpleScoreMap.size() << std::endl;
    
    return 0;
}
```

## 맵 만들기

```cpp
map<<key_type>, <value_type>><name>
```

* 빈 map을 생성한다

```cpp
map<<key_type>, <value_type>><name>(const map& x)
```

* x라는 map과 같은 크기(size)와 데이터를 갖는 map을 생성

```cpp
std::map<std::string, int> simpleScoreMap;
std::map<StudentInfo, int> complexScoreMap;

std::map<std::string, int> copiedSimpleScoreMap(simpleScoreMap);
```

## `std::pair<key, value>`

```cpp
pair<first_type, second_type>
```

* 두 데이터를 한 단위로 저장하는 구조

```cpp
std::pair<std::string, int> student1("Coco", 10);

simpleScoreMap.insert(std::pair<std::string, int>("Mocha", 100));
```

## `insert()`

```cpp
std::pair<iterator, bool> insert(const value_type& value)
```

* **새 요소**를 map에 삽입한다
* 반복자와 bool 값을 한 쌍으로 반환
    * 반복자는 요소를 가리키고
    * Bool 값은 삽입 결과를 알려줌
* 키를 중복으로 삽입할 수 없음

    ```cpp
    // <iterator, true>를 반환한다
    simpleScoreMap.insert(std::pair<std::string, int>("Mocha", 100));

    // <iterator, false>를 반환한다
    simpleScoreMap.insert(std::pair<std::string, int>("Mocha", 0));
    ```

## `operator[]`

```cpp
mapped_type& operator[](const Key& key);
```

* key에 대응하는 값을 참조로 반환한다
* map에 **키가 없으면 새 요소를 삽입**
* map에 **키가 이미 있으면 그 값을 덮어씀**

    ```cpp
    std::map<std::string, int> simpleScoreMap;

    simpleScoreMap["Coco"] = 10; // 새 요소를 삽입한다
    simpleScoreMap["Coco"] = 50; // "Coco"의 값을 덮어쓴다
    ```

## 자동 정렬

```cpp
simpleScoreMap.insert(std::pair<std::string, int>("Mocha", 100));
simpleScoreMap.insert(std::pair<std::string, int>("Coco", 50));

for (std::map<std::string, int>::iterator it = simpleScoreMap.begin(); it != simpleScoreMap.end(); ++it)
{
    std::cout << "(" << it->first << ", " << it->second << ")" << std::endl;
    // Output: 넣은 순서와 상관없이 key에 따라 정렬이 된다
    // ("Coco", 50)
    // ("Mocha", 100)

    // it->first는 key
    // it->second는 value
}
```

## 예시: 요소 찾기

```cpp
#include <map>

int main()
{
    std::map<std::string, int> simpleScoreMap;
    simpleScoreMap.insert(std::pair<std::string, int>("Mocha", 100));

    std::map<std::string, int>::iterator it = simpleScoreMap.find("Mocha"); // "Mocha"를 찾으면 그 위치 iterator를 반환, 찾지 못하면 end()를 반환
    if (it != simpleScoreMap.end())
    {
        it->second = 80;
    }
    return 0;
}
```

## `find()`

```cpp
iterator find(const Key& key);
```

* map에서 key를 찾으면 그에 대응하는 값을 가리키는 반복자를 반환
* 못 찾으면 `end()` 반환

    ```cpp
    std::map<std::string, int>::iterator it = simpleScoreMap.find("Coco");
    ```

## `swap()`, `clear()`

```cpp
void swap(map& other);
```

* 두 map의 키와 값을 서로 맞바꾼다

```cpp
void clear();
```

* map을 비운다

    ```cpp
    std::map<std::string, int> scoreMap; // ("Mocha", 10), ("Coco", 40)
    std::map<std::string, int> anotherScoreMap; // ("Nana", 100)

    scoreMap.swap(anotherScoreMap); // scoreMap: ("Nana", 100)
    // anotherScoreMap: ("Mocha", 10), ("Coco", 40)
    anotherScoreMap.clear(); // anotherScoreMap: empty
    ```

* `erase()`

```cpp
void erase(iterator position);
size_type erase(const key_type& key);
void erase(iterator first, iterator last);
```

* map의 요소들을 제거한다

    ```cpp
    std::map<std::string, int>::iterator foundIt = simpleScoreMap.find("Coco");
    simpleScoreMap.erase(foundIt);

    simpleScoreMap.erase("Coco");
    ```

## 예시: 개체를 키로 사용하기

```cpp
// StudentInfo.h
class StudentInfo
{
public:
    // 생성자들
private:
    std::string mName;
    std::string mStudentID;
};

// Main.cpp
int main()
{
    std::map<StudentInfo, int> scores;
    scores.insert(std::pair<StudentInfo, int>(StudentInfo("Poppy", "a556"), 30));
    scores.insert(std::pair<StudentInfo, int>(StudentInfo("Lulu", "a112"), 70));
    // 불가능... 
    // STL 맵은 항상 정렬된다
    // 반드시 두 키를 비교하는 함수를 만들어야 함
}
```

## STL 맵은 항상 정렬된다
* 반드시 두 키를 비교하는 함수를 만들어야 함
    * operator<()

    ```cpp
    bool StudentInfo::operator<(const StudentInfo& other) const
    {
        if (mName == other.mName)
        {
            return mStudentID < other.mStudentID;
        }

        return mName < other.mName;
    }
    ```

## 요소들을 정렬하는 또 다른 방법
* map을 만들 때 비교함수(comparer)를 넣을 수도 있음

    ```cpp
    struct StudentInfoComparer
    {
        bool operator()(const StudentInfo& left, const StudentInfo& right) const
        {
            return (left.getName() < right.getName());
        }
    };

    // Main.cpp
    std::map<StudentInfo, int, StudentInfoComparer> Scores;
    ```

## 맵의 장점과 단점
* 장점
    * `std::list`나 `std::vector`보다 탐색 속도가 더 빠름 (구현체가 이진 탐색 트리 기반이기 때문에 O(logN)) (cf. std::list는 O(N), std::vector는 O(N))
* 단점
    * 자동으로 정렬됨
    * 해쉬맵(hashmap)이 아님. 따라서 O(1)이 아님
    * C++11에 해결책이 있음

## 셋(Set)이란?
* 역시 정렬되는 컨테이너
* 중복되지 않는 키를 요소로 저장함
* 역시 이진 탐색 트리 기반
    * 오름차순

```
셋은 맵과 거의 같다
```

## 예시: 셋 만들기

```cpp
#include <set>

int main()
{
    std::set<int> scores;
    scores.insert(20);
    scores.insert(100);

    for (std::set<int>::iterator it = scores.begin(); it != scores.end(); ++it)
    {
        std::cout << *it << std::endl; // 20\n 100\n
    }

    return 0;
}
```

## 셋의 장점과 단점
* 맵과 같음

