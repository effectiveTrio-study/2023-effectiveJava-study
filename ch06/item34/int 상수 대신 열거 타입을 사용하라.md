**요약** <br>
> - 열거 타입(enum)은 정수 상수보다 더 읽기 쉽고 안전하고 강력하다.
> - 대다수 열거 타입이 명시적 생성자나 메서드 없이 쓰이지만, 각 상수를 특정 데이터와 연결짓거나 상수마다 다르게 동작하게 할 때는 필요하다. 드물게는 하나의 메서드가 상수별로 다르게 동작해야 할 때도 있다. 이런 열거 타입에서는 switch문 대신 상수별 메서드 구현을 사용하자. 열거 타입 상수 일부가 같은 동작을 공유한다면 전략 열거 타입 패턴을 사용하자.


## ✔️ 정수 열거 패턴

```java
package Enum;

public class EnumExample {

        private final static int MONDAY = 1;
        private final static int TUESDAY = 2;
        private final static int WEDNESDAY = 3;
        private final static int THURSDAY = 4;
        private final static int FRIDAY = 5;
        private final static int SATURDAY = 6;
        private final static int SUNDAY = 7;

        private final static int SPRING = 1;
        private final static int SUMMER = 2;
        private final static int FALL = 3;
        private final static int WINTER = 4;

        public static void main(String[] args) {

            int day = MONDAY;
            int season = SPRING;

            //상수를 선언했지만, 정수값을 비교하게 됨
            System.out.println(day == season); //true
            
            }

}
```

**단점**
---

1. 상수명으로 구분을 했지만 정수를 나열한 것 뿐이라 컴파일하면 그 값이 클라이언트 파일에 그대로 새겨진다. 따라서 상수의 값이 바뀌면 클라이언트도 반드시 다시 컴파일해야 한다.
2. 정수 상수는 문자열로 출력하기가 까다롭니다. 그 안에 상수가 몇 개인지도 알 수 없다.
3. 문자열 열거 패턴은 문자열에 오타가 있어도 컴파일러는 확인할 수 없어서 런타임 버그가 발생하고 문자열 비교에 따른 성능 저하가 발생한다.
    
    ```java
    private final String APPLE = "1";
    private final String GRAPE = "2";
    private final String ORANGE = "3";
    ```
    

<br>

## ✔️Enum 열거 타입

- Enum = Enumeration = 열거, 목록, 일람표
- 열거형(enum)은 요소, 멤버라 불리는 명명된 값의 집합을 이루는 자료형, 상수 데이터들의 집합
- 데이터 주제에 따라 몇가지로 한정된 값만을 가지는 경우가 존재한다. 예를 들어 “요일”, “계절”, “주사위” 같은 경우이다.
- 이렇게 한정된 데이터 묶음을 열거형 타입인 Enum 으로 묶어주면 보다 구조적으로 프로그래밍할 수 있다.
- enum의 핵심은 상수를 단순히 정수로 치부하지말고 객체 지향적으로 객체화해서 관리하자는 취지
- JAVA에서 enum은 인터페이스와 같이 독립된 특수한 클래스로 구분한다. 즉, 일종의 객체이기 때문에 힙(heap)메모리에 저장되며 각 enum 상수들은 별개의 메모리 주소값을 가짐으로써 완벽히 독립된 상수를 구성할 수 있는 것이다.

### 장점

- 열거 타입 잘체는 클래스이며, 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static final 필드로 공개한다. 열거 타입 선언으로 만들어진 인스턴스들은 딱 하나씩만 존재함이 보장 된다.(싱글턴은 원소가 하나뿐인 열거 타입이라 할 수 있고, 거꾸로 열거 타입은 싱글턴을 일반화한 형태라고 볼 수 있다.)
- 열거 타입은 컴파일타임 타입 안전성을 제공한다.
- 열거 타입에는 각자의 이름공간이 있어서 이름이 같은 상수도 평화롭게 공존한다.
- 열거 타입의 toString 메서드는 출력하기에 적합한 문자열을 내어준다.
- 열거 타입에 메서드나 필드를 추가할 수 있고 임의의 인터페이스를 구현할 수 도 있다.

```java
package Enum;

public enum Operation {

        PLUS("+") {
            public double apply(double x, double y) {
                
                return x + y;
            }
        },
        MINUS("-") {
            public double apply(double x, double y) {
                
                return x - y;
            }
        },
        MULTI("*") {
            public double apply(double x, double y) {
                
                return x * y;
            }
        },
        DIVIDE("/") {
            public double apply(double x, double y) {
                
                return x / y;
            }
        };

        // 클래스 생성자와 멤버
        private final String symbol;
        Operation(String symbol) {
            this.symbol = symbol;
        }

        // toString을 재정의하여 열거 객체의 매핑된 문자열을 반환하도록
        @Override
        public String toString() {
            return symbol;
        }

        // 열거 객체의 메소드에 사용될 추상 메소드 정의
        public abstract double apply(double x, double y);
        
}
```

