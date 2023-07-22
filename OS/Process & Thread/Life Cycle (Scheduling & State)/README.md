## Process, Thread's Life Cycle

### Process Scheduling & State

- OS에서는 CPU를 사용할 수 있는 Process를 선택해, CPU를 할당.
  - 여러 알고리즘(FCFS, SJF, Priority, RR, Multilevel Queue) 을 이용해 Process를 Scheduling하고, Multi-tasking이 가능하도록 함. (종류는 아래 Thread Scheduling에서.)
- Process는 실행되는 동안 아래와 같은 고유의 여러 상태를 갖게 됨.
  - 생성 (new) : Process 생성 직후 (ready되지 않음).
  - 준비 (ready) : 실행 대기 상태의 Process. 언제든 CPU를 할당받아 실행될 수 있는 상태.
  - 실행 (running) : CPU를 할당받아 실행되는 상태.
  - 대기 (waiting) : 특정 이벤트 (I/O 등)에 의해 대기 중인 상태. 이벤트가 종료되고, 다시 ready 상태로 돌아오기를 대기.
  - 종료 (terminated) : 실행이 끝나고 종료된 Process 상태. 메모리에서 제거.
- Process가 실행되는 동안, OS에서의 Process Scheduling에 의해 프로세스의 상태가 변경됨.
  알고리즘에 의해 ready상태의 Process 중 어떤 것을 running 상태로 선택하여 올릴 지가 결정.
  - Admitted (new → ready)
  - Dispatch (ready→ running)
  - Interrupt (running → ready)
  - I/O or event wait (running → wainting)
  - I/O or event completion (waiting → ready)



---



### Thread Scheduling & State

- Process Scheduling과 마찬가지로, Thread에 CPU를 할당하는 작업.

- Process Scheduling Algorithm과 유사하게 동작.

  - FCFS 스케줄링(First Come First Served Scheduling)
  - SJF 스케줄링(Shortest Job First Scheduling)
  - HRRN 스케줄링(Highest Response Ratio Next Scheduling)
  - 라운드 로빈 스케줄링(Round Robin Scheduling)
  - 다단계 큐 스케줄링(Multilevel Queue Scheduling)
  - 다단계 피드백 큐 스케줄링(Multilevel Feedback Queue Scheduling)

  등이 존재한다.

- Process Scheduling과 다른 점은, 하나의 Process 내에 다수의 Thread가 연속적으로 동작하기 때문에, 스레드 간의 상호작용, 그리고, **공유 자원의 동기화 문제**를 고려해야 함!

- Process와 마찬가지로, Thread 또한 실행되는 동안 여러 상태를 갖게 됨.

  - new : 스레드가 생성되고 아직 호출되지 않은 상태
  - runnable : 스레드가 실행되기 위해 기다리는 상태 (CPU를 할당받을 수 있는 상태)
  - running
  - blocked : 스레드가 특정 이벤트(입출력 요청 등)가 발생하여 대기하는 상태. CPU를 할당받지 못하며, 이벤트가 발생하여 다시 runnable 상태로 전환될 때까지 대기.
  - terminated : 스레드가 실행을 완료하고 종료된 상태. 더 이상 실행될 수 없으며, 메모리에서 제거.
