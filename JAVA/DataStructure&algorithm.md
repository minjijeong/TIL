### 배열 Array 
- 초기 사이즈가 고정으로 정의되어야 하는 자료구조
- 초기에 선언되기 때문에 메모리에 연달아 할당됨. 
- 인덱스 기준으로 쉽게 찾을 수 있다. (인덱스 기준으로 조회 시 속도가 빠르다) = cache hit 높다 


### List 
- 초기에 사이즈를 고정하는 옵션도 있지만, 보통은 사이즈 고정없이 목록을 구성한다. 
- 리스트에 자료를 추가할때마다 메모리에 적재되므로 데이터끼리 비슷한 위치에 존재하지 않을 수 있다. 
- 특정 인덱스 기준으로 삭제 추가가 자유롭다. 

### Map 
- key value 기준으로 맵핑되어 있다. 
- key를 모른다면 keyset을 가지고 와서 다시 조회해야 한다. 
- 리스트와 동일하게 메모리 활당이 유동적으로 된다. 

### Queue
- FIFO (First In First Out) 처음 적재된 데이터를 제일 먼저 꺼내쓸수 있다. 
- 데이타 적재된 순서대로 데이타를 꺼낼수 있다. 
- 또는 제일 첫번째 데이터를 peek을 통해 확인 가능하다. 
- Queue.add vs Queue.offer - add()는 빈값을 추가했을때 exception으로 처리, offer()는 빈값이더라도 처리안되고 false 리턴
- Queue<T> queue = new LinkedList<>()

### Stack 
삽입과 삭제가 한쪽 끝에서 이루어지는, 순서가 매겨진 리스트이다. 
- LIFO (Last in First Out) 후입선출 마지막에 적재된 데이타를 제일 먼저 꺼낼수 있다. 
  (= FILO, First In Last Out) 선입후출 
- push(삽입), pop(삭제) 으로 적재 또는 꺼내기 가능하다. 
- queue와 동일하게 peek으로 제일 먼저 적재된 데이터를 확인 가능하다 
- Stack<T> stack = new Stack<>(); 
- top : 스택에 마지막에 추가된 항목을 삭제하지 않고 리턴 
- isEmptyStack : 스택에 항목이 저장되어 있는지 확인
- isFullStack : 스택이 가득 찼는지 아닌지를 확인
  

### Deck (Double Ended Queue)
- Deque(Double Ended Queue) 양쪽 끝에서 데이타 추가/삭제 가능하다 
- 연속적인 메모리를 기반으로 하는 '시퀀스 컨테이너'이다. 따라서, 임의 접근 반복자 제공.
- 여러 개의 메모리 단위로 데이터를 저장한다. vector는 메모리를 재할당하고 모든 요소를 복사하여야 하는데, deque는 새로운 메모리 단위를 할당하여 요소를 추가한다.또 데이터 요소를 저장하는 여러 개의 메모리 단위를 갖습니다.
- 크기가 가변적이다. (선언 후에 변경할 수 있다.)
- 중간 요소가 삽입, 삭제될 때, 요소들을 앞/뒤로 밀 수 있으므로 vector보다는 좋은 성능을 갖음. 그래도, 앞/뒤에서의 삽입/삭제 성능은 좋지만 중간에서는 좋지 않다.

### Vector 
- 기존 배열의 크기 제한에 따른 문제를 해결한 것으로 자료구조 중에 배열의 개념을 구현
- **ArrayList와 동일한 기능을 가지고 있어, JAVA에서는 해당 자료형을 거의 쓰지 않고, 호환성 때문에 남겨놓았다.** 
```java
    Vector<Integer> vector = new Vector<>();
    vector.add(5);
    vector.add(4);
    vector.add(3);
    vector.add(-1);
    vector.add(2,100);

    System.out.printf("벡터 안의 요소 객체 수 : %d \n", vector.size());
    System.out.printf("벡터의 현재 용량 : %d \n", vector.capacity());

    for(Integer a : vector){
        System.out.println(a);
    }
        /**
         * 벡터 안의 요소 객체 수 : 5 
         * 벡터의 현재 용량 : 10 
         * 5
         * 4 
         * 100  // 인덱스 2인 위치에 삽입
         * 3    // 그 뒤에 항목들은 뒤쪽으로 한칸씩 밀림 
         * -1
         */
```

