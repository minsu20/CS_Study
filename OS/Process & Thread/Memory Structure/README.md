## Process & Thread's Memory Structure

### Process'

- Code(Text) Area [Static]
  실행되는 프로그램의 코드가 기계어 형태로 번역되어 저장.

- Data Area [Static]
  코드가 실행되며 사용되는 전역 변수 등의 데이터를 저장. .rodata .data, .BSS 영역으로 세분화.
  (.rodata, .data, .BSS 순으로 메모리가 Code(Text)영역에 가까움)
  - .rodata
    const와 같은 상수 키워드 선언 변수, 문자열 상수 등이 저장.
  - .data
    전역 변수, static 변수 등을 저장.
  - .BSS
    초기값 없는 전역 변수, static 변수 등을 저장.

- Heap Area [Dynamic]
  생성자, 인스턴스와 같은 동적으로 할당되는 데이터들을 저장.
  사용자에 의해 메모리 공간이 동적으로 할당되고 해제.
- Stack Area [Dynamic]
  지역 변수 등을 저장.
  Stack은 함수의 호출과 함께 할당되며, 함수의 호출이 완료되면 소멸.
  만일 Stack 영역을 초과하면 stack overflow 에러가 발생.



---



### Thread's 

- Process의 메모리 영역 중 Stack 영역만 복사하여 각각의 Thread에 할당.
  - Stack 영역을 각각 할당받는다는 것은 -> 독립적인 함수 호출이 가능하다는 것. 즉, 독립적인 실행의 흐름(Thread의 개념)을 가질 수 있게 됨.
- Heap, Data, Code 영역은 하나의 Process에 있는 Thread끼리는 공유하는 자원.
- 이를 통해 Process의 수행 능력을 향상시킬 수 있음



#### Process의 자원 공유

- 소켓 통신, IPC, LPC 등으로 Process도 다른 Process의 정보에 접근이 가능하나, 기회비용이 커 잘 사용하지 않음.
