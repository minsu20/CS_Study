![image](https://github.com/minsu20/CS_Study/assets/86006389/684e7685-fe0c-4b20-a003-961639929ee5)


| Array | Linked List |
| :---: | :---: |
| 유사한 데이터 type의 collection | 포인터를 사용하여 서로 연결된 동일한 유형의 요소의 순서가 지정된 Collection |
| Random Access를 지원하므로 인덱스를 사용하여 요소에 직접 액세스할 수 있다. 따라서 배열의 요소에 액세스하는 것은 의 일정한 시간 복잡도로 빠르다 -> O(1) . | Linked List는 Sequential Access를 지원하므로 연결 목록의 모든 요소/노드에 액세스하려면 전체 연결 목록을 해당 요소까지 순차적으로 탐색해야 한다. 연결 리스트의 n번째 요소 에 접근하기 위한 시간복잡도는 O(n). |
| 배열에서 요소는 연속적인 메모리 위치 또는 연속적인 방식으로 메모리에 저장 | 연결된 목록에서 새 요소는 메모리의 아무 곳에나 저장할 수 있다. 새 요소에 할당된 메모리 위치의 주소는 연결 목록의 이전 노드에 저장되므로 두 노드/요소 사이에 링크가 형성된다. |
| 배열에서는 메모리 위치가 연속적이고 고정되어 있기 때문에 삽입 및 삭제 작업에 더 많은 시간이 걸린다. 인덱스로 접근 및 삭제 O(1) + 전체 배열 요소를 1씩 Shift O(N) | 링크드 리스트의 경우 새로운 요소가 첫 번째 여유 있고 사용 가능한 메모리 위치에 저장되며, 링크드 리스트의 이전 노드에 메모리 위치의 주소를 저장하는 단일 오버헤드 단계만 있다. 삽입하려는 위치 접근 후 삽입 및 삭제 O(N). 만약, 맨 앞과 뒤에 삽입 및 삭제한다면 O(1) |
| 컴파일 타임 에 배열이 선언되자마자 메모리가 할당 → 정적 메모리 할당 | 메모리는 새 노드가 추가될 때 런타임 에 할당 → 동적 메모리 할당   |
| 배열에서 각 요소는 독립적이며 인덱스 값을 사용하여 액세스할 수 있다. | 연결된 목록의 경우 각 노드/요소는 다음, 이전 또는 두 노드를 가리킨다. |
| 배열은 1차원 , 2차원 또는 다차원 | 선형 연결 리스트, 이중 연결 리스트, 원형 연결 리스트 가 될 수 있다. |
| 배열 크기는 배열 선언 시 지정해야 한다. | 링크드 리스트 크기는 가변적, 더 많은 노드가 추가됨에 따라 런타임에 커진다. |
