---
layout: post
title: 3-1. Process 1
subtitle: OS Study Week3-1
categories: OS
tags: OS
sidebar: []
---



반효경 교수님의 [2014-1 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)를 듣고 정리한 글입니다.

<br>

### 프로세스의 개념

프로세스: 실행 중인 프로그램

- 프로세스의 문맥; 이 프로그램이 무엇을 어떻게 실행했는지, 

  현재 시점이 어떤 상태에 있는지

  를 정확하게 나타내기 위한 것

  - 예시
    - PC(Program Counter)가 코드의 어느 부분을 가리키고 있는가
    - 프로세스의 메모리에 어떤 내용을 담고 있는가
    - 함수가 호출된 상태였다면 스택에 뭔가가 쌓여있을 텐데 현재 어디까지 쌓여있는가
    - 데이터 영역의 변수의 값은 얼마인가
    - 레지스터에 어떤값을 넣어 놓고 어떤 인스트럭션까지 실행했는가 등
  - CPU 수행 상태를 나타내는 하드웨어 문맥: PC, 각종 레지스터
  - 프로세스의 주소 공간(메모리와 관련): code, data, stack
  - 프로세스 관련 커널 자료 구조: PCB(Process Control Block), Kernel stack

<br>

### 프로세스의 상태

→ 프로세스는 상태를 변경하며 수행 됨

![image](https://user-images.githubusercontent.com/71377968/202898046-6d9d65f2-6bfd-40ef-a43f-ad9c0b285f72.png)

- Running: CPU를 잡고 인스트럭션을 수행 중인 상태
- Ready: 메모리 등 다른 조건을 모두 만족하고 CPU를 기다리는 상태
- Blocked(wait, sleep)
  - CPU를 주어도 당장 인스트럭션을 수행할 수 없는 상태
  - 프로세스 자신이 요청한 이벤트(예: I/O와 같은 오래 걸리는 작업)가 즉시 만족되지 않아 이를 기다리는 상태
  - ex: 디스크에서 파일을 읽어와야 하는 경우
- 경우에 따라 추가되는 상태
  - New: 프로세스가 생성 중인 상태
  - Terminated: 프로세스의 수행이 끝난 상태(약간 정리할 게 남아 있는 그런 상태 → 완전히 끝나면 프로세스가 아님)



![image](https://user-images.githubusercontent.com/71377968/202898163-bf600c8f-9384-4909-8518-2f86b2b93ba2.png)

- 예시
  - running 상태의 프로세스가 사용자로부터 키보드 입력을 받아서 결과를 보고 실행한다고 가정했을때, 해당 프로세스는 keyboard I/O queue에 줄을 서게 됨(running → blocked)
  - 키보드 입력을 받으면 keyboard controller가 CPU에게 인터럽트를 걸어서 알려주면, CPU는 하던 일을 멈추고 운영체제에게 넘어가서 프로세스 상태를 ready로 바꿈(blocked → ready)
- 하드웨어 뿐만 아니라 공유데이터에 동시에 접근하는 경우나 어떠한 오래 걸리는 작업을 하면 프로세스는 blocked 상태가 될 수 있음
- 또, 여기의 queue는 운영체제 커널의 data 영역에 있는 것임

<br>

### PCB

운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보

아래의 구성 요소를 구조체로 유지하며 가짐

- OS가 관리상 사용하는 정보
  - Process state, Process ID
  - scheduling information, priority
- CPU 수행 관련 하드웨어 값
  - Program counter, registers
- 메모리 관련
  - Code, data, stack의 위치 정보
- 파일 관련
  - Open file descriptors 등

<br>

### 문맥 교환

CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정

- 과정
  - CPU를 내어주는 프로세스 상태를 해당 프로세스의 PCB에 저장
  - CPU를 새롭게 얻는 프로세스의 상태를 해당 프로세스의 PCB에서 읽어옴

![image](https://user-images.githubusercontent.com/71377968/202899040-1d407baf-d006-4a1c-8459-a5a4c4926e74.png)

- 이때, System call이나 인터럽트 발생 시 반드시 context switch가 일어나는 것은 아님
  - CPU가 운영체제로 넘어가는 것은 context switch가 아님!! 만약 운영체제가 여기서 다른 프로세스로 CPU를 넘겨줘야만 context switch가 되는 것

<br>

**프로세스를 스케줄링하기 위한 큐**

- Job queue: 현재 시스템 내에 있는 모든 프로세스의 집합
- Ready queue: 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
- Device queue: I/O device의 처리를 기다리는 프로세스의 집합

![image](https://user-images.githubusercontent.com/71377968/202899321-b2354848-e201-4756-b5a6-5b795a0ecfaa.png)

<br>

### 스케줄러

- Long-term scheduler(장기 스케줄러 or job scheduler) → 지금의 시스템에는 없음
  - 메모리를 어떤 프로세스에게 줄 지 결정
  - 프로세스가 new → ready 상태로 변환될 때 admit되는 게 메모리에 admit되는 건데 여기서 장기 스케줄러가 해당 프로세스에 메모리를 줄지 안 줄지 결정하는 것
  - degree of Multiprogramming(메모리에 올라가있는 프로세스의 수)을 제어함
  - time sharing system에는 무조건 ready 상태로 올리기 때문에 보통 장기 스케줄러가 없음
- Short-term scheduler(단기 스케줄러 or CPU scheduler)
  - 굉장히 짧은 시간 단위(millisecond 단위)로 스케줄링이 일어남
  - 다음번에 어떤 프로세스에게 CPU를 줄 지 결정
- Medium-term scheduler(중기 스케줄러 or Swapper) → 지금의 시스템
  - 여유 공간 마련을 위해 일부 프로세스를 통째로 메모리에서 디스크로 쫓아냄 → degree of Multiprogramming 제어
  - 프로세스에서 메모리를 빼앗는 문제
- 중기 스케줄러의 존재로 새로 정의된 프로세스의 상태 → **Suspended(stopped)**
  - 외부적인 이유로 프로세스의 수행이 정지된 상태
  - 프로세스는 통째로 디스크에 swap out됨
  - 예시
    - 사용자가 프로그램을 일시 정지시킨 경우(break key) 시스템이 여러 이유로 프로세스를 잠시 중단시킴
    - 메모리에 너무 많은 프로세스가 올라와 있을 때 중기 스케줄러가 정지시킴
    - 사람이 ctrl-z 키를 누른다던가 해서 프로세스를 정지시키는 경우에도 suspended 상태가 됨. 이때 active 되려면 사람이 다시 실행해줘야함
- Suspended와 Blocked 상태 구분
  - blocked: 자신이 요청한 이벤트가 만족되면 ready 상태로 변환
  - suspended: 외부에서 resume을 해주어야만 active됨

<br>

### 프로세스 상태도(suspended 포함)

![image](https://user-images.githubusercontent.com/71377968/202899909-5965645c-0bb0-40b1-9185-26ce47e66aa9.png)

- Running 상태
  - user mode: 프로세스를 CPU를 가지고 있으면서 본인의 코드를 실행 중인 상태
  - kernel mode: system call이나 interrupt 혹은 trap으로 인해 운영체제의 코드가 실행 중인 상태

<br>

<br>

[사진 출처] [https://hyojaedev.tistory.com/30](https://hyojaedev.tistory.com/30)
