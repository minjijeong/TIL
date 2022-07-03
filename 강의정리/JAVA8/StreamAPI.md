# Stream 
sequence of elements supporting sequential and parallel aggregate operation.
순차 및 병렬 집계 작업을 지원하는 요소 시퀀스입니다.
- 데이타를 담고 있는 저장소(컬렉션)이 아니다. 
- Functional in nature. 스트림이 처리하는 데이터 소스를 변경하지 않는다. 
- 스트림으로 처리하는 데이타는 오직 한번만 처리한다. 
- 무제한일 수도 있다. (Short Circuit(데이타 양을 제한하는) 메소드를 사용해서 제한 할 수 있다.)
- 중개 오퍼러레이션은 근본적으로 lazy하다. 
  (streamAPI가 제공하는 메소드는 중계오퍼레이터(계속 이어지는)과 터미널오퍼레이션(종료)으로 나뉘는데,
   map은 중계오퍼레이션 stream을 리턴한다. 중계형 오퍼레이션은 터미널오퍼레이션에 도달하기 전까지 실행이 되지 않는다. lazy하다는 다 처리하지 않아도 중간에 끝낼수 있다라는 의미와 유사. 중계형오퍼레이션을 통해 처리하고 터미널오퍼레이션을 중간에 끝낼수 있다?? 요런 느낌인가?)
    ```java
         // 중계형 오퍼레이션은 터미널오퍼레이션에 도달하기 전까지 실행이 되지 않는다.
        List<String> collect = names.stream()
                                    .map(s ->{
                                                System.out.println(s);
                                                return s.toUpperCase();
                                    }) // 여기까지는 stream을 리턴해주므로, 중계오퍼레이션 처리
               .collect(Collectors.toList()); // collect를 통해 종료하므로, 터미널 오퍼레이션 처리
        // [keesun,whiteship,toby,foo]
        collect.forEach(System.out::println);
        // [KEESUN, WHITESHIP, TOBY, FOO]

        System.out.println("====================");
        // 원본소스
        names.forEach(System.out::println);
        // [keesun,whiteship,toby,foo]
    ```
- 손쉽게 병렬 처리할 수 있다. 
  (stream을 안쓰고 병력처리 하는 것, 즉 루프를 도는 작업을 병렬적으로 처리하기가 어려움
   stream parellel에서 trysplit 등으로 쪼개서 병렬적으로 처리가능하다)

*ParallelStrem(병렬처리)과 Stream의 효율 차이는 케이스 마다 다를수 있다* 
```java
/**
 * 쓰레드를 만들고 병렬적으로 처리한것을 수집하는 비용, 콘텍스트를 세팅할때 이런 것 때문에 
 * 데이타 양이 정말 방대하거나, 데이타 소스에 따라 병렬처리하는 것이 효과가 있는지 테스트해서 효율적으로
 *  사용할 수 잇따.  
 */
    // 병렬처리
    List<String> collect_prallel = names.parallelStream().map(s ->{
                System.out.println(s + " " + Thread.currentThread().getName());
                return s.toUpperCase();
            }) // 여기까지는 stream을 리턴해주므로, 중계오퍼레이션 처리
            .collect(Collectors.toList());
    collect_prallel.forEach(System.out::println);
    /**
    * 출력결과 
    * - 각자 다른 쓰레드에서 실행되는 것 확인. 
    * foo ForkJoinPool.commonPool-worker-5
    * toby main
    * whiteship ForkJoinPool.commonPool-worker-3
    * keesun ForkJoinPool.commonPool-worker-7
    */

``` 

### 스트림 파이프라인
- 0 또는 다수의 중개 오퍼레이션(intermediate operation)과 한 개의 종료 오퍼레이션(terminal operation)으로 구성한다. 
- 스트림의 데이타 소스는 오직 터미널 오퍼레이션을 실행할 때에만 처리한다. 

### 중개 오퍼레이션(intermediate operation)
- Stream을 리턴한다. 
- Stateless/ Stateful 오퍼레이션으로 더 상세하게 구분할 수도 있다. 
  (대부분은 stateless지만 distinct나 sort처럼 이전 이전 소스 데이터를 참조해야 하는 오퍼레이션은 stateful 오퍼리에션이다)
- filter, map, limit, skip, sorted

### 종료 오퍼레이션(terminal operation)
- Stream을 리턴하지 않는다. 
- collect, allMatch, count, forEach, min, max...


## Stream API 예제
...to be continue..
