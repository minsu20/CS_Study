## Process

- 메모리에 적재되고 CPU 자원을 할당받아 실행되고 있는 '프로그램'
- OS로부터 자원을 할당받은 '작업의 단위'



## Thread

- 하나의 Process 내에서 동시에 진행되는 작업단위.
- 1 Process : N Thread. 스레드 수가 많을수록 (평균적으로) 작업속도가 빨라져 성능 상승.
  - Thread의 Context Switching이나, Synchronize(동기화) 문제들을 고려하면, 오히려 작업속도가 느려지는 경우도 종종 있을 수 있음.
