## Bubble Sort(버블정렬)

![Bubble-sort](https://user-images.githubusercontent.com/19471818/111146534-e5ca6000-85cc-11eb-820a-e22e08f9ae6d.gif)

### 개념 

- 인접한 두개의 데이터를 비교해서, 앞의 데이터가 뒤의 데이터 보다 크면 둘을 맞바꾸는 알고리즘
- 최악의 경우 O(N^2)의 시간복잡도를 가진다.
  - 우선, 루프를 통해 모든 인덱스에 접근한다 - O(N)
  - 루프 하나 당 인접한 값들의 대소 비교와 위치 바꿈을 진행한다 - O(N)
  - 결국 O(N)) * O(N) 이므로 O(N^2)
- 최선인 경우는 완전 정렬이 되어 있을 때이며, 이 역시  O(N^2)의 시간복잡도를 가진다.

### 구현

~~~ python
def bubble_sort(data):
    for num in range(len(data) - 1):
        for i in range(len(data) - num - 1):
            if data[i] > data[i+1]:
                data[i], data[i+1] = data[i+1], data[i]
            i += 1
    
    return data
~~~

## Insertion Sort(삽입정렬)

![Insertion-sort-example](https://user-images.githubusercontent.com/19471818/111146658-08f50f80-85cd-11eb-9c88-ea59498097d4.gif)

### 개념 

- 자료 배열의 모든 요소를 앞에서부터 차례대로 이미 정렬된 배열 부분과 비교 하여, 자신의 위치를 찾아 삽입함으로써 정렬을 완성하는 알고리즘
- 시간복잡도는 O(N^2)이다.

### 구현

~~~python
# insertion sort 
def insertion_sort(data):
    for num in range(len(data) - 1):
        for i in range(num + 1, 0, -1):
            if data[i] <= data[i-1]:
                data[i-1], data[i] = data[i], data[i-1]
            i += 1        
            
    return data
~~~

- 개선안 - 앞의 배열은 모두 정렬되어 있으므로, 한 번도 교환이 일어나지 않으면 탐색을 종료한다.

~~~python
def improved_insertion_sort(data):
    for i in range(1, len(data)):
        while i > 0 and data[i-1] > data[i]:
            data[i-1], data[i] = data[i], data[i-1]
            i -= 1 
            
    return data
~~~

## Selection Sort(선택정렬)

![Selection-Sort-Gif](https://user-images.githubusercontent.com/19471818/111146787-2cb85580-85cd-11eb-825c-33e03b5ad5ea.gif)

### 개념 

- 최소값을 선택하는 과정을 반복해서 선택 정렬이라고 부른다.
- 시간복잡도는 O(N^2)이다.

### 구현

~~~ python
def selection_sort(data):
    for num in range(len(data) - 1):
        min_idx = num
        for i in range(num + 1, len(data)):
            if data[i] < data[min_idx]:
                min_idx = i 
             
        data[num], data[min_idx] = data[min_idx], data[num]
            
    return data
~~~

## 출처 

- [삽입 정렬(insertion sort)이란](https://gmlwjd9405.github.io/2018/05/06/algorithm-insertion-sort.html)
- [초보몽키의 개발공부로그](https://wayhome25.github.io/cs/2017/04/16/cs-17/)
- [잔재미코딩](https://www.fun-coding.org/Chapter12-bubblesorting.html)