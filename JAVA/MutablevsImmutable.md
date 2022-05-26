## mutable 
``` 
형용사
1.	격식
변할 수 있는; 잘 변하는
```

## immutable 
``` 
형용사
1.	격식
변경할 수 없는, 불변의
```
  
| immutable                                          | mutable                                        |
| -------------------------------------------------- | ---------------------------------------------- |
| 생성된 이후 수정 불가능                            | 생성된 이후 수정 가능                          |
| 값 변경시, 기존객체가 아닌 새로운 객체 생성,재할당 | 값 변경시, 이미 존재하는 객체에 재할당         |
| Getter/Setter 미존재(변경가능 메소드 미존재)       | Getter/Setter 존재(변경가능 메소드)            |
| Thread Safe                                        | Thread Unsafe (병렬처리 시, 값 보장할 수 없음) |
| Legacy, Wapper, String classes 등                  | StringBuilder, StringBuffer, java.util.Date 등 |

mutable을 immutable 하게 변경하려면? 
- Setter 메소드 제거 (값을 변경하는 메소드 제거)
- final class 선언 - final 메소드, 클래스로 선언 시 해당영역 수정을 막을 수 있다. 
- 모든 클래스 변수를 private & final로 선언 - 이렇게 되면 private로 변수에 직접 접근을 막고 final로 해당 변수를 수정할 수 없기 때문에 setter 메소드 또한 생성될 수 없다. 

``` java
package com.javabasic;

import java.util.ArrayList;
import java.util.List;

public class ImmutableTest{

    public static class Name{
        /**
         * private final로 선언하여 setter 메소드를 작동할 수 없도록 처리
         */
        private final String name;
        public Name(String name){
            this.name = name;
        }

//        public void setName(String name) {
//            this.name = name;
//        }

        @Override
        public String toString() {
            return "Name{" +
                    "name='" + name + '\'' +
                    '}';
        }
    }
    public static class Names{
        /**
         * names를 final로 선언해도 그 하위 모든 객체를 immutable로 만들지 않으면 mutable 하게 작동한다.
         */
        private final List<Name> names;
        public Names(List<Name> names){
            this.names = new ArrayList<>(names);
        }

        @Override
        public String toString() {
            return "Names{" +
                    "names=" + names +
                    '}';
        }
    }

    public static void main(String[] args) {
        Name crew1 = new Name("Fafi");
        Name crew2 = new Name("Kevin");

        List<Name> originalNames = new ArrayList<>();
        originalNames.add(crew1);
        originalNames.add(crew2);

        Names crewNames = new Names(originalNames);
        System.out.println(originalNames);
        System.out.println(crewNames);
//        crew2.setName("Sally"); // [오류] setName 성립불가능하므로
        System.out.println(originalNames);
        System.out.println(crewNames);
    }
}

```

```
[Name{name='Fafi'}, Name{name='Kevin'}]
Names{names=[Name{name='Fafi'}, Name{name='Kevin'}]}
[Name{name='Fafi'}, Name{name='Kevin'}]
Names{names=[Name{name='Fafi'}, Name{name='Kevin'}]}
```


## String vs StringBuffer vs StringBuilder 

### String 
  
> primitive, Wapper class 형태가 존재
> 원시변수로 사용시 Heap 영역의 String 상수(Constant) 영역에 보관되어 동일하게 선언된 것은 같은것을 가르킨다.
> Wapper class는 객체를 생성하여 할당할때 마다 각기 다른 객체를 heap에 생성한다. 
  
### StringBuffer vs StringBuilder
  
> StringBuffer는 Synchronized key가 있어 동기화를 보장하여 멀티쓰레드에서 사용가능하나, StringBuilder는 동기화를 보장하지 않아 단일쓰레드에서 사용가능하다 
> 성능측면에서는 StringBuilder가 속도가 더 높다. 

  
**JDK1.5 이후 원시변수로 +연산 (concat)을 하여도 내부적으로 StringBuilder로 컴파일되기 때문에 속도적인 측면은 크게 차이 없다. 다만, 반복루프에서 사용시 계속 객체를 신규를 생성되는 것은 동일하다.** 


**연산이 많다고 가정할때, 성능적인 측면 StringBuilder >> StringBuffer >> String 순으로 사용할 수 있다.**


```java
package com.javabasic;

public class StringTest {
    public static void main(String[] args) {
        String str1 = "abc";
        String str2 = new String("abc");
        String str3 = "abc";
        String str4 = new String("abc");

        /**
         * str1, str3 는 같은 문자열을 선언하여 Heap 내의 constant poll 동일한 데이타를 가르킨다.
         * str2, str4 각각 객체를 선언하였기 때문에 서로 다른 객체이다.
         */
        System.out.println(System.identityHashCode(str1)); // 1078694789
        System.out.println(System.identityHashCode(str2)); // 1831932724
        System.out.println(System.identityHashCode(str3)); // 1078694789
        System.out.println(System.identityHashCode(str4)); // 1747585824
        System.out.println("-----------------------");
        String str5 = "abcdef";
        String str6 = str1 + "def";
        str1 = str1 + "def"; // str1의 "abc"는 GC 대상이 되고 "abc"+"def" 된 새로운 값을 가르킨다.
        /**
         * str1에 "def"를 추가하여도 str5와 같은 메모리를 가르키지 않는다.
         */
        System.out.println(System.identityHashCode(str1)); // 1023892928
        System.out.println(System.identityHashCode(str5)); // 558638686
        System.out.println(System.identityHashCode(str6)); // 1149319664

        System.out.println("-----------------------");
        /**
         * String - immutable, 객체 수정 안됨, 멀티쓰레드 safe
         * StringBuffer - mutable, 객체 수정 가능, 멀티쓰레드 safe, 동기화가능
         * StringBuilder - mutable, 객체 수정 가능, 단일쓰레드만 가능
         *
         * 연산이 많은 성능 측면 String Builder >> StringBuffer >> String
         */
        StringBuffer buffer = new StringBuffer();
        buffer.append("adc");
        System.out.println(System.identityHashCode(buffer)); // 2093631819
        buffer.append("def");
        System.out.println(System.identityHashCode(buffer)); // 2093631819

        StringBuilder builder = new StringBuilder();
        builder.append("adc");
        System.out.println(System.identityHashCode(builder)); // 2074407503
        builder.append("def");
        System.out.println(System.identityHashCode(builder)); // 2074407503
    }
}

```