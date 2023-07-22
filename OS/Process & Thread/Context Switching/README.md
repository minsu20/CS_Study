## Context Switching

### Process Context Switching

- 하나의 CPU (CPU 코어) 에서는 동시에 하나의 Process만 실행할 수 있으므로, Context Switching을 통해 여러 개의 Process를 번갈아서 실행. 이 과정은 Scheduler가 담당.
- 좀 더 구체적으로는, running 상태이던 Process가 ready가 되며 해당 Process의 상태(Context)를 보관하고, ready 상태이던 다음 순서의 Process가 동작하면서 이전에 보관했던 Process의 상태를 복구하는 작업



### PCB (Process Control Block)

- OS에서 Process를 관리하기 위해, Process의 Context 정보를 담는 Data Structure.
- Process Context Switching 과정에서, running 상태이던 Process를 ready 상태로 전환하며, 그 Process의 Context 정보를 PCB에 저장. 이후 다시 running 상태로 돌려 놓을 때, 어느 작업을 개시할 지 PCB에서 Process Context 정보를 꺼내와 결정.

- 아래와 같은 정보들이 PCB에 담긴다고 한다.
  - 포인터 (Pointer) : 프로세스의 현재 위치를 저장하는 포인터 정보
  - 프로세스 상태 (Process state) : 프로세스의 각 상태 - 생성(New), 준비(Ready), 실행(Running), 대기(Waiting), 종료(Terminated) 를 저장
  - 프로세스 아이디 (Process ID, PID) : 프로세스 식별자를 지정하는 고유한 ID
  - 프로그램 카운터 (Program counter) : 프로세스를 위해 실행될 다음 명령어의 주소를 포함하는 카운터를 저장
  - 레지스터 (Register) : 누산기, 베이스, 레지스터 및 범용 레지스터를 포함하는 CPU 레지스터에 있는 정보
  - 메모리 제한 (Memory Limits) : 운영 체제에서 사용하는 메모리 관리 시스템에 대한 정보
  - 열린 파일 목록 (List of open file) : 프로세스를 위해 열린 파일 목록



### Process' Context Switching Overhead

- Context Switching 과정에서는, 앞서 execute되던 Process에 이어 바로 다음 Process가 execute되기까지, 변경 과정에 시간적 간극이 발생하는데, 이를 Overhead라 한다.

- Context Switching에서 발생하는 Overhead는 보통,

  - PCB 저장 및 복원에 따른 cost.
  - CPU cache memory 무효화에 따른 cost.
  - Process scheduling cost.

  등에 의해 발생된다.

  

---



### Thread Context Switching

- 하나의 Process 내의 Thread들을 교체!
- Thread마다의 교유한 Stack 영역, regsiter 주소 등의 context을 교체해준다는 느낌. Heap, Data, Text(Code) 영역은 공유 자원!



### TCB (Thread Control Block)

- PCB와 비슷하게, OS에서 각 Thread의 Context 정보를 담고 있는 Data Structure.
- TCB는 PCB 내에 담겨 있으며, TCB 역시 Thread의 state, ID, 우선순위, 스케줄링 정보 등의 정보들을 담고 있다.
- PCB, TCB는 Linked List로 구현되어 있다고 한다.
- Thread간의 자원 공유와 동기화가 TCB를 이용하여 관리된다.
  뮤텍스(mutex)나 세마포어(semaphore)와 같은 동기화 기법을 이용할 때, TCB에서 해당 running 상태의 뮤텍스/세마포어 정보를 관리한다. 



### 자원 동기화 문제 (Synchronization Problem)

- Thread Context Switching 과정에서, 새로 running 상태가 된 Thread가 Heap 영역의 공유 자원에 접근할 때,
  이전의 Thread가 이미 그 공유 자원(임계 영역, Critical Section)에 접근하고 있었다면 동기화 문제가 발생할 수 있다.
- 예를 들어, Heap 영역의 동일한 변수를 두 개의 Thread에서 동시에 수정하려고 하면, 잘못된 값으로 변수가 update될 수 있을 것이다. 이것을 Thread 간의 race condition(경쟁 조건) 이라고 한다.
- Process의 경우에도, IPC와 같은 공유 자원을 사용하는 경우, 똑같이 race condition이 발생할 수 있다.
- 이를 해결하기 위해서, 상기했던 mutex나 semaphore와 같은 적절한 동기화 매커니즘이 필요하다.
