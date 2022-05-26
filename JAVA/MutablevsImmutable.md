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
  
| immutable | mutable |
| ----- | ----- |
| 생성된 이후 수정 불가능 | 생성된 이후 수정 가능 |
| 값 변경시, 기존객체가 아닌 새로운 객체 생성,재할당 | 값 변경시, 이미 존재하는 객체에 재할당 |
| Getter/Setter 미존재(변경가능 메소드 미존재) | Getter/Setter 존재(변경가능 메소드) |
| Thread Safe | Thread Unsafe (병렬처리 시, 값 보장할 수 없음) |
| Legacy, Wapper, String classes 등 | StringBuilder, StringBuffer, java.util.Date 등 |

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