
### ✔️Enum의 메소드들

---

> String 같은 자바의 여러 클래스가 자체 내장 메소드를 가지고 있듯이,
enum 역시 내장 메소드를 지니고 있다. <br>
모든 Enum 타입은 컴파일 시에 java.lang.Enum 클래스를 상속하기 때문에
여기에 선언된 메소드를 이용할 수 있다. <br>
name() : 열거 객체의 문자열을 리턴(리턴타입 - String) <br>
**ordinal() : 열거 객체의 순번(0부터 시작)을 리턴(리턴타입 - int)** <br> 
compareTo() : 열거 객체를 비교해서 순번 차이를 리턴(리턴타입 - int) <br>
valueOf(String name) : 문자열을 입력받아서 일치하는 열거 객체를 리턴(리턴타입 - enum) <br>
values() : 모든 열거 객체들을 배열로 리턴(리턴타입 - enum[])

### ✔️ordinal 메서드

```java
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
public static void main(String[] args) {

        Day w = Day.FRIDAY;

		//열거 객체의 순번을 리턴
	    //전체 enum 객체 중 몇 번째 인지 알려준다
        int weekNum = w.ordinal();
        System.out.println(weekNum); //4 (0부터 시작이니깐)
}
```

- 열거 타입 상수와 연결된 정수값이 필요하면 ordinal 메서드를 이용하고 싶은 유혹에 빠진다.

### 합주단의 종류를 연주자가 1명인 solo 부터 10명인 dectet 까지 정의한 열거 타입이다.

```java
package Enum;

public enum Ensemble {
    SOLO, DUET, TRIO, QUARTET, QUINTET, SEXTET, SEPTET, OCTET, NONET, DECTET;

    public int numberOfMusicians() {
        return ordinal() + 1;
    }

    public static void main(String[] args) {
        System.out.println(SOLO.numberOfMusicians()); //1
        System.out.println(DECTET.numberOfMusicians()); //10
    }
}
```

- 동작은 하지만 유지보수하기가 끔찍한 코드다.
- 상수 선언 순서를 바꾸는 순간 오동작한다. (TRIO 와 DECTET의 위치를 바꾸게 되면 TRIO를 출력하면 3이 아닌 10이 출력됨)
- 이미 사용 중인 정수와 값이 가은 상수를 추가할 방법이 없다.
- 예를 들면 8중주(OCTET)상수가 이미 있으니, 똑같이 8명 연주하는 복4중주(DOUBLE QUARTET)은 추가할 수 없다.
- 또한 1,2,4,5 이렇게 값을 중간에 비워둘 수 도 없다.
- 그래서 만약 12명이 연주하는 종류를 추가한다면 사용하지 않을 11명짜리 상수(dummy)도 채워야 한다.
- 코드가 깔끔하지 못하고 실용성이 떨어진다.
<br><br>
### 🔎해결방법 : 인스턴스 필드를 사용하자

- 열거 타입 상수에 연결된 값은 ordinal 메서드로 얻지말고 인스턴스 필드에 저장하자
- 대부분의 프로그래머들은 ordinal 메서드를 쓸 일이 없다. 사용하지말자

```java
public enum Ensemble {
    SOLO(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5), SEXTET(6), SEPTET(7), OCTET(8), NONET(9), DECTET(10), TRIPLE_QUARTET(12) ;

    private final int numberOfMusicians;
    Ensemble(int size){
        this.numberOfMusicians = size;
    }

    public int NumberOfMusicians() {
        return numberOfMusicians;
    }

    public static void main(String[] args) {
        System.out.println(SOLO.numberOfMusicians); //1
        System.out.println(TRIPLE_QUARTET.numberOfMusicians); //12
    }
}
```

