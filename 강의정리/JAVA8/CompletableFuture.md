# Completable Future

## 자바 Concurrent 프로그래밍
### Concurrent Software
- 동시에 여러 작업을 할 수 있는 소프트웨어 
  예) 웹브라우저로 유튜브를 보면서 키보드로 문서에 타이핑을 할 수 있다. 
      녹화를 하면서 인텔리J로 코딩을 하고 워드에 적어둔 문서를 보거나 수정할 수 있다. 
### 자바에서 지원하는 Concurrent 프로그래밍 
- 멀티프로세싱(ProcessBuilder)
- 멀티쓰레드

### 자바 멀티쓰레드 프로그래밍
- Thread/ Runnable
  *실직적으로 Thread 메소드를 직접 개발자가 운용하기는 어렵다..*

#### 사용방법 
1. 상속 
```java
public class ThreadExample {
    public static void main(String[] args){
        HelloThread helloThread = new HelloThread();
        helloThread.start();
        System.out.println("hello: "+Thread.currentThread().getName());
    }
}

public class HelloThread extends Thread{
    @Override
    public void run() {
        System.out.println("world : " + Thread.currentThread().getName());
    }
}

// 출력
// hello: main
// world : Thread-0
```

2. Runnable 구현 또는 람다
```java
    Thread thread = new Thread(() ->{
        // 다른 쓰레드가 먼저 일하도록 처리
        try {
            Thread.sleep(1000L);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Thread:" +Thread.currentThread().getName() 
    );
    thread.start();

    System.out.println("Hello: "+Thread.currentThread().getName());
/**
* sleep을 걸었기 때문에
* - sleep이 없다면 해당 순서 보장할 수 없음
* [출력]
* Hello: main
* Thread:Thread-0
*/
```

### 쓰레드 주요기능 
- 현재 쓰레드 멈줘두기 (sleep) : 다른 쓰레드가 처리할 수 있도록 기회를 주지만 그렇다고 락을 놔주진 않는다. 
  (잘못하면 데드락 걸리수 있다. )
- 다른 쓰레드 깨우기 (interrupt) : 다른 쓰레드를 깨워서 interruptedException을 발생 시킨다. 
  그 에러가 발생했을 때 할 일은 코딩하기 나름. 종료 시킬 수도 있고 계속 하던 일 할 수도 있다. 
  *Thread.run()의 경우 return 값이 void 이므로 interrupt 시, return 되지 않으면 적용되지 않는다.*
- 다른 쓰레드 기다리기(joint) : 다른 쓰레드가 끝날때까지 기다린다. 
```java
public class ThreadExample {
    public static void main(String[] args){
        Thread thread = new Thread(() ->{
            // 쓰레드 무한으로 돈다
            while (true){
                System.out.println("Thread:" +Thread.currentThread().getName());
                try {
                    Thread.sleep(1000L);
                } catch (InterruptedException e) {
                    System.out.println("exit!");
                    // return을 없애면 interrupt 작용안됨
                    return;
                }
            }
        });
        thread.start();

        System.out.println("Hello: "+Thread.currentThread().getName());
        // 3초 재우기
        try {
            Thread.sleep(3000L);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }

        /**
         * 쓰레드를 무한으로 실행 시키는 것을 interrupt하여 종료 시킴
         */
        thread.interrupt();

        /**
         * 다른 쓰레드 끝날때 까지 대기하여 종료
         */
        try {
            thread.join();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        System.out.println(thread + " is finished");
        
        /**
         * Hello: main
         * Thread:Thread-0
         * Thread:Thread-0
         * Thread:Thread-0
         * exit!
         * Thread[Thread-0,5,] is finished
         */
    }
}

```


### Executors 

#### 고수준(High-Level) Concurrency 프로그래밍 
- 쓰레드를 만들고 관리하는 작업을 애플리케이션에서 분리. 
- 그런 기능을 Executors에게 위임. 

#### Executor가 하는 일
- 쓰레드 만들기 : 애플리케이션이 사용할 쓰레드 풀을 만들어 관리한다. 
- 쓰레드 관리 : 쓰레드 생명 주기를 관리한다.
- 작업 처리 및 실행: 쓰레드로 실행할 작업을 제공 할 수 있는 API를 제공한다. 

