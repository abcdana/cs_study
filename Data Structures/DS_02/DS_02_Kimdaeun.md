# 단일 LinkedList와 이중 LinkedList의 차이는 무엇인가요?
단일 LinkedList
- 각 노드는 다음 노드에 대한 포인터를 하나만 가지고 있음
- 단방향으로만 탐색 가능
- 메모리 사용이 적음
- 삽입과 삭제 시, 이전 노드를 추적해야 함
  
이중 LinkedList
- 각 노드는 이전 노드와 다음 노드에 대한 포인터를 모두 가지고 있음.
- 양방향으로 탐색 가능.
- 더 많은 메모리가 필요함(두 개의 포인터).
- 이전 노드로의 이동이 쉬워 삭제와 삽입이 더 간단함.

# LinkedList의 삽입/삭제 성능은 언제 효율적이지 않을 수 있나요?
- 검색 시간이 많이 걸릴 때 -> 특정 인덱스를 찾기 위해 처음부터 탐색해야 하므로 **O(n)**의 시간이 걸림
- 중간에 삽입/삭제할 때 -> 탐색 후 위치를 찾는 과정 때문에 효율적이지 않을 수 있음

# ArrayList와 LinkedList는 각각 어떤 상황에서 더 적합한가요?
ArrayList
- 랜덤 접근이 빠를 때: 인덱스 기반 접근은 O(1)의 시간 복잡도를 가진다.
- 삽입/삭제가 드물고, 데이터가 고정적일 때 : 그러나 끝에서 추가 및 삭제는 효율적임
- 메모리 사용이 더 적음
  
LinkedList
- 빈번한 삽입/삭제가 필요할 때 : 노드 삽입/삭제는 O(1)의 시간 복잡도를 가진다.
- 데이터 크기가 가변적일 때
- 탐색이 많은 경우 비효율적일 수 있음(접근 시간 O(n))