```java
package Enum;

public class Step04_Enum {
    public static void main(String[] args) {
        double x = 2.5;
        double y = 5.0;

        // Operation 상수집합의 PLUS 상수를 정의
        Operation plus = Operation.PLUS;

        // enum 매핑값 출력
        String name = plus.toString();
        System.out.println(name); // +

        // enum 확장 메소드 실행
        double result = plus.apply(x, y); // 덧셈을 수행하는 메소드 (Operation.PLUS.apply(x, y) 로 해도됨)
        System.out.println(result); // 7.5

        // ------------------------------------------------- //

        // Operation 상수집합의 PLUS 상수를 정의
        Operation multi = Operation.MULTI;

        String name2 = multi.toString();
        System.out.println(name2); // *

        // enum 확장 메소드 실행
        double result2 = multi.apply(x, y); // 곱셈을 수행하는 메소드
        System.out.println(result2); // 12.5
    }
}
```

### values()

```
package Enum;

public enum Day {
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SARTURDAY,
    SUNDAY
}
```

```java
package Enum;

import java.util.Arrays;

public class Step02_EnumMethod {
    /*
    String 같은 자바의 여러 클래스가 자체 내장 메소드를 가지고 있듯이,
    enum 역시 내장 메소드를 지니고 있다.
    모든 Enum 타입은 컴파일 시에 java.lang.Enum 클래스를 상속하기 때문에
    여기에 선언된 메소드를 이용할 수 있다.
    name() : 열거 객체의 문자열을 리턴(리턴타입 - String)
    ordinal() : 열거 객체의 순번(0부터 시작)을 리턴(리턴타입 - int)
    compareTo() : 열거 객체를 비교해서 순번 차이를 리턴(리턴타입 - int)
    valueOf(String name) : 문자열을 입력받아서 일치하는 열거 객체를 리턴(리턴타입 - enum)
    values() : 모든 열거 객체들을 배열로 리턴(리턴타입 - enum[])
     */
    public static void main(String[] args) {

        Day w = Day.FRIDAY;

        //열거 객체의 문자열을 리턴
        String weekName = w.name();
        System.out.println(weekName); //FRIDAY

        //열거 객체의 순번을 리턴
        //전체 enum 객체 중 몇 번째 인지 알려준다
        int weekNum = w.ordinal();
        System.out.println(weekNum); //4 (0부터 시작이니깐)

        //enum 객체를 비교해서 순번차이를 리턴
        Day w1 = Day.MONDAY; //0
        Day w2 = Day.FRIDAY; //4

        //w1 - w2 (w2가 w1이 되기 위한 값인 -4를 리턴)
        int compare1 = w1.compareTo(w2);
        System.out.println(compare1); //-4
        //매개값(w1)이 enum값(w2)이 되기 위한 +4 를 리턴
        int compare2 = w2.compareTo(w1);
        System.out.println(compare2);

        //문자열을 입력받아서 일치하는 enum 객체를 리턴
        //w3는 SUNDAY값을 가지고 있는 enum 객체를 참조하게 된다.
        Day w3  = Day.valueOf("SUNDAY");
        System.out.println(w3);

        //모든 enum 객체들을 배열로 리턴
        Day[] w4 = Day.values();

        System.out.println(Arrays.toString(w4));// [MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SARTURDAY, SUNDAY]

        for (Day type : Day.values()){ //enum 순회
            System.out.println(type); // 순서대로 enum 객체 출력
        }
    }
}
```

- values() 는 상수 이름을 문자열로 반환하므로 println과 printf로 출력하기에 안성맞춤이다.
- 일반 클래스와 마찬가지로 기능을 클라이언트에게 노출해야 할 합당한 이유가 없다면 열거 타입을 선언한 클래스 혹은 그 패키지에서만 유용한 기능은 private나 package-private 메서드로 구현한다.

--- 
### 📌 Reference
- https://jjingho.tistory.com/81
- https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%97%B4%EA%B1%B0%ED%98%95Enum-%ED%83%80%EC%9E%85-%EB%AC%B8%EB%B2%95-%ED%99%9C%EC%9A%A9-%EC%A0%95%EB%A6%AC