#### 주요 인터페이스 
- Executor : execute(Runnable)
- ExeecutorService : Exectuor 상속 받은 인터페이스로, Callable도 실행할 수 있으며, Executor를 종료 시키거나, 여러 Callable을 동시에 실행하는 등의 기능을 제공한다. 
  - SingleThread 
    ```java
    public class ExecutorsExample {
        public static void main(String[] args){
            ExecutorService executorService = Executors.newSingleThreadExecutor();
            executorService.submit(() -> {
                System.out.println("Hello : "+ Thread.currentThread().getName());
            });

            executorService.shutdown(); // 처리중인 작업 기다렸다가 종료
            executorService.shutdownNow(); // 당장 종료
        }
    }
    ```
  - multiThread
   ```java
    public class ExecutorsExample {
        public static void main(String[] args){
            // 쓰레드 개수지정하여 실행
            ExecutorService executorService = Executors.newFixedThreadPool(2);
            /**
            * main -> executorService -> 쓰레드 2개 세팅
            * 각 쓰레드 안에 BlockingQueue 존재, 각 쓰레드에 각 작업을 쌓아놓음
            * 쓰레드풀을 사용하면, 추가적으로 쓰레드를 생성하지 않아도 되기때문에 비용절감 된다.
            *
            * [출력] 쓰레드를 2개로 지정하여 2개로 나뉘어 처리
            * Java pool-1-thread-2
            * The pool-1-thread-1
            * Thread pool-1-thread-1
            * Executor pool-1-thread-1
            */
            executorService.submit(getRunnable("The"));
            executorService.submit(getRunnable("Java"));
            executorService.submit(getRunnable("Thread"));
            executorService.submit(getRunnable("Executor"));

            executorService.shutdown(); // 처리중인 작업 기다렸다가 종료
        }
    }
    ```
- scheduledExecutorService : ExeecutorService를 상속 받은 인터페이스로 특정 시간이후에 또는 주기적으로 작업을 실행할 수 있따. 
```java
public class ExecutorsExample {
    public static void main(String[] args){
        ScheduledExecutorService scheduledExecutorService = Executors.newSingleThreadScheduledExecutor();
        // 2초마다 갱신
        scheduledExecutorService.scheduleAtFixedRate(getRunnable("Schedule"),1,2, TimeUnit.SECONDS);
    }
    // Runnable 쓰레드 명 출력하는 메소드
    private static Runnable getRunnable(String message) {
        return () -> System.out.println(message + " " + Thread.currentThread().getName());
    }
}
```

### Fork/Join 프레임워크 
- ExecutorService의 구현쳉로 손쉽게 멀티 프로세서를 활용할 수 있게끔 도와준다. 


### Callable과 Future

#### Callable
- Runnable과 유사하자미나 차이는 작업의 결과를 return 할 수 있다. 

#### Future
- 비동기적인 작업의 현재 상태를 조회하거나 결과를 가져올 수 있다. 
- 결과 가져오기 
  - 블록킹 콜
  - 타임아웃(최대한으로 기다릴 시간)을 설정할 수 있다. 
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class CallableExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        // Runnable과 달리 return 값 가질수 있음
        Future<String> helloFuture = executorService.submit(() ->{
           Thread.sleep(3000L);
           return "Callable";
        });

        System.out.println("Hello");

//         executorService.shutdownNow();
        // get 보다 강제종료를 먼저 실행시, sleep을 마저 처리하지 못하고 interruptedException으로 빠짐
        // Caused by: java.lang.InterruptedException: sleep interrupted
        //	at java.base/java.lang.Thread.sleep(Native Method)

        String result = helloFuture.get();
        System.out.println(result);
        /**
         * [출력]
         * Hello
         * Callable (3초후 출력)
         */
        executorService.shutdown();
    }
}
```
#### 직압싱테 확인하기 isDone()
- 완료 True 아니면 False 리턴
```java
    ExecutorService otherExecutorService = Executors.newSingleThreadExecutor();

    Callable<String> hello = () ->{
        Thread.sleep(2000L);
        return "Hello Callable~ ";
    };
    Future<String> helloOtherFuture = otherExecutorService.submit(hello);
    System.out.println("helloOtherFuture.isDone: " + helloOtherFuture.isDone());
    System.out.println("Started!!");

    helloOtherFuture.get();
    
    System.out.println("helloOtherFuture.isDone: " + helloOtherFuture.isDone());
    System.out.println("End!!");
    otherExecutorService.shutdown();
    /**
    * [출력]
    * helloOtherFuture.isDone: false
    * Started!!
    * helloOtherFuture.isDone: false
    * // Get 실행
    * helloOtherFuture.isDone: true
    * End!!
    */
