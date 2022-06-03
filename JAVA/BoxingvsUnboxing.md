## Boxing vs Unboxing 
  

| Boxing | Unboxing |
| -- | -- |
| 기본형(primitive type) -> 참조형(reference type)  | Unboxing : 참조형(reference type) -> 기본형(primitive type) |    

  

```java
    Integer iA = new Integer(123);
    Integer iB = new Integer(123);
    int ia = (int)iA; //(1) 언박싱(unboxing)
    int ib = iB; //(2) 오토언박싱(auto unboxing)
    Integer iC = (Integer)456; //(3)박싱(boxing)
    Integer iD = ia; //(4)오토 박싱(auto boxing)
```

### *boxing and unboxing이 중요한 이유

### 1. 식별성
```java
    /**
    * iA == iB => false, ia == ib => true
    * Integer로 박싱이 되면, 각각의 객체는 서로 다르기때문에 비교연산자를 사용하면 두 객체의 값을 비교하는 것이 아니라 객체 자체를 비교
    * equals로 변경하여 비교되어야 한다.
    */

    if(iA==iB){
        System.out.println("iA와 iB는 산술연산자 기준 같다");
    }else{
        System.out.println("iA와 iB는 산술연산자 기준 다르다");
    }

    if(iA.equals(iB)){
        System.out.println("iA와 iB는 객체의 값 기준 같다");
    }else{
        System.out.println("iA와 iB는 객체의 값 기준 다르다");
    }
```

### 2. NullPointerException 생성가능
```java
    Integer n = null;
    int b = 10;
    if(n == b){ // n이 null이기 때문에 NullPointerException 발생
        System.out.println("source is working");
    }else{
        System.out.println("n != b");
    }
```

### 3. 속도, 메모리 효율성
 연속적인 연산을 처리시에 참조변수 타입은 객체를 변경시마다 생성되기 때문에 효율성이 좋지 못하다. 
    
    
    

#### *참조블로그 
[자바-박싱-언박싱은-낯설어서](https://velog.io/@skyepodium/%EC%9E%90%EB%B0%94-%EB%B0%95%EC%8B%B1-%EC%96%B8%EB%B0%95%EC%8B%B1%EC%9D%80-%EB%82%AF%EC%84%A4%EC%96%B4%EC%84%9C)

[피보나치수열 연산으로 성능비교](https://ammff.tistory.com/20)

***기본형(primitive type) : 실제값을 나타내는 비트를 직접저장**
- boolean, char, byte, short, int, long, float, double 
  
***참조형(reference type) : 객체의 위치를 알려주는 비트값을 저장**