### Collection 


### Iterator


### Tree 
단하나의 루트 노드가 있고 하위노드들이 연결된 비선형 계층 구조
뿌리(Root) : 부모가 없는 노드
간선(Edge) : 부모로부터 자식에게 이어지는 연결 선
잎(Leaf) : 자식이 없는 노드
형제(Siblings) : 같은 부모를 가진 자식들
레벨 : 주어진 깊이의 모든 노드의 집합 (같은 깊이)
노드의 깊이 : 뿌리로부터 그 노드까지의 경로의 길이
노드의 높이 : 그 노드부터 가장 깊은 노드까지의 경로의 길이
트리의 높이 : 트리의 모든 노드의 높이 중 최대 값
트리의 깊이 : 트리의 모든 노드의 깊이의 최대 값
노드의 크기 : 자기 자신을 포함하여 그 노드가 가진 자손의 수 
경사(skew) : 트리의 모든 노드가 오직 한 개의 자식만을 가진 트리(잎노드 제외) 
- 오른쪽 자식만 있으면 오른쪽 경사 트리
- 왼쪽 자식만 있으면 왼쪽 경사트리  


### Binary Tree 
모든 노드가 최대 2개의 자식노드를 가질 수 있는 구조 
(빈 트리 역시 유효한 이진 트리)
- 엄격한 이진 트리 : 모든 노드가 두 개의 자식을 가지거나 자식이 없는 트리
- 포화 이진 트리 : 모든 노드가 두 개의 자식을 가지고 잎 노드가 같은 레벨에 있는 노드 
- 완전 이진 트리 : (확인필요) 뿌리부터 시작해서 각 노드에 번호를 매기면 1~ 트리 안의 노드 수까지의 완전한 순열을 얻는다. 
  * 완전 이진트리는 마지막 레벨을 제외 하고 모든 레벨이 완전히 채워져 있다.
  * 마지막 레벨은 꽉 차 있지 않아도 되지만, 노드가 왼쪽에서 오른쪽으로 채워져야 한다.
  * 마지막 레벨 h에서 1~2h-1 개의 노드를 가질 수 있다.
  * 완전 이진 트리는 배열을 사용해 효율적으로 표현 가능하다.

- 편향 이진 트리 : 왼쪽 혹은 오른쪽 서브 트리만을 가지는 트리. 

### 우선순위 큐
먼저 들어온 것이 먼저 처리되지 않고, 정해진 우선순위에 따라 처리되는 작업 스케줄링을 가진 자료구조
- 키 값에 따라 정렬되어 배열에 삽입된다. 삭제는 한쪽 끝에서만 수행  


### Heap 
노드의 값이 그 자식노드의 값보다 크거나 작아야 한다. 
트리의 높이 h > 0 일때 모든 잎 노드들이 h 혹은 h-1레벨에 있어야 한다. (= 완전 이진 트리의 특성)
여러개의 값 중에서 가장 큰 값이나 가장 작은 값을 빠르게 찾을 수 있도록 구성된 자료구조. 

- 시스템의 프로세스의 우선순위를 결정하는 방법으로 많이 사용 
  - 우선순위가 부여된 프로세스를 최대 힙으로 구성하면, 우선순위 숫자가 큰 프로세스가 최상위에 위치하게 됩니다
  - 프로세스를 요청할 때는 최상위 노드에 있는 프로세스를 반환합니다. 
  - 루트 노드가 반환되면 나머지 노드를 우선순위 숫자에 근거하여 트리 구조를 재구성하고, 가장 높은 우선순위를 가지는 프로세스가 루트 노드에 위치하게 됩니다. 
  

