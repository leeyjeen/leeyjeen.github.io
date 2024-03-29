---
layout: post
title:  "C++ 언매니지드 프로그래밍 section 1: Intro"
author: "Yujin Lee"
comments: true
tags: C++
---

# C++ 언매니지드 프로그래밍 section 1: Intro
## C++에 대한 소문과 사실

과거 20년 동안 프로그래밍 언어 사용량 변화를 살펴 보면, 많은 C/C++ 프로젝트들이 Java로 대체되었다. 일부 사람들은 C/C++이 코볼(COBOL)처럼 죽을 거라고 말했으나, 2020년 11월 기준으로 C++은 4위를 유지하고 있다.

## 업계는 여전히 C/C++을 사용한다!

**성능이 필요한 곳**에서는 C/C++이 Java보다 많이 사용된다.

Java 등의 매니지드 언어(managed language) 즉 언어단에서 직접 메모리를 관리해주는 언어는 완전히 C/C++을 대체할 수 없다.

- Java를 포함한 많은 언어들이 매니지드 언어이다. 경쟁 언어가 많다.
- C/C++은 **언매니지드**(unmanaged language) 언어이며, **이를 대체할 만한 경쟁 언어가 많이 없다.**

## 누가 C/C++을 사용하는가?

- 운영체제(OS)
    - 운영체제는 주로 C, C++ 혹은 이와 유사한 것들을 기반으로 개발되었다.
    - Java를 혼용으로 쓰는 운영체제가 있긴 한데, 매니지드 언어와 언매니지드 언어를 비교해보면 어떤 운영체제가 훨씬 성능이 좋고 잘 도는지는..!
- 임베디드 시스템(Embedded Systems)
- 비디오 게임!
    - 아마도 가장 C/C++을 많이 쓰는 업계일 것이다.
    - 상용 게임 엔진: 언리얼 엔진, 크라이 엔진 등
    - 3D 비디오 게임
- 그래픽 어플리케이션
    - 어도비 포토샵, 어도비 일러스트레이터, 오토데스크 마야 등 → 속도가 빠르고, 실시간으로 그려야 하고, 데이터 빨리 처리해야 하므로 C/C++로 개발한다.
- 웹 브라우저
    - 모질라 파이어폭스, 구글 크롬, 인터넷 익스플로러 등
- 의료 장비
    - CT, MRI, 혈압 측정기, 심장 박동 모니터기 등

## 평생 매니지드 언어만 사용할 거라고? 하지만,

- 다음의 것들을 알기 위해 여전히 언매니지드 언어를 배워야 한다.
    - 메모리 동작원리
    - CPU 동작원리
    - 컴퓨터처럼 생각하는 방법
    - 매니지드 언어에서 지원하는 모든 마법같은 기능들의 동작원리
- 이른 시기에 컴퓨터처럼 생각하는 습관을 들이면, 그러지 못한 사람들보다 빠르게 성장할 것이다.

## 향후 10년 커리어에 대한 고민

- 여전히 프로그래머로서 일하고 싶은가?
- 더 높은 연봉을 받고 싶은가?
- 그러면 장기적인 커리어를 보장해주는 과목에 초점을 맞출 것
    - C/C++ (언매니지드 언어)
    - 컴퓨터 아키텍쳐
    - 기타 등등

## 참고
* https://www.udemy.com/share/101ZmQAEYfcltbRXkH/