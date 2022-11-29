---
layout: post
title: 5. CPU Scheduling 1
subtitle: OS Study Week5-1
categories: OS
tags: OS
sidebar: []
---



반효경 교수님의 [2014-1 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)를 듣고 정리한 글입니다.

<br>

프로그램을 실행하게 되면 어떤 프로그램이건 아래의 절차에 따라 실행이 됨(CPU만 연속적으로 쓰는 단계와 I/O를 하는 단계가 반복되며 나타남)

![image](https://user-images.githubusercontent.com/71377968/204443913-c43ca5d3-6196-4ff2-ad4f-a4a15a390fd7.png)

- 사람과 interactive한 job이 CPU burst와  I/O burst가 빈번하게 번갈아가며 나타남
- 과학 계산용 프로그램과 같은 연산이 긴 프로그램은 CPU를 오래 잡게 됨

![image](https://user-images.githubusercontent.com/71377968/204443955-7af8208b-1327-470b-867b-945a925464cf.png)

- 여러 종류의 Job(=Process)이 섞여 있기 때문에 CPU 스케줄링이 필요함 (I/O bound job 때문이라 생각하면 됨)
  - Interactive job에게 적절한 response 제공
  - CPU와 I/O 장치 등 시스템 자원을 효율적으로 사용
- CPU 스케줄링의 두 가지 이슈
  - 누구에게 CPU를 줄 것인가
  - CPU를 넘긴 상황에서 다시 뺏어올 것인가 혹은 계속 CPU를 사용하게 할 것인가

<br>

### 프로세스의 특성 분류

1. I/O-bound process: CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job → many short CPU bursts
2. CPU-bound process: 계산 위주의 job → few very long CPU bursts

<br>

### CPU Scheduler & Dispatcher

- CPU Scheduler → 운영 체제 내의 CPU 스케줄링을 하는 커널 코드
  - Ready 상태의 프로세스 중에서 어떤 프로세스에게 CPU를 줄지 고름
- Dispatcher → CPU를 넘겨주는 역할을 하는 커널 코드
  - CPU의 제어권을 CPU Scheduler에 의해 선택된 프로세스에게 넘김
  - 이 과정을 **Context switch**라고 함
- CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우
  1. Running → Blocked (ex: I/O 요청 시스템 콜)
  2. Running → Ready (ex: 할당 시간 만료로 timer interrupt)
  3. Blocked → Ready (ex: I/O 완료 후의 Interrupt)
  4. Terminate
     - 1, 4에서의 스케줄링은 **non-preemptive(비선점)** → 자진 반납
     - 다른 스케줄링은 **preemptive(선점)** → 강제로 빼앗음 (현대에는 거의 대부분 해당 방식 사용)

<br>

### Scheduling Criteria

이때, 척도는 CPU burst 한번에 따른 것임

**시스템 입장에서의 성능 척도** → CPU가 일을 많이 하는 것이 중요

- CPU utilization(이용률): 전체 시간 중에서 CPU가 놀지 않고 일한 시간의 비율 → CPU는 가능한 바쁘게 일을 시켜라
- Throughput(처리량): 주어진 시간 동안에 몇 개의 작업을 완료했는가

<br>

**프로세스 입장에서의 성능 척도** → 시간이 빨리 처리되는 것이 중요

- Turnaround time(소요 시간, 반환 시간): CPU를 사용하러 들어와서 다 사용할 때까지 걸리는 시간
  - ready queue에서 기다린 시간 + 실제로 CPU를 사용한 시간
- Waiting time(대기 시간): CPU를 사용하려할 때 기다리는 시간
- Response time(응답 시간): 프로세스가 ready queue에 들어와서 처음으로 CPU를 얻기까지 기다린 시간
  - waiting time과의 차이 → preemptive 방식의 경우, CPU를 빼앗길 수 있음. 이때, 여러번 ready queue에 들어와서 기다릴 수 있는데, waiting time은 이러한 모든 기다린 시간을 합친 개념임. 반면, response time은 처음 CPU를 얻기까지 기다린 시간임!

<br>

비유 예시) 중국집

- utilization: 전체 시간 중에서 주방장이 놀지 않고 일한 시간의 비율
- throughput: 중국집에서 단위 시간 당 손님이 몇명 다녀왔는가
- turnaround time: 손님이 중국집에 들어와서 다 먹고 나갈 때까지의 총 시간
- waiting time: 손님이 밥 먹는 시간 말고 총 기다린 시간(코스요리를 시켰을 때, 각 음식을 기다린 시간을 총 합친 것)
- response time: 처음 음식이 나올 때까지 기다린 시간

<br>

### FCFS(First-Come First-Served)

- 먼저 온 순서대로 처리하는 방식 (비선점형)
- 그리 효율적이지 않음 → CPU를 굉장히 오래 잡는 프로세스가 먼저 도착하면 짧게 CPU를 잡는 프로세스들도 오래 기다려야 함

![image](https://user-images.githubusercontent.com/71377968/204446593-380ea323-2710-4bbd-a4c8-4ee19348af35.png)

- waiting time: P1 = 0, P2 = 24, P3 = 27
- average waiting time = (0 + 24 + 27) / 3 = 17

![image](https://user-images.githubusercontent.com/71377968/204449289-72d98691-9a5a-4621-8bb9-58779468b6a4.png)

- waiting time: P1 = 6, P2 = 0, P3 = 3
- average waiting time = (6 + 0 + 3) / 3= 3

⇒ 어떤 프로세스가 먼저 도착하냐에 따라 기다리는 시간이 영향을 많이 받음

- **Convoy effect**: 짧은 프로세스가 긴 프로세스 뒤에 있어 오래 기다려야 하는 현상

<br>

### SJF(Shortest-Job-First)

- CPU를 사용하고자 하는 시간이 가장 짧은 프로세스에게 CPU를 먼저 줌 → 전체적인 큐가 짧아짐
- 주어진 프로세스들에 대해 **minimum average waiting time**을 보장함(preemptive 방식에 대해)
- 두 가지 방식
  - Nonpreemptive: 가장 짧은 프로세스가 도착하여 실행 중일 때, 더 짧은 프로세스가 도착하여도 CPU를 넘겨주지 않는 방식
  - Preemptive: 더 짧은 프로세스가 도착하면 CPU를 빼앗기는 방식
  - Shortest-Remaining-Time-First(SRTF)라고도 부름 → 남은 시간이 가장 짧은 애를 먼저 실행

![image](https://user-images.githubusercontent.com/71377968/204450723-ae20a8f5-5411-4287-9028-705200ec23aa.png)

→ CPU를 다 쓰고 나가는 시점에 스케줄링을 할지 말지 정함

![image](https://user-images.githubusercontent.com/71377968/204450691-db9a508b-c978-4340-b031-a11ad21d1cd1.png)

→ 프로세스가 도착하는 시점에 스케줄링을 할지 말지 정함

<br>

**문제점**

- Starvation(기아 현상): SJF는 극단적으로 CPU 사용이 짧은 job을 선호하므로, CPU 사용 시간이 긴 프로세스는 영원히 실행되지 않을 수 있음
- CPU 사용 시간을 미리 알 수 없음
  - 추정값 이용 가능(과거의 CPU 사용 시간을 이용해 추정 가능 ← **exponential averaging**)

![image](https://user-images.githubusercontent.com/71377968/204452082-a757b3c4-1b8d-48c1-ba39-66466fdad049.png)

<br>

### Priority Scheduling

- 우선 순위가 제일 높은 프로세스에게 CPU를 주는 것
- 두 가지 방식
  - preemptive: 중간에 우선순위가 더 높은 프로세스가 오면 넘겨줌
  - nonpreemptive: CPU를 한 번 잡으면 넘겨주지 않음
- SJF도 우선순위 스케줄링의 일종임 (우선순위 ⇒ CPU 사용 시간)
- 문제점: Starvation ⇒ 해결: Aging(시간이 지날수록 우선순위를 높여줌)

<br>

### Round Robin(RR)

- 현대적인 CPU 스케줄링
- CPU를 줄 때 동일한 크기의 할당 시간을 세팅해서 주게 되고, 해당 할당 시간이 끝나면 timer interrupt에 의해 ready queue의 제일 뒤에 줄을 서게 됨
- 응답 시간이 빨라진다는 장점이 있음 → 누구든지 짧은 시간만 기다리면 CPU를 사용할 수 있음
- n개의 프로세스가 ready queue에 있고, 할당 시간을 q라고 잡았을 때 q time unit 단위로 CPU 시간의 1/n을 얻을 수 있음 → 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않게 됨.
  - q를 아주 짧게 잡으면 CPU를 사용할 수 있는 시간이 빨리 돌아옴
  - 대기 시간이 본인이 CPU를 사용하는 시간에 비례하게 됨
- 성능
  - q가 클 경우 → FCFS
  - q가 작을 경우 → context switch라는 오버헤드가 커짐

![image](https://user-images.githubusercontent.com/71377968/204469472-2431512e-05b0-4f2a-b743-d5d03d1be90f.png)

→ q가 20인 경우의 예시

- 일반적으로 SJF보다 average turnaround time이 길지만 response time은 더 짧아짐
- CPU 사용 시간이 동일한 경우에는 RR을 사용하게 되면 비효율적일 수 있으나, 그런 경우는 드문 경우임
