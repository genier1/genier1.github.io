# 자료구조 - Hash Table 



## Hash Table이란 

![1920px-Hash_table_3_1_1_0_1_0_0_SP svg](https://user-images.githubusercontent.com/19471818/110459297-27ac5f80-8110-11eb-8c9d-1e899231a6e7.png)

- 키와 밸류를 저장하는 데이터 구조 
- Key를 통해 바로 데이터를 받아올 수 있으므로, 속도가 획기적으로 빨라짐
  - 대부분의 연산이 분할 상환 분석에 따른 시간 복잡도가 O(1)이다.
  - 최악의 경우(충돌이 모두 발생하는 경우)에는 O(n)이다. 
- 보통 배열로 미리 Hash Table 사이즈만큼 생성 후에 사용 (공간과 탐색 시간을 맞바꾸는 기법)
- 파이썬에서는 딕셔너리 타입을 사용하면 된다.
- 주요 용도
  - 검색이 많이 필요한 경우
  - 저장, 삭제, 읽기가 빈번한 경우
  - 캐쉬 구현시 

## 용어 설명

- 해시(Hash): 임의 값을 고정 길이로 변환하는 것
- 해시 테이블(Hash Table): 키 값의 연산에 의해 직접 접근이 가능한 데이터 구조
- 해싱 함수(Hashing Function): Key에 대해 산술 연산을 이용해 데이터 위치를 찾을 수 있는 함수
- 해시 값(Hash Value) 또는 해쉬 주소(Hash Address): Key를 해싱 함수로 연산해서, 해쉬 값을 알아내고, 이를 기반으로 해쉬 테이블에서 해당 Key에 대한 데이터 위치를 일관성있게 찾을 수 있음
- 슬롯(Slot): 한 개의 데이터를 저장할 수 있는 공간

## 충돌(Collision)

1. 다른 내용의 데이터가 같은 키를 갖는 경우 이를 해시 충돌이라고 한다.
2. 충돌은 생각보다 쉽게 일어난다.
   1. 생일 문제 - 23명만 모여도 생일이 겹칠 확률이 50%를 넘는다.
3. Open Hashing과 Close Hashing 기법이 있다. 

### Chaining 

- Open Hashing 기법이라고 부르기도 한다. 

- 충돌이 발생할 경우, 링크드 리스트로 연결하는 방식이다.

- 기본적인 자료구조와 간단한 알고리즘만 있으면 되므로 인기가 높다.

- 가장 전톡적인 방식이다.

  

### Open Addressing

![724px-HASHTB12 svg](https://user-images.githubusercontent.com/19471818/110459497-64785680-8110-11eb-8e87-13a89c84c38c.png)

- Linear Probing 또는 Close Hashing이라고 부르기도 한다.
- 충돌이 발생할 경우 해당 해쉬 값의 다음 index부터 빈 공간을 탐색한다.
- 전체 슬롯의 개수 이상 저장할 수 없으며, 이 경우 공간을 확장해주어야 한다. 
- 해시테이블에 저장되는 데이터들이 고르게 분포되지 않고 뭉치는 현상이 발생하는 문제가 있다.(클러스터링)  
- 파이썬의 경우 오픈 어드레싱 방식으로 해시 테이블을 구현하였다. 
  - 체이닝 시 malloc으로 메모리를 할당하는 오버헤드가 높기 때문
  - 오픈 어드레싱의 한 방식인 선형 탐사 방식은 일반적으로 체이닝에 비해 성능이 좋다.
    - 단, 슬롯의 80% 이상이 차게 되면 급격한 성능 저하가 발생함.

## Hash Table 구현하기 

### Chaining 기법

~~~python
class HashTable:
    def __init__(self, table_size):
        self.size = table_size
        self.hash_table = [0 for i in range(table_size)]
    
    def get_key(self, data):
        self.key = ord(dataa)
        return self.key
    
    def hash_func(self, key):
        return key % self.size
    
    def save_data(self, key):
        hash_key = get_key(key)
        hash_address = self.hash_func(hash_key)
        
        # Hash Table에 이미 값이 있는 경우
        if self.hash_table[hash_address] != 0:
            for i in range(len(hash_table[hash_address])):
                if hash_table[hash_address][i][0] == hash_key:
                    hash_table[hash_address][i][0] == value
                    return
            hash_table[hash_address].append([index_key, value])
            return 
        else:
            self.hash_table[hash_address] = [[hash_key, value]]
        		return
    def read_data(self, key):
        hash_key = get_key(key)
        hash_address = self.hash_func(hash_key)
        
        if self.hash_table[hash_address] != 0:
            for i in range(len(self.hash_table[hash_address])):
                if self.hash_table[hash_address][i][0] == hash_key:
                    return self.hash_table[hash_address][i][1]
            return None
        else:
            return None
~~~

### Open Addressing 기법

~~~ python
class HashTable:
    def __init__(self, table_size):
        self.size = table_size
        self.hash_table = [0 for i in range(table_size)]
    
    def get_key(self, data):
        self.key = ord(dataa)
        return self.key
    
    def hash_func(self, key):
        return key % self.size
    
    def save_data(self, key):
        hash_key = get_key(key)
        hash_address = self.hash_func(hash_key)
        
        # Hash Table에 이미 값이 있는 경우
        if self.hash_table[hash_address] != 0:
            for i in range(hash_address, len(self.hash_table)):
                if self.hash_table[i] == 0:
                    self.hash_table[i] = [hash_key, value]
                elif self.hash_table[i][0] == hash_key:
                    self.hash_table[i][1] = value
                    return
                
        else:
            self.hash_table[hash_address] = [hash_key, value]
        
    def read_data(self, key):
        hash_key = get_key(key)
        hash_address = self.hash_func(hash_key)
        
        if self.hash_table[hash_address] != 0:
            for i in range(hash_address, len(self.hash_table)):
                if self.hash_table[i] == 0:
                    return None
                elif self.hash_table[i][0] == index_key:
                    return self.hash_table[i][1]
        else:
            return None
~~~

## 출처

- [잔재미코딩 - 해쉬테이블](https://www.fun-coding.org/Chapter09-hashtable.html)
- [파이썬 알고리즘 인터뷰](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791189909178)
- [Wikipedia - 해시 테이블](https://ko.wikipedia.org/wiki/%ED%95%B4%EC%8B%9C_%ED%85%8C%EC%9D%B4%EB%B8%94)

