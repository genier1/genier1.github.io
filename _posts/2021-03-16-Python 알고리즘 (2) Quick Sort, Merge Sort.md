## Quick Sort

![Quicksort-example](https://user-images.githubusercontent.com/19471818/111298892-a9613780-8692-11eb-9dd1-6e3a29bbc928.gif)

### 개념 

- 분할 정복(Divide and Conquer) 방식을 통해 리스트를 정렬하며 재귀적으로 수행한다.
- 평균적인 시간 복잡도는 O(nlogn)이며 최악의 경우에는 시간 복잡도가 O(n^2)이다.
  - 최악의 경우 - 리스트가 계속 불균형하게 나누어지는 경우 등 

### 구현

- 리스트 안에 있는 한 요소를 선택한다. 이렇게 고른 원소를 피벗(pivot) 이라고 한다.
- 피벗을 기준으로 피벗보다 작은 요소들은 모두 피벗의 왼쪽으로, 피벗보다 큰 요소들은 모두 피벗의 오른쪽으로 옮긴다.
- 이렇게 나뉘어진 왼쪽 리스트와 오른쪽 리스트에 대해서도 위의 작업을 재귀적으로 진행한다.

~~~ python
def quick_sort(data):
    if len(data) <= 1:
        return data 

    left, right = list(), list()
    pivot = data[0]
    
    for index in range(1, len(data)):
        if pivot > data[index]:
            left.append(data[index])
        else:
            right.append(data[index])
       
    return quick_sort(left) + [pivot] + quick_sort(right)

# 개선된 버전 - 리스트 컴프리헨션 

~~~

## Merge Sort(병합정렬)

![Merge-sort-example-300px](https://user-images.githubusercontent.com/19471818/111299015-c72e9c80-8692-11eb-92c9-7d1d8d347e5a.gif)

### 개념 

- 분할 정복(Divide and Conquer) 방식을 통해 리스트를 정렬하며 재귀적으로 수행한다.
- 시간복잡도는 최악의 경우에도 O(nlogn)이다. 

### 구현

- 리스트를 중간 지점인 mid를 중심으로 왼쪽 리스트(left_list)와 오른쪽 리스트(right_list)로 분할한다
- Left_listd와 right_list에 대해서도 분할작업을 재귀적으로 진행한다.
- 분리된 리스트를 merge 함수를 통해 합친다.
- merge 함수에서는 왼쪽과 오른쪽 리스트의 첫번째 원소부터 비교하여 작은 값을 데이터로 저장하고 이를 왼쪽과 오른쪽 리스트의 원소가 모두 없어질 때까지 반복한다.

~~~python
def merge(left_list, right_list):
    left, right = 0, 0
    data = list()
    
    # 둘 다 있을 때 
    while left < len(left_list) and right < len(right_list): 
        if left_list[left] < right_list[right]:
            data.append(left_list[left])
            left += 1 
        else:
            data.append(right_list[right])
            right += 1    

    # 왼쪽만 있을 때 
    while left < len(left_list):
        data.append(left_list[left])
        left += 1 
    
    # 오른쪽만 있을 때 
    while left < len(left_list):
        data.append(right_list[right])
        right += 1 
    
    return data

def merge_sort(data):
    if len(data) <= 1:
        return data
    mid = int(len(data) / 2)
    left_list = merge_sort(data[:mid])
    right_list = merge_sort(data[mid:])
    return merge(left_list, right_list)
~~~

## 출처 

- [합병정렬(Merge Sort)](https://ratsgo.github.io/data structure&algorithm/2017/10/03/mergesort/)
- [합병 정렬(merge sort)이란](https://gmlwjd9405.github.io/2018/05/08/algorithm-merge-sort.html)
- [잔재미코딩](https://www.fun-coding.org/DS&AL4-6.html)
- [위키피디아](https://ko.wikipedia.org/wiki/%ED%80%B5_%EC%A0%95%EB%A0%AC)

