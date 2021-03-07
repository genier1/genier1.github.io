# 자료구조 - Linked List



## Linked List란 

- 배열과 함께 가장 기본이 되는 대표적인 선형 자료구조이며 다양한 추상 자료형(ADT) 구현의 기반이 된다.
- 떨어진 곳의 데이터를 화살표로 연결하여 관리하는 데이터 구조
- 탐색에는 O(N)이 소요된다. 
- 파이썬에서는 리스트를 통해 링크드 리스트를 구현 

## Linked List의 장단점

### 장점

- 미리 데이터 공간을 할당하지 않아도 된다.
- 배열에 비해 데이터의 추가/삽입 및 삭제가 쉽다.

### 단점

- 연결을 위한 별도의 공간이 필요하므로, 저장공간 효율이 높지 않다.
- 연결 정보를 찾는 시간이 필요하므로 접근 속도가 느리다.

## Linked List의 주요 용어

- 노드(Node) - 데이터 저장 단위 (데이터값, 포인터) 로 구성
- 포인터(Pointer) - 각 노드 안에서, 다음이나 이전의 노드와의 연결 정보를 가지고 있는 공간

## Singly Linked List 구현

![Singly-linked-list](https://user-images.githubusercontent.com/19471818/110234672-17a94a00-7f6f-11eb-8081-bb30270c7d7f.png)

~~~ python
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next
    
class singly_linked_list:
    def __init__(self):
        self.head = None
 
    def insert(self, data):
        # 노드가 없을 경우, head에 새 노드를 저장
        if self.head == '':
            self.head = Node(data)
        # 노드가 존재하는 경우에는 head에 새 노드를 저장한 후, 기존 노드를 새 노드의 next로 저장
        else:
            self.head = Node(data, self.head)
    
    def delete(self, data):
    # 노드가 없을 경우
        if self.head == 'None':
            print("삭제할 노드가 없습니다.")
            return 
            # self.head를 삭제할 경우
        if self.head.data == data:
            tmp = self.head
            self.head = self.head.next
            del tmp
        # self.head가 아닌 중간 노드를 삭제할 경우 
        else:
            if node.next.data == data:
                tmp = node.next
                node.next = node.next.next
                del tmp
            else:
                node = node.next
       
    # 리스트에 data가 있는지 확인하는 함수    
    def search(self, data): 
        node = self.head
        while node:
            if node.data == data:
                return True
            else:
                node = node.next
                return False

    def print_list(self):
        node = self.head
        while node:
            print(node.data)
            # 다음 노드로 이동
            node = node.next 
~~~



## Doubly Linked List 구현

![Doubly-linked-list](https://user-images.githubusercontent.com/19471818/110234704-4cb59c80-7f6f-11eb-8c39-55bf34e57311.png)

~~~ python
class Node:
    def __init__(self, data, prev=None, next=None):
        self.prev = prev
        self.data = data
        self.next = next
    
class doubly_linked_list:
    def __init__(self, data):
        self.head = Node(data)
        self.tail = self.head
 
    def insert_before(self, data, before_data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
            return True
            
        else:
            node = self.tail
            while node.data != before_data:
                node = node.prev
                if node==None:
                    return False

            prev_node = node.prev
            new_node = Node(data,prev=prev_node,next=node)
            if prev_node:
                prev_node.next = new_node
            else:
                self.head = new_node
            node.prev = new_node
            return True
            
            
    # 노드 데이터가 data인 노드 뒤에 데이터 추가
    def insert_after(self, data, after_data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
            return True 
        else:
            node = self.head
            while node.data != after_data:
                node = node.next
                if node==None:
                    return False
            next_node = node.next
            new_node = Node(data, prev=node, next=next_node)
            if next_node:
                next_node.prev = new_node
            else:
                self.tail = new_node
            node.next = new_node
            
    def delete(self, data):
      	# 노드가 없을 경우
        if self.head == None:
            print("삭제할 노드가 없습니다.")
            return False 
        
        # data가 head에 있을 경우
        elif self.head.data == data:
            tmp = self.head 
            self.head = self.head.next
            self.head.prev = None
            del tmp 
            
        # data가 tail에 있을 경우
        elif self.tail.data == data:
            tmp = self.tail
            self.tail = self.tail.prev 
            self.tail.next = None
            del tmp
        
        else:
            node = self.head
            while node:
                if node.data == data:
                    tmp = node 
                    node.prev.next = node.next 
                    node.next.prev = node.prev 
                    del tmp 
                node = node.next

    
    def print_list(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next
~~~

## 출처

- [잔재미코딩 - 링크드리스트](https://www.fun-coding.org/DS&AL1-4.html)
- [초보몽키의 개발공부로그](https://wayhome25.github.io/cs/2017/04/18/cs-19/)
- [daim's blog](https://daimhada.tistory.com/72)
- [파이썬 알고리즘 인터뷰](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791189909178)
- [Wikipedia - Linked list](https://en.wikipedia.org/wiki/Linked_list)

