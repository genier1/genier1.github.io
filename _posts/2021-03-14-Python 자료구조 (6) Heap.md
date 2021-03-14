# Heap

## Heap이란

- 최대값과 최소값을 빠르게 찾기 위해 고안된 완전 이진 트리
- 최대값, 최소값 탐색시 시간 복잡도가 O(logn)으로 배열보다 빠르다. 
- 루트 노드가 최대 값이면 Max Heap, 최소 값이면 Min heap으로 분류된다.
- 이진 탐색 트리와 자주 비교된다.
  - 이진 탐색 트리와 달리 Heap은 중복된 값을 허용한다.
  - 부모노드가 자식 노드보다 항상 크거나(Max Heap) 작지만(Min Heap), 같은 레벨의 노드끼리는 굳이 정렬하지 않는다. - 느슨한 정렬

## Heap 구현

- Python에서는 Heapq 모듈을 통해 Min Heap을 구현하며, 리스트로도 구현 가능하다.
- 구현을 쉽게 하기 위하여 배열의 첫 번째 인덱스를 None으로 처리한다.

### Max Heap의 추가와 삭제 

- 원소 추가
  - Heap의 가장 마지막에 원소를 추가한 후 
  - Max Heap의 추가 예시

![max_heap_animation](https://user-images.githubusercontent.com/19471818/111065915-9ec26900-84ff-11eb-93b4-3e86ff1c8dd7.gif)

- 원소 삭제
  - 루트 노드와 가장 마지막에 있는 노드를 맞바꾼 후 규칙에 따라 Heap을 정렬한다.
  - Max Heap의 삭제 예시

![max_heap_deletion_animation](https://user-images.githubusercontent.com/19471818/111065840-25c31180-84ff-11eb-8f6b-085a4bba6d09.gif)

### Heapq를 통한 구현 

~~~python
import heapq 

heap = [] 
# heap에 노드 추가 
heapq.heappush(heap, 1) 
heapq.heappush(heap, 3)
# 노드 삭제 - 가장 작은 원소를 삭제함
heapq.heappop(heap)

# 리스트를 힙으로 변환
heap_list = [7, 5, 8, 3]
heapq.heapify(heap_list)

# heapq 모듈로 Max Heap 구현하기 
nums = [4, 1, 7, 3, 8, 5]
max_heap = []
for i in nums:
  heapq.heappush(max_heap, (-i, i)) # max_heap에 (우선순위, 값) 추가
~~~

### List를 통한 구현 (Max Heap)

~~~python
class heap:
    def __init__(self):
        self.array = [None]
        
    def insert(self, data):
        if len(self.array) == 1:
            self.array.append(data)
            return True
        
        self.array.append(data)
        current_idx = len(self.array) - 1
        while current_idx > 1:
            parent_idx = self.find_parent(current_idx)
            if self.array[parent_idx] < self.array[current_idx]:
                self.array[parent_idx], self.array[current_idx] = self.array[current_idx], self.array[parent_idx]
                current_idx = parent_idx
            else:
                break

        
    def find_parent(self, index):
        return index // 2 
    
    def delete(self): 
        if len(self.array) == 1:
            return None
        
        self.array[1], self.array[-1] = self.array[-1], self.array[1]
        delete_data = self.array.pop(-1)
        # heap 재정렬
        self.max_heapify(1)
        return delete_data
        
        
    def max_heapify(self, index):
        idx = index
        left_idx = index * 2 
        right_idx = index * 2 + 1
        
        # 왼쪽 노드가 있고, 노드값이 왼쪽 노드보다 작을 경우
        if left_idx <= len(self.array) - 1 and self.array[idx] <= self.array[left_idx]:
            idx = left_idx
        # 오른쪽 노드가 있고, 노드값이 오른쪽 노드보다 작을 경우
        if right_idx <= len(self.array) - 1 and self.array[idx] <= self.array[right_idx]:
            idx = right_idx

        if idx != index:
            self.array[idx], self.array[index] = self.array[index], self.array[idx]
            self.max_heapify(idx)
~~~

## 출처

- [잔재미코딩](https://www.fun-coding.org/Chapter11-heap.html)
- [[파이썬] heapq 모듈 사용법](https://www.daleseo.com/python-heapq/)
- [Python으로 구현하는 자료구조: Heap](https://daimhada.tistory.com/108)
- [애니메이션 출처 - tutorialspoint](https://www.tutorialspoint.com/data_structures_algorithms/heap_data_structure.htm)