### Grape 
정점(vertex)라 불리는 노드 집합 V와 간선(edge)이라고 불리는 정점들의 쌍의 집합 E로 이루어진 쌍(V,E) 이다. 
- 정점과 간선은 위치이고 항목을 저장한다. 
  - 방향 간선(Directed Edge)
    - 순서가 정해진 정점의 쌍(u,v)
    - 첫번째 정점 u가 출발점
    - 두번째 정점 v가 도착점
    - 예) 일방통행 도로
  - 무방향 간선(Undirected Edge)
    - 순서가 정해지지 않은 정점의 쌍(u,v)
    - 예) 기차의 철로
  - 방향 그래프
    - 모든 간선들이 방향 간선일 때
    - 예) 네트워크의 라우트
  - 무방향 그래프 
    - 모든 간선들이 무방향일때 
    - 예) 항공 노선도 
- 어떤 간선이 두 정점을 이으면 그 정점들은 서로에게 인접하다고 하고, 그 간선은 두 정점에 부속되었다고 한다. 
- 사이클이 없는 그래프는 트리라고 한다. 트리는 사이클이 없는(acyclic) 연결된 그래프이다. 
- 자기 루프(Selef Loop)는 정점 자신을 연결하는 간선
- 두 간선은 그들의 같은 정점 쌍을 연결하면 병렬(parallel) 이다. 
- 점점의 차수(degree) 는 그 점에 부속된 간선의 개수
- 부속 그래프는 그래프의 간선들의 부속집합으로 이루어진 그래프 
- 경로(path)는 인접한 정점들의 순열이다. 단순경로는 반복되는 정점이 없는 경로이다. 아래 그래프에서 점선으로 된 것이 G에서 E까지의 경로이다. 
- 이분그래프(Bipartite Graph) - 정점들이 두 집합으로 나우어지고 모든 간선들이 한 집합의 정점들과 다른 집합의 정점들을 연결하는 그래프 
- 가중치 그래프(Weighted Graph) - 정수들(가중치)이 각 간선에 할당된다. 
- 완전 그래프(Complate Graph) - 모든 간선이 존재하는 그래프 (모든 노드와)   

#### DFS(Deotg First Search)
- 스택사용
- 시작 정점에서 한 방향으로 갈 수 있는 가장 먼 경로까지 탐색하다가 갈 곳이 없으면, 가장 마지막에 만났던 부모 노드로 돌아와서 다른 방향을 탐색하는 방법 
  - 그래프의 단절점 찾기 
  - 강하게 연결된 구성 요소 찾기 
  - 미로와 같은 퍼즐 풀기 
  - 연결된 구성 요소 찾기 
  - 위상정렬 
  

#### BFS(Breadth First Search)
- 큐 사용
- 시작 정점에서 인접한 모든 정점들을 우선 방문한 후, 더 이상 방문하지 않은 정점이 없을 때까지 방문했던 정점들을 다시 시작점으로 해서 모든 정점들을 차례로 방문하는 방법
  - 하나의 연결된 구성 요소 안의 모든 노드 찾기
  - 두 노드 사이의 최단 경로 찾기 
  - 그래프에서 연결된 모든 구성 요소 찾기
  - 그래프가 이분 그래프인지 검사하기

#### DFS vs BFS 
- DFS는 BFS보다 훨씬 적은 메모리를 필요로 한다.(DFS 모든 자식 포인터를 저장할 필요가 없어서)
- DFS는 전위탐색과 관련있고, BFS 트리의 레벨 순서 탐색과 비슷하게 동작한다.
- DFS는 깊은 레벨부터 탐색하여 깊은 곳에 원하는 결과가 있을때 사용
- BFS는 근접한 레벨부터 방문하여 근접에는 용이하지만 깊은 레벨은 많은 시간이 소요된다. 

#### 최단경로 알고리즘 
- 비가중치 그래프의 최단경로
- 가중치 그래프의 최단경로 
- 음수 간선가 있는 가중치 그래프의 최단경로
- 



### 재귀와 백트래킹 
#### 재귀 
자기 자신을 호출하는 함수를 재귀적이라고 부른다. 
재귀단계 - 재귀적 방법은 자신의 복사본을 호출하여 더 작은 문제를 풀게함으로써 문제를 해결한다. 

```
if(기본 경우인지 테스트)
  return 기본 경우 값 
else if(또 다른 기본 경우 테스트)
  retrun 다른 기본 경우 값
// 재귀경우
else
  return (어떤작업) 그런 다음(재귀호출)
```