```


#### 작업 취소하기 cancle()
- 취소했으면 True 못하면 False
- parameter로 true를 전달하면 현재 진행중인 쓰레드를 interrupt하고 그러지 않으면 현재 진행중인 작업이 끝날때까지 기다린다.
- 취소하면 땡땡땡 다시 못살림
```java
    ExecutorService otherExecutorService = Executors.newSingleThreadExecutor();

    Callable<String> hello = () ->{
        Thread.sleep(2000L);
        return "Hello Callable~ ";
    };
    Future<String> helloOtherFuture = otherExecutorService.submit(hello);
    System.out.println("helloOtherFuture.isDone: " + helloOtherFuture.isDone());
    System.out.println("Started!!");

    helloOtherFuture.cancel();
    helloOtherFuture.get();
    // cancel() 이후에 get 처리시 에러 발생
    // Exception in thread "main" java.util.concurrent.CancellationException
    //	at java.base/java.util.concurrent.FutureTask.report(FutureTask.java:121)
    //	at java.base/java.util.concurrent.FutureTask.get(FutureTask.java:191)

    System.out.println("helloOtherFuture.isDone: " + helloOtherFuture.isDone());
    System.out.println("End!!");
    otherExecutorService.shutdown();
```


#### 여러 작업 동시에 실행하기 invokeAll()
- 동시에 실행한 작업 중에 제일 오래 걸리는 작업 만큼 시간이 걸린다. 
```java
import java.util.Arrays;
import java.util.List;
import java.util.concurrent.*;

public class CallableExample2 {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        ExecutorService executorService = Executors.newFixedThreadPool(5);

        Callable<String> spring = () -> {
            Thread.sleep(1000L);
            return "spring";
        };
        Callable<String> java = () -> {
            Thread.sleep(3000L);
            return "java";
        };
        Callable<String> keesun = () -> {
            Thread.sleep(5000L);
            return "keesun";
        };
        Callable<String> hello = () -> {
            Thread.sleep(2000L);
            return "hello";
        };

        // 각 callable 끝날때까지 기다림.
        // 가장 긴 keesun이 끝날때까지 기다린다.
        // 제일 먼저 끝나는 작업이 무엇이든 나머지 친구들은 유사하게 끝남..
        List<Future<String>> futureList = executorService.invokeAll(Arrays.asList(keesun,spring, java,hello));

        int idx = 0;
        long before = System.currentTimeMillis();
        System.out.println("before :: "+ before);
        for (Future<String> future : futureList) {
            double end = (System.currentTimeMillis() - before) / 1000.0;
            System.out.printf(future.get());
            System.out.printf(" :: futureList[%d] - end :: %f \n",idx, end);
            idx++;
        }
        /**
         * keesun :: futureList[0] - end :: 0.018000
         * spring :: futureList[1] - end :: 0.026000
         * java :: futureList[2] - end :: 0.026000
         * hello :: futureList[3] - end :: 0.026000
         */
        executorService.shutdown();
    }
}
```

#### 여러 작업 중에 하나라도 먼저 응답이 오면 끝내기 invokeAny()
- 동시에 실행한 작업 중에 제일 짧게 걸리는 작업 만큼 시간이 걸린다. 
- 블로킹 콜
```java
public class CallableExample2 {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        ExecutorService executorService = Executors.newFixedThreadPool(5);

        Callable<String> spring = () -> {
            Thread.sleep(1000L);
            return "spring";
        };
        Callable<String> java = () -> {
            Thread.sleep(3000L);
            return "java";
        };
        Callable<String> keesun = () -> {
            Thread.sleep(5000L);
            return "keesun";
        };
        Callable<String> hello = () -> {
            Thread.sleep(2000L);
            return "hello";
        };

        /**
         * 서버 3대인데, 똑같은 파일 가져오라고 했는데 각 서버에 다 똑같은 파일(copy)를 가지고 있다.
         * 이때 어느 서버든 먼저 도착하는애 착안하면 되는 상황
         * -> invokeAny 사용
         */
        // ** 쓰레드가 List 개수보다 작은 경우, List 넣은 순서대로 큐에 쌓이므로.. 정해진 쓰레드에 먼저 적재된 작업중에 짧은것 리턴
        String s = executorService.invokeAny(Arrays.asList(hello, java,keesun,spring));
        System.out.println(s);
        // [출력] spring  -> sleep 제일 짧음..

        executorService.shutdown();
    }
}


```