## 생성 패턴 (Creational Pattern)
### 싱글톤 Singleton 패턴
인스턴스를 오직 한개만 제공하는 클래스, Global 하게 접근 가능하도록 제공

- 시스템 런타임, 환경 세팅에 대한 정보 등 인스턴스가 여러개 일때 문제가 생길 수 있는 경우가 있다. 
  *인스턴스를 오직 한개만* 만들어 제공하는 클래스가 필요

#### 구현 방법 
- private 생성자에 static 메소드 
  - 생성자 직접 생성 제한할 수 있다. 
  - 객체 생성 시도 시, 기존에 생성된 객체가 있다면 추가 생성 방지할 수 있다. 
  - 단, 멀티쓰레드로 실행되는 경우 동시에 getInstance 호출 시, 중복으로 생성 가능   
```java 
public class PrivateStaticSingleton {

    private static PrivateStaticSingleton instance;

    // 생성자
    // 생성자를 private인 이유 : 객체 직접 생성 제한 
    private PrivateStaticSingleton(){}

    // 인스턴스(=객체)를 한회만 생성 하기 위해 instance가 생성이 안된 경우만 객체 생성
    public static PrivateStaticSingleton getInstance(){
        // 멀티 쓰레드가 동시에 해당 메소드를 호출하는 경우 
        // -> thread safe 하지 않다. 
        if(instance == null){
            instance = new PrivateStaticSingleton();
        }
        return instance;
    }
}

```

- 동기화(Synchronized)를 사용해 멀티 쓰레드 환경에 안전하게 만드는 방법
  - 메소드를 동기화 블럭 처리한다. 
  - lock을 걸어 동기화 처리 한다. 

```java
public class SynchronizedSingleton{

    private static SynchronizedSingleton instance;

    private SynchronizedSingleton(){}

    // synchronized 예약어를 통해 class lock
    // 메소드 호출할때마다 동기화 처리 되므로 느릴수 있다.
    public static synchronized SynchronizedSingleton getInstance(){
        if(instance == null){
            instance = new SynchronizedSingleton();
        }
        return instance;
    }
}
```

- 이른 초기화 (eager initialization)을 사용하는 방법 
  *객체를 미리 생성하는 비용이 높지 않을때 사용*
  - thread safe 함. 
  - loading 시점 초기화 됨
  - 객체를 미리 생성하는 것이 오래걸리거나 메모리 점유가 크다면 비용 낭비 일 수 있다!

```java
public class EagerSingleton {
    // static한 final, 고정 값이므로, 상수로 생각하여 instance 대문자로 표기
    private static final EagerSingleton INSTANCE = new EagerSingleton();

    // 생성자 contructor
    private EagerSingleton(){
    }

    public static EagerSingleton getInstance(){
        return INSTANCE;
    }
}
``` 

- Double chekced locking으로 효율적인 동기화 블럭만들기 
  *JDK 1.5 부터 volatile이 사용 가능하므로, 1.5버젼 이상에서만 구현 가능*
  - instance == null 인 케이스에만 동기화 블럭 생성하여 class lock을 한다. 

```java
public class DoubleCheckedSingleton {
    private static volatile DoubleCheckedSingleton instance;
    /**
     * ** 메인메모리와 스레드의 Working 메모리 간에 데이터의 이동이 있기 때문에
     * 메인메모리와 Working 메모리간에 동기화가 진행되는 동안 빈틈이 생기게 되므로,
     * 이를 위해 volatile로 선언해주어야 한다.
     *
     * volatile :
     * 각 스레드가 해당 변수의 값을 메인 메모리에서 직접 읽어온다.
     * volatile 변수에 대한 각 write 는 즉시 메인 메모리로 플러시 된다.
     * 스레드가 변수를 캐시하기로 결정하면 각 read/write 시 메인 메모리와 동기화 된다.
     */
    private DoubleCheckedSingleton(){}

    public static DoubleCheckedSingleton getInstance(){
        // null이 아닌경우 바로 return 해주기 위해
        // null 이 아닌 경우 동기화 블록 지정
        if(Objects.isNull(instance)){
            // synchronized는 해당 메소드를 호출할때마다 다른 스레드에서 접근할 수 없게 처리한다.
            synchronized (DoubleCheckedSingleton.class){
                // 실제 동기화 된 상태에서 인스턴스가 null인지 체크
                // 1개만 생성되게 할려고 null 체크를 한다.
                if(Objects.isNull(instance)){
                    instance = new DoubleCheckedSingleton();
                }
            }
        }
        return instance;
    }
}

```


- static inner 클래스를 사용하는 방법
  - inner class를 선언하여 상수 객체를 생성하여 처리

```java
public class StaticInnerSingleton {
    private StaticInnerSingleton(){}

    // inner class 선언
    private static class StaticInnerSingletonHolder{
        // static final 상수 선언
        private static final StaticInnerSingleton INSTANCE = new StaticInnerSingleton();
    }

    public static StaticInnerSingleton getInstance(){
        return StaticInnerSingletonHolder.INSTANCE;
    }
}
```

#### 깨트리는 방법
- 리플렉션을 사용하여 singleton 깨트리기
  -   private로 선언되어 있는 것들을 접근 가능하도록 설정 -> 생성자 실행으로 인스턴스 생성
