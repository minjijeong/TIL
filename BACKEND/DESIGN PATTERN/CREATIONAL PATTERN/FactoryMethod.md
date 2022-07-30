## Factory Method 팩토리 메소드
어떤 인스턴스를 생성하는 책임을 구체적인 클래스가 아니라 추상적인 인터페이스로 감싸는 형태 패턴
**확장에 열려있고 변경에 닫혀있는 구조(OCP를 준수한 구조)로 만들어보자**
- concreate class에 모든 로직을 담기에 크다면, 추상화된 팩토리 역할을 할 인터페이스를 만들고, 일부 바뀌어야하는 부분을 추상메소드로 만들고 하위 클래스에서 구체적인 클래스 메소드를 재정의하여 사용 
- 다양한 구현체(Product)가 있고, 그중에서 특정한 구현체를 만들 수 있는 다양한 팩토리(Creator)를 제공할 수 있다. 
- 구체적인 팩토리 안에서 구체적인 제품을 만들어 낸다. 

### 장점
  - 확장에 열려있고 변경에 닫혀 있어 OCP를 준수 하기 때문에 신규 구현체를 쉽게 생성 및 확장이 가능하다
    - product와 creator의 느슨한 결합을 가지고 있기 때문에 확장이 쉽게 가능하다  
###  단점
  - 각자의 역할을 나누다 보니 클래스가 늘어난다

<details>
<summary> [예제] 화이트쉽 생성 로직에 블랙쉽 추가 </summary>
<div markdown="1">

```java
@Data
public class Ship {
    String name;
    String logo;
    String color;
}

/**
 * 배 생성 비지니스 로직 모두 포함
 */
public class ShipFactory {
    
    static final String BLACKSHIP = "blackship";
    static final String WHILESHIP = "whiteship";

    public static Ship orderShip(String name, String email) {
        // validate
        if(name.isEmpty()){
            throw new IllegalArgumentException("배 이름을 지어주세요");
        }
        if(email.isEmpty()){
            throw new IllegalArgumentException("연락처를 남겨주세요.");
        }

        prepareFor(name);

        Ship ship = new Ship();
        ship.setName(name);

        // Customizing for specific name
        if(name.equalsIgnoreCase(WHILESHIP)){
            ship.setLogo("\uD83D\uDEE5");
        } else if (name.equalsIgnoreCase(BLACKSHIP)) {
            ship.setLogo("블랙쉽로고");
        }
        
        // Coloring 
        if(name.equalsIgnoreCase(BLACKSHIP)){
            ship.setColor("black");
        } else if (name.equalsIgnoreCase(WHILESHIP)) {
            ship.setColor("while");
        }

        sendEmailTo(email, ship);
        return ship;
    }

    private static void sendEmailTo(String email, Ship ship) {
        System.out.println(ship.name + " 다 만들었습니다.");
    }

    private static void prepareFor(String name) {
        System.out.println(name + " 만들 준비 중");
    }
}

/**
 * 배 생성을 위한 클라이언트에서 호출
 */
public class Client {
    public static void main(String[] args){
        Client client = new Client();

        Ship whiteShip = ShipFactory.orderShip("WhiteShip", "keesun@gamil.com");
        System.out.println(whiteShip);

        Ship blackShip = ShipFactory.orderShip("blackShip", "keesun@gmail.com");
        System.out.println(blackShip);

    }
}

```

</div>
</details>
<br>

<details>
<summary> [예제] 배 만드는 로직 별도 분리 </summary>
<div markdown="1">

<details>
<summary> [예제] 배 만드는 로직 별도 분리 - Factory 생성 </summary>
<div markdown="1">

```java
// 공통 비지니스 로직 - 배 공정 부분 인터페이스로 생성 
//(자바8 이전은 추상클래스로 생성해야함 default 안되서..)
public interface ShipFactory {
    default Ship orderShip(String name, String email){
        validate(name, email);
        prepareFor(name);
        Ship ship = createShip();
        sendEmailTo(email, ship);
        return ship;
    }

    Ship createShip();

    default void validate(String name, String email){
        if(name.isEmpty()){
            throw new IllegalArgumentException("배 이름을 지어주세요");
        }
        if(email.isEmpty()){
            throw new IllegalArgumentException("연락처를 남겨주세요.");
        }
    }

    default void sendEmailTo(String email, Ship ship) {
        System.out.println(ship.name + " 다 만들었습니다.");
    }

    default void prepareFor(String name) {
        System.out.println(name + " 만들 준비 중");
    }
}
```
</div>
</details>
<br>

<details>
<summary> [예제] 배 만드는 로직 별도 분리 - whiteship 생산 </summary>
<div markdown="1">

```java
// 배 설정 하는 부분은 따로 구현 - 변경 가능 영역
public class WhiteShipFactory implements ShipFactory{
    @Override
    public Ship createShip() {
        return new WhiteShip();
    }
}

@Data
public class Ship {
    String name;
    String logo;
    String color;
}

// 배 생성 
public class WhiteShip extends Ship{
    public WhiteShip(){
        setName("whiteShip");
        setLogo("whiteShip Logo");
        setColor("white");
    }
}
```
</div>
</details>
<br>