##### 재귀 vs 반복 
|재귀|반복|
|--|--|
|기본 경우에 도달하면 종료한다. | 조건이 거짓이 될 때 종료한다. |
|각 재귀 호출은 스택 프레임(즉 메모리)에 부가 공간을 필요로 한다. | 각 반복이 부가 공간을 필요로 하지 않는다. | 
|무한 재귀에 들어가게 되면 메모리 용량을 초과해서 스택 오버플로우를 초래하게 된다. | 무한 루프는 추가 메모리가 필요하지 않으므로 무한히 반복된다. |
| - | 반복적 해법은 재귀적 해법에 비해 간단하지 않을 때가 있다|

#### 백트래킹 
분할 정복을 이용한 완전 검색 기법 
- 모든 경우의 수를 다 잘펴 볼수 있다.
- 가지치기를 이용해 완전 검색을 빠르게 한다. 


### 연결리스트 
- 연속되는 항목들이 포인터로 연결된다. 
- 마지막 항목은 null을 포인트한다. 
- 프로그램이 수행되는 동아 ㄴ크기가 커지거나 작아질 수 있다. 
- 메모리 공간을 낭비하지 않는다.(포인터르 ㄹ위한 추가의 메모리를 필요로 한다.)
- (시스템 메모리가 허용하는 한) 필요한 만큼 길어질 수 있다. 
  
#### 배열 vs 연결리스트 
연결리스트와 배열 모두 데이터 집합을 저장하기 위해 사용된다. 
배열은 항목에 접근이 더 빠르지만, 고정된 크기, 한 블록의 할당이 어렵거나, 특정 위치에 데이타를 삽입 시, 기존항목들을 이동을시켜야지만 삽입 가능하므로 어려움이 있다. 

연결리스트를 사용하면 하나의 항목을 위한 공간으로 시작해서 복사나 재할당 없이 새항목을 쉽게 추가할 수 있다. 
다만, 개별항목에 접근하는 접근 시간이 배열에 비해 길다. 

### 이중연결리스트 
리스트의 특정노드로부터 양방향으로 탐색가능 

이중연결리스트 단점 
- 각 노드가 포인터를 하나씩 더 필요로 하기 때문에 저장 공간이 더 필요하다. 
- 삽입, 삭제 연산이 조금 더 오래 걸린다(포인터 연산이 더 많아져서이다).
  
### 원형연결리스트 
단일, 이중 연결리스트와 달리 맨 끝의 노드라는 것이 존재하지 않아. 노드의 다음노드가 null인 노드가 존재하지 않는다. 
* 노드 개수 세기
  임의의 노드를 시작점으로 선택하고 다음노드가 시작 노드와 같아질때까지 순환하며 개수를 센다. 


### 버킷 정렬 알고리즘(Bucket Sort)
자료를 버킷이라는 단위 기억 장소에 정렬하고 버킷별 키 값에 따라 다시 정렬하는 알고리즘 
**버킷 정렬은 데이터 모양에 따른 제한이 존재, 데이터 1, 1000이면 1000개의 데이터 공간이 필요**

- 정렬할 데이터 확보
- 정렬할 데이터 숫자 이상의 공간을 확보 후, 숫자를 차례대로 할당
- 데이터를 각자 위치에 넣습니다. 

### 기수 정렬 알고리즘(Radix Sort) 
버킷정렬의 개선 버젼, 각 자릿수별로 버킷 정렬을 반복 수행하는 방법
```java
import java.util.Arrays;

public class RadixSort {
    public static void main(String[] args){
        int[] array = {12,326,127,467,110, 58};
        array = rSort(array);
        System.out.println(Arrays.toString(array));
    }

    private static int[] rSort(int[] array) {
        for(int shift=Integer.SIZE -1; shift > -1; shift--){
            int[] tmp = new int[array.length];
            int j=0;
            System.out.println(shift + "-1=> " + Arrays.toString(array));
            for(int i=0; i < array.length;i++){
                boolean move = array[i] << shift >= 0;
                if(shift == 0 ? ! move : move){
                    tmp[j] = array[i];
                    j++;
                } else{
                    array[i - j] = array[i];
                }
            }
            System.out.println(shift + "-2=> " + Arrays.toString(array));

            for(int i=j; i < tmp.length;i++){
                tmp[i] = array[i-j];
            }
            array = tmp;
            System.out.println(shift + "-3=> " + Arrays.toString(array));
        }
        return array;
    }
}

```