```java
public class DestroySingletonByReflection {
    public static void main(String[] args){
        
        DoubleCheckedSingleton singleton = DoubleCheckedSingleton.getInstance();

        try {
            // constructor
            Constructor<DoubleCheckedSingleton> declaredConstructor = DoubleCheckedSingleton.class.getDeclaredConstructor();
            declaredConstructor.setAccessible(true); // private로 선언되어 있는 것 접근 가능하도록 처리
            // 인스턴스 신규 생성
            // DoubleCheckedSingleton.getInstance()를 통해 생성된 객체와 다른 신규 객체 생성
            DoubleCheckedSingleton singleton1 = declaredConstructor.newInstance();
            System.out.println(singleton1 == singleton);
        } catch (NoSuchMethodException e) {
            throw new RuntimeException(e);
        } catch (InvocationTargetException e) {
            throw new RuntimeException(e);
        } catch (InstantiationException e) {
            throw new RuntimeException(e);
        } catch (IllegalAccessException e) {
            throw new RuntimeException(e);
        }
    }
}

```

- 직렬화 & 역직렬화를 사용
```java
// 직렬화를 가능하도록 Serializable 구현
public class DoubleCheckedSingleton implements Serializable {
    ...
}
public class DestroySingletonBySerialized {
    public static void main(String[] args){
        DoubleCheckedSingleton singleton = DoubleCheckedSingleton.getInstance();
        DoubleCheckedSingleton singleton1;

        // 직렬화하여 파일로 생성 - singleton.obj
        try(ObjectOutput out = new ObjectOutputStream(new FileOutputStream("singleton.obj"))){
            out.writeObject(singleton);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

        // singleton.obj 파일 역직렬화하여 객체정보 주입 - 신규 객체로 생성처리
        try(ObjectInput in = new ObjectInputStream(new FileInputStream("singleton.obj"))){
            singleton1 = (DoubleCheckedSingleton) in.readObject();
        } catch (ClassNotFoundException | IOException e) {
            throw new RuntimeException(e);
        }

        System.out.println(singleton1 == singleton);
    }
}

```


#### 싱글톤을 깰 수 없도록 처리하는 방안
- Enum을 사용하는 방법
```java
public enum EnumSingleton {
    INSTANCE;
}
```
  - 상속을 하지 못한다. 
  - 컴파일 시 미리 생성된다. 
  - 직렬화/역질렬화에 의한 싱글톤 파괴 시도 - 역직렬화하여도 enum이므로 동일객체를 가져옴
    ```java
    public class DestroySingletonBySerialized {
        public static void main(String[] args){
            EnumSingleton eSingleton = EnumSingleton.INSTANCE;
            EnumSingleton eSingleton2;
            // 직렬화하여 파일로 생성 - singleton.obj
            try(ObjectOutput out = new ObjectOutputStream(new FileOutputStream("enumSingleton.obj"))){
                out.writeObject(eSingleton);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }

            // singleton.obj 파일 역직렬화하여 객체정보 주입
            try(ObjectInput in = new ObjectInputStream(new FileInputStream("enumSingleton.obj"))){
                eSingleton2 = (EnumSingleton) in.readObject();
            } catch (ClassNotFoundException | IOException e) {
                throw new RuntimeException(e);
            }

            System.out.println(eSingleton2 == eSingleton);
        }
    }
    ```
  - 리플랙션으로 파괴 - 인스턴스 생성이 불가하여 예외 처리됨
    ```java
    public class DestroySingletonByReflection {
        public static void main(String[] args){
            EnumSingleton enumSingleton = EnumSingleton.INSTANCE;
            EnumSingleton enumSingleton1;
            try {
                Constructor<EnumSingleton> enumSingletonConstructor = EnumSingleton.class.getDeclaredConstructor();
                enumSingletonConstructor.setAccessible(true);
                // enum은 생성자를 가질 수 없어 NoSuchMethodException 발생
                enumSingleton1 = enumSingletonConstructor.newInstance();
            } catch (NoSuchMethodException | InvocationTargetException | InstantiationException|IllegalAccessException e ) {
                throw new RuntimeException(e);
            }
            System.out.println(enumSingleton == enumSingleton1);
        }
    }
    ```

#### 싱글톤 사용 케이스
- 스프링에서 빈의 스코프 중에 하나로 싱글톤 스코프
- java.lang.Runtime 
- 다른 디자인 패턴(빌더, 퍼사드, 추상 팩토리 등) 구현체의 일부로 쓰이기도 한다. 
```java
public class SingletonExample {
    public static void main(String[] args){
        // 1. Runtime
        // Runtime 인스턴스는 싱글톤으로 구현되어 있어 신규 인스턴스 생성 안됨
        // Runtime runtime = new Runtime();
        Runtime runtime = Runtime.getRuntime();
        System.out.println(runtime.maxMemory());
        System.out.println(runtime.freeMemory());

        // 2. Spring
        // singleton scope으로 구현해서 쓴다.
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
        String hello = applicationContext.getBean("hello", String.class);
        String hello2 = applicationContext.getBean("hello", String.class);
        System.out.println(hello == hello2);
    }
}
```