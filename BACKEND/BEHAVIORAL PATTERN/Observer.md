## 옵저버 패턴 Observer
> 다수의 객체가 특정 객체 상태 변화를 감지하고 알림을 받는 패턴 
- 발행(publish) - 구독(subscribe) 패턴을 구현 
  - subject 주요한 기능 - 여러 옵저버를 등록하거나 해지
  - subject 상태가 변경이 되면, 모든 옵저버를 순회하면서 특정한 메소드를 호출 

### 장/단점 
#### 장점
- 상태를 변경하는 객체(publisher)와 변경을 감지하는 객체(subscriber)의 관계를 느슨하게 유지할 수 있다. 
  - 관계가 느슨하면, 코드 작성, 변경, 테스트, 재사용하기 쉽다
- subject의 상태 변경을 주기적으로 조회하지 않고 자동으로 감지할 수 있다.
  - chatServer에서 주기적으로 polling하지 않아도 받을 수 있다.  
- 런타임에 옵저버를 추가하거나 제거할 수 있다. 
  - 런타임 도중에 등록하거나 해제할 수 있다. 
#### 단점
- 복잡도가 증가한다. 
- 다순의 Observer객체를 등록 이후 해지 않는다면 memory leak이 발생할 수 있다. 
  - 적절한 순간에 객체를 해지하는 것이 제일 좋다
  - WeakReference를 이용하여 HashMap에 쓰면, 사용을 하지 않으면 HashMap을 해지한다. (명시적으로 어려운 경우만!!!)
    - 언제 없어지는것을 알수 없어서...시점에 주의해야한다.

<details>
<summary> [문제] -   </summary>
<div markdown="1">

- User 
  - sendMessage - 채팅서버 메세지 등록
  - getMessage - 채팅서버 메세지 출력

- ChatServer 
  - add : 주제 별 메세지 등록
  - getMessage : 메세지 조회
  
```java
public class User {
    private ChatServer chatServer;
    public User(ChatServer chatServer){
        this.chatServer = chatServer;
    }

    public void sendMessage(String subject, String message){
        chatServer.add(subject, message);
    }

    public List<String> getMessage(String subject){
        return chatServer.getMessage(subject);
    }
}


public class ChatServer {
    private Map<String, List<String>> messages;

    public ChatServer(){
        this.messages = new HashMap<>();
    }

    public void add(String subject, String message){
        if(messages.containsKey(subject)){
            messages.get(subject).add(message);
        }else{
            List<String> messageList = new ArrayList<>();
            messageList.add(message);
            messages.put(subject, messageList);
        }
    }

    public List<String> getMessage(String subject){ return messages.get(subject);}
}
```

```java
public class Client {
    public static void main(String[] args) {
        ChatServer chatServer = new ChatServer();

        User user1 = new User(chatServer);
        user1.sendMessage("디자인패턴","이번엔 옵저버 패턴입니다.");
        user1.sendMessage("알고리즘", "자료구조부터 하세요!");

        User user2 = new User(chatServer);
        System.out.println(user2.getMessage("디자인패턴"));

        user1.sendMessage("디자인패턴", "예제 코드 보는 중...");
        System.out.println(user2.getMessage("디자인패턴"));
    }
}
```

</div>
</details>
<br>


<details>
<summary> [수정] 옵저버 패턴 적용 </summary>
<div markdown="1">

**Subscriber(Observer)** : Observe 할 행위에 대한 정의를 위해 인터페이스 사용
```java
public interface Subscriber {
    void handleMessage(String message);
}
```

