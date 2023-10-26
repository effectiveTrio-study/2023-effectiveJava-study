## 유틸리티 클래스(utility class)
`정의`: 정적 멤버만 담은 클래스

### `java.lang.Math`, `java.util.Arrays`
```
public final class Math {
    private Math() {}

    public static double sin(double a) { ... }
}
```
```
public class Arrays {
    private Arrays() {}

    public static void sort(int[] a) { ... }
    public static void parallelSort(byte[] a) { ... }
    public static int binarySearch(long[] a, long key) { ... }
}
```
위 코드를 보면 전부 static으로 선언되어 있고 생성자가 private로 선언된 것을 확인할 수 있다.
즉, 사용 시, 인스턴스화를 통한 메서드 호출이 아닌, `Arrays.sort()`와 같은 방식으로 사용한다.

### abstract class
추상 클래스를 만들더라도 하위 클래스를 통해 인스턴스화가 가능하다

```
abstract class MyAbstractClass {
    // 추상 메서드
    public abstract void abstractMethod();
    
    // 일반 메서드
    public void nonAbstractMethod() {
        System.out.println("This is a non-abstract method.");
    }
}

public class Main {
    public static void main(String[] args) {
        // 추상 클래스를 인스턴스화하려고 하면 컴파일 에러는 발생하지 않습니다.
        // MyAbstractClass abstractObj = new MyAbstractClass(); // 컴파일 에러
        // 그러나 하위 클래스를 사용하여 추상 클래스를 인스턴스화할 수 있습니다.
        MySubclass subclassObj = new MySubclass();
        subclassObj.abstractMethod();
        subclassObj.nonAbstractMethod();
    }
}

class MySubclass extends MyAbstractClass {
    // 추상 메서드를 구현
    public void abstractMethod() {
        System.out.println("This is the implementation of abstractMethod.");
    }
}

```

### ✅ private 생성자를 추가하여 클래스의 인스턴스화를 막자
유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한게 아니다.  
하지만 생성자를 명시하지 않으면 생성자는 자동으로 public 생성자를 만들고, 의도치않게 인스턴스화를 할 수도 있다.  
이를 방지하고자, 명시적으로 private 생성자를 통해 클래스 바깥으로부터의 접근을 방지한다.  
이 방식을 통해 상속도 방지가능하다. 모든 생성자는 명시적/묵시적으로 상위 클래스의 생성자를 호출하는데 생성자가 private으로 선언되어있으니 하위 클래스가 상위 클래스의 생성자에 접근이 불가능하다.
```
public class UtilityClass {
    // 기본 생성자가 만들어지는 것을 막는다(인스턴스화 방지용)
    private UtilityClass() { 
        throw new AssertionError(); 
    }
}
```  

### 📌 Reference
- [[Java] Abstract Class(추상 클래스)](https://velog.io/@gillog/Java-Abstract-Class%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)