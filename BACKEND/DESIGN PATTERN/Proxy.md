## 프록시 패턴 
> 특정 객체를 접근하기 전에 Proxy(대리인)를 먼저 접근하여 진행
> 해당 객체에 대한 접근 제어를 하거나, 생성 리소스가 많이 비용이 된다면, 최초로 쓰일때 만들기 (초기화 지연), security 특정 권한 있는 유저만 사용가능, 프록시를 거쳐서 캐싱한 데이타가 있다면 오퍼레이터를 거치지 않고 갈수 있다. 

### 장점 
- 기존 코드를 변경하지 않고 새로운 기능을 추가할 수 있다. (OCP)
- 기존 코드가 해야 하는 일만 유지할 수 있다. (SRP)
- 기능 추가 및 초기화 지연 등으로 다양하게 활용할 수 있다. 

### 단점 
- 코드의 복잡도가 증가한다. 


<details>
<summary> [문제] 게임 - 스탑워치 추가  </summary>
<div markdown="1">

```java
public class Client {
    public static void main(String[] args) {
        // startGame 실행 시간을 측정하는 Stop Watch 추가하기
        GameService gameService = new GameService();
        gameService.startGame();
    }
}
public class GameService {
    public void startGame(){
        System.out.println("이 자리에 오신 여러분을 진심으로 환영합니다.");
     }
}

```
</div>
</details>
<br>


<details>
<summary> [해결방안] 타임워치 추가 - 기존소스 변경없이 </summary>
<div markdown="1">

**Proxy 클래스 생성하여 스탑워치 추가**
```java
public class GameServiceProxy extends GameService{
    @Override
    public void startGame() {
        long before = System.currentTimeMillis();
        super.startGame();
        System.out.println(System.currentTimeMillis() - before);
    }
}
```
**구현체만 GameService -> GameServiceProxy로 교체**
```java
public class Client {
    public static void main(String[] args) {
        // startGame 실행 시간을 측정하는 Stop Watch 추가하기
        GameService gameService = new GameServiceProxy();
        gameService.startGame();
    }
}
```
</div>
</details>
<br>


<details>
<summary> [해결방안2] 인터페이스 분리 </summary>
<div markdown="1">

- 데코레이터 패턴과 유사하게 인터체이스 분리! 각기 구현하여 처리 
  
**인터페이스 생성 **
```java
public interface GameService {
    void startGame();
}
```
**기존 로직 유지(default 서비스)**
```java
public class DefaultGameService implements GameService{
    @Override
    public void startGame(){
        System.out.println("이 자리에 오신 여러분을 진심으로 환영합니다.");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}
```

**Proxy 서비스 생성 및 부가기능 추가**
- GameService 인터페이스를 통해 DefaultGameService 기본 기능과의 의존성을 낮춘다.
```java
public class GameServiceProxy implements GameService{

    private GameService gameService;
    public GameServiceProxy(GameService gameService){
        this.gameService = gameService;
    }
    @Override
    public void startGame() {
        long before = System.currentTimeMillis();
        gameService.startGame();
        System.out.println(System.currentTimeMillis() - before);
    }
}
```

**Client에서 사용**
```java
public class Client {
    public static void main(String[] args) {
        // startGame 실행 시간을 측정하는 Stop Watch 추가하기
        // proxy로 default 서비스 의존성 주입하고 감싸준다
        GameService gameService = new GameServiceProxy(new DefaultGameService());
        gameService.startGame();
    }
}

```

</div>
</details>
<br>



### 실제 사용되는 케이스 
- 자바 
  - 다이나믹 프록시 
    - 런타임시 proxy 적용, 객체 생성 되는 것
  - java.lang.reflect.Proxy

```java
public class ProxyInJava {
    public static void main(String[] args) {
        // 런타임시 proxy 적용, 객체 생성 되는 것 = 다이나믹 프록시
        ProxyInJava proxyInJava = new ProxyInJava();
        proxyInJava.dynamicProxy();
    }

    private void dynamicProxy() {
        GameService gameServiceProxy = getGameServiceProxy(new DefaultGameService());
        gameServiceProxy.startGame();
    }

    private GameService getGameServiceProxy(GameService target) {
//        return (GameService) Proxy.newProxyInstance(this.getClass().getClassLoader(),
//                // 동적으로 생성되어야 하는 인터페이스 타입
//                new Class[]{GameService.class},(proxy, method, args) -> {
//                    System.out.println("hello dynamic proxy");
//                    method.invoke(target,args);
//                    return null;
//                });
        return (GameService) java.lang.reflect.Proxy.newProxyInstance(this.getClass().getClassLoader(),
                // 동적으로 생성되어야 하는 인터페이스 타입
                new Class[]{GameService.class}, new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("hello dynamic proxy");
                        method.invoke(target,args); // method 실행 어떤거 할지, 파라미터 어떤거 줄지
                        return null;
                    }
                });
    }
}
```
- Spring 
  - 스프링AOP
    -  특정 bean AOP 등록
    -  AspectJ 사용
  ```java
    @Service
    public class GameService {
        public void startGame() {
            System.out.println("이 자리에 오신 여러분을 진심으로 환영합니다.");
        }

    }

    @Aspect
    @Component
    public class PerfAspect {
        // ProceedingJoinPoint 특정 메소드를 지칭
        @Around("bean(gameService)")
        public void timestamp(ProceedingJoinPoint point) throws Throwable {
            long before = System.currentTimeMillis();
            point.proceed(); // 타켓메소드를 실행, 별도의 지정 없이
            System.out.println(System.currentTimeMillis() - before);
        }
    }

    // 스프링부트 실행
    @SpringBootApplication
    public class App {
        public static void main(String[] args) {
            SpringApplication app = new SpringApplication(App.class);
            app.setWebApplicationType(WebApplicationType.NONE);
            app.run(args);
        }

        // 특정 빈만 실행하도록 처리
        @Bean
        public ApplicationRunner applicationRunner(GameService gameService) {
            return args -> {
                gameService.startGame();
            };
        }
    }

  ```
  **pom.xml AspectJ 디펜던시 추가**
  ```
  		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjweaver</artifactId>
			<version>1.9.7</version>
		</dependency>
  ```