# Optional Class
**오직 값 한 개가 들어있을 수도 없을 수도 있는 컨테이너**

### 자바 프로그래밍에서 NullPointerException을 종종 보게 되는 이유
- null을 리턴하니깐 or null 체크 깜박해서
  * 실제로 값 입력받는 영역에서 null 체크 validation 처리 안되어 있어서 종종 넘어오는 경우
  * 외부 인터페이스 받아오다가 값 손실 되는 경우 등등
#### 메소드에서 값을 제대로 리턴할수 없는 경우 방법 
- 예외를 던진다. (비싸다. stacktrace를 찍어두기 때문에 로그로 다 남고.. 기타 등등)
  >참고 [java exception 생성 비용은 비싸다](https://meetup.toast.com/posts/47) 
- null을 리턴한다. (비용 문제가 없지만 그 코드를 사용하는 클라이언트 쪽에 주의해야한다.)
  > 외부 API 경우 Controller에서 생성해서 넘어오는 경우도 있었지만 서비스 차제가 core적 성향을 띠고 있어서.. 항상 서비스 단에서 null check 많이 했는데..차후 서비스 만들때 좀 더 확실히 공부하고 적용해봐야겠다. 
- (java8부터) Optional을 리턴한다. (클라이언트에 코드에게 명시적으로 빈값일수도 있다는 걸 알려주고, 빈값인 경우에 대한 처리를 강제한다.)
  > JPA에서 repository에서 CRUDRepository.findById ID값은 null 값이 올수 없기때문에 해당 return 타입에 Optional로 처리되어있다. 
  ```
  <!-- Retrieves an entity by its id.
  매개변수: id – must not be null.
  반환: the entity with the given id or Optional#empty() if none found.
  던지기: IllegalArgumentException – if id is null. -->
  ```
### 주의할 것 
- 리턴값으로만 쓰기를 권장한다. (매소드 매개변수 타입, 맵의 키 타입, 인스턴스 필드 타입으로 쓰지말자.)
  ```java
    // nullpoint exception 발생
    // null check를 더블로 해야하므로 파라미터로 Optional은 안받는 것이 좋다
    public void setProgress(Optional<Progress> progress) {
        if(progress != null) {
            progress.ifPresent(p -> this.progress = p);
        }
    }
  ```
- Optional을 리턴하는 메소드에서 null을 리턴하지 말자. 
  ***Effective Java p325. null이 아닌, 빈 배열이나 컬렉션을 반환하라.** null을 반환하는 API는 사용하기 어렵고 오류 처리 코드도 늘어난다. 그렇다고 성능이 좋은 것도 아니다.*
  ```java
    // Retrun 타입으로 넘길때, null로 바로 넘기는 것이 아니라 empty로 넘기게끔 강제할 수 있다. 
    public Optional<Progress> getProgress() {
        // null이면 retrun empty, 아니면 of를 리턴
        return Optional.ofNullable(progress);
    }
  ```
- Primitive 타입용 Optional은 따로 있다. OptionalInt, OptionalLong...
  (Primitive 타입을 Optional로 감싸면 넣고 뺄때 boxing unboxing이 발생해 효율을 떨어뜨린다.)
  ```java
  // boxing , unboxing이 발생해서 성능상 안좋음
  // Optional.of(10);
  // 차라리 OptionalInt를 사용하는것이 좋다.
  // 박싱(boxing)이란 기본형을 참조형으로 변환
  // 언박싱(unboxing)이란 반대로 참조형을 기본형으로 바꾸는 것
  OptionalInt.of(10);
  ```
- Collection, Map, Stream, Array, Optional은 Optional로 감싸지 말자. 
  (구지 콘테이너를 2중으로 감쌀 필요가 없다...)



### Optional 예제 
#### Optional 만들기
- Optional.of
  ```java
  OnlineClass onlineClass = new OnlineClass(1, "spring boot", true);
  Optional<OnlineClass> opClass = Optional.of(onlineClass);
  System.out.println(opClass.toString());
  // Optional[OnlineClass{id=1, title='spring boot', closed=true, progress=null}]
  ```
- Optional.ofNullable
  ```java
  OnlineClass nullClass = null;
  Optional<OnlineClass> opNullClass = Optional.ofNullable(nullClass);
  System.out.println(opNullClass.toString());
  // Optional.empty

  ```
- Optional.empty
  ```java
  Optional<OnlineClass> opEmptyClass = Optional.empty();
  System.out.println(opEmptyClass.toString());
  // Optional.empty
  ```
#### Optional에 값이 있는지 없는지 확인하기
- isPresent()
- isEmpty() (JAVA11부터 제공)
  ```java
  // isPresent - boolean
  Optional<OnlineClass> onlineClass = new OnlineClass(1, "spring boot", true);
  System.out.println(opClass.isPresent());
  // 출력 true 

  // isEmpty
  System.out.println(opClass.isEmpty());
  // 출력 false 
  ```

#### Optional에 있는 값 가져오기 
- get()
  ```java
  // 만약에 비어있는 Optional에서 무언가 꺼낸다면?? -> RuntimeException 발생
  Optional<OnlineClass> opEmptyClass = Optional.empty();
  System.out.println(opEmptyClass.get());
  // 출력 Exception in thread "main" java.util.NoSuchElementException: No value present
  ```
#### Optional에 값이 있는 경우에 그 값을 가지고 ~~를 하라
- ifPresent(Consumer)
  ```java
  // progress가 존재하면 this.progress에 해당 progress 주입
  public void setProgress(Optional<Progress> progress) {
      progress.ifPresent(p -> this.progress = p);
  }
  // spring으로 싲가하는 수업이 있으면 id를 출력하라. 
  //Consumer<OnlineClass> consumer = new Consumer<OnlineClass>() {
  //  @Override
  //  public void accept(OnlineClass onlineClass) {
  //      if (onlineClass.getTitle().startsWith("spring")) {
  //          System.out.println(onlineClass.getId());
  //      }
  //  }
  //};

  opClass.ifPresent(s -> {
    if(s.getTitle().startsWith("spring")){
        System.out.println(s.getId());
    }
  });
  ```
#### Optional에 값이 있으면 가져오고 없는 경우에 ~~를 리턴하라.
- orElse(T)
  orElse는 무조건 그안의 어떤 것들이 반드시 행해진다. 이미 만들어진 것을 참조시키는 것이 좋다. 
  ```java
  // jpa로 시작하는 수업이 없다면 비어있는 수업을 리턴하라 
  Optional<OnlineClass> otherClass = null;
  OnlineClass springDataClass = new OnlineClass(2,"spring data", true);
  // 애초에 null 인 경우는 객체 생성이 안되었기때문에 orElse 조차 사용할수 없다. 
  // OnlineClass newClass = otherClass.orElse(springDataClass);
  // 출력 Runtime Exception -> Exception in thread "main" java.lang.NullPointerException

  // 빈 컨테이너, Optional로 선언
  otherClass = Optional.empty();
  // otherClass가 비었을때 springDataClass를 대신 가져옴
  // orElse는 무조건 그안의 어떤 것들이 반드시 행해진다. 
  // 이미 만들어진 것을 참조시키는 것이 좋다. 
  OnlineClass newClass = otherClass.orElse(springDataClass);
  System.out.println(newClass);
  // 출력 OnlineClass{id=2, title='spring data', closed=true, progress=null}
  ```
#### Optional에 값이 있으면 가져오고 없는 경우에 ~~를 하라. 
- orElseGet() 
  Supplier 형태로 .. 동적으로 어떤 것이 추가될때 orElseGet을 사용하는게 적합하다.
  ```java
  Optional<OnlineClass> otherClass = Optional.empty();
  // 
  Supplier<OnlineClass> supplier = s -> new OnlineClass(99,"how to study java", true);
  System.out.println(otherClass.orElseGet(supplier));
  // OnlineClass{id=99, title='how to study java', closed=true, progress=null}

  // jpa로 시작하는 클래스가 없다면, 새로운 객체 생성하라
  List<OnlineClass> springClasses=  new ArrayList<>();
  springClasses.add(new OnlineClass(1,"spring boot", true));
  springClasses.add(new OnlineClass(2,"spring data", true));

  Optional<OnlineClass> springJpa = springClasses.stream()
                                      .filter(oc -> oc.getTitle().startsWith("jpa"))
                                      .findFirst();

  Supplier<OnlineClass> supplier = () -> new OnlineClass(99,"how to study java", true);
  System.out.println(springJpa.orElseGet(supplier));
  ```

#### Optional에 값이 없으면 에러를 던져라. 
- orElseThrow()
  ```java
  otherClass = Optional.empty();
  otherClass.orElseThrow();
  // 출력 Exception in thread "main" java.util.NoSuchElementException: No value present
  ```

#### Optional에 들어 있는 값 걸러내기 
- Optional filter(Predicate)
  ```java
  // OnlineClass.isClosed = false 필터처리
  Optional<OnlineClass> online = optional.filter(oc-> !oc.isClosed());
  System.out.println(online.isEmpty());
  // 출력 true
  ```

#### Optional에 들어 있는 값 반환하기
- Optional map(Function)
- Optional flatMap(Function) : Optional 안에 들어있는 인스턴스가 Optional인 경우 사용하면 편리하다. 
  ```java
  // Optional에서 Optional인 것을 반환 받으면 map을 사용하게되면 복잡해진다.
  // 이를 개선하기 위해 flatMap을 사용하면 한껍질 벗기고 바로 나오게 할수 있다.
  Optional<Optional<Progress>> mapProgress = optional.map(oc -> oc.getProgress());
  Optional<Progress> progress = mapProgress.orElse(Optional.empty());
  // Optional<Progress> progress = mapProgress.orElseThrow()
  // 출력 Exception in thread "main" java.util.NoSuchElementException: No value present

  Optional<Progress> flatMapProgress = optional.flatMap(OnlineClass::getProgress);
  System.out.println(flatMapProgress);
  // Optional.empty
  ```



  참고 자료
  - [자바 익셉션 처리](http://java-performance.info/throwing-an-exception-in-java-is-very-slow/)