## Multi Process & Multi Thread

### Multi Process

- OS에서 하나의 프로그램에 대해 동시에 여러 Process를 생성하여 실행하는 기술.
- 하나의 부모(Parent) Process가 여러 자식(Child) Process를 생성하는 방식.
- 각각의 Process는 Process ID (PID)가 존재하며, Parent Process는 Child Process의 PID를 알고 있어 제어가 가능하고, Child Process는 Parent Process의 PID (PPID)를 알고 있어, 이를 이용해 Parent Process와 통신이 가능하다.

#### Pros.

1. 프로그램 안전성

각 Process가 독립적인 메모리 공간을 지니고, 각각 독립적으로 실행되므로, 한 Process가 비정상적으로 종료되어도, 다른 Process는 정상적으로 작동한다.

2. 프로그램 병렬성

Multi Process와 수 개의 CPU Core를 이용해, 작업을 분산시켜 빠른 속도로 처리할 수 있다.

3. 시스템 확장성

각 Process가 독립적으로 작동하므로, 새로운 기능과 모듈을 추가할 때 다른 Process에 영향을 끼치지 않는다.

#### Cons.

1. Context Switching Overhead

2. 자원 공유의 비효율성

Process 간의 정보 공유가 불가피할 때는, IPC 기법을 활용하여 통신해야 하는데, IPC를 사용하는 자체로 Overhead가 발생한다.



---



### Multi Thread

- 하나의 Process에서 여러 Thread를 생성해 두 가지 이상의 동작을 동시에 처리하도록 하는 기능.

#### Pros.

1. Process보다 가벼운 Thread

당연하게도, 용량이 Process보다 가볍고, Context Switching 시 Overhead도 적다. (Stack 영역만을 교체하기 때문.)

2. 자원 효율성

Thread 간 Heap, Data, Text 영역을 공유하기 때문에, 자원의 효율적인 운용이 가능하고, 시스템 자원 소모가 감소한다.

3. Context Switching 비용 감소.

4. 응답 시간 단축.

#### Cons.

1. 안정성 문제.

Multi Processing과 다르게, 하나의 Thread가 죽으면 다른 Thread도 영향을 받아 전체 프로그램이 종료될 수 있음.

이는 예외 처리를 하는 등 여러 기법을 통해 방지가 가능하나, 추가적인 메모리가 소모된다.

2. 동기화로 인한 성능 저하.

여러 Thread가 공유 자원에 동시에 접근할 수 있어, Synchronization Problem이 발생한다. 따라서 이를 mutex, semaphore 등의 방식으로 관리해 주어야 하고, 이는 Thread의 접근을 제한하는 것이기 때문에, 성능이 저하될 수 있다.

3. Deadlock.

다수의 Thread가 서로가 점유하는 공유 자원에 접근하려 대기하게 되면, 여러 Thread가 무한정 대기 상태가 되는 교착 상태가 될 수 있다.

상호배제(Mutual Exclusion), 점유와 대기(Hold and Wait), 비선점(No Preemption), 순환 대기(Circular Wait) 와 같은 알고리즘으로 방지한다.

4. Context Switching Overhead.

때문에 Multi-Threading이 항상 단일 Thread의 작업보다 성능이 향상된다고 할 수 없다.

5. 디버깅 시의 어려움
