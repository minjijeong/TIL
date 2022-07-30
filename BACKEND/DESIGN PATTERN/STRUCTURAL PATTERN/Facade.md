## 퍼사드 패턴 Facade 
> 복잡한 서브 시스템 의존성을 최소화 하는 방법
>  - 서브 시스템 : 라이브러리, 프레임워크
>  - 서브 시스템의 의존성을 직접 만드는 것이 아니라, 중간에 복잡한 서브시스템의 특정 기능등을 압축 시켜서 나머지는 퍼사드 뒤에 숨기는 형태 
> 퍼사드 : 불어로 입구쪽을 바라보는 전경을 말함 
>  - 건물 외벽만 알수 있고, 실제 건물 내와 구조는 알 수 없음

역할별로 메소드 분리하는 형식으로 Facade 뒤로 기능을 감추는 것이 코드의 양 및 로직상의 흐름으로 보았을때는 
크게 효과적이지 않아보일 수 있다. 하지만 *퍼사드 패턴*을 이용하면, 기술 및 특정 라이브러리에 종속되지 않고 사용할 수 있도록 한다.

### 장점 
- 서브 시스템에 대한 의존성을 한곳으로 모을 수 있다. 

### 단점 
- 퍼사드 클래스가 서브 시스템에 대한 모든 의존성을 가지게 된다.
 



<details>
<summary> [문제] 자바 이메일 전송  </summary>
<div markdown="1">
이슈사항 
- properties를 세팅하고, Session 만들고 , MimeMessage 까지 라이브러리 파악해서 생성 하는 등
  기술에 대해 cliend가 순차적으로 처리해야할 것들이 많다. 
- 기술 및 라이브러리를 몰라도, 기능별 재구성하여 노출시키서 back에서 처리하는 것이 Facade 같다!

```java
public class Client {
    public static void main(String[] args) {
        String from = "test@kakao.com";
        String to = "test@gmail.com";
        String host = "smtp.kakao.com";
        String port = "465";

        Properties properties = System.getProperties();
        properties.setProperty("mail.smtp.host", host);
        properties.setProperty("mail.smtp.port", port);

        Session session = Session.getDefaultInstance(properties);

        MimeMessage message = new MimeMessage(session);
        try {
            message.setFrom(new InternetAddress(from));
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));
            message.setSubject("Test Mail from Java Program");
            message.setText("message");

            Transport.send(message);
        } catch (MessagingException e) {
            throw new RuntimeException(e);
        }
    }
}
```
</div>
</details>
<br>


<details>
<summary> [해결방안] 의존성을 낮추기 위해, 세팅, 메세지, 전송으로 역할별 클래스 분리 </summary>
<div markdown="1">

**기본 모델 세팅**
```java
@Data
public class EmailSettings {
    private String host;
    private String port;
}

@Data
public class EmailMessage {
    private String to;
    private String from;
    private String subject;
    private String text;
}
```
**이메일 전송처리**
```java
public class EmailSender {
    EmailSettings emailSettings;

    public EmailSender(EmailSettings emailSettings){
        this.emailSettings = emailSettings;
    }

    public void sendEmail(EmailMessage emailMessage){
        Properties properties = System.getProperties();
        properties.setProperty("mail.smtp.host", emailSettings.getHost());
        properties.setProperty("mail.smtp.port", emailSettings.getPort());

        // 메일 서버 세팅
        Session session = Session.getDefaultInstance(properties);

        try {
            // 메일 메세지 세팅
            MimeMessage message = new MimeMessage(session);
            message.setFrom(new InternetAddress(emailMessage.getFrom()));
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(emailMessage.getTo()));
            message.setSubject(emailMessage.getSubject());
            message.setText(emailMessage.getText());

            // 메일 전송
            Transport.send(message);
        } catch (MessagingException e) {
            throw new RuntimeException(e);
        }
    }
}
```

**이메일 전송처리 클라이언트 영역**
```java
public class Client {
    public static void main(String[] args) {
        String from = "test@kakao.com";
        String to = "test@gmail.com";
        String host = "smtp.kakao.com";
        String port = "465";

        // 이메일 세팅
        EmailSettings emailSettings = new EmailSettings();
        emailSettings.setHost(host);
        emailSettings.setPort(port);

        EmailSender emailSender = new EmailSender(emailSettings);

        // 이메일 내용 세팅
        EmailMessage emailMessage = new EmailMessage();
        emailMessage.setFrom(from);
        emailMessage.setTo(to);
        emailMessage.setSubject("Test Mail from Java Program");
        emailMessage.setText("이메일 입니다!!! \n");
        // 이메일 전송
        emailSender.sendEmail(emailMessage);
    }
}
```
</div>
</details>
<br>


### 실제 사용되는 케이스 
- Spring 
  - Spring MVC 
  - 스프링이 제공하는 대부분의 기술 독립적인 인터페이스와 그 구현체
```java
    // client는 mailsender 인터페이스지만, 실질적으로 사용하는 것은 JavaMailSenderImpl
    // 특정 기술에 종속되지 않는다.
    MailSender mailSender = new JavaMailSenderImpl();

    // jpa, jdbc, hibernate 각각 구현체를 가지고 사용 가능
    PlatformTransactionManager platformTransactionManager = new JdbcTransactionManager();

    // Spring5 이전에는 spring webmvc - servelt 이었다!
    // spring5 이후에는 servelt 아닐수 있다, mono, flux 등 다른 것을 사용할 수 있다.
    // spring mvc 일종의 facade로 볼수 있다. 기술의 독립적인 인터페이스를 쓰도록 설계된다.
```