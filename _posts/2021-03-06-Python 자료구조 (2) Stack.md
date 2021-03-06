# 자료구조 - Stack

## 스택이란

- 나중에 넣은 데이터를 가장 먼저 꺼내는 구조 
  - LIFO(Last-In-First-Out)이라고도 함 
  - 가장 먼저 넣은 데이터가 먼저 나오는(FIFO) 스택과는 반대
- 스택의 장단점 
  - 장점 - 구현이 쉽다, 데이터 저장과 읽기 속도가 빠르다. 
  - 단점 - 데이터의 최대 개수를 미리 정해야 한다. 
- 파이썬에서는 스택 자료구조를 모듈로 따로 지원하지 않는다.

## 스택 구현하기

- 2가지 주요 연산을 지원한다.

  - push - 데이터를 스택에 추가한다.
  - pop - 가장 최근에 넣은 데이터를 스택에서 제거한다.

- 그 외 연산

  - is_empty() - 리스트가 비어있는지를 파악한다.

  - peek() - 스택의 변형 없이 맨 위의 값을 호출한다.

    

- **list를 이용한 구현** 

~~~ python
class stack(list):
  def __init__(self):
    self.stack = list()
    
  def push(self, n):
    self.stack.append(n)
  
  def pop(self):
    if len(self.stack) == 0:
      return -1 
    return self.stack.pop() 
  
  def is_empty(self):
    if len(self.stack) == 0:
      return True 
    return False 
  
  def peek(self):
    return self.stack[-1]
    
~~~

- **Linked List를 이용한 구현** 

~~~ python
class Node:
  def __init__(self, data):
    self.data = data
    self.next = None
    
class Stack:
  def __init__(self, data):
    self.head = None 
    
  def push(self, data):
    new_node = Node(data)
    new_node.next = self.head
    self.head = new_node
      
  def pop(self):
    if self.is_empty():
      return -1 
    
    data = self.head.data
    self.head = self.head.next
    return data
    
  def is_empty(self):
    if self.head:
      return False
    
    return True
    
  def peek(self): 
    if self.is_empty():
      return -1 
    
    return self.head.data
~~~



## 출처

- [잔재미코딩 - 스택](https://www.fun-coding.org/Chapter06-stack.html)
- [초보몽키의 개발공부로그](https://wayhome25.github.io/cs/2017/04/18/cs-20/)
- [daim's blog](https://daimhada.tistory.com/105)
- [파이썬 알고리즘 인터뷰](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791189909178)