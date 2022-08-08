## 1장 오브젝트와 의존관계 
- 스프링의 핵심 철학
  - 객체 지향 기술의 진정한 가치를 회복하고, 객체지향 프로그래밍이 제공하는 폭넓은 혜택을 누릴 수 있도록 기본으로 돌아가조 하는 것
  - Object에 초점을 맞추자

### 1.1 초난감 DAO
- **DAO (Data Access Object)** 
  - DB를 사용해 데이터를 조회하거나 조작하는 기능을 전담하도록 만든 오브젝트를 말한다. 

- **자바빈 (Java Bean)**
  - 초기 비주얼 툴에서 조작 가능한 컴포넌트를 뜻했음
  - JSP 빈, EJB와 같은 표준기술과 자바빈 스타일이 오브젝트 사용
  - 현재 
    - 디폴트 생성자 : 파라미터가 없는 디폴트 생성자를 보유
      - 프레임워크 또는 리플렉션을 이용해 오브젝트 생성할 때 필요
    - 프로퍼티 : 자바빈이 노출하는 이름을 가진 속성 (e.g. Setter, Getter)


<details>
<summary> <b>디비 모델 등록</b>  </summary>
<div markdown="1">

  - User.java
    - 사용자 정보 저장을 위한 자바빈 규약에 따른 오브젝트 
  ```java
  @Getter
  @Setter
  public class User {
      String id;
      String name;
      String password;
  }
  ```  
</div>
</details>

  <details>
  <summary> <b>DAO 구현</b>  </summary>
  <div markdown="1">

  - UserDao.java
    - 사용자의 정보를 관리하는 DAO
  ```java
  public class UserDao {
      public void add(User user) throws ClassNotFoundException, SQLException {
          // 1. DB Connection
          Class.forName("com.mysql.jdbc.Driver");
          Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");

          // 2. SQL을 담은 Statement 또는 PreparedStatement를 만든다.
          PreparedStatement ps = c.prepareStatement("insert into users(id, name, passwrod) values(?,?,?)");
          ps.setString(1, user.getId());
          ps.setString(2, user.getName());
          ps.setString(3, user.getPassword());

          // 3. Statement 실행
          ps.executeUpdate();

          // 4. 조회인 경우 실행결과 ResultSet으로 받아서 정보를 저장할 오브젝트에 옮겨준다.
          // 조회가 아니므로 pass

          // 5. 작업중에 생성된 리소스는 반환
          ps.close();
          c.close();
          // 6. JDBC API가 만들어내는 예외를 잡아서 처리
          // 없음
      }

      public User get(String id) throws ClassNotFoundException, SQLException {
          // 1. DB Connection
          Class.forName("com.mysql.jdbc.Driver");
          Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");

          // 2. SQL을 담은 Statement 또는 PreparedStatement를 만든다.
          PreparedStatement ps = c.prepareStatement("select * from users where id=?");
          ps.setString(1, id);

          // 3. Statement 실행
          ResultSet rs = ps.executeQuery();

          // 4. 조회인 경우 실행결과 ResultSet으로 받아서 정보를 저장할 오브젝트에 옮겨준다.
          rs.next();
          User user = new User();
          user.setId(rs.getString("id"));
          user.setName(rs.getString("name"));
          user.setPassword(rs.getString("password"));

          // 5. 작업중에 생성된 리소스는 반환
          rs.close();
          ps.close();
          c.close();
          // 6. JDBC API가 만들어내는 예외를 잡아서 처리
          // 없음
          return user;
      }
  }
  ```
</div>
</details>
<br>



### 1.2 DAO 분리
#### 관심사 분리
- 관심이 같은 것끼리 모으고, 관심이 다른 것은 따로 떨어져 있게 하는 것

#### UserDao 관심사항 
- DB 커넥션 
- 사용자등록을 위해 Statemnet 생성 및 실행 
- 사용한 리소스 반환

#### UserDao 리펙토링
##### 방법 1. 중복코드 메소드 추출
- DB가 변경이 되거나 해당 DAO를 여러 DB와 연결되어 사용하려고 하면 이슈가 있을 수 있다. 
```java
private Connection getConnection() throws ClassNotFoundException, SQLException {
    // 1. DB Connection
    Class.forName("com.mysql.cj.jdbc.Driver");
    return DriverManager.getConnection("jdbc:mysql://localhost:3306/spring", "spring", "book");
}
```

