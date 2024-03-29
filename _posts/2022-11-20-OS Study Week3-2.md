---
layout: post
title: 3-2. Process 2, 3
subtitle: OS Study Week3-2
categories: OS
tags: OS
sidebar: []
---



반효경 교수님의 [2014-1 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)를 듣고 정리한 글입니다.

<br>

### 수업 전 Q&A

동기식 입출력과 비동기식 입출력의 차이

- 동기식 입출력
  - 어떤 프로세스가 입출력 요청을 했을 때, 운영체제를 통해서 해야하므로 프로세스는 운영체제에 입출력 요청을 함. 그리고 해당 프로세스가 입출력이 끝날 때까지 아무것도 안 하고 기다려야 하는 경우
  - 구현 방법
    - CPU를 가지고 있으면서 기다리게 구현 → CPU 낭비
    - 당장 일을 할 수 있는 다른 프로세스에게 CPU를 넘겨주게 구현 → 일반적으로 구현하는 방법
- 비동기식 입출력
  - 프로세스가 입출력 요청을 한 후 그 프로세스가 곧바로 CPU를 잡아 인스트럭션을 수행하는 경우

<br>

### Thread

프로세스 내부에 CPU 수행 단위가 여러개 있는 것

A thread (or lightweight process) is a basic unit of CPU utilization

![image](https://user-images.githubusercontent.com/71377968/202900957-83ad52f0-865e-47ae-8315-c428cb918360.png)

- Thread의 구성
  - program counter
  - register set
  - stack space
- Thread가 동료 thread와 공유하는 부분(=task)
  - code section
  - data section
  - OS resources
- 전통적인 개념의 heavyweight process 는 하나의 thread를 가지고 있는 task로 볼 수 있음

<br>

### Thread 사용의 장점

- Responsiveness(응답성)
  - 사용자 입장에서 빠름
  - 웹브라우저에 접속한다고 할 때 html 화면에 그림이 있는 경우 이를 해석하여 서버에서 이미지를 가져온 후 그걸 확인하여 화면을 완성하면 사용자에게 보여주는데, 이 과정에서 이미지를 불러올 때, 이는 오래 걸리는 작업이므로 block을 시킬 것임. 하지만 여러개의 스레드를 사용하면 이미지를 요청한는 스레드만 block이 되므로 다른 스레드가 이미 읽어온 html 문서라도 화면에 디스플레이해줄 수 있음 → 일종의 비동기식 입출력
- Resource Sharing(자원 공유)
  - 똑같은 일을 하는 프로그램이 여러개 있는 것 보다 하나의 프로세스를 만들고 그 안에 CPU 수행 단위만 여러개 두게 되면 code, data, 각종 자원을 공유하여 자원을 효율적으로 사용하는 효과를 얻을 수 있음
- Economy(경제성)
  - 응답성과는 다른 의미로 빠름
  - 프로세스를 하나 생성(creating)하고 문맥교환(switching) 하는 것보다 스레드를 하나 생성하고 문맥교환하는 것이 오버헤드가 더 적음(switching의 경우, 동일한 주소 공간을 사용하므로 스레드를 사용하는 것이 더 경제적)
- Utilization of MP(Multi Processor) Architectures
  - CPU가 여러개 있는 경우에만 해당
  - 서로 다른 CPU에서 병렬적으로 작업하여 결과를 빠르게 얻을 수 있음

<br>

### 스레드 구현 방식

- Kernel Threads: 운영체제의 지원을 받음
  - 스레드가 여러개 있다는 사실을 운영체제가 알고 있음
  - 스레드를 수행하는 것도 커널이 CPU 스케줄링을 하듯이 넘겨주게 됨
- User Threads: 라이브러리 형태로 구현
  - 스레드가 여러개 있다는 사실을 운영체제가 모름
  - 유저 프로그램이 스스로 여러개의 스레드를 라이브러리의 지원을 받아 관리함
  - 운영체제가 모르기 때문에 구현 상의 제약점이 있을 수 있음
- Real-Time Threads

<br>

<br>

[사진 출처] [https://hyojaedev.tistory.com/30](https://hyojaedev.tistory.com/30)