### 선택 정렬 알고리즘(Selection Sort)
가장 작은 데이터를 찾아 가장 앞 데이터와 교환하는 알고리즘 
- 데이터 중에서 가장 작은 것을 찾아서 처음에 있는 것과 위치를 교환
- 두번째부터 마지막까지 데이터 중에서 가장 작은것을 찾아 교환
- 반복

```java
import java.util.Arrays;

/**
 * 선택 정렬 알고리즘
 */
public class SelectionSort {
    public static void main(String[] args){
        int[] array = {12,326,127,467,110,58};
        int size = 6;
        array = SelectionSort(array, size);
        System.out.println(Arrays.toString(array));
    }
    public static int[] SelectionSort(int[] arr, int MAX){
        int i,j;
        int min, temp;
        System.out.println(Arrays.toString(arr));
        for(i=0; i< MAX;i++){
            min = i;
            for(j=i+1;j<MAX;j++){
                if(arr[j] < arr[min]) min = j;
            }
            temp = arr[i];
            arr[i] = arr[min];
            arr[min] = temp;
            System.out.println(Arrays.toString(arr));
        }
        return arr;
    }
}
```

### 교환 정렬 알고리즘(Exchange Sort)
작은 것부터 큰 순서로 정렬할 경우, 작은 키를 갖는 데이터를 찾아 앞 데이터와 교환하는 알고리즘  
- 대상 데이터의 앞에서부터 인접한 두 개의 크기를 비교하여 작은 것이 앞으로 가도록 교환
- 나머지 데이터도 계속 비교하면서 작은 것이 앞으로 가도록 교환 
- 큰 수에서 작은 수로 정렬하고자 하는 경우 반대로 적용 

```java
import java.util.Arrays;

/**
 * 교환 정렬 알고리즘
 */
public class ExchangeSort {
    public static void main(String[] args){
        int[] array = {12,326,127,467,110,58};
        array = ExchangeSort(array);
        System.out.println(Arrays.toString(array));
    }
    public static int[] ExchangeSort(int[] arr){
        int i,j;
        int temp;
        int numLength = 6;
        for(i=0; i < numLength;i++){
            for(j=i+1; j < numLength;j++){
                if(arr[i] > arr[j]){
                    if(arr[i] > arr[j]){
                        temp = arr[i];
                        arr[i] = arr[j];
                        arr[j] = temp;
                    }
                }
            }
        }
        return arr;
    }
}
```

### 삽입 정렬 알고리즘(Insert Sort)
교환 정렬 알고리즘과 비슷하지만, 다른 점은 위치 교환이 발생한 것을 대상으로 주변의 것과 비교하여 위치를 교환하는 알고리즘

```java
import java.util.Arrays;

/**
 * 삽입 정렬 알고리즘
 */
public class InsertSort {
    public static void main(String[] args){
        int[] a = {12,326,127,467,110,58};
        for(int j=1; j < a.length;j++){
            int key = a[j];
            int i = j-1;
            while(i >= 0 && a[i] > key){
                a[i+1] = a[i];
                i = i-1;
            }
            a[i+1] = key;
        }

        System.out.println(Arrays.toString(a));
    }
```

### 쉘 정렬 알고리즘(Shell Sort)
삽입 정렬 알고리즘의 느린 속도를 보와하기 위해 만들어진 알고리즘으로, 데이터의 그룹을 나우어 그룹 안에서 쉘 정렬을 수행하고 마지막에 삽입 정렬을 수행하는 알고리즘 
- 작은 수에서 큰수로 정렬한다고 가정
- 정렬 데이터를 2개의 무리로 분할하고 각 무리의 처음 값을 비교하여 작은 것이 앞으로 가도록 합니다. 
- 반복
- 한 무리가 1개의 데이터로 구성될때 까지 진행합니다. 