[(커밋 바로가기) 예제 - 1. 메소드 분리 ](https://github.com/minjijeong/javastudy/commit/c3b12183c83a4a3dae36447603baa0d2a83882d7)

##### 방법 2. 상속을 통한 확장
- **UserDao** 
  - 추상클래스로 변경
  - getConnection 메소드 추상 메소드로 선언
    - 각 N사, D사를 구현하기 위해 각각 자식클래스(서브클래스)에서 구현
    
    
  ***템플릿 메소드 패턴 Template Method*** : 
  *슈퍼클래스에 로직 흐름을 만들고, 그 기능의 일부를 추상 메소드나 오버라이딩이 가능한 protected/public 메소드 등으로 만든 뒤 서브클래스에서 이런 메소드를 필요에 맞게 구현해서 사용하도록 하는 방법*
  - 중복코드 제거가 목적
<br>

  ***팩토리 메소드 패턴 Factory Method*** : 
  *서브클래스에서 구체적인 오브젝트 생성 방법을 결정하게 하는 것*
  - 중복코드 제거와 상관없이, 구현을 서브클래스에 하도록 하는 것


  ```java
  public abstract class UserDao {
      ...
      public abstract Connection getConnection() throws ClassNotFoundException, SQLException;

  }

  // N사 DB 커넥션 구현 
  public class NUserDao extends UserDao{
      @Override
      public Connection getConnection() throws ClassNotFoundException, SQLException {
      }
  }

  // D사 DB 커넥션 구현 
  public class DUserDao extends UserDao{
      @Override
      public Connection getConnection() throws ClassNotFoundException, SQLException {
      }
  }
  ```
[(커밋 바로가기) 예제 - 2. 상속을 통한 확장](https://github.com/minjijeong/javastudy/commit/7ec681156f571c11cbc8f9df119ed70100f96725)


### 1.3 DAO 확장 
- 관계설정 책임의 분리 
- 개방 폐쇄 원칙에 따라 높은 응집도와 낮은 결합도를 가지도록 구성 
  - 기존의 기능은 변경되지 않아야하고, 추가(확장)은 가능해야 한다.

#### 방법 3. 인터페이스 분리 
- **UserDao 클래스** 
  - 기본 기능을 구현 (주요 클래스) 
  - N,D사 ConnectionMaker에 의존하지 않도록 결합도를 낮추기 위해 ConnectionMaker 인터페이스 생성자로 받아서 처리

- DB 커넥션 연결 역할을 분리하여 구현
  - **ConnectionMaker 인터페이스** 
    - DB 커넥션 연결을 위해 구현되어야 하는 추상 메소드 정의

  - **NConnectionMaker 클래스**
    - N사 커넥션 별도 구현
    
  - **DConnectionMaker 클래스**
    - D사 커넥션 별도 구현

[(커밋 바로가기) 예제 - 3. 인터페이스 분리](https://github.com/minjijeong/javastudy/commit/0b2f9b8756cc750099ff293abfdf4b20bada2812)


 ***전략패턴 :***
  *자신의 기능 맥락(context)에서 필요에 따라 변경이 필요한 알고리즘을 인터페이스를 통해 통째로 외부로 분리시키고, 이를 구현한 구체적인 알고리즘 클래스를 필요에 따라 바꿔서 사용할 수 있도록하는 디자인 패턴*



### 1.4 제어의 역전(IoC)
- 애플리케이션의 흐름에 따라 처리되는것이 아닌, 프레임워크 혹은 다른 객체에 의해 제어되는 것
  ex) DaoFactory에 의해 UserDaoTest는 UserDao가 컨트롤되었다. 

```java
// 클라이언트에서는 DaoFactory가 알아서 디비 커넥션 연결해주기때문에
// 별도로 처리 하지 않고 사용 가능
public class UserDoaTest {
    public static void main(String[] args) {
        UserDoaTest userDoaTest = new UserDoaTest();
        UserDao dao = new DaoFactory().userDao();
        ...
    }
}

// DaoFactory에 의해서 D사 커넥션 연결
public class DaoFactory {
    public UserDao userDao(){
        return new UserDao(new DConnectionMaker());
    }
}
```

### 1.5 스프링의 IoC
#### 스프링 Bean
- 스프링이 제어권을 가지고 직접 만들고 관계를 부여하는 오브젝트
- 스프링 컨테이너가 생성과 관계설정, 사용 등을 제어해주는 오브젝트
- 제어의 역전이 적용된 오브젝트
  
#### 빈 팩토리
- 빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트, 인터페이스
- IoC 기본 기능에 초첨 

#### 애플리케이션 컨텍스트 
- Bean Factory를 상속받아 확장된 것
- 애플리케이션 전반에 걸쳐 모든 구성요소의 제어 작업을 담당하는 IoC 엔진의 의미
- IoC 컨테이너 (=스프링 컨테이너)
  

(이해가 안가는 부분 다시 보기 100p)
- 클라이언트는 구체적인 팩토리 클래스를 알 필요가 없다. 
- 애플리케이션 컨텍스트는 종합 IoC 서비스를 제공해준다. 
- 애플리케이션 컨텍스트는 빈을 검색하는 다양한 방법을 제공한다. 


#### 설정정보/설정 메타 정보
애플리케이션 컨텍스트 또는 빈팩토리가 IoC를 적용하기 위해 사용하는 메타정보

- @Configuration
  - 구성 정보 or 형상 정보 
  - IoC 컨테이너의 의해 관리되는 애플리케이션 오브젝트를 생성하고 구성할 때 사용

#### 컨테이너 또는 IoC 컨테이너
IoC 방식으로 빈을 관리한다는 의미에서 IoC 컨테이너

#### 스프링 프레임워크 
IoC컨테이너, 애플리케이션 컨텍스트를 포함하여 스프링이 제공하는 모든 기능을 통틀어말할때 사용한다. 


### 1.6 싱글톤 레지스트리와 오브젝트 스코프 
- 애플리케이션 컨텍스트는 싱글톤을 저장하고 관리하는 싱글톤 레지스트리

#### 배경 
- 대규모 엔터프라이즈 경우 클라이언트 요청 마다 오브젝트를 새로 생성한다면 GC 부하를 발생시킬 수 있으므로 잦은 생성을 방지하고자 서비스 오브젝트라는 개념이 생성
- 서비스 오브젝트
  - ex) 서블릿 
    - 서블릿 클래스당 하나의 오브젝트만 생성 
    - 사용자 요청을 담당하는 여러 스레드에서 해당 오브젝트를 공유해 동시에 사용한다. 
- 무상태 방식 
  - 동시에 싱글톤 오브젝트의 인스턴스 변수를 수정하는 것이 위험함 

#### 싱글톤 패턴의 한계 
- private 생성자를 갖고 있기 때문에 상속할 수 없다. 
  - 객체지향의 특징인 상속, 다형성을 적용할 수 없다
- 싱글톤은 테스트하기 어렵다
- 서버환경에서는 싱글톤이 하나만 만들어지는 것을 보장하지 못한다
- 여러 개의 JVM에 분산되어 설치 시, 각각 독립적으로 오브젝트가 생성되어 싱글톤으로서 가치가 떨어진다.
- 싱글톤의 사용은 전역 상태를 만들 수 있기 때문에 바람직하지 못하다

#### 스프링 빈의 스코프 Scope
- 싱글톤 스코프 : 컨테이너 내의 한 개의 오브젝트만 만듬
  - 스프링 컨테이너가 존재하는 동안 유지
- 프로토타입 스코프 : 컨테이너 빈을 요청할때마다 새로운 오브젝트 생성 
- 세션 스코프 : 세션동안만 한개의 오브젝트가 유지되고 세션이 종료되면 다시 재성

  

### 1.7 의존관계 주입
의존된다 의미 
- A->B가 의존관계 -> B가 변경되는 경우 A에게 영향이 있다는 의미
- 방향성을 가진다

#### 런타임 의존관계 주입 조건 
- 런타임 시점의 의존관계가 드러나지 않는다. 
  - 인터페이스에만 의존하고 있어야 한다. 
- 런타임 시점의 의존관계는 컨테이너나 팩토리 같은 제 3의 존재가 결정
- 의존관계는 사용할 오브젝트에 대한 레퍼런스를 외부에서 제공해줌으로써 만들어짐
  
#### DI Dependency Insection 의존관계 주입
- 컨테이너가 관리하는 빈으로 등록이 되면, 

#### DL 의존관계 검색
- ApplicationContext에서 해당 오브젝트를 검색하여 getBean으로 해당 오브젝트를 불러온다


<details>
<summary> <b>의존 관계 주입 예제</b>  </summary>
<div markdown="1">
- LocalDBConnectionMaker 추가


</div>
</details>

#### 메소드를 이용한 의존관계 주입 
- 수정자(Setter) 메소드를 이용한 주입
  - 파라미터로 전달된 값을 내부의 인스턴스 변수에 저장
  - 부가적으로 검증 그 밖의 작업을 수행 가능  
  ```java
      public void setId(String id) {
        this.id = id;
    }
  ```

- 일반 메소드를 이용한 주입
  ```java
    // 생성자를 통한 주입
    public UserDao(ConnectionMaker connectionMaker){
        this.connectionMaker = connectionMaker;
    }
    
    // 메소드를 통한 주입
    public void setConnectionMaker(ConnectionMaker connectionMaker){
        this.connectionMaker = connectionMaker;
    }
  ```



### 1.8 XML을 이용한 설정

#### Property
- name : DI에 사용할 수정자 메소드의 프로퍼티 이름 
- ref : 주입할 오브젝트를 정의한 빈의 ID
```java
@Configuration
public class DaoFactory {

    @Bean 
    public UserDao userDao(){ 
        UserDao userDao = new UserDao();
        userDao.setConnectionMaker(connectionMaker());
        return userDao;

    }
    ...
    public class UserDao {
      private ConnectionMaker connectionMaker;

      public void setConnectionMaker(ConnectionMaker connectionMaker){
        this.connectionMaker = connectionMaker;
    } 
    //<bean id="userDao" class ="srpingbook.dao.UserDao">
    //  <property name="connectionMaker" ref="connectionMaker">
    //</bean>

    ...
    @Bean //----------> <bean  
    public ConnectionMaker realConnectionMaker() { //--> id="connectionMaker"
        return new DConnectionMaker();//-------> "class="springbook...DConnectionMaker" />
    }
    //<bean id="connectionMaker" class="springbook.dao.DConnectionMaker">

}
```