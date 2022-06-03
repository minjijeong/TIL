## JAVA 
> 자바의 Write Once Run Anywhere(WORA)의 개념을 도입하였습니다. 
> JVM이라는 가상머신을 통해 자바 소스를 컴파일하며 기본 시스템이 이해하는 언어로 해석(interpreter)합니다. 
   
- 자바 기초 참고 서적
    
    [언제나 휴일](http://ehpub.co.kr/category/programming-language/java-%ec%9e%85%eb%ac%b8/)
    
    [점프 투 파이썬](https://wikidocs.net/book/31)
   
------------------------

## 1. 자바의 특징


- 객체 지향적이다 (Object-oriented) 
    ``` 
    숫자 또는 논리 값등을 제외하고 객체로 이루어져 있다. 또한 대부분의 클래스가 Object 클래스를 super-class로 가진다. 이를 통해, 쉽게 재사용이 가능하며 유연성이 높다. 또한 코드의 집접 접근을 막을 수 있어 안전성을 높일 수 있다.
    ```

- 인터프리터 언어이다 (Interpreted)
    ```
    JVM에서 인터프리터를 포함하고 있으며 .class 파일의 바이트 코드 형태로 보관하며, 해당 소스를 컴파일러가 해석하여 실행한다. 
    ```

- 강력하다(robust)
    ```
    자바는 모든 메모리에 대한 접근을 GC를 통해 직접 관리 및 제한하여 메모리 관리 미숙으로 인한 프로그램 오류는 없다. 
    특히 GC는 사용이 끝난 리소스를 할당 해제하여 메모리를 효율적으로 자동적으로 쓸수 있도록한다.
    ```

- 안전하다(Secured)
    ```
    Java는 명시적 포인터(explicit pointer)가 존재하지 않고 클래스의 엑세스를 정희하는 보안 관리자가 있다.
    ```

- **플랫폼 독립적이다 (Platform independent)**
    ```
    class파일은 이진코드(바이트코드)로 구성되었으며, 이는 JRE가 설치된 모든 시스템에서 자바 프로그램을 실행할 수 있다.
    ```
    ![javaIndependent](Java%20Images/javaIndependent.png)

- **멀티 쓰레딩을 지원한다 (Multithreaded)**

- 동적이다(Dynamic)
    ```
    하나의 모듈을 갱신할때 다른 모듈을 모두 갱신할 필요가 없다. 수정 된 class 파일만 컴파일하여 교체도 가능하다
    ```
   


## 2. OPP 캡슐화
> 관련이 있는 데이터 그리고 동작들을 하나로 묶어 요약하고 사용자에게는 내부적인 접근을 허용하지 않는 대신에 사용의 편의성을 제공
[[Java] 4. OOP - 캡슐화](http://ehpub.co.kr/java-4-oop-%ec%ba%a1%ec%8a%90%ed%99%94/)

![IMG_C400B4FBBDED-1.jpeg](Java%20Images/IMG_C400B4FBBDED-1.jpeg)
   

### 1. class의 정의 
> 접근지정자에 의해 클래스 내부의 접근을 제어할 수 있고, 이를 통해 개체의 정보를 은닉하여 신뢰성을 높일수 있다.
   

### **2. 정적(static) 변수와 메소드** 
> static 정적 변수 및 메소드는 별도의 메모리에 할당되어 해당 클래스가 생성될 때 같이 생성된다. 
__static 으로 설정하면 같은 곳의 메모리 주소만을 바라보기 때문에 static 변수의 값을 공유하게 되는 것이다.__
(메모리 할당 관련해서는 JVM 작동원리 부분을 참고)

*※ [객체명.정적 변수명] or [클래스명.정적 변수명]으로 정적 변수에 접근 가능. 일반적으로는 [클래스명.정적 변수명]을 많이 사용*

```java
    public class Unit {
        // 클래스 생성시 static 변수는 Method Area(Static Area) 메모리에 할당
        static int last_seq;
        // 지역변수는 JVM내의 Heap영역에 메모리 할당을 받고 객체와 함께 생성
        int seq; 
        public Unit(){
            last_seq++;
            seq = last_seq;
        }

        public int getSeq(){
            return seq;
        } 
        // 정적 메소드 또한 Method Area 메모리 할당
        public static int getLastSeq(){
            return last_seq;
        }
    }

    public class Program {
        public static void main(String[] args){
            // 객체가 미생성 되었을 때 int의 default 값은 0이다.
            System.out.println("Unit의 가장 최근에 부여한 Seq No:"+Unit.getLastSeq());
            Unit u1 = new Unit();
            System.out.println("Unit의 가장 최근에 부여한 Seq No:"+Unit.getLastSeq());
            Unit u2 = new Unit();
            System.out.println("Unit의 가장 최근에 부여한 Seq No:"+Unit.getLastSeq());
            // 객체 인스턴스가 생성 후 생성자를 통해 static 변수 값이 세팅 된다.
            // 객체 u2 인스턴스가 생성되었을 때는 이미 last_seq = 1로 세팅되어 있어 생성자를 통해 seq=2로 세팅된다.
            System.out.println("u1의 Seq No:"+u1.getSeq());
            System.out.println("u2의 Seq No:"+u2.getSeq());
        }
    }

    /**
    Unit의 가장 최근에 부여한 Seq No:0
    Unit의 가장 최근에 부여한 Seq No:1
    Unit의 가장 최근에 부여한 Seq No:2
    u1의 Seq No:1
    u2의 Seq No:2
    */
```

static으로 선언된 메소드는 객체 생성없이 클래스를 통해 메서드를 직접 호출할 수 있다.

> ※ static 메소드 안에서는 객체 변수(static으로 선언되지 않은 필드) 또는 non-static 메소드에 접근이 불가능 하다. 
또한, this 키워드를 통한 non-static 변수를 호출할 수 없다.
> 

정적(static) 메소드는 공유라는 이점이 있기 때문에 Util과 같이 어디서든 접근하여 공통으로 쓸수 있는 클래스 내에 메소드에서 활용 가능하다.

*** 주의할 점은 static 변수, 메소드는 프로그램이 종료 시 메모리 삭제가 이루어지기 때문에 과다 사용시 메모리 초과, 속도 저하 등의 이슈가 발생할 수있다.* 

*※  정적변수와 동일하게  [클래스명.정적 메소드]로 사용가능*

```java
import java.text.SimpleDateFormat;
import java.util.Date;

// [공통유틸] 
class Util {
    // 현재일자 조회
    public static String getCurrentDate(String fmt) {
        SimpleDateFormat sdf = new SimpleDateFormat(fmt);
        return sdf.format(new Date());
    }
}

public class Sample {
    public static void main(String[] args) {
        System.out.println(Util.getCurrentDate("yyyyMMdd"));  // 오늘 날짜 출력
    }
}
```

[[Java] java static 멤버와 static 메서드 - Heee's Development Blog](https://gmlwjd9405.github.io/2018/08/04/java-static.html)

[Java | Static 정적 변수 & 정적 메소드](https://pathas.tistory.com/124)

## 접근지정자

[자바 접근 지정자(Java Access Modifier)](https://madplay.github.io/post/java-access-modifier)

| 종류 | 대상 | 내용 | 비교 |
| --- | --- | --- | --- |
| Public | 클래스, 멤버 | 모든 외부에서 접근 가능 | public 멤버를 제외하고 다른 패키지에서 접근 절대 불가 |
| Protected | 멤버 | 같은패키지 내에 상속한 경우 가능 | 클래스에 적용 불가능 |
| Default | 클래스, 멤버 | 같은 패키지 내에 속한 경우 가능 | 접근 지정자를 생략한 경우 |
| Private | 멤버 | 오직 같은 클래스 내부에서만 접근 가능 | 클래스에 적용 불가능 |

## 오버로딩(Overloading) - 중복 정의

입력 매개변수가 다르면, 같은 이름으로 여러개의 메소드를 정의할 수 있다.

생성자 선언시, 매개변수를 다르게 하여 해당 클래스의 생성자를 다르게 정의할 수 있다. 

```java
public class OverloadingExams{
	boolean result;
  int fieldCnt;
	public OverloadingExams(){
			OverloadingExams(fieldCnt);	
  }
  public OverloadingExams(int cnt){
		
	}

}
```

# 자료형

## 숫자

| 자료형 | 표현범위 |
| --- | --- |
| int | -2147483648 ~ 2147483647 |
| long | -9223372036854775808 ~ 9223372036854775807 |
| float | -3.4*10^38 ~ 3.4*10^38 |
| double | -1.7*10^308~1.7*10^308 |

### 정수 - int, long

```java
int age = 10;
long countOfStar = 8764827384923849L;
```

long 타입 정수의 경우 접미사 ‘L’을 붙여주어야한다. 누락시 컴파일 에러가 발생한다.

### 실수 - float, double

```java
float pi = 3.14f;
double morePi = 3.14159265358979323846
double d1 = 123.4;
double d2 = 1.234e2;
```

float형 변수 또한 ‘f’라는 접미사를 붙여주어야 한다. 

double형 변수에서 10^2 = e2로 표기 가능하다. (공학용 계산에서 해당 연산의 표기를 볼수 있다)

### 8진수, 16진수

8진수 : ‘0’으로 시작  

```java
int octal = 023; // 10진수 19 
//'0' 8진수의 시작 의미 
// 2*8, 3*1
int hex = 0xC // 10진수 12
// '0x' 16진수의 시작 의미
// C = 12
```

### 연산

### 산술연산자

- 나누기 연산(/)
    
    ```java
    int i = 4; 
    int j = 3;
    
    re = i / j;
    System.out.println("1. re:"+re);
    
    float f = 3.0f;
    float re2 = 0.0f;
    
    re2 = i/f;
    System.out.println("2. re:"+re);
    re2 = i/0.0f;
    System.out.println("3. re:"+re);
    re2 = -i/0.0f;
    System.out.println("4. re:"+re);
    re2 = 0.0f/0.0f;
    System.out.println("5. re:"+re);
    
    /**
    1. re : 1
    2. re2 : 1.3333334
    3. re2 : Infinity
    4. re2 : -Infinity
    5. re2 : NaN // 0/0은 수학적으로도 해가 없으므로 NaN로 표기
    */
    ```
    
- 나머지 연산
    
    ```java
    int a = 7 % 3; // 7을 3으로 나눈 나머지는 1
    int b = 3 % 7; // 3을 7로 나눈 나머지는 3 
    ```
    
- 증감연산
    
    변수의 값의 전 후에 추가 하여 해당 변수의 값을 증감처리한다. 
    
    ```java
    int i=0; 
    // 값이 참조된 후에 증가
    System.out.println(i++); // 0으로 출력
    System.out.println(i); // 1로 출력
    System.out.println(i--); // 1으로 출력
    System.out.println(i); // 0로 출력
    
    // 값이 참조되기 전에 증가
    System.out.println(++i); // 1으로 출력
    System.out.println(i); // 1로 출력
    System.out.println(--i); // 0으로 출력
    System.out.println(i); // 0로 출력
    ```
    
   
### 쉬프트연산자

\>> 빈자리를 부호비트 1로 채움

\>>> 빈자리를 부호비트 0으로 채움
   
      
## 문자열
   
   
### 원시(Primitive)자료형

- 리터럴 표기(literal)
    
    > String a = “happy java”;
    String a = new String(”happy java”);
    > 
    
    이 두개는 같은 값을 가지게 되지만 완전히 동일하지 않다. 첫번째 방식이 리터럴 표기라고 하는데 객체 생성없이 고정된 값을 그대로 대입하는 방법을 말한다. 
    
    “happy java” 문자열을 리터럴표기법에서는 intern pool이라는 곳에 저장하고 다음에 다시 동일한 문자열이 선언될때는 cache 된 문자열을 리턴한다. 
    
    두번째 방식은 항상 새로운 String 객체 만든다. 
    

int, long, double, float, boolean, char 등을 자바의 원시 자료형(Primitive) 라고 한다. 이런 primitive 자료형은 new 키워드로 그 값을 생성할 수 없다. 

** String은 리터럴로 표기가 가능하지만, primitive 자료형은 아니다. String은 리터럴 표현식을 사용할 수 있는 예외 경우이다.

원시 자료형은 래퍼(Wrapper) 클래스가 존재한다. 

![Java Wrapper Class](../JAVA/Java%20Images/Untitled%201.png)

[[Java] 래퍼 클래스(Wrapper Class)란 무엇인가? (박싱, 언박싱)](https://coding-factory.tistory.com/547)

- 추가 확인 - Wrapper Class
    
    앞으로 공부할 ArrayList, HashMap, HashSet 등은 데이터 저장시 원시 자료형 대신 그에 대응하는 Wrapper 클래스들을 사용해야 한다. 원시 자료형 대신 Wrapper 클래스를 사용하면 값 대신 객체를 주고 받을 수 있어 코드를 객체 중심적으로 작성하는데 유리하다. 또한 멀티스레딩 환경에서 동기화를 지원하기 위해서는 Wrapper 클래스가 반드시 필요하다.
    


### 문자열 내장 메소드

- equals
- indexOf - 단어의 위치 찾기
- contains - 단어 포함여부 확인
- charAt - 특정 위치 문자(char) 리턴
- replaceAll - 특정 문자열을 다른 문자열로 치환
- substring - 문자 자르기
- toUpperCase - 대문자로 변경
- split - 문자열을 특정 구분자로 분리
   


### 문자열 포매팅

Stirng.format 안에 각 대상 타입을 넣고 값을 나중에 추가 할수 있따. 

```java
/* 숫자 대입 */ 
System.out.println(String.format(”I eat %d apples.”,3));

/* 문자열 대입 */
 System.out.println(String.format(”I eat %s apples.”,five”));

/* 복합 대입 */
 System.out.println(String.format(”I eat %s apples, so I was sick for %d days”,five”,6));

// "Error is 98%." 출력
System.out.println(String.format("Error is %d%%.", 98));  

/* 정렬과 공백 */
System.out.println(String.format("%10s", "hi"));
//앞의 예문에서 %10s는 전체 길이가 10개인 문자열 공간에서 대입되는 값을 오른쪽으로 정렬하고 그 앞의 나머지는 공백으로 남겨 두라는 의미이다. 

//반대쪽인 왼쪽 정렬은 %-10s 
System.out.println(String.format("%-10sjane.", "hi"));  // "hi        jane." 출력

// 소수점 노출 
System.out.println(String.format("%10.4f", 3.42134234));

// String.format 없이도 printf를 사용하면 출력 가능
System.out.printf("I eat %d apples.", 3);  // "I eat 3 apples." 출력
```

![printf 코드](Java%20Images/Untitled%202.png)

## StringBuffer

문자열을 추가하거나 변경 할 때 주로 사용하는 자료형이다. 

- append - 문자열 붙이기
    
    Stringbuffer를 사용하지 않고 각 String에 원시형 자료형 형태로 +를 이용하여 문자열을 추가할 경우, 자바는 ‘+’ 행위를 할때마다 새로운 String 객체를 생성한다. String 원시형 자료형은 값을 변경할수 없는 immutable한 형태를 가진다. 
    
    trim, toUpperCase 등의 메소드는 문자열을 변경하는 것이 아니라 수행시 또 다른 String 객체를 생성하여 리턴하는 것이다. 
    
    But, StringBuffer는 String 자료형보다 무거운 편이다. 메모리 사용량도 많고 속도도 느리다. **문자열 변경 작업이 많은 경우에만 사용하도록 한다.** 
    
    ** StringBuilder는 StringBuffer보다 성능이 우수한 장점이 있으므로, 동기화를 고려할 필요가 없는 상황에서는 StringBuilder를 사용하는 것이 유리하다. 
    
- insert - 문자열 삽입
    
    특정 위치에 원하는 문자열을 삽입할 수 있다. 
    
- substring
    
    substring(시작,끝위치) - StringBuffer 객체의 시작위치에서 끝위치까지의 문자를 뽑는다.
    

## 배열(Array)

```java
int[] arr = new int[3]();
arr[0] = 1;
arr[1] = 2; 
arr[3] = 3; 

String[] arr2 = {"월","화","수","목"};
```

- 배열의 길이는 고정되어 있다.
- 배열에 값의 접근은 index를 이용해야 한다.
- 빈번한 배열의 오류 - 배열의 길이를 초과 하여 값을 불러오거나, 입력하려고 할때

## 리스트(List)

크기가 정해져 있지 않고, 동적으로 할당. 

### ArrayList

List 자료형에는 ArrayList, Vector, LinkedList 등의 List 인터페이스를 구현한 자료형이 있다.

> **제네릭스** 
자바5 이후 부터 ArrayList<String> pitches = new ArrayList<>(); 처럼 객체를 포함하는 자료형도 어떤 객체를 포함하는지에 대해서 명확하게 표현할 것을 권고하고 있다.  해당 자료형을 제네릭스라고 표기한다. <>
> 
- add
- get
- contains
- remove - 객체 or 인덱스를 통해 데이터 삭제 가능하다.

### 제네릭스(Generics)

자바5이후에 도입이 되었으며, 커스텀하여 생성도 가능하다.

제네릭스로 List에서 명확하게 하는 이유는 데이터를 넣을때는 상관없지만, 넣은 것을 가져올때 Object로 가져와서 모두 각각 자료형에 맞게 형변환(casting)을 해주어야 하기 때문이다. 

제네릭 사용의 장점
- 제네릭 클래스 타입의 객체를 생성할때 개발자가 원하는 타입을 지정할 수 있음.
- 타입 안정성을 제공.
- 의도하지 않은 타입의 객체가 저장되는 것을 막아 잘못 형변환 되는 오류를 방지.
- 형변환의 번거로움을 줄여줌. -> 간결한 코드 유지 가능

```java
    import java.util.ArrayList;
    import java.util.List;

    public class GenericTest {
        public static class Storage<T> {
            T item;

            public T getItem() {
                return item;
            }

            public void setItem(T item) {
                this.item = item;
            }
        }

        public static void main(String[] args){
            Storage<String> storage1 = new Storage<>();
            storage1.setItem("test!!!");

            Storage<Integer> storage2 = new Storage<>();
            storage2.setItem(128883991);

            System.out.println(storage1.getItem()); // test!!!
            System.out.println(storage2.getItem()); // 128883991

            GenericTest gt2 = new GenericTest();

            System.out.println(gt2.convert(storage1)); // [t, e, s, t, !, !, !]
            System.out.println(gt2.convert(storage2)); // [1, 2, 8, 8, 8, 3, 9, 9, 1]
        }

        // return type, 매개변수 모두 wildcard 사용됨. 어떠한 형태든 허용된다...?
        // 컴파일단에서는 exception 안나고 parsing 중 지원이 안되는 타입일때 오류 날듯.. 
        public <T> List<Character> convert(Storage<T> storage) {
            ArrayList<Character> list = new ArrayList<>();

            String s = String.valueOf(storage.getItem());
            int size = s.length();
            for (int i = 0; i < size; i++) {
                list.add(s.charAt(i));
            }
            return list;
        }
    }
```
    
- String.join
    
    ```java
    import java.util.ArrayList;
    import java.util.Arrays;
    
    public class Sample {
        public static void main(String[] args) {
            String[] data = {"138", "129", "142"};  // 이미 투구수 데이터 배열이 있다.
            ArrayList<String> pitches = new ArrayList<>(Arrays.asList(data));
    				
    				// String.join 없이 치환하여 붙이기
    				String result = "";
    				for(String pitche : pitches){
    					result += pitche;
    					result += ",";
    				}        
    				result = result.substring(0, result.length() -1); // 마지막 콤마 제거
    				System.out.println(result);  // 138,129,142 출력
    				
    				// String.join 사용 - ArrayList
    				result = String.join(",", pitches);
    				System.out.println(result);  // 138,129,142 출력
    
    				// String.join 사용 - Array
    				result = String.join(",", data);
    				System.out.println(result);  // 138,129,142 출력
    
        }
    }
    ```
    
- 리스트 정렬하기 (JAVA8 부터 사용가능)
    - 오름차순 - Comparator.naturalOrder
    - 내림차순 - Comparator.reverseOrder
    
    ```java
    import java.util.ArrayList;
    import java.util.Arrays;
    import java.util.Comparator;
    
    public class Sample {
        public static void main(String[] args) {
            String[] data = {"138", "129", "142"};  // 이미 투구수 데이터 배열이 있다.
            ArrayList<String> pitches = new ArrayList<>(Arrays.asList(data));
    				pitches.sort(Comparator.naturalOrder()); // 오름차순
    				pitches.sort(Comparator.reverseOrder()); // 내림차순
            System.out.println(pitches);  // [138, 129, 142] 출력
        }
    }
    ```
    

## 맵(Map)

key, value를 가지고 한쌍의 대응 관계를 가지는 자료형이다. 

- HashMap
    
    Map 역시 List와 마찬가지로 인터페이스이다. Map 인터페이스를 구현한 Map자료형에는 HashMap, LinkedHashMap, TreeMap등이 있다. 
    
    - put - key, value를 한쌍으로 데이타 입력
    - get - key에 해당하는 value 값을 가져올 수 있다.
        - getOrDefault - 해당 key값이 미존재 시 default 값을 호출한다.
    - containsKey - 키가 존재하는지 확인
    - remove - 해당하는 key값에 대한 value를 삭제 후, value return
    - size - 맵의 개수
    - keySet - 맵의 모든 key를 모아서 Set 자료형으로 리턴
    - LinkedHashMap과 TreeMap
        
        Map은 순서를 가질 수 없어 두개의 순서를 가진 Map을 사용할 수 있다. 
        
        - LinkedHashMap - 입력된 순서대로 데이터를 저장
        - TreeMap - key의 오름차순 순서로 데이터를 저장

## 집합(Set)

Set 자료형에는 HashSet, TreeSet, LinkedHashSet 등의 Set인터페이스를 구현한 자료형이 있다. 

- 중복을 허용하지 않는다.
- 순서가 없다(Unordered)
- 교집합, 합집합, 차집합
    
    ```java
    import java.util.Arrays;
    import java.util.HashSet;
    
    public class Sample {
        public static void main(String[] args) {
            HashSet<Integer> s1 = new HashSet<>(Arrays.asList(1, 2, 3, 4, 5, 6));
            HashSet<Integer> s2 = new HashSet<>(Arrays.asList(4, 5, 6, 7, 8, 9));
    
            //교집합
            HashSet<Integer> intersection = new HashSet<>(s1); // s1으로 intersection 생성
            intersection.retainAll(s2); // 교집합 수행 
            System.out.println(intersection); // [4,5,6] 출력 
            /**
             * s1 대신에 intersection을 별도로 선언한 이유는, 
             * retainAll은 원천 데이터를 수정하기 때문에 s1.retainAll을 할 경우 s1에 4,5,6만 남는다
             */
    
            //합집합
            HashSet<Integer> union = new HashSet<>(s1);;
            union.addAll(s2); // 합집합 수행
            System.out.println(intersection); // [1, 2, 3, 4, 5, 6, 7, 8, 9] 출력 
    
            //차집합
            HashSet<Integer> substract = new HashSet<>(s1);
            substract.removeAll(s2);
            System.out.println(intersection); // [1, 2, 3] 출력 
        }
    }
    ```
    
- 집합 자료형 관련 메소드
    - 값 추가하기(add)
    - 값 여러개 추가하기(addAll) -Array.asList or ArrayList 형태로 추가
    - 특정 값 제거하기(remove)
    
- TreeSet - 오름차순으로 값을 정렬하여 저장
- LinkedHashSet - 입력한 순서대로 값을 정렬하여 저장

## 상수집합(Enum)

서로 관련이 있는 여러 개의 상수 집합을 정의할 때 사용하는 자료형이다. 

```java
public class Sample {
    enum CoffeeType {
        AMERICANO,
        ICE_AMERICANO,
        CAFE_LATTE
    };

    public static void main(String[] args) {
        System.out.println(CoffeeType.AMERICANO);  // AMERICANO 출력
        System.out.println(CoffeeType.ICE_AMERICANO);  // ICE_AMERICANO 출력
        System.out.println(CoffeeType.CAFE_LATTE);  // CAFE_LATTE 출력

        for(CoffeeType type: CoffeeType.values()) {
            System.out.println(type);  // 순서대로 AMERICANO, ICE_AMERICANO, CAFE_LATTE 출력
        }
    }
}
```

- Enum 사용 이유
    - 매직넘버(1과 같은 숫자 상수값)를 사용할 때보다 코드가 명확해 진다.
    - 잘못된 값을 사용함으로 인해 발생할 수 있는 위험성이 사라진다.
    
    ** DB에 코드값으로 01, 02, 03 이런식으로 저장되어 있고 실제 기능에 대해서 정의서가 없어지면 알기 어려울 때가 있다. 이럴때 enum을 통해 의미를 좀더 명확하게 사용할 수 있다. Order(”01”), Void(”02”), Cancel(”03”) 
    

## 형변환과 final

자료형간의 타입을 변환하는 방법과 설정한 값을 변경하지 못하게 하는 방법

- 형변환
    
    String <=> Integer 
    
    Wrapper Class에 존재하는 메소드를 통해 형변환 할 수 있다.
    
    ```java
    public class Sample {
        public static void main(String[] args) {
            String num = "123";
            int n = Integer.parseInt(num);
            System.out.println(n);  // 123 출력
    
            String numStr = "" + n;
            System.out.println(numStr);  // 123 출력
    
            String num1 = String.valueOf(n);
            String num2 = Integer.toString(n);
            System.out.println(num1);  // 123 출력
            System.out.println(num2);  // 123 출력
    
            int n1 = 123;
            double d1 = n1;.  // 정수를 실수로 바꿀때에는 캐스팅이 필요없다.
            System.out.println(d1);  // 123.0 출력
    
            double d2 = 123.456;
            int n2 = (int) d2;. // 실수를 정수로 바꿀때에는 반드시 정수형으로 캐스팅해 주어야 한다.
            System.out.println(n2);  // 소숫점이 생략된 123 출력
    
            String num = "123.456";
            int n = Integer.parseInt(num);  // 실수 형태의 문자열을 정수로 변환할 경우 NumberFormatException이 발생한다.
        }
    }
    ```
    
- final
    
    자료형에 값을 단 한번만 설정할 수 있게 강제하는 키워드이다. 
    
    - Unmoifiable List
        
        final로 선언된 리스트는 더하거나 빼는 것은 가능하다. 
        완전 수정 불가능 하도록 하려면 List.of로 수정 불가능한 리스트를 생성해야 한다. 
        
        ```java
        import java.util.List;
        
        public class Sample {
            public static void main(String[] args) {
                final List<String> a = List.of("a", "b");
                a.add("c");  // UnsupportedOperationException 발생
            }
        }
        ```
        

- **자료형 연습문제**
    
    ```java
    package test.java;
    
    import com.sun.deploy.util.StringUtils;
    import java.util.ArrayList;
    import java.util.Arrays;
    import java.util.Comparator;
    import java.util.HashMap;
    import java.util.HashSet;
    import java.util.Map;
    import java.util.Set;
    
    public class WikidocsExam01 {
        public static void main(String [] args) {
            // 01. 평균구하기
            Map<String, Integer> scores = new HashMap<>();
            scores.put("국어",80);
            scores.put("영어",75);
            scores.put("수학",55);
    
            int tot = 0;
            int idx = 0;
            Set<String> keys = scores.keySet();
            for(String key: keys){
                tot += scores.get(key);
                idx++;
            }
            int avg = tot / scores.size();
            System.out.println("avg :: " + avg);
    
            // 02. 홀짝수 판별
            int a = 13;
            String check = "";
            if(2 % a == 0){
                check = "짝수";
            }
            else{
                check = "홀수";
            }
            System.out.println(" 확인 :: "+check);
    
            // 03. 주민번호 쪼개기
            String juminNumber = "881120-1068234";
            String[] jumin = juminNumber.split("-");
            for(String j : jumin) {
                System.out.println(j);
            }
            // 04. 주민번호 성별확인
            System.out.println(juminNumber.charAt(juminNumber.indexOf("-")+1));
    
            // 05. 문자열 치환
            String str = "a:b:c:d";
            System.out.println(str.replaceAll(":","#"));
    
            // 06. 리스트 정렬
            ArrayList<Integer> myList = new ArrayList<>(Arrays.asList(1, 3, 5, 4, 2));
            System.out.println(myList); // [1, 3, 5, 4, 2] 출력
            myList.sort(Comparator.reverseOrder());
            System.out.println(myList);
    
            // 07. 배열을 문자열로 출력
            ArrayList<String> myListStr = new ArrayList<>(Arrays.asList("Life", "is", "too", "short"));
            String myListString = String.join(" ",myListStr);
            System.out.println(myListStr + "::: "+ myListString );
    
            // 08.B 값 추출
            HashMap<String, Integer> grade = new HashMap<>();
            grade.put("A", 90);
            grade.put("B", 80);
            grade.put("C", 70);
    
            System.out.println(grade.remove("B"));
    
            // 09. 중복값 제거
            ArrayList<Integer> numbers = new ArrayList<>(Arrays.asList(1, 1, 1, 2, 2, 3, 3, 3, 4, 4, 5));
            System.out.println(numbers);  // [1, 1, 1, 2, 2, 3, 3, 3, 4, 4, 5] 출력
            Set<Integer> newNumbers = new HashSet<>(numbers);
            System.out.println(newNumbers);
    
            // 10.
            printCoffeePrice(CoffeType.Americano);  // "가격은 3000원 입니다." 출력
            printCoffeePrice(CoffeType.CafeLatte);  // NullPointerException 발생
        }
        enum CoffeType {
            Americano,
            IceAmericano,
            CafeLatte
        };
        static void printCoffeePrice(CoffeType type) {
            HashMap<CoffeType, Integer> priceMap = new HashMap<>();
            priceMap.put(CoffeType.Americano, 3000);  // 1: 아메리카노
            priceMap.put(CoffeType.IceAmericano, 4000);  // 2: 아이스 아메리카노
            priceMap.put(CoffeType.CafeLatte, 5000);  // 3: 카페라떼
    
            int price = priceMap.get(type);
            System.out.println(String.format("가격은 %d원 입니다.", price));
        }
    }
    ```
    

# 객체지향 프로그래밍

- 객체지향 프로그래밍이란
    
    1개의 계산기 클래스를 같이 사용하면서 연속으로 진행되는 연산의 값이 반영되는것을 확인할수 있다. 
    
    ```java
    class Calculator {
        static int result = 0; 
    
        static int add(int num) {
            result += num;
            return result;
        }
    }
    
    public class Sample {
        public static void main(String[] args) {
            System.out.println(Calculator.add(3));
            System.out.println(Calculator.add(4));
        }
    }
    
    //출력 
    // 3
    // 7
    ```
    
    계산기를 static으로 구현하지 않으면 서로의 값에 영향을 주지 않고, 객체를 따로 따로 생성하여 계산기 2대로 사용할 수 있다. 
    
    ```java
    class Calculator {
        int result = 0;
    
        int add(int num) {
            result += num;
            return result;
        }
    }
    
    public class Sample {
        public static void main(String[] args) {
            Calculator cal1 = new Calculator();  // 계산기1 객체를 생성한다.
            Calculator cal2 = new Calculator();  // 계산기2 객체를 생성한다.
    
            System.out.println(cal1.add(3));
            System.out.println(cal1.add(4));
    
            System.out.println(cal2.add(3));
            System.out.println(cal2.add(7));
        }
    }
    ```
    
    객체를 사용하면 각 계산기의 값들이 독립적으로 유지가되며,  계산기 대수가 늘어나더라도 객체를 생성만 하면되기 때문에 클래스를 더 만들필요가 없어진다. 
    
<br>
##### - 클래스
  
  **객체 지향적 (Object Oriented)이라는 말의 의미도 곱씹어 보면 결국 이 객체 변수의 값이 독립적으로 유지되기 때문에 가능한 것이다.** 
    
```java
    class Animal {
    	String name; // 객체 변수 
    	
    	// 객체 메소드 
    	public void setName(String name){
    		this.name = name;
    	}
    }
    
    public class Sample {
        public static void main(String[] args) {
            Animal cat = new Animal(); // new 키워드를 통해 객체 생성한다.
    				cat.setName("brawn"); // 객체 메소드를 통해 객체 변수 값 할당
    				System.out.println(cat.name); // 객체: cat, 객체 변수 : name
        }
    }
```
    
Animal 클래스의 인스턴스인 cat, 즉 애니몰 클래스 객체가 생성되었다. 
-  객체와 인스턴스
    Animal cat = new Animal() 에서 cat은 객체이다. 이는 Animal의 인스턴스이다. 인스턴스는 특정 객체(cat)가 어떤 클래스(Animal)의 객체인지를 관계위로 설명할때 사용된다.
<br>

- 객체변수(Instance variable)
인스턴스 변수, 멤버 변수, 속성이라 한다. 
객체가 다르다면, 객체 변수의 값은 공유되지 않는다.
객체.객체변수  사용 
ex) [cat.name](http://cat.name)
<br>

- 메소드    
    클래스 내에 구현된 함수를 의미,  객체 값에 접근하기 위해 메소드를 사용
    객체.메소드  사용
<br>

- 찾아봐야할것 - this
<br>

- 메소드
    클래스 내의 함수 = 메소드
    같은 행위를 반복할 때, 메소드로 생성하여 동일한 행위를 처리한다. 
    
    ```java
    public class Sample {
    		// 일반적인 메소드 - 매개변수, 리턴값 존재 
        int sum(int a, int b) {  // a, b 는 매개변수(=인수, 인자)
            return a+b; // 리턴값(=결과값, 반환값)
        }
    		
    		// 입력값이 없는 메소드 
    		String say(){
    			return "Hello";
    		}
    
    		// 리턴값이 없는 메소드 
    		void sum(int a, int b){
    			System.out.println("결과의 합:"+(a+b));
    		}
    
    		// 입력값, 리턴값이 없는 메소드 
    		String say(){
    				System.out.println("Empty~");
    			return;
    		}
    
        public static void main(String[] args) {
            Sample sample = new Sample();
            int c = sample.sum(3, 4);  // 3, 4는 인수
            System.out.println(c);  // 7 출력
    
    				String say = sample.say();
            System.out.println(say);  // Hello 출력
    		
            System.out.println(sample.sum(3,4));  // 결과의 합: 7 출력
    				sample.say(); // Empty~ 출력
        }
    }
    ```
    
    ```java
    public class Sample {
        void sayNick(String nick) {
            if ("fool".equals(nick)) {
                return; // 메소드에서 빠져나가야 할때, return 사용
            }
            System.out.println("나의 별명은 "+nick+" 입니다.");
        }
    		
    		// 메소드 내에서만 쓰이는 로컬변수를 전달하기 위해서는 return 으로 반환해주어야 한다. 
    		int varTest(int a) {
            a++; // 로컬변수
            return a;
        }
    
    		
    		// 클래스 멤버 변수와 객체 전달한 경우 
    		int a;  // 객체변수 a
        void varTest(Sample sample) {
    		void varTest(){	
            sample.a++; // 객체를 다시 반환해주지 않기 때문에, 해당 값은 main에서 적용x
    				this.a++; // 클래스 멤버 변수(전역변수)의 값을 직접 수정할 수 있다. 
        }
    
        public static void main(String[] args) {
            Sample sample = new Sample();
            sample.sayNick("angel");
            sample.sayNick("fool");  // 출력되지 않는다.
    
    				int a = 1;
    				a = sample.varTest(a);
            System.out.println(a); // 2 출력
    
    				sample.varTest(sample); // 어디에도 영향이 없다..ㅎ
    
    				
        }
    }
    ```
    
- Call by Value
    
    **Call by Value(메소드에 원시 자료형 값 전달) vs Call by reference(메소드에 객체를 전달)** 
    
    - Call by Value - 값을 복사하여 새로운 지역변수에 저장
    
    - Call by reference - 애초에 '참조 타입'인 이유가 Heap Memory 영역에 생성된 객체의 주소값을 참조하기 때문에 객체를 넘기면 해당 객체의 값이 변경된다.
<br>

- 상속 (Inheritance)    
    자식 클래스가 부모의 클래스의 기능을 그대로 물려받을 수 있는 상속(Inheritance) 기능이 있다. 상속 받을 때는 extends라는 키워드를 사용한다. 
    
    - IS-A관계
        
        ```java
        class Animal {
                String name;
        
                void setName(String name) {
                    this.name = name;
                }
            }
        
            class Dog extends Animal {
        				void sleep(){
        						System.out.println(this.name+" zzzz");
        				}
            }
        
            public class Sample {
                public static void main(String[] args) {
                    Dog dog = new Dog();
                    dog.setName("poppy");
                    System.out.println(dog.name);  // poppy 출력
                    dog.sleep(); // poppy zzz 출력
                    
        						// IS-A관계를 이용한 상속관계에 의해서 부모자료형에 자식자료형 객체를 생성할 수 있다. 
                    Animal dog = new Dog();
                    
                    // 기본적으로 모든 클래스는 Object 클래스를 상속하기 때문에 IS-A관계가 성립힌다. 
                    Object cat = new Animal();
                    
                }
            }
        ```

<br>

  - 메소드 오버라이팅 ( Method Overriding)  
    HouseDog클래스에 Dog의 sleep이랑 동일한 형태 메소드 재구현하면 HouseDog.sleep()이 우선순위를 가진다. 
    
    **부모클래스의 메소드를 자식클래스가 동일한 형태로 또다시 구현하는 행위를 메소드 오버라이딩(Method Overriding)이라고 한다.** 
        
    ```java
        class Aninal{
        	String name;
        	
        	void setName(String name){
        			this.name = name;
        	}
        }
        
        class Dog extends Animal{
        	void sleep(){ // 애니몰의 자식클래스인 Dog 클래스에서 메소드 생성
        			System.out.println(this.name + " zzz");
        	}
        }
        
        class HouseDog extends Dog{ // Dog클래스가 부모인, 자식클래스 HouseDog
        	void sleep(){ // 동일한 메소드를 재정의하여 메소드 오버라이딩 구현
        			System.out.println(this.name + " zzz in house");
        	}
        }
        
        public class Sample {
        	public static void main(String[] args){
        			HouseDog houseDog = new HouseDog();
        			houseDog.setName("happy");
        			houseDog.sleep(); // happy zzz in house 출력 
        }
    ```
        
  - 메소드 오버로딩( Method Overloading)
        
    **동일한 이름의 메소드를 추가할 수 있다. 단, 메소드의 입력항목(=매개변수)의 자료형이 다른 경우만 가능하다. 이를 메소드 오버로딩(Method Overloading)이라 부른다.** 
        
    ```java
    class Aninal{
        String name;
        
        void setName(String name){
                this.name = name;
        }
    }
    
    class Dog extends Animal{
        void sleep(){ // 애니몰의 자식클래스인 Dog 클래스에서 메소드 생성
                System.out.println(this.name + " zzz");
        }
    
        void sleep(int hour){
                System.out.println(this.name + " zzz in house for "+hour + " hours");
        }
    }
    
    class HouseDog extends Dog{ // Dog클래스가 부모인, 자식클래스 HouseDog
        void sleep(){ // 동일한 메소드를 재정의하여 메소드 오버라이딩 구현
                System.out.println(this.name + " zzz in house");
        }
    }
    
    public class Sample {
        public static void main(String[] args){
                HouseDog houseDog = new HouseDog();
                houseDog.setName("happy");
                houseDog.sleep(); // happy zzz in house 출력 
                houseDog.sleep(3); // happy zzz in house for 3 hours 출력
    }
    ```
        
- 다중상속
자바는 다중상속을 지원하지 않는다. 
<br>        
    
- 생성자 (Constructor)
객체를 생성할 당시 처리하고자 하는 것들을 선언하고 프로그래밍할 수 있다. 
생성자가 매개변수를 가질 때는 객체를 생성할 때 동일하게 매개변수를 부여해야 한다. 
<br>

- 생성자 생성 규칙
    1. 클래스명과 메소드명이 동일하다. 
    2. 리턴타입을 정의하지 않는다.(void도 사용하지 않는다) 
<br>    
  
- 디폴트(default) 생성자     
    컴파일러는 클래스내에 생성자를 별도로 생성하지 않으면, 임의의 디폴트 생성자를 자동으로 구현한다. 
        
    ```java
    class Dog extends Animal{
            // 임의로 생성되는 Default 생성자
            // 코드에 직접 노출되지는 않지만, 컴파일러가 임의로 생성한다. 
            Dog() {
            }
    
            void sleep(){
                    System.out.println(this.name + " zzz");
            }
    }
    ```
        
- 생성자 오버로딩
        
    하나의 클래스에 여러개의 다른 생성자를 구현하여 오버로딩하도록 만들수 있다. 
        
    ```java
    class Aninal{
        String name;
        
        void setName(String name){
                this.name = name;
        }
    }
    
    class Dog extends Animal{
        void sleep(){ // 애니몰의 자식클래스인 Dog 클래스에서 메소드 생성
                System.out.println(this.name + " zzz");
        }
    }
    
    class HouseDog extends Dog{ // Dog클래스가 부모인, 자식클래스 HouseDog
        // 하우스도그의 생성자, 매개변수 강아지 이름
        HouseDog(String name){
                this.setName(name); // 객체 생성시, 별도로 호출하지 않아도 된다. 
        }
        
        // 하우스도그 또 다른 생성자, int형 매개변수 추가
        HouseDog(int type){
                if ( type = 1){
                        this.setName("yorkshire");
                }
                else if( type == 2) {
                        this.setName("bulldog");
                }
        }
    
        void sleep(){ // 동일한 메소드를 재정의하여 메소드 오버라이딩 구현
                System.out.println(this.name + " zzz in house");
        }
    }
    
    public class Sample {
        public static void main(String[] args){
                HouseDog happy = new HouseDog("happy"); // 1번 생성자 
                HouseDog yorkshire = new HouseDog(1); // 2번 생성자
    
                System.out.println(happy.name ); // happy 출력 
                System.out.println(yorkshire.name ); // yorkshire 출력
    }
    ```
        
    
- 인터페이스
    
    ```java
    interface Predator {
    		String getFood(); // 구현체 메소드 
    }
    
    class Animal {
        String name;
    
        void setName(String name) {
            this.name = name;
        }
    }
    
    class Tiger extends Animal implements Predator {
    		// 강제적으로 인터페이스에 구현되어 있는 메소드이므로 구현해야 한다. 
    		public String getFood(){ 
    				return "apple";
    		}
    }
    
    class Lion extends Animal implements Predator {
    		// 강제적으로 인터페이스에 구현되어 있는 메소드이므로 구현해야 한다. 
    		public String getFood(){ 
    				return "banana";
    		}  
    }
    
    class ZooKeeper {
    		// 변경전 각 자식클래스를 객체로 하여 따로 따로 구현
        void feed(Tiger tiger) {
            System.out.println("feed apple");
        }
        void feed(Lion lion) {
            System.out.println("feed banana");
        }
    		// 변경 후 인터페이스를 이용한 구현
        void feed(Predator predator) {
            System.out.println("feed "+predator.getFood());
        }
    }
    ```
    
    | 물리적세계 | 자바 세계 |
    | --- | --- |
    | 컴퓨터 | ZooKeeper(구현체) |
    | USB 포트 | Predator(인터페이스) |
    | 하드디스크, 디지털카메라,.. | Tiger, Ling, Crocodile.. (객체) |
    
    Predator 인터페이스 대신 Animal 클래스(부모클래스)에 getFood 메소드를 추가하고 Tiger, Lion 등 에서 getFood 메소드를 오버라이딩한 후 Zookeeper의 feed메소드가 Predator 대신 Animal을 입력 자료형으로 사용해도 동일한 효과를 거둘수 있다. 
    
    하지만, 상속은 자식 클래스가 부모 클래스의 메소드를 오버라이딩하지 않고 사용할 수 있기 때문에 해당 **메소드를 반드시 구현해야 한다는 “강제성"을 갖지 못한다.** 상황에 맞게 상속을 사용할 것인지 인터페이스를 사용해야 할 지를 결정해야 겠지만 인터페이스는 인터페이스의 메소드를 반드시 구현해야 하는 “강제성”을 갖는다는 점 기억하자.
    
    - 디폴트 메소드
        
        Java8 부터 사용 가능한 메소드. 인터페이스의 메소드는 몸통(구현체)을 가질 수 없지만 디폴트 메소드를 사용하면 실제 구현된 형태의 메소드를 가질 수 있다. 
        
        ```java
        interface Predator{
        		String getFood();
        
        		default void printFood() { // 별도의 구현없이 사용가능, 또한 오버라이딩도 가능!
        				System.out.printf("my food is %s\n, getFood());
        		}
        }
        ```
        
    - 스태틱 메소드
        
        Java8 버젼부터 사용가능 한 메소드로, 인터페이스명.스태틱메소드명 과 같이 사용하면 일반 클래스의 스태틱메소드를 사용하는 것과 동일하게 사용할 수 있다. 
        
        ** 인터페이스에서 정의한 상수는 public static final을 생략하여도 자동으로 적용되며, 수정불가능하다. 
        
        ```java
        interface Predator{
        		String getFood();
        
        		default void printFood() { // 별도의 구현없이 사용가능, 또한 오버라이딩도 가능!
        				System.out.printf("my food is %s\n, getFood());
        		}
        		
        		int LEG_COUNT = 4; 
        
        		static int speed(){
        				return LEG_COUNT * 30;
        		}
        }
        
        public class Sample {
        	public static void main(String[] args){
        			System.out.println(Predator.speed()); // 120 출력
        	}
        }
        ```
        
    

## **다형성**
    
폴리모피즘, Polymorphism 다형성은 하나의 객체가 여러개의 자료형 타입을 가질 수 있는 것
*자식클래스는 부모클래스, 부모인터페이스 자료형으로 자유롭게 변환 가능*
    
```java
Tiger tiger = new Tiger(); 
Animal animal = new Tiger(); // 부모클래스 자료형에 자식 클래스 객체 생성
Predator predator = new Tiger(); // 부모인터페이스 자료형에 자식 클래스 객체 생성
Barkable barkable = new Tiger(); // 부모인터페이스 자료형에 자식 클래스 객체 생성
```
    
  - 다형성 예시
      
      ```java
      interface Predator{
      		String getFood();
      
      		default void printFood() { // 별도의 구현없이 사용가능, 또한 오버라이딩도 가능!
      				System.out.printf("my food is %s\n", getFood());
      		}
      		
      		int LEG_COUNT = 4; 
      
      		static int speed(){
      				return LEG_COUNT * 30;
      		}
      }
      
      public interface Barkable {
          void bark();
      }
      
      // 인터페이스는 다중 상속 가능하다. 
      // 동물로써 먹이 주는 것과, speed를 구현한 것 + 짖는 기능 합쳐 놓은 인터페이스
      public interface BarkablePredator extends Predator, Barkable{
      }
      
      class Animal {
          String name;
      
          void setName(String name) {
              this.name = name;
          }
      }
      
      class Tiger extends Animal implements BarkablePredator {
      		// 강제적으로 인터페이스에 구현되어 있는 메소드이므로 구현해야 한다. 
      		public String getFood(){ 
      				return "apple";
      		}
      
      		public void bark(){
      			System.out.println("어흥");
      		}
      }
      
      class Lion extends Animal implements BarkablePredator {
      		// 강제적으로 인터페이스에 구현되어 있는 메소드이므로 구현해야 한다. 
      		public String getFood(){ 
      				return "banana";
      		}
      
      		public void bark(){
      			System.out.println("으르렁");
      		}
      
      }
      
      class ZooKeeper {
         void feed(Predator predator) {
              System.out.println("feed "+predator.getFood());
          }
      }
      
      class Bouncer { // 짖는 기능만을 가진 interface
          void barkAnimal(Barkable animal) {
              animal.bark();
          }
      }
      
      public class Sample {
      	public static void main(String[] args){
      			Tiger tiger = new Tiger();
      			Lion lion = new Lion();
      
      			Bouncer bouncer = new Bouncer();
      			bouncer.barAnimal(tiger); // barkerable이 부모 이므로 사용 가능
      			bouncer.barAnimal(lion); // barkerable이 부모 이므로 사용 가능
      	}
      }
      ```
        
    
##추상클래스
    
인터페이스의 역할도 하면서 클래스의 기능도 가지고 있는 클래스

- class 앞의 키워드 abstract를 붙여야 한다.
- 인터페이스 역할을 하는 메소드에도 abstract를 붙여야 한다.
- 인터페이스와 달리, static, final 상수들도 해당 키워드를 붙여야 변경불가능하다.

> **인터스페이스와 추상 클래스의 차이**
자바8버젼부터 인터페이스에 default 메소드가 추가되어 추상 클래스와의 차이점이 살짝 모호해졌다. 하지만 추상 클래스는 인터페이스와는 달리 일반 클래스처럼 객체변수, 생성자, private 메소드 등을 가질 수 있다.

  - 예시
      
      ```java
      interface Predator{
      abstract class Predator extends Animal{
      		**abstract** String getFood(); // 추상클래스로 변경되며 메소드도 abstract으로 정의
      
      		default void printFood() { // 추상클래스 이므로 default 키워드 삭제 
      				System.out.printf("my food is %s\n", getFood());
      		}
      		
      		**static** int LEG_COUNT = 4; // static 추가 선언
      
      		static int speed(){
      				return LEG_COUNT * 30;
      		}
      }
      
      public interface Barkable {
          void bark();
      }
      
      // 인터페이스는 다중 상속 가능하다. 
      // 동물로써 먹이 주는 것과, speed를 구현한 것 + 짖는 기능 합쳐 놓은 인터페이스
      // **Predator를 추상클래스로 변경하며 해당 인터페이스 사용할 수 없다.** 
      public interface BarkablePredator extends Predator, Barkable{
      }
      
      class Animal {
          String name;
      
          void setName(String name) {
              this.name = name;
          }
      }
      
      class Tiger extends Animal implements BarkablePredator {
      class Tiger extends Predator implements Barkable {
      		// Animal을 상속받은 추상클래스 Predator를 상속 받고 
      		// 짖는 기능은 Barkable 인터페이스를 통해 정의한다. 
      		public String getFood(){ 
      				return "apple";
      		}
      
      		public void bark(){
      			System.out.println("어흥");
      		}
      }
      
      class Lion extends Animal implements BarkablePredator {
      class Lion extends Predator implements Barkable {
      		// Animal을 상속받은 추상클래스 Predator를 상속 받고 
      		// 짖는 기능은 Barkable 인터페이스를 통해 정의한다. 
      		public String getFood(){ 
      				return "banana";
      		}
      
      		public void bark(){
      			System.out.println("으르렁");
      		}
      
      }
      
      class ZooKeeper {
          void feed(Predator predator) {
              System.out.println("feed "+predator.getFood());
          }
      }
      
      class Bouncer { // 짖는 기능만을 가진 interface
          void barkAnimal(Barkable animal) {
              animal.bark();
          }
      }
      
      public class Sample {
      	public static void main(String[] args){
      			Tiger tiger = new Tiger();
      			Lion lion = new Lion();
      
      			Bouncer bouncer = new Bouncer();
      			bouncer.barAnimal(tiger); // barkerable이 부모 이므로 사용 가능
      			bouncer.barAnimal(lion); // barkerable이 부모 이므로 사용 가능
      	}
      }
      ```
        

# 자바의 날개달기

- 패키지
    - 클래스 분류가 용이하다.
    - 패키지가 다르다면 동일한 클래스명을 사용할 수 있다.
- 접근제어자
    
    접근 제어자를 통해 변수, 메서드, 성생자에 대한 접근 권한을 지정할 수 있다.
    
    종류는 public, private, protected, 아무것도 쓰지 않는 default 접근 제어자가 존재한다.
    
    [[JAVA]접근 제어자(Access modifier) 개념 및 구현](https://peemangit.tistory.com/394)
    
    | 접근자 | 클래스 내부 | 패키지 | 상속받은 클래스 | 이외의 영역 |
    | --- | --- | --- | --- | --- |
    | private | ○ | X | X | X |
    | defualt | ○ | ○ | X | X |
    | protected | ○ | ○ | ○ | X |
    | public | ○ | ○ | ○ | ○ |
- 정적 변수와 메소드
    - static 변수
        
        static 키워드를 붙이면 자바는 메모리 할당을 딱 한번만 하게 되어 메모리 사용에 이점이 있다. 
        
        **만약, 변경되지 않기를 바란다면 static 키워드 앞에 final이라는 키워드를 붙인다.**
        
        static을 사용하는 이유
        
        - 공유 : static으로 설정하면 같은 곳의 메모리 주소만을 바라보기 때문에 변수의 값을 공유한다.
<br>

    - static 메소드
        
        Counter.getCount()와 같이 객체 생성없이 메소드를 직접 호출할 수 있다. 
        
        계산, 날짜구하기 등 Util성 메소드인 경우 사용한다.  
<br>

    - 싱글톤 패턴(singleton pattern)
        
        1개의 객체만 생성할 수 있도록 한다. 
        
        ```java
        class Singleton{
            private static Singleton one;
            // 다른 클래스에서 생성자 직접 호출이 어렵다. 
            private Singleton(){
            }
            
            // 다른 클래스에서 기존 생성자를 직접호출하지 않고 getInstance를 통해 호출
            public static Singleton getInstance(){
                // Singleton 클래스에서 객체는 오직 1번만 생성 되도록 처리
                if(one == null){
                    one = new Singleton()
                }
                return one;
            }
        }
        
        public class Sample{
            public static void main(String[] args){
                    Singleton singleton1 = new Singleton.getInstance();
                    Singleton singleton2 = new Singleton.getInstance();
                    System.out.println(singleton1 == singleton2); // true 출력
                    // Singleton 클래스에서 객체는 1번만 생성 되어
                    // singleton2도 singleton1에서 만든 객체를 바라본다. 
                    // 이 예제는 Thread Safe 하지 않다. 
            }
        }
        ```
        
- 예외처리
    - 예외케이스
        
        Exception으로 인해 오류가 나는 케이스는 ... 엄청 다양하다 ^^
        
        - FileNotFoundException
            
            ```java
            BufferedReader br = new BufferedRead(new FileReader("없는파일"));
            br.readLine();
            br.close(); 
            
            ==============================================
            Exception in thread "main" java.io.FileNotFoundException: 나없는파일 (지정된 파일을 찾을 수 없습니다)
                at java.io.FileInputStream.open(Native Method)
                at java.io.FileInputStream.<init>(Unknown Source)
                at java.io.FileInputStream.<init>(Unknown Source)
                at java.io.FileReader.<init>(Unknown Source)
                ...
            ```
            
        - ArithmeticException
            
            ```java
            int c = 4 / 0;
            
            ==============================================
            Exception in thread "main" java.lang.ArithmeticException: / by zero
                at Test.main(Test.java:14)
            ```
            
        - ArrayIndexOutOfBoundsException
            
            ```java
            int[] a = {1, 2, 3};
            System.out.println(a[3]);
            
            ==============================================
            Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 3
                at Test.main(Test.java:17)
            ```
            
    - 예외 처리
        
        try-catch문에서 catch안에 Exception에 대한 처리를 만들 수 있다. 
        
        ```java
        int c;
        try{
            c = 4/0;
        }catch(ArithmeticException e){
            c = -1;
        }
    ```
        
- finally  
  어떤 예외가 발생하더라도 꼭 수행되어야 하는 부분이 있다면 finally 문으로 정의 
<br>

    ```java
    public class Sample{
        public void shouldBeRun(){
                System.out.println("ok thanks");
        }

        public static void main(String[] args){
            Sample sample = new Sample();
            int c;

            try{
                    c = 4/0;
            }catch(ArithmeticException e){
                    c = -1;
            }finally{ // 예외와 상관없이 무조건 수행된다. 
                    sample.shouldBeRun();
            }
        }
    }
        ```
        
- RuntimeException과 Exception
  - RuntimeException : 실행시 발생하는 예외
            
    ```java
    public class FoolException extends RuntimeException{
    }
    
    public class Sample{
        public void sayNick(String nick){
            if("fool".equals(nick)){
                throw  new FoolException();
            }
            System.out.println("당신의 별명은 "+nick+" 입니다.");
        }
        
        public static void main(String[] args){
            Sample sample = new Sample();
            sample.sayNick("fool");
            sample.sayNick("genious");
        }
    }
    
    =====================================
    Exception in thread "main" FoolException
        at Sample.sayNick(Sample.java:7)
        at Sample.main(Sample.java:14)
    ```
            
    - Exception : 컴파일 시 발생하는 예외
            
    ```java
    public class FoolException extends Exception{
    }
    
    public class Sample{
        public void sayNick(String nick){
            try {
                if ("fool".equals(nick)) {
                    throw new FoolException();
                }
                System.out.println("당신의 별명은 "+nick+" 입니다.");
            }catch (FoolException e){
                System.err.println("FoolException이 발생하였습니다.");
            }
        }
    
        public static void main(String[] args){
            Sample sample = new Sample();
            sample.sayNick("fool");
            sample.sayNick("genious");
        }
    }
    ```
            
  - 예외 던지기 (throws)
        
    Exception을 처리하는 level에 따라서 프로그램의 수행여부를 결정하기도 하고 트랜제션 처리와도 밀접한 관계를 가지고 있다. 

    ex) 데이타를 업데이트 하는데 주소지 1, 주소지2 업데이트 중 메소드단에 exception이 존재하였다면, 주소지1은 오류가 났지만 주소지2는 업데이트가 되어 잘못된 주소가 되었을 수 있다. 이런 경우는 주소지1, 주소지2 모두 동시에 rollback 혹은 업데이트가 되어야 한다. 그래서 main과 같이 메소드를 호출하는 쪽에서 exception 처리를 하는 것이 낫다. 
        
    ```java
    public class FoolException extends Exception{
    }
    
    public class Sample{
            // throw FoolException 처리하면, Exception은 상위단계로 보낼수 있다. 
            // 이것이 바로 예외를 뒤로 미루기라고도 한다. 
        public void sayNick(String nick) ***throw FoolException***{
            **try {**
                if ("fool".equals(nick)) {
                    throw new FoolException();
                }
                System.out.println("당신의 별명은 "+nick+" 입니다.");
            **}catch (FoolException e){
                System.err.println("FoolException이 발생하였습니다.");
            }**
        }
            
            // main에서 예외처리가 되면, sayNick fool에서 exception이 발생하여
            // genious는 처리 되지 않는다. 
        public static void main(String[] args){
            Sample sample = new Sample();
                    ***try{***
                sample.sayNick("fool");
                sample.sayNick("genious");
                    ***} catch(FoodException e){
                            System.err.println("FoolException이 발생하였습니다.");
                    }***
        }
    }
    ```
        
  - 트랜잭션 (Transaction)
        
    트랜잭션 : 하나의 작업 단위
<br>

    > **수도코드란?**
    수도코드( pseudocode)는 특정 프로그래밍 언어의 문법을 따라 씌여진 것이 아니라, 일반적인 언어로 코드를 흉내내어 알고리즘을 써놓은 코드를 말한다.  특정 언어로 프로그램을 작성하기 전에 알고리즘의 모델을 대략적으로 모델링하는데 쓰인다.
        
        
    ```java
    상품발송() {
        **try {**
            포장();
            영수증발행();
            발송();
        **}catch(예외) {
            모두취소();  // 하나라도 실패하면 모두 취소한다.
        }**
    }
    // 포장, 영수증발행, 발송에서 각각 예외처리가 되고 상품발송에 전체 취소가 없다면 
    // 각 단계에서 일괄적으로 처리 되지 않기 때문에 제각각 취소인데 포장되거 발송되거나 할수 있다. 
    포장() throws 예외 {
        try {
            ...
        }catch(예외) {
            포장취소();
        }
    }
    
    영수증발행() throws 예외 {
            try {
            ...
        }catch(예외) {
            영수증발행취소();
        }
    }
    
    발송() throws 예외 {
            try {
            ...
        }catch(예외) {
            발송취소();
        }
    }
    ```
        
- 쓰레드 (Thread)
    
    동작하고 있는 프로그램을 프로세스(Process)라고 한다. 1개 프로세스 = 1개의 일, 쓰레드를 이용하면 한 프로세스 내에서 2가지 이상의 일을 동시에 할 수 있다. 
    
    - 순서대로 실행되지 않고 동시에 실행된다.
    - 쓰레드 종료 전에 mian 메소드가 먼저 종료되었다.
    - 예시
        
        ```java
        public class Sample extends Thread {
            int seq;
        
            public Sample(int seq) {
                this.seq = seq;
            }
        
            public void run() {
                System.out.println(this.seq + " thread start.");  // 쓰레드 시작
                try {
                    Thread.sleep(1000);  // 1초 대기한다.
                } catch (Exception e) {
                }
                System.out.println(this.seq + " thread end.");  // 쓰레드 종료 
            }
        
            public static void main(String[] args) {
                for (int i = 0; i < 10; i++) {  // 총 10개의 쓰레드를 생성하여 실행한다.
                    Thread t = new Sample(i);
                    t.start(); // Thread 상속 받았으므로 Thread 내의 start 메소드
                }
                System.out.println("main end.");  // main 메소드 종료
            }
        }
        ```
        
    - **쓰레드가 종료되지 않았는데, 그 다음 로직을 수행하도록 하는 것이 있다면 join을 사용하여 완전 종료 후에 처리한다.**
    - Join
        
        쓰레드가 종료되기 전까지 main을 종료시키지 않는다. 
        
        - 예시
            
            ```java
            import java.util.ArrayList;
            
            public class Sample extends Thread {
                int seq;
                public Sample(int seq) {
                    this.seq = seq;
                }
            
                public void run() {
                    System.out.println(this.seq+" thread start.");
                    try {
                        Thread.sleep(1000);
                    }catch(Exception e) {
                    }
                    System.out.println(this.seq+" thread end.");
                }
            
                public static void main(String[] args) {
                    ArrayList<Thread> threads = new ArrayList<>();
                    for(int i=0; i<10; i++) {
                        Thread t = new Sample(i);
                        t.start();
                        threads.add(t);
                    }
            
                    for(int i=0; i<threads.size(); i++) {
                        Thread t = threads.get(i);
                        try {
                            t.join(); // t 쓰레드가 종료할 때까지 기다린다.
                        }catch(Exception e) {
                        }
                    }
                    System.out.println("main end.");
                }
            }
            ```
            
        
    - Runnable 인터페이스
        
        Thread를 상속받으면 다중 상속이 안되므로 Runnable 인테페이스를 구현한다. 
        
        - 예시
            
            ```java
            import java.util.ArrayList;
            
            public class Sample extends Thread {
            public class Sample implements Runnable {
                int seq;
                public Sample(int seq) {
                    this.seq = seq;
                }
            
                public void run() {
                    System.out.println(this.seq+" thread start.");
                    try {
                        Thread.sleep(1000);
                    }catch(Exception e) {
                    }
                    System.out.println(this.seq+" thread end.");
                }
            
                public static void main(String[] args) {
                    ArrayList<Thread> threads = new ArrayList<>();
                    for(int i=0; i<10; i++) {
                        Thread t = new Sample(i);
                        Thread t = new Thread(Sample(i)); 
            						// Sample이 Runnable의 구현체이므로 해당 객체를 넘겨주어야 한다.  
                        t.start();
                        threads.add(t);
                    }
            
                    for(int i=0; i<threads.size(); i++) {
                        Thread t = threads.get(i);
                        try {
                            t.join(); // t 쓰레드가 종료할 때까지 기다린다.
                        }catch(Exception e) {
                        }
                    }
                    System.out.println("main end.");
                }
            }
            ```
            

- 함수형 프로그래밍
    
    Java8 부터 함수형 프로그래밍을 지원하기 위해 람다와 스트림이 도입되었다. 
    
    람다와 스트림을 사용하는 이유는 작성하는 코드의 양이 줄어들고 읽기 쉬운 코드를 만드는데 유리하기 때문이다. 
    
    - 람다 (Lambda)
        
        익명함수(Anonymous functions)를 의미
        
        - 인터페이스의 메서드가 1개 이상이면 람다를 사용할수 없다.
        - @FunctionalInterface 어노테이션을 사용하면 인터페이스는 2개 이상의 메서드를 가질 수 없다.
        - 람다를 사용하여 메소드 구현없이 Integer::sum을 이용하여 구현
        - 함수형 프로그래밍을 위해 제공되는 BiFunction와 같은 인터페이스들을 사용하여 축약
        - 입출력이 모두 동일하여 BinaryOperator 사용 가능
        
        ```java
        @FunctionalInterface
        interface Calculator {
            int sum(int a, int b);
        }
        
        class MyCalculator implements  Calculator {
            public int sum(int a, int b) {
                return a+b;
            }
        }
        
        public class Sample {
            public static void main(String[] args) {
        				// 람다를 사용하여 MyCalculator 클래스를 별도로 선언하지 않을 수 있다. 
                MyCalculator mc = new MyCalculator();
        				// 1) 람다를 사용하여 메소드 구현없이 Integer::sum을 이용하여 구현할 수 있다. 
        				Calculator mc = (int a, int b) -> a +b;
        				// 2) 함수형 프로그래밍을 위해 제공되는 인터페이스들을 사용하여 축약
        				Calculator mc = Integer::sum;
        				// BiFunction
        				BiFunction<Integer,Integer,Integer> mc = (a,b) -> a+b;
        				// 3) 입출력이 모두 동일하여 BinaryOperator 사용 가능
        				BinaryOperator<Integer> mc = (a,b) -> a+b;
                int result = mc.sum(3, 4);
                System.out.println(result);  // 7 출력
            }
        }
        ```
        
    - 스트림(Stream)
        데이타가 물결처럼 흘러가면서 필터링 과정을 통해 여러번 변경되어 반환되기 때문에 Stream 이란 이름을 가지게 됨. 
<br>

        <스트림 API의 특징> 

        1. 스트림은 외부 반복을 통해 작업하는 컬렉션과는 달리 내부 반복(internal iteration)을 통해 작업을 수행합니다.
        2. 스트림은 재사용이 가능한 컬렉션과는 달리 단 한 번만 사용할 수 있습니다.
        3. 스트림은 원본 데이터를 변경하지 않습니다.
        4. 스트림의 연산은 필터-맵(filter-map) 기반의 API를 사용하여 지연(lazy) 연산을 통해 성능을 최적화합니다.
        5. 스트림은 parallelStream() 메소드를 통한 손쉬운 병렬 처리를 지원합니다. 
<br>

        <스트림 API의 동작 흐름>

        1. 스트림의 생성
        2. 스트림의 중개 연산 (스트림의 변환)
        3. 스트림의 최종 연산 (스트림의 사용)
<br>
          
        <스트림의 생성>
        스트림 API는 다음과 같은 다양한 데이터 소스에서 생성할 수 있습니다.

        1. 컬렉션
        2. 배열
        3. 가변 매개변수
        4. 지정된 범위의 연속된 정수
        5. 특정 타입의 난수들
        6. 람다 표현식
        7. 파일
        8. 빈 스트림
<br>

        <예시>
        1. 짝수만 포함하는 ArrayList 생성
        2. Set을 사용하여 중복을 제거
        3. Set을 다시 List로 변경
        4. 역순으로 정렬
        5. Integer 리스트를 정수 배열로 변환
   
     - 소스코드 (OLD)
        ```java
        public class Sample {
            public static void main(String[] args) {
                int[] data = {5, 6, 4, 2, 3, 1, 1, 2, 2, 4, 8};
        
                // 짝수만 포함하는 ArrayList 생성
                ArrayList<Integer> dataList = new ArrayList<>();
                for(int i=0; i<data.length; i++) {
                    if(data[i] % 2 == 0) {
                        dataList.add(data[i]);
                    }
                }
        
                // Set을 사용하여 중복을 제거
                HashSet<Integer> dataSet = new HashSet<>(dataList);
        
                // Set을 다시 List로 변경
                ArrayList<Integer> distinctList = new ArrayList<>(dataSet);
        
                // 역순으로 정렬
                distinctList.sort(Comparator.reverseOrder());
        
                // Integer 리스트를 정수 배열로 변환
                int[] result = new int[distinctList.size()];
                for(int i=0; i< distinctList.size(); i++) {
                    result[i] = distinctList.get(i);
                }
            }
        }
        ```
            
    - 소스코드 (NEW)          

        ```java
        import java.util.Arrays;
        import java.util.Comparator;
        
        public class Sample {
            public static void main(String[] args) {
                int[] data = {5, 6, 4, 2, 3, 1, 1, 2, 2, 4, 8};
                int[] result = Arrays.stream(data)  // IntStream을 생성한다.
                        .boxed()  // IntStream을 Stream<Integer>로 변경한다.
                        .filter((a) -> a % 2 == 0)  //  짝수만 걸러낸다.
                        .distinct()  // 중복을 제거한다.
                        .sorted(Comparator.reverseOrder())  // 역순으로 정렬한다.
                        .mapToInt(Integer::intValue)  // Stream<Integer>를 IntStream으로 변경한다.
                        .toArray()  // int[] 배열로 반환한다.
                        ;
            }
        }
        ```