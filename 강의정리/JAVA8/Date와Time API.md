# Date와 Time API

## 소개 
*Java8에 새로운 날짜와 시간 API가 생긴 이유*
- 그 전까지 사용하던 java.util.Date 클래스는 mutable 하기 때문에 thread safe 하지 않다. 
  (멀티 쓰레드 환경에서 각 쓰레드가 동시에 date를 수정할 경우, 보장되지 않는다. immutable한 객체인 경우는 쓰레드가 사용중일때 lock을하고 수정 후 다른 쓰레드를 사용하도록 처리한다. 또는 쓰레드 마다 새로운 객체를 생성하여 각각 쓰인다.)
- 클래스 이름이 명확하지 않다. (Date인데 시간까지 다루고, Time도 EPOCK 기준이다)
- 버그 발생할 여지가 많다. (타입 안전성이 없고, 월이 0부터 시작한다거나..)
- 날짜 시간 처리가 복잡한 애플리케이션에서 보통 **[Joda Time](https://www.joda.org/joda-time/)**을 쓰곤했다.
 
**참고 -** [all about java util date](https://codeblog.jonskeet.uk/2017/04/23/all-about-java-util-date/)

### Java8에서 제공하는 Date-Time API
- [JSR-301 스팩](https://jcp.org/en/jsr/detail?id=310)의 구현체를 제공한다. 
- 디자인 철학 
  * Clear - 의미적 명확. 기계/사람이 쓰는 용도를 구분
  * Fluent
  * Immutable - 비동기 환경에서 Thread safe를 보장하기 위해 
  * Extensible

### 주요 API
- 기계용 시간(machine time)과 인류용 시간(human time)으로 나눌수 있다. 
- 기계용 시간은 EPOCK(1970년 1월 1일 0시 0분 0초)부터 현재까지의 타임스탬프를 표현한다. 
- 인류용 시간은 우리가 흔히 사용하는 연,월,일,시,분,초 등을 표현
- 타임스탬프는 instant를 사용한다. -> 기계시간으로! 
  ```java
    Instant instant = Instant.now();
    System.out.println(instant); // 기준시 UTC, GMT(그리니치 천문대 있는 곳 시간)
  ```
- 특정날짜(LocalDate), 시간(LocalTime), 일시(LocalDateTime)을 사용할 수 있다. 
- 기간을 표현할 때는 Duration(시간 기반)과 Period(날짜기반)를 사용할 수 있다. 
- DateTimeFormatter를 사용해서 일시를 특정한 문자열로 포매팅할 수 있다. 

### 예제 
...to be continue..
  