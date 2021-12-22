---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 18: STL 알고리듬(Algorithm)"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 18: STL 알고리듬(Algorithm)

## STL 알고리듬이란?
* 요소 범위에서 쓸 수 있는 함수들
    * [처음, 마지막)
* 배열 또는 몇몇 STL 컨테이너에 쓸 수 있음
* 반복자를 통해 컨테이너에 접근
* 컨테이너의 크기를 변경하지 않음(따라서 추가 메모리 할당도 없음)

## STL 알고리듬의 유형

### `#include <algorithm>`
* 변경 불가 순차(sequence) 연산
    * find(), for_each(), ...
* 변경 가능 순차 연산
    * copy(), swap(), ...
* 정렬 관련 연산
    * sort(), merge(), ...

### `#include <numeric>`
* 범용 수치 연산
    * accumulate(), ...

## vector를 다른 vector로 복사하기

```cpp
#include <algorithm>
//...

int main()
{
    std::vector<int> scores;

    scores.push_back(10);
    scores.push_back(70);
    scores.push_back(40);

    std::vector<int> copiedScores;
    copiedScores.resize(scores.size());

    std::copy(scores.begin(), scores.end(), copiedScores.begin()); // scores의 begin부터 end까지 copiedScores의 begin부터에 복사하기

    for (std::vector<int>::iterator it = copiedScores.begin(); it != copiedScores.end(); ++it)
    {
        std::cout << *it << std::endl;
    }

    return 0;
}
```

## `copy()`

```cpp
template<class _InIt, class _OutIt>
_OutIt copy(_InIt _First, _InIt _Last, _OutI _Dest);
```

    ```cpp
    // std::vector<int> scores;
    // std::vector<int> copiedScores;
    // std::vector<std::string> names;
    // std::vector<std::string> copiedNames;

    // int vector
    std::copy(scores.begin(), scores.end(), copiedScores.begin());
    
    // string vector
    std::copy(names.begin(), names.begin() + 2, copiedNames.begin());
    ```

## `copy()` 구현

```cpp
templatae<class _InIt, class _OutIt>
_OutIt copy(_InIt _First, _InIt _Last, _OutIt _Dest)
{
    for (; _First != _Last; ++_Dest, (void)++_First)
    {
        *_Dest = *_First;
    }

    return (_Dest);
}
```