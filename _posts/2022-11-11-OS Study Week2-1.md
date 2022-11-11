---
layout: post
title: 2-1. System Structure & Program Execution 1
subtitle: OS Study Week2-1
categories: OS
tags: OS
sidebar: []
---



반효경 교수님의 [2014-1 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)를 듣고 정리한 글입니다.

<br>

### 컴퓨터 시스템 구조

![image](https://user-images.githubusercontent.com/71377968/201288523-a2bb4dfe-3967-40d9-b83e-23d4c9c48079.png)
![image](https://user-images.githubusercontent.com/71377968/201288578-020a7768-b55c-4e8e-8fd9-04e80544a120.png)

**device controller**: I/O 디바이스를 전담하는 작은 CPU 같은 것(HW)

- ex: 디스크의 내부 컨트롤은 디바이스 컨트롤러가 맡음
- 제어 정보를 위해 control register, status register를 가짐
- local buffer: device controller의 작업 공간
- I/O는 실제 device와 local buffer 사이에서 일어남
- I/O가 끝났을 경우 device controller는 interrupt로 CPU에 그 사실을 알림

*device driver(장치구동기): OS 코드 중 각 장치별 처리 루틴(SW)

<br>

**CPU**는 메모리에 있는 인스트럭션을 불러와서 실행하고 interrupt line을 체크함

register: 메모리보다 더 빠른 CPU 내의 저장 공간

**mode bit**: CPU에서 실행되는 것이 운영체제인지 사용자 프로그램인지 구분해주는 것

- 모니터모드[0] (커널 모드, 시스템 모드): 운영체제가 CPU에서 수행중인 것
  - CPU는 I/O device 접근, 메모리 접근 등 모든 것을 다 할 수 있음
- 사용자모드[1]: 사용자 프로그램이 CPU에서 수행중인 것
  - 제한된 instruction만 CPU에서 할 수 있음

**interrupt line**: 요청을 전달하는 곳

<br>

**timer**: 특정 프로그램이 CPU를 독점하는 것을 막기 위한 하드웨어(사용자 프로그램에게 timer에 할당된 시간에 따라 CPU를 넘겨줌(→ timer의 시간이 되면 interrupt로 CPU에게 사용 시간이 끝남을 알리고 CPU 제어권이 운영체제로 넘어감)

<br>

**DMA controller**(Direct Memory Access controller): 원래는 메모리에는 CPU만 접근할 수 있으나 DMA controller도 접근할 수 있음

- I/O device에서 다수의 인터럽트를 발생시키고, 모든 인터럽트에 대해 CPU가 실시간 대응하면 CPU 사용에 있어서 오버헤드 발생 → DMA가 I/O의 데이타(I/O 디바이스의 buffer의 값)를 모음 → 데이터를 바이트가 아닌 block 단위로 한 번에 메모리에 접근해서 저장하며, 처리 결과를 단 한 번의 인터럽트로 cpu에 전달.

<br>

**memory controller**: 특정 메모리 영역을 동시에 접근하려고 할 때 문제가 발생할 수 있으므로 해당 문제를 중재하기 위한 역할을 수행함

<br>

*I/O 작업은 보안 상의 이유로 OS가 하게 됨

사용자 프로그램에서 I/O device를 사용하려고 하면 알아서 OS로 CPU를 넘겨주게 됨(그 이후에는 다른 프로그램이 CPU를 넘겨 받음)

→ I/O device의 처리가 끝나면 device controller가 interrupt를 걸고, 해당 interrupt에 따라 CPU 제어권은 OS에 넘어가게 됨

→ CPU를 넘겨받은 OS는 interrupt의 이유를 파악해 I/O device의 결과값을 해당 사용자 프로그램으로 넘겨주게 되고 일반적으로는 기존에 실행 중이던 프로그램의 시간이 남았으면 그 프로그램이 계속 실행됨

→ 언젠가는 I/O가 끝난 프로그램이 실행되는 것

<br>

### **I/O의 수행**

- 모든 입출력 명령은 특권 명령임 (OS의 소관)
- 사용자 프로그램의 I/O 수행
  - 시스템콜(system call): 사용자 프로그램은 운영체제에게 I/O 요청
  - trap을 사용하여 인터럽트 벡터의 특정 위치로 이동
  - 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동
  - 올바른 I/O 요청인지 확인 후 I/O 수행
  - I/O 완료 시 제어권을 시스템콜 다음 명령으로 옮김

<br>

### **인터럽트**

- 인터럽트 당한 시점의 레지스터와 program counter를 저장한 후 CPU의 제어를 인터럽트 처리 루틴에 넘김
- 넓은 의미의 interrupt
  - interrupt(HW interrupt): 하드웨어가 발생시킨 인터럽트
  - Trap(SW interrupt): Exception(프로그램이 오류를 범한 경우), System call(프로그램이 커널 함수를 호출하는 경우)
- 인터럽트 벡터: 해당 인터럽트의 처리 루틴 주소를 가지고 있음
- 인터럽트 처리 루틴(Interrupt Service Routine, 인터럽트 핸들러): 해당 인터럽트를 처리하는 커널 함수

*현대의 운영체제는 인터럽트에 의해 구동됨(OS는 인터럽트가 들어오는 경우에만 CPU를 사용하게 됨)