**ChatServer** : subscriber 기준으로 등록/삭제, 메세지 전송처리  
```java
public class User implements Subscriber{
    private String name;
    public User(String name){
        this.name = name;
    }

    public String getName() {
        return name;
    }

    @Override
    public void handleMessage(String message) {
        System.out.println(message);
    }
}

public class ChatServer {
    private Map<String, List<Subscriber>> subscribers = new HashMap<>();

    public void register(String subject, Subscriber subscriber){
        if(subscribers.containsKey(subject)){
            subscribers.get(subject).add(subscriber);
        }else{
            List<Subscriber> list = new ArrayList<>();
            list.add(subscriber);
            this.subscribers.put(subject,list);
        }
    }

    public void unregister(String subject, Subscriber subscriber){
        if(subscribers.containsKey(subject)){
            subscribers.get(subject).remove(subscriber);
        }
    }

    public void sendMessage(User user, String subject, String message){
        if(this.subscribers.containsKey(subject)){
            String userMessage = user.getName() + " : " + message;
            this.subscribers.get(subject).forEach(s-> s.handleMessage(userMessage));
        }
    }
}

```
</div>
</details>
<br>




### 실제 사용되는 케이스 
- 자바 
  - JAva8 이하 Observer<I>, Observable<C> 상태 변경에 따라 알림 전달 
    ```java
    public class ObserverInJava {
        static class User implements Observer {// Java9 사용 권장하지 않음
            @Override
            public void update(Observable o, Object arg) {
                System.out.println(arg);
            }
        }

        static class Subject extends Observable{
            // Java9 사용 권장하지 않음
            // - JavaBeans 패키지- PropertyChangeListener, , Java
            public void add(String message){
                setChanged(); // 상태가 변경 되어지야만 notifyObserver 실행가능, 여러번 notify 안됨
                notifyObservers(message);
            }
        }

        public static void main(String[] args) {
            Subject subject = new Subject();
            User user = new User();
            subject.addObserver(user);
            subject.add("Hello Java, Observer");
        }
    }
    ```
  - Java9 - Flow API
    ```java
    /**
    * Flow
    * - Publisher
    * - Subscriber
    * - Subscription
    * - Processor = Publisher + Subscriber
    */
    public class FlowInJava {
        public static void main(String[] args) {
            /**
            * 동기적 publish
            */
            Flow.Publisher<String> publisher = new Flow.Publisher<String>(){
                @Override
                public void subscribe(Flow.Subscriber<? super String> subscriber){
                    subscriber.onNext("hello flow"); // 메세지 전송
                    subscriber.onComplate();
                }
            };
            /**
            * 비동기적 publishing
            */
    //        Flow.Publisher<String> publisher = new SubmissionPublisher<>();

            Flow.Subscriber<String> subscriber = new Flow.Subscriber<String>(){
                @Override
                public void onSubscribe(Flow.Subscription subscription){
                }

                @Override
                public void onNext(String item){
                    System.out.println(item);
                }

                @Override
                public void onError(Throwable throwable){
                }

                @Override
                public void onComplate(){
                    System.out.println("complated");
                }
            };
            publisher.subscribe(subscriber);
        }
    }

    ```
  - java.beans.PropertyChange
    - 사용하지 않는 Observer 해지 등이 이루어 지지 않으면 GC 대상이 아니기 때문에 메모리 누수가 발생할 수 있다.
    ```java
    public class PropertyChangeExample {
        static class User implements PropertyChangeListener{
            @Override
            public void propertyChange(PropertyChangeEvent evt) {
                System.out.println(evt.getPropertyName());
                System.out.println(evt.getNewValue());
            }
        }

        static class Subject{
            PropertyChangeSupport support = new PropertyChangeSupport(this);

            // 등록할때 이벤트 property 줄수 있음
            public void addObserver(PropertyChangeListener observer){
                support.addPropertyChangeListener(observer);
            }

            // 등록된 observer 삭제
            public void removeObserver(PropertyChangeListener observer){
                support.removePropertyChangeListener(observer);
            }

            // firePropertyIndexed 이런걸로 했을때, 우선순위 지정할 수 있다.
            public void add(String message){
                support.firePropertyChange("eventName", null, message);
            }
        }

        public static void main(String[] args) {
            Subject subject = new Subject();
            User observer = new User();
            subject.addObserver(observer);
            subject.add("자바 PCL 예제 코드");
            subject.removeObserver(observer);
            subject.add("이 메세지는 볼수 없지.");
        }
    }
    ```

- Spring 
```java
```
