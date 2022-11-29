---
layout: post
title: 5-2. CPU Scheduling 2
subtitle: OS Study Week5-2
categories: OS
tags: OS
sidebar: []
---



반효경 교수님의 [2014-1 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)를 듣고 정리한 글입니다.

<br>

### Multilevel Queue

![image](https://user-images.githubusercontent.com/71377968/204631070-a00b53c7-425e-4e5f-9e23-931ac59c061f.png)

- 위로 갈수록 우선순위가 높음
- 고려 사항
  - 프로세스를 어느 줄에 넣을 것인가
  - 우선순위가 높은 큐가 비어있지 않으면 계속 우선순위가 높은 큐가 CPU를 가지고 있고 다른 큐로는 넘기지 않을 것인가 ← 우선 순위가 낮은 큐는 starvation
- ready queue를 여러개로 분할 (여러 줄로 CPU를 기다리는 것)
  - foreground(interactive) ← RR(사람과 상호작용을 해야하므로 짧은 시간 여러번 도는 게 나음)
  - background(batch - CPU만 오래 잡고 있는 job) ← FCFS(어차피 response가 빨라야 하는 것도 아니니 context overhead를 줄이는 게 나음)
- 큐에 대한 스케줄링 필요
  - Fixed priority scheduling: 우선순위가 높은 큐가 비어있을 때만 낮은 큐가 기회를 얻는 방식 → starvation
  - Time slice: 각 큐에 CPU time을 적절한 비율로 할당
- 차별적인 방안이지만, 우선 순위가 높은 프로세스가 더 빨리 CPU를 얻어야 하므로 이런 방법을 사용함
- 그러나, 우선순위에 변동이 없다는 것은 문제가 있음 → multilevel feedback queue

<br>

### Multilevel Feedback Queue

![image](https://user-images.githubusercontent.com/71377968/204631655-480bff64-53de-4531-b05f-8c3e55b243ed.png)

- 프로세스가 다른 ready queue로 갈 수 있음
- 고려 사항
  - queue를 몇 개를 둘 건지
  - 큐 내의 스케줄링 알고리즘을 어떤걸로 사용할 것인가
  - 우선순위가 높은 큐에서 낮은 큐로 떨어지는 기준은 어떻게 정할 것인가
  - 우선순위가 낮은 큐에서 높은 큐로 올라가는 기준은 어떻게 정할 것인가
  - 처음 프로세스가 들어갈 때 어떤 큐로 들어갈 것이냐
- 보통은 처음 들어오는 프로세스는 우선순위가 가장 높은 큐에 집어넣음(RR로, q를 짧게 줌) → 아래의 큐로 갈 수록 RR의 q를 길게 줌(결국 마지막 단은 FCFS) → 상위 큐에서 할당 시간을 써도 안되면 점차 하위 큐로 내려가게 됨
- CPU 사용 시간이 짧은 프로세스에게 우선 순위를 많이 주는 스케줄링 방식임 → 사용 시간 예측이 필요 없으나 CPU 사용 시간이 짧은 프로세스가 이점을 얻는 방식!

<br>

### Multiple-Processor Scheduling

- Homogeneous processor인 경우
  - Queue에 한 줄로 세워서 각 프로세스가 알아서 꺼내가게 할 수 있음
  - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐
- Load Sharing ← 특정 CPU는 일 하고 나머지 CPU는 놀고 있으면 안됨
  - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 매커니즘 필요함
  - 별도의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- Symmetric Multiprocessing (SMP)
  - 모든 CPU가 대등한 것으로, 각 프로세서가 각자 알아서 스케줄링 결정
- Asymmetric multiprocessing
  - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

<br>

### Real-Time Scheduling

→ 정해진 시간 안에 반드시 실행이 되어야 하는, deadline이 있는 프로세스일 경우 사용

- hard real-time systems: 정해진 시간 안에 반드시 작업이 끝나도록 스케줄링 해야 함
- soft real-time computing: deadline이 있긴 하지만 지키지 못했다고 해서 위험한 상황이 발생하지는 않음. but, 일반 프로세스에 비해서는 높은 우선순위를 갖도록 구현함

<br>

### Thread Scheduling

- Local Scheduling
  - 유저 레벨 스레드의 경우 운영체제가 해당 스레드의 존재를 모르고 사용자 프로세스가 직접 스레드를 관리함
- Global Scheduling
  - 커널 레벨 스레드의 경우 운영체제가 해당 스레드의 존재를 알고 있어 일반 프로세스처럼 커널의 단기 스케줄러가 어떤 스레드를 스케줄할 지 결정함

<br>

### Algorithm Evaluation

**Queueing model**

- 굉장히 이론적인 방법
- 프로세스들의 도착률과 CPU의 처리율이 확률 분포로 주어질 때, 복잡한 수학적 계산을 통해 여러가지 성능 척도 결과를 얻을 수 있음 (throughput, average waiting time, etc.)

**Implementation(구현) & Measurement(성능 측정)**

- 실제 시스템에 구현해서 그걸 돌려보고 실제 성능을 측정함
- 실제 환경을 만드는 것은 어려움

**Simulation(모의 실험)**

- 가상으로 CPU 스케줄링 프로그램을 작성한 후 프로그램의 CPU 요청을 입력값으로 넣어 어떠한 결과가 나오는지 확인함
- 실제 시스템에서 추출한 입력값을 trace라 함
