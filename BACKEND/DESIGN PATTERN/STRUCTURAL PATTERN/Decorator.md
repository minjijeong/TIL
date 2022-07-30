## 데코레이터 패턴 
- 기존의 코드를 변경하지 않고 부가 기능을 추가하는 패턴
  - 상속이 아닌 위임을 사용해서 보다 유연하게(런타임에) 부가 기능을 추가하는 것도 가능
  - 데코레이터 패턴을 통해서 상속이 아닌 조합을 해서 사용할 수 있음

Component <- ConcreteComponent, Decorator 각각 구현체를 가짐
Decorator는 단 1개의 wrapping된 component 구현체를 가진다

### 장점 
  - 새로운 클래스를 만들지 않고 기존 기능을 **조합**할 수 있다. 
  (= 단일책임원칙에 따라서 각 데코레이터의 역할은 유지하고 애플리케이션 단에서 조합을 해서 사용가능)
    - 기존코드를 수정하지 않고 부가 가능을 추가할 수있음(OCP 개방 폐쇄 원칙)
    - 인터페이스로 역할을 분리하고 데코레이터로 여러개의 구현체를 만들어 적용(ISP 인터페이스 분리 원칙)
  - 컴파일 타임이 아닌 런타임에 동적으로(다이나믹하게) 기능을 변경할 수 있다. 
  - 상속으로 구현하였을때는 시간복잡도는 데코레이터 케이스가 많아지면 2^n으로 증가할수 있다. 하지만 데코레이터로 처리되면 조합 가능하기때문에 O(n) = 1 예상된다.

### 단점 
  - 데코레이터를 조합하는 코드(애플리케이션에서 분기되는 부분) 가 복잡할 수 있다. 



<details>
<summary> [문제] - 댓글입력 기능 추가 </summary>
<div markdown="1">

**문제**
다중 상속이 허용이 안되는데... Trim, SpamFilter를 둘다 적용하고 싶은데.. 
- TrimmingCommentService : "..." -> "" 로 치환
- SpamFilteringCommentService : "http"를 포함하면 스팸으로 취급
*상속을 통해서 *

- CommentService 인터페이스를 만ㄷ르기 
- Trimming, SpamFiltering -> Decorate로 각각 구현체를 만든다. 

```java
public class CommentService {
    public void addComment(String comment) {
        System.out.println(comment);
    }
}

public class SpamFilteringCommentService extends CommentService {

    @Override
    public void addComment(String comment) {
        boolean isSpam = isSpam(comment);
        if (!isSpam) {
            super.addComment(comment);
        }
    }

    private boolean isSpam(String comment) {
        return comment.contains("http");
    }
}

public class TrimmingCommentService extends CommentService {

    @Override
    public void addComment(String comment) {
        super.addComment(trim(comment));
    }

    private String trim(String comment) {
        return comment.replace("...", "");
    }

}

public class Client {

    private CommentService commentService;

    public Client(CommentService commentService) {
        this.commentService = commentService;
    }

    private void writeComment(String comment) {
        commentService.addComment(comment);
    }

    public static void main(String[] args) {
        Client client = new Client(new SpamFilteringCommentService());
        client.writeComment("오징어게임");
        client.writeComment("보는게 하는거 보다 재밌을 수가 없지...");
        client.writeComment("http://whiteship.me");
    }
}
```
</div>
</details>
<br>


<details>
<summary> [해결방안] - 데코레이션 패턴으로 Trimming, SpamFilter를 데코레이트(wrapping)해서 각 서비스를 조합하여 사용할 수 있도록  </summary>
<div markdown="1">

**코멘트서비스 인터페이스 분리**
```java
public interface CommentService {

    void addComment(String comment);
}

public class CommentDecorator implements CommentService {

    private CommentService commentService;

    public CommentDecorator(CommentService commentService) {
        this.commentService = commentService;
    }

    @Override
    public void addComment(String comment) {
        commentService.addComment(comment);
    }
}

```
**코멘트 필터 wrapping**
```java
public class DefaultCommentService implements CommentService {
    @Override
    public void addComment(String comment) {
        System.out.println(comment);
    }
}

public class SpamFilteringCommentDecorator extends CommentDecorator {

    public SpamFilteringCommentDecorator(CommentService commentService) {
        super(commentService);
    }

    @Override
    public void addComment(String comment) {
        if (isNotSpam(comment)) {
            super.addComment(comment);
        }
    }

    private boolean isNotSpam(String comment) {
        return !comment.contains("http");
    }
}


public class TrimmingCommentDecorator extends CommentDecorator {

    public TrimmingCommentDecorator(CommentService commentService) {
        super(commentService);
    }

    @Override
    public void addComment(String comment) {
        super.addComment(trim(comment));
    }

    private String trim(String comment) {
        return comment.replace("...", "");
    }
}
```

