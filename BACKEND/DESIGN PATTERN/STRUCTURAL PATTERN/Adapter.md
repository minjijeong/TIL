## Adapter 패턴
> 일상생활에서 돼지코(Adapter) 전격전압이 차이가 있는 것을 호환 가능하도록 세팅 
- 인터페이스가 전혀 다르지만, 기존 코드를 클라이언트가 사용하는 인터페이스의 구현체로 바꾸어주는 패턴
  - 클라이언트가 사용하는 인터페이스를 따르지 않는 기존 코드를 재사용할 수 있게 해준다. 

Client<C> -> Target<I> <-(구현)- Adapter<C> -> Adaptee<C>

- 방식 
  - 기존 코드를 변경할 수 없을때, target과 adaptee 호환 클래스 생성하여 처리
  - adaptee는 수정 가능, target을 ataptee에 구현하여 처리

- 장점
  - 기존 코드를 변경하지 않고 원하는 인터페이스 구현체를 만들어 재사용할 수 있다. (OCP특징 )
  - 기존 코드가 하던 일과 특정 인터페이스 구현체로 변환하는 작업을 각기 다른 클래스로 분리하여 관리할 수 있다.  (SRP원칙 특징)
- 단점
  - 새 클래스가 생겨 복잡도가 증가할 수 있다. 경우에 따라서는 기존 코드가 해당 인터페이스를 구현하도록 수정하는 것이 좋은 선택이 될 수 있다. 

Target<I> : UserDetails, UserDetailService


<details>
<summary> [문제] Security 패키지와 별도의 Account 서비스가 서로 호환하도록 만드는 예제  </summary>
<div markdown="1">

**Security Package**
- UserDetails : 사용자명, 비밀번호 로드
- UserDetailService : 사용자명을 기준으로 고객 정보 가져올수 있도록 하는 서비스
- LoginHandler : 사용자명, 비밀번호 기준으로 비밀번호 틀렸는지 체크하여 로그인 진행
```java
public interface UserDetails {
    String getUserName();
    String getPassword();
}

public interface UserDetailService{
    UserDetails loadUser(String userName);
}

public class LoginHandler {
    UserDetailService userDetailService;

    public LoginHandler(UserDetailService userDetailService){
        this.userDetailService = userDetailService;
    }

    public String login(String userName, String password){
        UserDetails userDetails = userDetailService.loadUser(userName);
        if(userDetails.getPassword().equals(password)){
            return userDetails.getUserName();
        }else{
            throw new IllegalArgumentException();
        }

    }
}

```
</div>
</details>
<br>

<details>
<summary> [패턴적용 방식1] 바꿀수 없는 security package, Account, AccountService가 존재할 때 Adapter 패턴을 사용하여 처리  </summary>
<div markdown="1">

**Adater 역할의 클래스**
- AccountUserDetails : Account -> UserDetails 변환
- AccountUserDetailsService : AccountService -> UserDetailsService 변환

```java
public class AccountUserDetails implements UserDetails {
    private Account account;

    public AccountUserDetails(Account account){
        this.account = account;
    }
    @Override
    public String getUserName() {
        return this.account.getName();
    }

    @Override
    public String getPassword() {
        return this.account.getPassword();
    }
}


public class AccountUserDetailsService implements UserDetailsService {

    AccountService accountService;

    public AccountUserDetailsService(AccountService accountService){
        this.accountService = accountService;
    }
    @Override
    public UserDetails loadUser(String userName) {
        Account accountByUserName = accountService.findAccountByUserName(userName);
        return new AccountUserDetails(accountByUserName);
    }
}
```

**Client(App)에서 변환된 내용 적용** 

```java
public class App {
    public static void main(String[] args) {
        AccountService accountService = new AccountService();
        // AccountService -> UserDetailsService 변환
        // UserDetailsService 내에 Account -> UserDetail로 변환 로직 포함
        UserDetailsService userDetailsService = new AccountUserDetailsService(accountService);
        // UserDetailService의 기존 로직 LoginHadler 처리
        LoginHandler loginHandler = new LoginHandler(userDetailsService);
        String login = loginHandler.login("keesun", "keesun");
        System.out.println(login);
    }
}

```
</div>
</details>
<br>


<details>
<summary> [패턴적용 방식2] Account, AccountService를 바꿀수 있을때는 security 패키지를 구현하여 Adapter 패턴을 적용가능 </summary>
<div markdown="1">

**Adaptee에 각각 인터페이스를 상속받아 구현**
```java
@Data
public class Account implements UserDetails {
    ...
    @Override
    public String getUserName() {
        return this.name;
    }
}

public class AccountService implements UserDetailsService {
    ...
    @Override
    public UserDetails loadUser(String userName) {
        return findAccountByUserName(userName);
    }
}
```

**Client(APP)에서 직접 AccountService를 주입하여 처리** 
```java
public class App {
    public static void main(String[] args) {
        // UserDetailsService의 구현체인 AccountService를 주입하여 처리
        UserDetailsService userDetailsService = new AccountService();
        LoginHandler loginHandler = new LoginHandler(userDetailsService);
        String login = loginHandler.login("keesun", "keesun");
        System.out.println(login);
    }
}
``` 

</div>
</details>
<br>



### 실제 사용되는 케이스 
- 자바 
  - java.util.Arrays#asList(T...)
  ```java
    // 배열 인터페이스 -> 리스트 인터페이스로 변환
    List<String> strings = Arrays.asList("a","b","c"); // 가변인자 T...
  ```
  - java.util.Collections#list(Enumeration), java.util.Collections#enumeration()
  ```java
    // 리스트 인터페이스 -> Enumeration 변환
    Enumeration<String> enumeration = Collections.enumeration(strings);
    while(enumeration.hasMoreElements()){
        String str = enumeration.nextElement();
        System.out.println(str);
    }

    // Enumeration -> List로 변환
    ArrayList<String> list = Collections.list(enumeration);
  ```
  - java.io.InputStreamReader(InputStream)
  ```java
    // IO
    // File -> InputStream으로 변환
    // FileInputStream은 InputStream을 상속받아서 해당형으로 리턴
    try(InputStream is = new FileInputStream("input.txt");
        InputStreamReader isr = new InputStreamReader(is);
        BufferedReader reader = new BufferedReader(isr)){
        while(reader.ready()){
            System.out.println(reader.readLine());
        }
    } catch (IOException e) {
        throw new RuntimeException(e);
    }  
  ```
  - java.io.OutputStreamWriter(OutputStream)

- Spring 
  - HandlerAdpter : 우리가 작성하는 다양한 형태의 핸들러 코드를 스프링 MVC가 실행할 수 있는 형태로 변환해주는 어댑터용 인터페이스
    - RequestmappingAdapterhandler : @Controller, @RequestMapping을 통해 adapter 세팅
    - 사용자가 HandlerAdapter 인터페이스를 제공함으로써 다른 Handler를 추가하거나 지원할 수 있도록 함
  ```java
  public class DispatcherServlet extends FrameworkServlet {
      ...
      protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
          ...
          // handlerAdapter handler를 찾는다
          HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());
          ...
          // handler 실행하여 요청을 처리하고 modelandview로 변환하여 전달 
          // view에 적절한 데이터를 넣어서 랜더링 한다.
          mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
          ...
      }
      ...
  }

  ```