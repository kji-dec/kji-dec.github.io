---
layout: post
title: 4. Process Management 1, 2
subtitle: OS Study Week4
categories: OS
tags: OS
sidebar: []
---



반효경 교수님의 [2014-1 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)를 듣고 정리한 글입니다.

<br>

### 프로세스 생성 (Process Creation)

- 부모 프로세스(Parent process)가 자식 프로세스(Child process)를 생성함
  - 주로 “복제”를 이용 → 프로세스의 문맥을 복제
- 프로세스의 트리(계층 구조) 형성
- 프로세스는 자원을 필요로 함
  - 운영체제로부터 받음
  - 부모와 공유하는 모델도 있고 아닌 경우도 있음
- 자원의 공유
  - 부모와 자식이 모든 자원을 공유하는 모델
  - 일부를 공유하는 모델
  - 전혀 공유하지 않는 모델 (→ 이게 일반적인 경우. 부모와 자식은 서로 CPU를 얻으려고 경쟁하게 됨)
  - Copy-on-write: 계속 공유하고 있다가 달라지는 점이 있을 때, 그 부분을 copy함
- 수행 (Execution)
  - 부모와 자식은 공존하며 수행되는 모델
  - 자식이 종료(terminate)될 때까지 부모가 기다리는(wait - blocked) 모델
- 주소 공간(Address space)
  - 자식은 부모의 공간을 복사함 (binary and OS data)
  - 자식은 그 공간에 새로운 프로그램을 올림
- 유닉스의 예
  - fork() 시스템 콜이 새로운 프로세스를 생성함
    - 부모를 그대로 복사(OS data except PID + binary)
    - 주소 공간 할당
  - fork() 다음에 이어지는 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올림 → 새로운 프로그램으로 주소 공간을 덮어 씌우는 것
  - 이때 fork, exec은 모두 시스템 콜임. OS의 일!

<br>

### 프로세스 종료(Process Termination)

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (exit) - 자발적
  - 자식이 부모에게 output data를 보냄 (wait) - 자식이 먼저 종료되고, 부모는 뒷일을 처리하는 구조
  - 프로세스의 각종 자원들이 운영체제에게 반납됨
- 부모 프로세스가 자식의 수행을 종료시킴 (abort) - 비자발적
  - 자식이 할당 자원의 한계치를 넘어선 경우
  - 자식에게 할당된 태스크가 더이상 필요하지 않은 경우
  - 부모가 종료(exit)하는 경우
    - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더이상 수행되도록 두지 않음
    - 단계적인 종료가 이루어짐

<br>

### fork() 시스템 콜

- 프로세스는 fork() 시스템 콜에 의해 생성됨
  - creates a new address space that is a duplicate of the caller
- child는 fork 시점부터 실행하게 됨
  - parent의 context (PC)를 복사하므로 어디서부터 실행해야하는지 알 수 있음

```c
int main() {
		int pid;
    pid = fork();
    if (pid == 0){  //child
        printf("\\n Hello, i am child!\\n");
    }
    else if (pid>0){ //parent
        printf("\\m Hello, i am parent!\\n");
    }
}
```

<br>

### exec() 시스템 콜

- 프로그램은 exec() 시스템 콜에 의해 다른 프로세스가 될 수 있음
  - replaces the memory image of the caller with a new program
- exec() 시스템 콜을 실행하면 새로운 프로그램으로 덮어씌워지고 처음부터(main부터) 실행하게 됨
- 한번 exec() 시스템 콜을 실행하면 되돌아갈 수 없음

```c
int main() {
    int pid;
    pud = fork();
    if (pid == 0){  //child
        printf("\\n Hello, i am child! Now i'll run date \\n");
        execlp("/bin/date", "/bin/date", (char*) 0);
    }
    else if (pid > 0 ){  //parent
        printf("\\n Hello, i am parent!\\n");
    }
}
```

- 꼭 자식 프로세스를 생성한 후 exec을 실행해야하는 건 아님! (아래와 같은 경우도 가능)
- 단, exec 이후의 코드인 parent 출력문은 영원히 실행되지 않음 ← 새로운 프로그램으로 덮어씌워졌기 때문

```c
int main() {
    printf("\\n Hello, i am child! Now i'll run date \\n");
    execlp("/bin/date", "/bin/date", (char*) 0);
    printf("\\n Hello, i am parent!\\n");
}
```

<br>

### wait() 시스템 콜

- 프로세스 A가 wait() 시스템 콜을 호출하면
  - 커널은 child가 종료될 때까지 프로세스 A를 sleep 시킴 (block)
  - child process가 종료되면 커널은 프로세스 A를 깨움 (ready)

![image](https://user-images.githubusercontent.com/71377968/204136101-039f19fc-6c88-4ea7-a911-71374c910bac.png)

- 위 사진의 경우, 부모 프로세스에서 wait 호출이 있음 → 자식 프로세스의 코드가 모두 수행된 후 종료된 시점에 else를 빠져나가 다음 코드를 실행할 수 있음
  - ex: 리눅스의 쉘 프롬프트

<br>

### exit() 시스템 콜

- 프로세스를 종료시킬 때 호출하는 시스템 콜
- 자발적 종료
  - 마지막 statement 수행 후 exit() 시스템 콜을 통해
  - 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌
- 비자발적 종료
  - 부모 프로세스가 자식 프로세스를 강제 종료시킴
    - 자식 프로세스가 한계치를 넘어서는 자원 요청
    - 자식에게 할당된 태스크가 더 이상 필요하지 않음
  - 키보드로 kill, break 등을 친 경우
  - 부모가 종료하는 경우
    - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료됨

<br>

### 프로세스 간 협력

- 독립적 프로세스 (Independent process)
  - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
- 협력 프로세스 (Cooperating process)
  - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
- 프로세스 간 협력 메커니즘(**IPC**: Interprocess Communication)
  - 메시지를 전달하는 방법 - **message passing**: 커널을 통해 메시지 전달
  - 주소 공간을 공유하는 방법 - **shared memory**: 서로 다른 프로세스 간 일부 주소 공간을 공유하게 하는 메커니즘
    - shared memory의 사용을 위해서는 해당 프로세스들이 신뢰할 수 있는 관계여야 함
    - Thread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 프로세스를 구성하는 Thread들 간에는 주소 공간을 공유하므로 협력이 가능

![image](https://user-images.githubusercontent.com/71377968/204136544-3c7c50fc-e709-4001-a37e-63dc1f0d68b0.png)

<br>

### Message Passing

원칙적으로 프로세스는 독립적이기 때문에 메시지를 서로 직접 전달할 수 없음 → 커널 이용

- Message system: 프로세스 사이에 공유 변수를 일체 사용하지 않고 통신하는 시스템

1. Direct Communication: 통신하려는 프로세스의 이름을 명시적으로 표시

![image](https://user-images.githubusercontent.com/71377968/204136558-d7303e9d-28d4-46e5-9353-5fb79d4758b7.png)

2. Indirect Communication: mailbox 또는 port를 통해 메시지를 간접 전달

![image](https://user-images.githubusercontent.com/71377968/204136564-1c325b65-4af9-4874-8bae-bcebe07eaf31.png)

- 위의 두 경우 모두 커널을 사용하는 건 동일!