<details>
<summary> [예제] 배 만드는 로직 별도 분리 - blackship 생산 </summary>
<div markdown="1">

```java
public class BlackShipFactory implements ShipFactory{
    @Override
    public Ship createShip() {
        return new BlackShip();
    }
}

public class BlackShip extends Ship {
    public BlackShip(){
        setName("blackShip");
        setColor("black");
        setLogo("blackShip logo");
    }
}
```
</div>
</details>
<br>

**클라이언트에서 whiteship, blackship 생산**
```java
public class Client {
    public static void main(String[] args){
        Client client = new Client();

        Ship whiteShip = new WhiteShipFactory().orderShip("WhiteShip", "keesun@gamil.com");
        System.out.println(whiteShip);

        Ship blackShip = new BlackShipFactory().orderShip("blackShip", "keesun@gmail.com");
        System.out.println(blackShip);

    }
}
```

</div>
</details>
<br>


<details>
<summary> [예제] 배 만드는 로직 별도 분리 - 공장에 따라 다르게 생산 DIP, LSP에 준수하여 수정 </summary>
<div markdown="1">

**클라이언트에서 print에 주입되는 ShipFactory 구현체에 따라 적용된다.**
```java
public class Client {
    public static void main(String[] args){
        Client client = new Client();
        // print에 주입되는 ShipFactory 구현체에 따라 적용된다.
        client.print(new WhiteShipFactory(), "WhiteShip", "keesun@mail.com");
        client.print(new BlackShipFactory(), "WhiteShip", "keesun@mail.com");
    }

    private void print(ShipFactory shipFactory, String name, String email){
        System.out.println(shipFactory.orderShip(name, email));
    }
}
```
</div>
</details>
<br>


### 실제 사용되는 케이스 

- 단순한 팩토리 패턴
  - 매개변수의 값에 따라 또는 메소드에 따라 각기 다른 인스턴스를 리턴하는 단순한 버젼의 팩토리 패턴
  - 예제 - SimpleFactory에 각각 product를 케이스에 따라 다른 타입의 인스턴스를 반환할수 있다. 
    product는 같은 인터페이스에 대한 구현체이다.
  ```java
  public class SimpleFactory {
    public Object createProduct(String name){
        if(name.equals("whiteship")){
            return new WhiteShip();
        }else if(name.equals("blackship")){
            return new BlackShip();
        }
        throw new IllegalArgumentException();
    }
  }

  ```
  - java.lang.Calendar 또는 java.lang.NumberFormat 
    - Calendar 인스턴스가 overloading 되어 있어 각가 다른 타입의 인스턴스 봔환
  ```java
    public class CalendarExample {
      public static void main(String[] args) {
          System.out.println(Calendar.getInstance().getClass()); // java.util.GregorianCalendar
          // Calendar Overloading 하여 실행
          System.out.println(Calendar.getInstance(Locale.forLanguageTag("th-TH-x-lvariant-TH"))); // sun.util.BuddhistCalendar
          System.out.println(Calendar.getInstance(Locale.forLanguageTag("ja-JP-x-lvariant-JP"))); // java.util.JapaneseImperialCalendar

          // NumberFormat Overloading Instance를 별도로 구현
          NumberFormat nf1 = NumberFormat.getInstance();
          System.out.println(nf1.getClass());
          System.out.println(nf1.format(3000000));
          NumberFormat nf2 = NumberFormat.getInstance(Locale.CANADA_FRENCH);
          System.out.println(nf2.getClass());
          System.out.println(nf2.format(2000000));
          NumberFormat nf3 = NumberFormat.getCurrencyInstance(Locale.JAPAN);
          System.out.println(nf3.getClass());
          System.out.println(nf3.format(30000000));
      }
  }
  ```
  
- 스프링 BeanFactory
  - Object 타입의 Product를 만드는 BeanFactory라는 Creator!
  - xml, annotation 기준으로 bean 등록하는 인스턴스를 각각 구현해, 객체 주입으로 다른 인스턴스를 사용할 수 있다. 
  
  ```java
  public class SpringBeanFactoryExample {
    /**
     * AnnotationConfigApplicationContext, ClassPathxmlApplicationContext가 ConCreator 클래스들!
     */
    public static void main(String[] args) {
        // java 파일 기반 bean factory
        BeanFactory javaFactory = new AnnotationConfigApplicationContext(Config.class);
        String hi = javaFactory.getBean("hello", String.class);
        System.out.println(hi);
        System.out.println(hi.hashCode());
        WhiteShip ship = javaFactory.getBean("whiteship", WhiteShip.class);
        System.out.println(ship);
        System.out.println(ship.hashCode());

        // xml 기반 bean factory
        BeanFactory xmlFactory = new ClassPathXmlApplicationContext("config.xml");
        String hello = xmlFactory.getBean("hello", String.class);
        System.out.println(hello);
        System.out.println(hello.hashCode());
        WhiteShip xmlShip = xmlFactory.getBean("whiteship", WhiteShip.class);
        System.out.println(xmlShip);
        System.out.println(xmlShip.hashCode());
    }
}

  
  ```
  