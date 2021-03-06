# 자료구조 - Queue

## Queue란 

- 가장 먼저 넣은 데이터를 가장 먼저 꺼내는 구조 
  - FIFO(First-In-First-Out - 선입선출)이라고도 함 
  - 나중에 넣은 데이터가 먼저 나오는(LIFO) 스택과는 반대
- 데크(Deque)나 우선순위 큐는 여러 분야에서 유용하게 사용.
  - BFS, 캐시 등 

## Queue 구현 방법 

### 리스트를 이용한 구현

- list의 append, pop을 이용해 구현

~~~ python
class list_queue(queue):
  def __init__(self):
    self.queue = list()
    
	def enqueue(self, data):
    self.queue.append(data)
    pass
	
  def dequeue(self, data):
    if len(self.queue) == 0:
      return False 
    return self.queue.pop(0)
~~~



### Deque를 이용한 구현

- Double-Ended Queue의 줄임말로, 양쪽 끝을 모두 추출할 수 있는, 큐를 일반화한 형태의 추상자료형(ADT)이다. 
- 양 끝 엘리먼트의 append와 pop이 압도적으로 빠르다는 장점이 있다.
  - 일반적인 리스트는 양 끝 엘리먼트 삽입/제거의 시간복잡도가 O(n)이지만, Deque는 시간복잡도가 O(1)에 불과하다
- collection 모듈에서 deque를 호출하여 사용할 수 있다.

~~~ python
from collection import deque 

queue = deque[1, 2, 3]
queue.append(5) # queue = [1, 2, 3, 5]
queue.popleft() # 1

queue2 = deque[4, 5, 6]
queue.appendleft(7) # queue = [7, 4, 5, 6]
queue.pop() # 6
~~~



### 파이썬의 queue 모듈의 Queue 클래스를 이용한 구현 

- queue 라이브러리에서는 Queue(), LifoQueue(), PriorityQueue()를 제공합니다.
  - Queue - 일반적인 큐 
  - LifoQueue() - 나중에 입력된 데이터가 먼저 출력(스택과 유사)
  - PriorityQueue() - 우선순위 큐 

~~~ python
from queue import Queue 

que = Queue()
que.put(4)
que.put(5)
print(que) # [4, 5]
que.qsize # 2
que.get(4) # 4
~~~



## 우선순위 큐 

- 특정 조건에 따라 우선순위가 가장 높은 요소가 추출되는 자료형이다.
- 힙과 관련이 깊다.
  - queue 모듈의 PriorityQueue는 내부적으로 heapq 모듈을 사용하도록 구현되어 있다.
  - 다만, PriorityQueue는 heapq와 달리 Thread-Safe 클래스라는 차이점이 있다.

~~~ python
from queue import PriorityQueue

que = PriorityQueue()
que.put(4) # 우선순위 큐에 원소 추가
que.get() # 우선순위 큐의 원소 삭제 

que_two = PriorityQueue()
que_two.put((3, 'Bus'))
que_two.put((1, 'Subway'))
que_two.put((2, 'Airplane'))

que_two.get()[1] # Subway
~~~



## 출처

- [잔재미코딩 - 큐](https://www.fun-coding.org/DS&AL1-5.html)
- [파이썬에서 큐(queue) 자료 구조 사용하기](https://www.daleseo.com/python-queue/)
- [daim's blog](https://daimhada.tistory.com/107)
- [파이썬 알고리즘 인터뷰](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791189909178)

