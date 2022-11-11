---
layout: post
title: 2-2. System Structure & Program Execution 2
subtitle: OS Study Week2-2
categories: OS
tags: OS
sidebar: []
---



반효경 교수님의 [2014-1 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)를 듣고 정리한 글입니다.

<br>

### 동기식 입출력과 비동기식 입출력

![image](https://user-images.githubusercontent.com/71377968/201299188-379d8c9e-91cd-447d-a05b-71fee9e4fb5b.png)

**Synchronous I/O**

- I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
- 구현방법 1
  - I/O가 끝날 때까지 CPU를 낭비시킴
  - 매시점 하나의 I/O만 일어날 수 있음
- 구현방법 2 ✔️
  - I/O가 완료될 때까지 해당 프로그램에게서 CPU를 빼앗음
  - I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움
  - 다른 프로그램에게 CPU를 줌

**Asynchronous I/O**

- I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감
- → write의 경우 일반적으로 비동기식이 자연스러움

*두 경우 모두 I/O의 완료는 인터럽트로 알려줌

<br>

### 서로 다른 입출력 명령어

![image](https://user-images.githubusercontent.com/71377968/201299256-77e7899a-9842-48cd-9ecc-6e39e09bc15d.png)

- I/O를 수행하는 special instruction에 의한 것 (좌)
- Memory Mapped I/O에 의한 것 (우)

<br>

### 저장장치 계층 구조

![image](https://user-images.githubusercontent.com/71377968/201299307-225e2f42-6745-4031-9ed2-226562733dea.png)

- 휘발성 매체는 전원이 나가면 데이터가 사라짐
- 비휘발성 매체는 전원이 나가도 데이터는 사라지지 않음
- Primary: CPU가 직접 접근할 수 있는 것(byte 단위로 접근이 가능해야 함 → DRAM)
- Secondary: CPU가 직접 접근할 수 없는 것(sector 단위로 접근 가능함)

<br>

**Caching**

- CPU와 메인 메모리의 속도 차이를 완충하기 위해 캐시 메모리를 둠
- 용량이 작아 모든 걸 담아 둘 수는 없음
- 캐싱은 재사용을 목적으로 함 → 두 번째 요청부터 이미 읽어온 데이터를 사용

<br>

### 프로그램의 실행(메모리 load)

![image](https://user-images.githubusercontent.com/71377968/201299357-8242c3b8-9369-4190-b89b-fbc391b9c4d9.png)

프로그램은 file system에 실행 파일 형태로 저장되어있는데, 이를 실행하면 메모리에 올라감 (그 중간에 virtual memory를 거침 → virtual memory는 프로그램만의 독자적인 메모리 주소 공간에 형성된 단계로, code, data, stack으로 구성) 이때, 물리적 메모리에 모든걸 다 올리게 되면 메모리를 낭비하게 되므로 필요한 부분만 physical memory에 올리게 됨(사용이 끝나면 physical memory에서 쫓아냄 → disk의 Swap area)

- code: CPU에서 실행할 기계어 코드 저장
- data: 변수 저장
- stack: 함수를 호출하거나 리턴할 때 데이터를 쌓아뒀다가 꺼냄

*Swap area와 File system은 디스크지만 다른 용도로 사용됨. File system은 전원이 나가도 저장되어 있어야 하지만, Swap area는 전원이 나가면 의미 없는 데이터이므로, 메모리 연장 공간의 용도로 사용됨.

<br>

### 커널 주소 공간의 내용

![image](https://user-images.githubusercontent.com/71377968/201299403-ee6707b9-95dd-425f-bf39-57562fde4a55.png)

- code: 인터럽트에서 무슨 일을 처리해야하는지를 저장함
- data: 운영체제가 사용하는 자료구조
  - PCB(Process Control Block): 프로세스를 관리하는 자료구조로, 프로세스마다 하나씩 존재함
- stack: 함수를 호출하거나 리턴할 때 stack 영역을 이용하는데, 어떤 프로세스가 커널의 코드를 실행 중인가에 따라 프로세스마다 커널 스택을 따로 가짐

<br>

### 함수

- 사용자 정의 함수: 자신의 프로그램에서 정의한 함수
- 라이브러리 함수
  - 자신의 프로그램에서 정의하지 않고 갖다 쓴 함수
  - 자신의 프로그램의 실행 파일에 포함됨
- 커널 함수
  - 운영체제 프로그램의 함수
  - 커널 함수의 호출 = 시스템 콜
    - 주소 점프를 할 수 없어 인터럽트 라인 세팅을 통해 CPU에 제어권을 넘겨야 함

*사용자 정의 함수와 라이브러리 함수는 프로세스의 주소 공간에 있고, 커널 함수는 커널 주소 공간에 있음

<br>

### 프로그램의 실행

![image](https://user-images.githubusercontent.com/71377968/201299463-b22e69e8-7400-4783-90b1-e32870c1f633.png)

- user mode: 프로그램이 직접 CPU를 잡고 있음
- kernel mode: 시스템 콜 시 커널 모드로 넘어감

<br>

<br>

[사진 출처] [https://hyojaedev.tistory.com/29](https://hyojaedev.tistory.com/29)