```java
// need to update
// 책의 코드 결과 잘못도어 수정 후 업로드 예정
```

### 병렬 정렬 알고리즘(Merge Sort) 
데이터를 분할한 다음 각자 계산하고 나중에 합쳐서 정렬하는 알고리즘
- 작은 수에서 큰수로 정렬한다고 가정
- 정렬할 데이터를 최소단위가 될때까지 분할 작업을 반복
- 분할된 데이터를 대상으로 2개, 4개, 8개 병합하면서 정렬을 수행
- 최종적으로 2개의 무리가 만들어진 후, 2차 병합 정렬 알고리즘 적용

```java
import java.util.Arrays;

public class MergeSort {
    public static void main(String[] args){
        int[] src = new int[]{1,9,8,5,4,2,3,7,6};
        int[] tmp = new int[src.length];
        System.out.println(Arrays.toString(src));
        mergeSort(0, src.length -1, src, tmp);
        System.out.println(Arrays.toString(src));
    }
    public static void mergeSort(int start, int end, int[] src, int[] tmp){
        if(start < end){
            int mid = (start + end) / 2;
            // 반반씩 나누어서 다시 시작
            mergeSort(start, mid, src, tmp);
            mergeSort(mid + 1, end, src, tmp);

            int p = start;
            int q = mid + 1;
            int idx = p;

            while(p<= mid || q <= end){
                if(q > end || (p <= mid && src[p] < src[q])){
                    tmp[idx++] = src[p++];
                }else{
                    tmp[idx++] = src[q++];
                }
            }
            for(int i=start; i <= end ; i++){
                src[i] = tmp[i];
            }
            System.out.printf("start : %d, mid : %d, end : %d \n", start, mid, end);
            System.out.println(Arrays.toString(src));
        }
    }
}

```


### 퀵 정렬 알고리즘(Quick Sort)
중앙 값 정렬 방식을 확장해서 개발한 방식의 알고리즘
- 임의로 선정된 데이터를 중심으로 데이터를 2등분
- 각 분리된 부분의 첫번째 원소를 기준으로 데이터를 분리 
- 반복 


```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class QuickSort {
    public static void main(String[] args){
        Integer[] array = {30,50,7,40,88,15,44,55,22,33,77,99,11,66,1,85};
        System.out.println(Arrays.toString(array));
        List<Integer> aList = new ArrayList<>();
        aList.addAll(Arrays.asList(array));

        aList = (ArrayList<Integer>) quicksort(aList);
        System.out.println(aList.toString());
    }

    private static <T extends Comparable<? super T>> List<T> quicksort(List<T> list) {
        if(list.size() <= 1) return list;
        int pivot = list.size() /2 ;
        List<T> a = new ArrayList<T>();
        List<T> b = new ArrayList<T>();
        int c = 0;
        for(T number : list){
            if(list.get(pivot).compareTo(number) < 0) b.add(number);
            else if(list.get(pivot).compareTo(number) > 0) a.add(number);
            else c++;
        }
        a = quicksort(a);
        for(int i=0; i < c; i++)
            a.add(list.get(pivot));
        b = quicksort(b);
        List<T> sorted = new ArrayList<>();
        sorted.addAll(a);
        sorted.addAll(b);
        return sorted;
    }
}

```

### 힙 정렬 알고리즘(Heap Sort)
- 큰 수에서 작은 수로 정렬하고자하면, 힙을 구성할때 최상위 데이터를 가장 큰 수로 설정하도록 한다. 
- 최상위 데이터를 빼고 나면, 나머지 데이터로 다시 힙을 구성.
- 반복 


### 정렬 알고리즘 선택 기준
|상황|정렬 알고리즘|
|--|--|
|항목이 몇개 되지 않는다. | 삽입정렬 |
|항목이 대부분 정렬되어 있다. | 삽입정렬 | 
|최저 상황을 고려해야 한다. | 힙 정렬 |
|평균 정렬 결과가 필요하다. | 쿽(빠른) 정렬 |
|항목을 조밀한 모집단에서 가져왔다. | 버킷정렬 |
|가능한 짧은 코드를 선호한다. | 삽입정렬  |