## SOLID
> 2004년 마이클 페더스(Michael Feathers)가 원칙들을 재배열하면 각 원칙의 첫번째 글자들로 SOLID라는 단어를 만들수 있다는 이메일 투고

- SRP(Single Responsibility Principle): 단일 책임 원칙
  콘웨이(Conway) 법칙에 따른 정리: *소트프웨어 모듈은 변경의 이유가 단 하나여야만 한다.* 

- OCP(Open-Closed Principle) : 개방-폐쇄 원칙
  *클래스 동작을 수정하지 않고 확장할 수 있어야 한다.*
  기존코드를 수정하기보다는 반드시 새로운 코드를 추가하는 방식으로 시스템의 행위를 변경할 수 있도록 설계해야만 소프트웨어 시스템을 쉽게 변경할 수 있다

- LSP(Liskov Substitution Principle) : 리스코프 치환 원칙 
  하위 타입에 관한 유명한 원칙. *파생클래스는 기본 클래스를 대체할 수 있어야 한다.*
  상호 대체 가능한 구성요소를 이용해 소프트웨어 시스템을 만들 수 있으려면, 이들 구성요소는 반드시 서로 치환 가능해야 한다. 

- ISP(Interface Segregation Principle) : 인터페이스 분리 원칙
  *클라이언트별로 세분화 된 인터페이스를 만들라*
  소프트웨어 설계자는 사용하지 않은 것에 의존하지 않아야 한다. 

- DIP(Dependency Inversion Principle) : 의존성 역전 원칙
  *구체화에 의존하지 말고 추상화에 의존하라*
  고수준 정책을 구현하는 코드는 저수준 세부사항을 구현하는 코드에 절대로 의존해서는 안된다. 
  대신 세부사항이 정책에 의존해야 한다. 

