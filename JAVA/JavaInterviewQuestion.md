## 메모리 상수풀 영역 
> JVM의 메모리 영역 중 힙 영역안에 Literal과 같은 사우들이 저장되는 곳
```
Method Area (Static Area)

JVM이 구동될 때 생성되며 모든 스레드가 공유하는 영역.
JVM 구동 중 사용될 클래스 파일을 읽고 클래스 별로 
runtime constant pool(런타임 상수 풀), 
file data(필드 데이터), 
method data(메서드 데이터), 
constructor(생성자) 등을 저장



1. 필드 정보 : 멤버변수 이름, 데이터 타입, 접근 제어자 등의 정보

2. 메서드 정보 : 메서드 이름, 리턴타입, 매개변수, 접근제어자 등의 정보

3. 상수 풀(String Constant Pool)

- Type에서 사용된 상수를 저장하는 곳(중복이 있을 시 기존의 상수 사용)

- 문자 상수, 타입, 필드, Method reference도 상수 풀에 저장

- final class 변수의 경우에도 상수 풀에 값 복사

4. Static 변수

- 모든 객체가 공유 할 수 있고, 객체 생성없이 접근 가능

5. 프로그램 종료까지 메모리에 상주

6. Static 영역 데이터는 프로그램 시작 ~ 종료 시까지 메모리에 남아있다.

- 장점 : 즉 프로그램이 종료될 까지 어디서든 사용 가능

- 단점 : 무분별하게 많이 사용하다 보면 메모리가 부족


```


## JDBC (Java Database Connectivity)
> 자바에서 데이터베이스를 사용하기 위해 필요한 API로 해당 DB를 사용할 수 있는 방법 제공

## 접근지정자 
> 보안때문에, 각 변수 메소드 등을 접근하는데 제한을 두어야 한다. 
> e.g. 비밀번호 혹은 객인정보등 getter를 외부에서 접근하지 못하도록 막는다
> private << default << protected << public 

## Spring과 Spring Boot의 차이점
> Spring Boot는 보다 Spring을 편리하게 사용하게 하기위해 starter를 통해서 dependency를 선언만 해주면 바로 쓸수 있도록 해놓았다. 그리고 tomcat등을 내장하여 별도 웹서버를 설치하지 않고 구동할 수 있다. 
> xml을 별도 구현하지 않아도 된다. (Bean 등록하거나 web.xml, server-context.xml 선언등)

## POJO(Plain Old Java Object)
> "POJO"라는 용어는 주요 Java 오브젝트 모델, 컨벤션 또는 프레임워크를 따르지 않는 Java 오브젝트를 의미합니다. 즉, 라이브러리나 프레임워크로부터 강제받지 않는 객체라는 것이다. 
  
**POJO의 제한**
- 미리 지정된 클래스를 extends 하는 것.
- 미리 정의된 인터페이스를 implement 하는 것.
- 미리 정의된 Annotation을 포함하는 것.

대표적인 예 
- 하이버네이트 
- 스프링 
  > 오브젝트 컨테이너를 제공해서, 인스턴스들의 라이프 사이클을 관리하고, OOP를 더 OOP답게 쓸 수 있게 해주는 AOP 기술을 적용해서 POJO 개발을 더 쉽게 만들어줍니다.


## 스프링 IoC 
### 왜 Ioc 처리를 할까요? 
> 객체를 관리해주는 독립적인 존재 (거시적으로는 컨테이너, 미시적으로는 디지인패턴적용)와 그 외 내가 구현 하고자 하는 부분으로 각각 관심을 분리하고 서로에 역활을 충실히하면서 변경에 유연한 코드를 작성 할 수 있는 구조이기 때문에 제어를 역전 하였다. 저는 이렇게 생각하였습니다




- https://jobjava00.github.io/language/java/basic/collections/