**Application 필터/조건 조합, 부가기능 추가되어도 데코레이터만 추가할 수 있다**
```java
public class Client {

    private CommentService commentService;

    public Client(CommentService commentService) {
        this.commentService = commentService;
    }

    public void writeComment(String comment) {
        commentService.addComment(comment);
    }
}

public class App {
    private static boolean enabledSpamFilter = true;

    private static boolean enabledTrimming = true;

    public static void main(String[] args) {
        CommentService commentService = new DefaultCommentService();
        System.out.println(commentService.getClass());
        if (enabledSpamFilter) {
            commentService = new SpamFilteringCommentDecorator(commentService);
        }
        System.out.println(commentService.getClass());
        if (enabledTrimming) {
            commentService = new TrimmingCommentDecorator(commentService);
        }
        System.out.println(commentService.getClass());
        Client client = new Client(commentService);
        client.writeComment("오징어게임");
        client.writeComment("보는게 하는거 보다 재밌을 수가 없지...");
        client.writeComment("http://whiteship.me");
    }
}
```
</div>
</details>
<br>


### 실제 사용되는 케이스 
- 자바 
  - InputStream, OutputStream, Reader, Writer의 생성자를 활용한 랩퍼
  ```java
      // IO
      // FileInputStream 기준으로 보면 adapter 패턴일수 있지만
      // 관점과 사용에 따라 Decorator 패턴으로도 볼수 있다.
      try(InputStream is = new FileInputStream("input.txt");
          // InputStreamReader로 InputStream을 감싸고(=decorating)
          InputStreamReader isr = new InputStreamReader(is);
          // BufferedReader로 InputStreamReader을 감싸고(=decorating)
          BufferedReader reader = new BufferedReader(isr)){
          while(reader.ready()){
              System.out.println(reader.readLine());
          }
      } catch (IOException e) {
          throw new RuntimeException(e);
      }
  ```
  - java.util.Collections가 제공하는 메소드들 활용한 랩퍼
  ```java
    // Collections가 제공하는 데코레이터 메소드
    ArrayList list = new ArrayList<>();
    list.add(new Book());

    // 부가적인 기능을 추가해서 변환해주는 메소드
    // checked~ 타입을 확인하여 반환
    List books = Collections.checkedList(list, Book.class);
    books.add(new Book());
    // checkedList로 생성되어, Book 외의 타입을 받을 수 없도록 세팅
    // books.add(new Item()); // 오류발생

    List unmodifiableList = Collections.unmodifiableList(list);
    // unmodifiableList 리스트는 더이상 변경이 어렵기 때문에 추가하는 것도 오류가 난다.
    // unmodifiableList.add(new Item()); // 오류발생
    // unmodifiableList.add(new Book()); // 오류발생


    // synchronized - Collection, List, Map, Set 등 변형 가능
    Collections.synchronized*()
  ```
  - javax.servlet.http.HttpServletRequest/ResponseWrapper
  ```java
        // 서블릿 요청 또는 응답 랩퍼
        // HTTP Request를 확장해서 처리할 수 있다. 
        // cache, logging 등을 filter에 세팅하는 것들 할 수있음
        HttpServletRequestWrapper requestWrapper;
        HttpServletResponseWrapper responseWrapper;

  ```

- Spring 
  - ServerHttpRequestDecorator
  ```java
    // 빈 설정 데코레이터
    BeanDefinitionDecorator decorator;

    // 웹플럭스 HTTP 요청/응답 데코레이터
    // 웹플럭스 쓰는 사람이면 쓸수 있음.
    // ServerHttpRequest, Response를 커스트 마이징
    // React의 웹필터(WebFilter)를 상속받아서 만든다음에 ServerHttpRequest로 감싼다.
    // 본문을 logging, 의심스러운 요청 처리 않는다거나 wrapper 기능으로 처리 한다.
    ServerHttpRequestDecorator httpRequestDecorator;
    ServerHttpResponseDecorator httpResponseDecorator;
  ```
