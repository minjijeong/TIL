## 0. 사전준비

### 타입 파라미터 
#### Generic Type 
제네릭 형식 은 형식에 대해 매개 변수화 된 제네릭 클래스 또는 인터페이스입니다
Object 의 모든 항목은 T 로 대체됩니다 . 유형 변수는 클래스 유형, 인터페이스 유형, 배열 유형 또는 다른 유형 변수와 같이 지정한 기본 유형이 아닌 모든 유형이 될 수 있습니다.

 
<유형 매개변수>
- E - Element (used extensively by the Java Collections Framework)
- K - Key
- N - Number
- T - Type
- V - Value
- S,U,V etc. - 2nd, 3rd, 4th types

> https://docs.oracle.com/javase/tutorial/java/generics/types.html

## 1. 인터페이스 
### 자바 인터페이스 
인터페이스를 구현하면 클래스가 제공하기로 약속한 동작에 대해 더 공식화될 수 있습니다. 인터페이스는 클래스와 외부 세계 간의 계약을 형성하고 이 계약은 빌드 시 컴파일러에 의해 시행됩니다. 클래스가 인터페이스를 구현한다고 주장하는 경우 해당 인터페이스에 의해 정의된 모든 메서드는 클래스가 성공적으로 컴파일되기 전에 소스 코드에 나타나야 합니다.
> https://docs.oracle.com/javase/tutorial/java/concepts/interface.html

### 일반적 인터페이스
사물과 사물 사이 또는 사물과 인간 사이의 경계에서, 상호 간의 소통을 위해 만들어진 물리적 매개체나 프로토콜을 말한다
시스템 간의 API 통신을 할때도 인터페이스라고 한다. DB, EAI, API 등 인터페이스 되는 형태를 가질 수 있다. 
> http://wiki.hash.kr/index.php/%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4

### 자바 컬렉션 프레임워크 
데이터를 저장하는 자료 구조와 데이터를 처리하는 알고리즘을 구조화하여 클래스로 구현
- http://www.tcpschool.com/java/java_collectionFramework_concept
- https://st-lab.tistory.com/142
- https://pridiot.tistory.com/63


### LinkedList, ArrayList 차이 
ArrayList와 LinkedList의 차이는 사용 방법이 아닌, 내부적으로 요소를 저장하는 방법에 있습니다.
> http://www.tcpschool.com/java/java_collectionFramework_list


## 2. 알고리즘 분석 
- 상수시간
  실행시간이 입력 크기에 의존하지 않으면 알고리즘은 상수시간(Constant time)을 따른다고 합니다. 예를 들어, 배열의 크기에 상관없이 같은 수의 동작을 하는 경우 상수시간으 따름.
<br>

- 선형
  실행 시간이 입력의 크기에 비례하면 알고리즘은 선형(Linear)이라고 합니다. 
  예를 들어 배열에 있눈 요소를 더한다면 n개의 요소에 접근하여 n-1번 더하기 연산을 해야합니다. 연산의 총횟수는 2n-1이고, n에 비례합니다. 
<br>

- 이차 
  실행시간이 n^2에 비례하면 알고리즘은 이차(Quadratic)라고 합니다. 예를 들어 리스트에 있는 어떤 요소가 두번이상 나타나는지를 알고 싶다고 가정하면, 각 요소를 다른 모든 요소와 비교해야 한다. 즉, n개의 요소가 있고 각각 n-1개의 다른 요소와 비교하면 총 비교 횟수는 n^2 - n이 되어 ndl 커지면서 n^2 비례합나다. 
  




## 3. ArrayList 클래스
AbstractList를 확장하고 List 인터페이스를 구현합니다. 
ArrayList는 필요에 따라 확장할 수 있는 동적 배열을 지원합니다.

> initialize 사이즈를 지정가능. array(배열) 과의 차이점은 동적으로 크기가 바뀔수 있다.. 정도? 그리고 저장시, 처음엔 같이 저장되고 그 후에 추가되는 것들은 일직선상으로 모여서 저장이 안될수 있다? 정도 인것 같습니다. 

## 4. LinkedList 클래스
Node를 가지고 다음노드의 정보를 가지고 있어, 각 노드만다 정해진 순서는 없지만 각각 노드에서 다음 노드를 지정하기때문에 배열처럼 index 순번은 없지만 순서는 생길수 있다. 
 
