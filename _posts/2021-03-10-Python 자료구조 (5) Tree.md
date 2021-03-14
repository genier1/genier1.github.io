# Tree 

![1920px-Binary_search_tree](https://user-images.githubusercontent.com/19471818/110778196-61629f00-82a5-11eb-9267-115fcae5f129.png)

## 개념 

- 비선형 자료구조이며 계층적 관계를 표현하는 자료구조이다. 
- 트리의 종류로는 이진 트리, 이진 탐색 트리, 균형 트리(AVL 트리, red-black 트리), 이진 힙 등이 있다.
- 평균 시간 복잡도가 O(logn)이다. 다만 최악의 경우는 O(n)이다. 
  - 최악 - 트리가 한 줄로 구성되어 있는 경우

## 용어 정리

![tree](https://user-images.githubusercontent.com/19471818/110778326-8d7e2000-82a5-11eb-9495-fa1600468126.png)

1. Node(노드) - 트리에서 데이터를 저장하는 기본 요소
2. Root node(루트) - 트리 맨 위에 있는 노드
3. Edge (간선) - 노드들을 연결하는 간선 
4. Depth(깊이) - 루트에서 어떤 노드에 도달하기 위해 거쳐야 하는 간선의 수
5. Height(높이) - 루트에서 어떤 노드에 도달하기 위해 거쳐야 하는 간선의 수
6. Degree(차수) - 각 노드가 가진 간선의 수 

## 이진 트리 

1. 이진 트리란

   1. 모든 노드의 차수가 2 이하일때 이진 트리라고 구분하여 부른다.
   2. 이진 탐색 트리의 경우 이진 트리에 추가적인 조건이 붙는다.
      1. 왼쪽 노드는 부모 노드보다 작은 값, 오른쪽 노드는 부모 노드보다 큰 값을 가짐 

2. 이진 트리의 유형 

   - 정 이진 트리(Full Binary Tree)
     - 모든 노드가 0개 또는 2개의 자식 노드를 갖는다. 
   - 완전 이진 트리(Complete Binary Tree)
     - 마지막 레벨을 제외하고 모든 레벨이 완전히 채워져 있는 트리이며, 마지막 레벨의 모든 노드는 가장 왼쪽부터 채워져 있다. 

   - 포화 이진 트리(Perfect Binary Tree)
     - 모든 노드가 2개의 자식 노드를 갖고 있으며, 모든 리프 노드가 동일한 깊이 또는 레벨을 갖는다. 

3. 이진 탐색 트리(Binary Search Tree - BST)

   1.  이진탐색(binary search)과 연결리스트(linked list)를 결합한 자료구조의 일종
   2. 이진탐색의 효율적인 탐색 능력을 유지하면서도, 빈번한 자료 입력과 삭제를 가능하게끔 고안되었음


## 그래프와 트리와의 차이 

- 트리는 순환구조를 갖지 않는 그래프이다. DAG(Directed Acyclic Graph)의 한 종류
- 트리는 하나의 부모 노드를 가지며 루트 노드 또한 하나라는 차이점이 있다. 

## 트리의 순회

- 순회란 트리의 각 노드를 체계적인 방법으로 중복 없이 모두 반복하는 과정을 말한다.
- 노드를 방문하는 순서에 따라 전위순회(preorder), 중위순회(inorder), 후위순회(postorder) 세 가지로 나뉜다.

### 전위순회(preorder)

- 루트노드에서 시작해서 노드 - 왼쪽 서브트리 - 오른쪽 서브트리 순으로 순회하는 방식

~~~python
def preorder(node):
    print(node.data)
    preorder(node.left)
    preorder(node.right, end='')
~~~

### 중위순회(inorder)

- 루트노드에서 시작해서 왼쪽 서브트리 - 노드 - 오른쪽 서브트리 순으로 순회하는 방식

~~~python
def inorder(node):
    inorder(node.left)
    inorder(node.right)
    print(node.data, end='')
~~~

### 후위순회(postorder)

- 루트노드에서 시작해서 왼쪽 서브트리 - 오른쪽 서브트리 - 노드 순으로 순회하는 방식

~~~python
def postorder(node):
    postorder(node.left)
    postorder(node.right)
    print(node.data, end='')
~~~

## 트리 탐색방법(BFS와 DFS) 

### BFS(Breadth First Search)

- 그래프의 최단 경로를 이용하는 문제 등에 사용된다.
- 주로 큐를 이용하여 탐색을 수행한다.  

### DFS(Depth First Search)

- 스택을 이용하여 탐색을 수행한다.  
- 백트래킹을 통해 뛰어난 효용성을 보인다.
- 일반적으로 BFS에 비해 더 널리 쓰인다.

## 트리 구현 

### 트리

~~~ python
class Node:
    def __init__(self, data):
        self.data = data
        self.left = None
        self.right = None
    
class tree:
    def __init__(self, data):
        self.head = data
    
    def insert(self, data):
        head = self.head
        if self.head > data:
            if head.left is None:
                head.left = Node(data)
            else:
                self.left.insert(data)
        elif self.head <= data:
            if head.right is None:
                head.right = Node(data)
            else:
                self.right.insert(data)
        else:
            self.data = data 
~~~

## 출처

- [파이썬 알고리즘 인터뷰](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791189909178)
- [[자료구조] 트리(Tree)란](https://gmlwjd9405.github.io/2018/08/12/data-structure-tree.html)
- [이진탐색트리(Binary Search Tree)](https://ratsgo.github.io/data%20structure&algorithm/2017/10/22/bst/)
- [[파이썬으로 구현한 알고리즘] (7) 트리(Tree)](https://onestep.tistory.com/42)