### References
- [객체지향의 개념 기사](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod) 
- [SOLID wiki](https://en.wikipedia.org/wiki/SOLID)

### SOLID의 목적 
- 변경에 유연하다
- 이해하기 쉽다 
- 많은 소프트웨어 시스템에 사용될 수 있는 컴포넌트의 기반이 된다.

#### SRP(Single Responsibility Principle) 단일 책임 원칙
> 메서드와 클래스 수준의 원칙 
> 컴포넌트 수준에서는 공통 폐쇄 원칙이 되고, 아키텍처 수준에서는 아키텍처 경계의 생성을 책임지는 변경의 축이 된다.(?)  
> **하나의 모듈은 오직 하나의 액터에 대해서만 책임져야 한다.**
- 액터 : 변경을 요청하는 한명 이상의 사람들
- 모듈 : 함수와 데이터 구조로 구성된 응집된 집합

- 문제 
  많은 사람들이 각기 다른 목적으로 동일한 소스파일을 변경하는 경우 충돌 및 병합이 발생
- 해결법 
  서로 다른 액터를 뒷받침하는 코드를 서로 분리
  - 데이터와 메서드를 분리
  - Facade 패턴을 활용하여 해결



<details>
<summary>[예제] 사용자 가입 </summary>
<div markdown="1">
[소스 코드 출처](https://mangkyu.tistory.com/194) 

- UserService 
  - 기획팀 : 사용자 추가할때 역할(Role)에 대한 정의 필요
  - 보안팀 : 사용자 비밀번호 암호화 방식에 개선이 필요
```java
@Service
@RequiredArgsConstructor
public class UserService {
    private final UserRepository userRepository;

    public void addUser(final String email, final String pw) {
        final StringBuilder sb = new StringBuilder();

        for (byte b : pw.getBytes(StandardCharsets.UTF_8)) {
            sb.append(Integer.toString((b & 0xff) + 0x100, 16).substring(1));
        }

        final String encrytedPassword = sb.toString();
        final User user = User.builder()
                .email(email)
                .pw(encrytedPassword).build();

        userRepository.save(user);
    }
}

@Builder
@RequiredArgsConstructor
public class User {
    String email;
    String pw;
}

@Repository
public class UserRepository {
    public void save(User user) {
        System.out.println("save!");
    }
}
```

액터 기준으로 책임을 분리한다. 
- 비밀번호 암호화 분리 
```java
@Component
public class SimplePasswordEncoder {
    /**
     * password 암호화 분리 클래스
     */
    public String encrytPassword(final String pw){
        final StringBuilder sb = new StringBuilder();

        for(byte b : pw.getBytes(StandardCharsets.UTF_8)){
            sb.append(Integer.toString((b & 0xff) + 0x100, 16).substring(1));
        }

        return sb.toString();
    }
}

@Service
@RequiredArgsConstructor
public class UserService {
    private final UserRepository userRepository;
    private final SimplePasswordEncoder passwordEncoder;

    public void addUser(final String email, final String pw) {
        // 비밀번호 암호화 처리 분리
        final String encrytedPassword = passwordEncoder.encrytPassword(pw);
        final User user = User.builder()
                .email(email)
                .pw(encrytedPassword).build();

        userRepository.save(user);
    }
}
```


</div>
</details>
<br>


#### OCP(Open-Closed Principle) 개방-폐쇄 원칙
> 소프트웨어 개체 artifact는 확장에는 열려 있어야 하고, (=요구사항 변경시, 동작을 추가하여 확장 가능) 
> 변경에는 닫혀 있어야 한다. (=기존 코드를 수정하지 않고 애플리케이션 동작을 추가하거나 변경) 
- 기능이 어떻게, 왜, 언제 발생하는지에 따라서 기능을 분리하고, 분리한 기능을 컴포넌트의 계층구조로 조직화 한다.

<details>
<summary>[예제] 사용자 가입 - 비밀번호 암호화 방식 변경 </summary>
<div markdown="1">
[소스 코드 출처](https://mangkyu.tistory.com/194) 

- UserService 
  - 보안팀 : 사용자 비밀번호 암호화 방식에 SHA-256 알고리즘으로 변경
  
```java
@Service
@RequiredArgsConstructor
public class UserService {
    private final UserRepository userRepository;
    /**
     * Encoder가 바뀜에 따라 기존 UserService의 소스가 변경되어야 하는 것은
     * 개방폐쇄원칙에서 수정에 대해서는 닫혀있어야 한다는 원칙을 위배
     */
    private final SHA256PasswordEncoder passwordEncoder;
    ...
}

@Component
public class SHA256PasswordEncoder {
    private final static String SHA_256 = "SHA-256";

    // 비밀번호 암호화 
    // SimplePasswordEncoder 동일 메소드명, 인자값 
    public String encryptPassword(final String pw){
        final MessageDigest digest;
        try {
            digest = MessageDigest.getInstance(SHA_256);
        } catch (NoSuchAlgorithmException e) {
            throw new IllegalArgumentException();
        }

        final byte[] encodeHash = digest.digest(pw.getBytes(StandardCharsets.UTF_8));
        return bytesToHex(encodeHash);
    }

    private String bytesToHex(byte[] encodeHash) {
        final StringBuilder hexString = new StringBuilder(2 * encodeHash.length);

        for(final byte hash : encodeHash){
            final String hex = Integer.toHexString(0xff & hash);
            if(hex.length() == 1){
                hexString.append('0');
            }
            hexString.append(hex);
        }
        return hexString.toString();
    }
}

```

**개방 폐쇄 원칙을 지키기 위해서 추상화에 의존해야 한다.** 
- 변화하는 것들을 숨기고 변하지 않는 것들에 의존
- 비밀번호 암호화 인터페이스 생성 
- 암호화 encoder 각각 인터페이스 구현
```java
public interface PasswordEncoder {
    String encryptPassword(final String pw);
}


@Component
public class SimplePasswordEncoder implements PasswordEncoder{

    @Override
    public String encryptPassword(final String pw){
        ...
    }
}

@Component
public class SHA256PasswordEncoder implements PasswordEncoder{
    ...

    @Override
    public String encryptPassword(final String pw){
        ...
    }
}

@Service
@RequiredArgsConstructor
public class UserService {
    ...
    /**
     * PasswordEncoder 인터페이스로 추상화하여 의존성을 낮춘다.
     * PasswordEncoder를 구현한 구현체로 언제든지 바꿔어도 해당 서비스는 변경이 없다.
     */
    private final PasswordEncoder passwordEncoder;
    ...
}
```
</div>
</details>
<br>


<details>
<summary>[예제] JDBC Connection </summary>
<div markdown="1">
[OOP 설계 5원칙 참고](https://devlog-wjdrbs96.tistory.com/380)

Java Program -> JDBC 
                <-(구현)-ORACLE
                <-(구현)-SQL Server
                <-(구현)-SYBASE
                <-(구현)-MYSQL

데이터베이스가 Mysql에서 Oracle로 변경되더라도 Connection을 설정 하는 부분만 변경하면 된다.
즉, 자바 애플리 케이션은 데이터베이스라고 하는 주변의 변화에 닫혀 있고, 데이터 베이스를 교체한다는 것은 데이터베이스가 자신의 확장에는 열려 있다는 것입니다. 
</div>
</details>
<br>

#### LSP(Liskov Substitution Principle) 리스코프 치환 원칙 
> 하위 타입은 상위 타입을 상호대체 가능해야한다. 
> 즉, 해당 객체를 사용하는 클라이언트는 상위 타입이 하위 타입으로 변경되어도, 차이점을 인식하지 못한 채 상위 타입의 퍼블릭 인터페이스를 통해 서브 클래스를 사용할 수 있어야 한다.

*자식 클래스가 부모 클래스를 대체하기 위해서는 부모 클래스에 대한 클라이언트의 가정을 준수해야 한다는 것을 강조*

<details>
<summary>[예제] Square is Rectangle </summary>
<div markdown="1">

**직사각형의 하위타입으로서 정사각형**
```
Ractangle 클래스 (직사각형)
-(subtype) 정사각형 클래스 

-> 직사각형 가로,세로 독립적으로 변경 
-> 정사각형 가로,세로 같이 변경 
```
- 클라이언트는 직사각형의 너비와 높이는 다를 것이라고 가정. 정사각형은 이를 준수하지 못한다.
- **대체 가능성을 결정해야 하는 것은 해당 객체를 이용하는 클라이언트**
- 정사각형인지 검사하기 위해 사각형 타입에 의존, if/else로 타입별 구현이 되게된다. 즉 하위타입을 대체할 수 없다. 

```java
@Data
@AllArgsConstructor
public class Rectangle {
    private int width, height;

    public int getArea(){
        return width * height;
    }
}

public class Square extends Rectangle{
    public Square(int size) {
        super(size, size);
    }

    // 가로 변경시, 세로 동시 변경
    @Override
    public void setWidth(int width) {
        super.setWidth(width);
        super.setHeight(width);
    }

    // 세로 변경시, 가로 동시 변경
    @Override
    public void setHeight(int height) {
        super.setHeight(height);
        super.setWidth(height);
    }
}

public class Main {
    public static void main(String[] args){
        // 클라이언트 관점에서 자식 클래스는 부모 클래스를 대체할 수 없다. 
        Rectangle rectangle = new Square(50);
        resize(rectangle,100,150);
        // 출력은 resize시 가로세팅 후 세로세팅하여 세로 길이가 한변의 길이인 정사각형으로 고정된다
        System.out.println(rectangle.toString()); // 출력 150
    }

    public static void resize(Rectangle rectangle, int width, int height){
        // 정사각형인 경우 해당 메소드를 탈수 없도록 추상화 레벨에 맞추어 오류 처리 또는 메소드 호출 자체를 막거나 레벨에 맞도록 오버라이드 하여 재정의 하는 방법이 있다.  
        if(rectangle.getClass() == Square.class){
            throw new IllegalArgumentException();
        }
        rectangle.setWidth(width);
        rectangle.setHeight(height);
        if(rectangle.getHeight() != height && rectangle.getWidth() != width){
            throw new IllegalStateException();
        }
    }
}

```

</div>
</details>
<br>



#### ISP(Interface Segregation Principle) 인터페이스 분리 원칙
> 소프트웨어 설계자는 사용하지 않은 것에 의존하지 않아야 한다. 
> 클라이언트의 목적과 용도에 적합한 인터페이스만을 제공

<details>
<summary>[예제] 사용자 정보 수정 - 이전 비밀번호 동일한지 확인</summary>
<div markdown="1">
[소스 코드 출처](https://mangkyu.tistory.com/194) 

- UserService 
  - 기획팀 : 이전 비밀번호와 동일한 경우 사용자 정보 저장

```java
@Component
public class SHA256PasswordEncoder implements PasswordEncoder{
    ...
    /**
     * 이전 패스워드와 동일한지 비교
     */
    private boolean isCorrectPassword(final String rawPw, final String pw){
        final String encryptedPw = encryptPassword(rawPw);
        return encryptedPw.equals(pw);
    }
}
``` 

**클라이언트의 목적과 용도에 적합한 인터페이스만을 제공, 목적이 다르면 다른 인터페이스로!!**
- encoder 암호화를 목적이지만, isCorrectPassword는 인증, 확인 절차를 위한 것이므로 별도 인터페이스로 분리

```java
public interface PasswordChecker {
    boolean isCorrectPassword(final String rawPw, final String pw);
}

@Component
public class SHA256PasswordEncoder implements PasswordEncoder, PasswordChecker{
    ...
    @Override
    public boolean isCorrectPassword(final String rawPw, final String pw){
        final String encryptedPw = encryptPassword(rawPw);
        return encryptedPw.equals(pw);
    }
}

@Service
@RequiredArgsConstructor
public class UserService {
    ...
    // 비밀번호 체크 인터페이스 별도 구현
    private final PasswordChecker passwordChecker;
    ...
    public void updateUser(final String email, final String pw){
        User user = userRepository.getUserInfo(email);
        // 디비에서 가져오지 않아서.. 인코딩 수기로..
        user.pw = passwordEncoder.encryptPassword(user.pw);
        // 비밀번화 확인
        if(email.equals(user.email) && passwordChecker.isCorrectPassword(user.pw, pw)){
            User updatedUser = User.builder()
                            .email(email)
                            .pw(passwordEncoder.encryptPassword(pw))
                            .build();
            userRepository.save(updatedUser);
        }else{
            System.out.println("잘못된 패스워드 입력!");
        }
    }
}

@Repository
public class UserRepository {
    ...
    public User getUserInfo(String email) {
        return  User.builder()
                .email(email)
                .pw("test")
                .build();
    }
}

```



</div>
</details>
<br>

#### DIP(Dependency Inversion Principle) 의존성 역전 원칙
> 고수준의 모듈은 저수준의 모듈의 구현에 의존해서는 안되며, 저수준 모듈이 고수준 모듈에서 정의한 추상 타입에 의존해야 한다
> - 고수준 모듈 : 변경이 없는 추상화된 클래스(또는 인터페이스)
> - 저수준 모듈 : 변하기 쉬운 구체 클래스 

**'유연성이 극대화된 시스템' 이란?** 
소스 코드 의존성이 추상 abstraction에 의존하며 구체 concretion에는 의존하지 않는 시스템 
             
<details>
<summary>[예제] 사용자 가입 </summary>
<div markdown="1">
UserService<C> -(의존)-> PasswordEncoder<I>
                            <-(구현)-SimplePasswordEncoder<C>
                            <-(구현)-SHA256PasswrodEncoder<C>
                            <-(구현)-BCryptPasswrodEncoder<C>

*UserService가 PasswordEncoder 추상화 없이 SimplePasswordEncoder에 직접 의존하는 것은 의존성 역전 원칙에 위배되는 것이다.*
UserService를 PasswordEncoder 인터페이스를 의존하게 함으로써 암호화 정책 구현체가 바뀌어도 다른 곳들로 변경이 전파되 않으며 유연한 애플리케이션이 된다.  

**의존 역전 원칙에서 의존성이 역전되는 시점은 컴파일 시점이라는 것을 주의 해야한다.** 

</div>
</details>
<br>


**운영체제나 플랫폼 같이 안정성이 보장된 환경에 대해서는 의존성은 벗어날수 없고 벗어나도 안된다**
변동성이 큰 volatile 구현체는 의존하지 않도록 피해야 한다!!




##### 안정된 추상화 
**안정된 소프트웨어 아키텍처란** 변동성이 큰 구현체에 의존하는 일은 지양하고, 안정된 추상 인터페이스를 선호나는 아키텍처라는 뜻 

**실천법**
- 변동성이 큰 구현체 클래스를 참조하지 말라.
  - 대신 추상 인터페이스를 참조하라
  - 객체 생성 방식을 강하게 제약하며, 추상 팩토리 Abstract Factory를 사용하도록 강제
- 변동성이 큰 구체 클래스로부터 파생하지 말라 
  - 상속은 의존성을 가질수 밖에 없기 때문에 변경하기 어려우므로 신중하라
- 구체 함수를 오버라이드 하지 말라 
  - 구체 함수를 오버라이드 하면 의존성을 제거할수 없고, 그 의존성을 상속하게 된다. 
  - 추상함수로 선언하고 구현체들에서 가자의 용도에 맞게 구현해야 한다. 
- 구체적이며 변동성이 크다면 절대로 그 이름을 언급하지 말라