어떤 노드를 찾기 위해서는 head부터 다음노드정보를 타고타고 움직여야 하므로 노드를 찾는데 오래 걸린다. 

**연결리스트 구현의 장점은 요소를 제거하면 리스트 크기가 줄어들고 사용하지 않는 노드는 즉시 가비지컬렉션이 될수 있다는 것입니다.**


## 5. 이중 연결 리스트 (Double Linked List)
#### ArrayList vs LinkedList 성능비교
||ArrayList-앞추가|LinkedList-앞추가|ArrayList-뒷추가|LinkedList-뒷추가|
|-----|-----|-----|-----|-----|
|추정 기울기|1.9441001650639913|1.0448684003634725|0.8940474404148383|1.0155307912793134|

### 5.4 이중 연결 리스트 
- 각 노드는 다음노드와 이전 노드에 대한 참조를 포함합니다. 
- LinkedList 객체는 첫번째와 마지막 요소에 대한 참조를 포함합니다. 

(Deque는 이중 연결 리스트로 구현되었기뿐에 양반향 순회가 가능합니다.. )

|구분|ArrayList|MyLinkedList(단일)|LinkedList(이중)| 비고|
|----|----|----|----|----|
|add(끝)|1|n|1|2중연결리스트는 tail이 존재하므로 직접 접근가능|
|add(시작)|n|1|1|연결리스트는 head를 통해 직접 접근|
|add(일반)|n|n|n||
|get/set|1|n|n|ArrayList는 순서를 가지고 있으므로 바로 접근 가능|
|indexOf/lastIndexOf|n|n|n||
|isEmpty/size|1|1|1||
|remove(끝)|1|n|1|단일 연결 리스트는 head에서 시작하여 n까지 순회해야지 끝노드를 구할수 있어 n|
|remvoe(시작)|n|1|1|연결리스트는 head를 null로 주고 head 다음노드를 head로 지정하면되므로 1이다|
|remove(일반)|n|n|n||

#### 5.5 자료구조 선택하기 

- 이중연결리스트 - ArrayList보다 시작에 요소를 추가하고 삭제하는 연산에 유리

- ArrayList - get/set으로 접근하는데 유리

- **ArrayList**에서 요소들은 **한 덩어리의 메모리 안에 나란히 저장**되어 거의 낭비되는 공간이 없고, 컴퓨터 하드웨어도 연속된 덩어리에서 종종 속도가 더 빠릅니다. 

- **연결리스트**에서 각 요소는 하나 또는 두개의 참조가 있는 노드가 필요합니다. **참조는 공간을 차지합니다.**(때때로 데이타보다 클 수 있음). 메모리 여기저기에 **노드가 흩어져 있으면 하드웨어의 효율이 떨어질 수 있습니다.** 
  
  ###### 알고리즘 자료구조를 선택하는것이 유효할 조건
   1. 응용 프로그램의 실행시간이 중요하다 
   2. 응용 프로그램의 실행시간이 선택한 자료구조에 의존한다. 
   3. 증가 차수에 따라 어느 자료구조가 나은지 실제로 예측할 수 있을 만큼 문제 크기가 충분히 크다. 

## 6. 트리순회

### 6.1 검색엔진 
- 크롤링(crawling)
  - 웹페이지를  다운로드하고 파싱하고 텍스트와 다른 페이지로의 링크를 추출하는 프로그램
- 인덱싱(indexing)
  - 검색어를 조회하고 해당 검색어를 포함한 페이지를 찾는데 필요한 자료구조
- 검색(retrieval)
  - 인덱스에서 결과를 수집하고 검색어와 가장 관련된 페이지를 식별하는 방법
  
    \* retrieval : 1. 되찾다, 회수 2.(정보의) 검색

### 6.5 깊이 우선 탐색 (depth-first search, DFS)
- 재귀 또는 반복

### 6.6 스택(Stack)

스택을 구현시, **ArrayDeque클래스 같은 Deque 인터페이스를 구현한 클래스를 사용하는것이 가장좋다**


## 7. 철학으로 가는길 

### 7.2 Iterable과 Iterator