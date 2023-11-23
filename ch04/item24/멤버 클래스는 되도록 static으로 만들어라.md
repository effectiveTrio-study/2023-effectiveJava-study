**요약** <br>
> 중첩 클래스는 네 가지가 있으며, 각각의 쓰임이 다르다.<br>1. 정적 멤버 클래스<br>2. 비정적 멤버 클래스<br>3. 익명 클래스<br>4. 지역 클래스


## 정적 멤버 클래스
```
public class TestA {
    private int a = 1;

    static class TestB {
        static void run(){
            TestA testA = new TestA();
            System.out.println(testA.a = 10);
        }
    }

    public static void main(String[] args) {
        TestA.TestB.run();
    }
}
```
## 비정적 멤버 클래스
```
public class TestA {
    private int a = 1;

    class TestB {
        void run() {
            System.out.println(TestA.this.a = 10);
        }
    }

    public static void main(String[] args) {
        TestA testA = new TestA();
        TestB testB = testA.new TestB();
        testB.run();
    }
}
```
## 익명 클래스
### 특징
- 이름이 없다.
- 바깥 클래스의 멤버가 아니다.
- 선언과 동시에 인스턴스가 만들어진다.
- 코드의 어디서든 만들 수 있다.
- 오직 비정적인 문맥에서 사용될때만 바깥 클래스의 인스턴스를 참조할 수 있다.

```
public class TestA {
    TestB testB() {
        return (int a) -> {
            System.out.println(a);
        };
    }
    interface TestB {
        void value(int a);
    }

    public static void main(String[] args) {
        TestA testA = new TestA();
        TestA.TestB testB = testA.testB();
        testB.value(10);
    }
}
```
## 지역 클래스
지역 클래스는 네 가지 중첩 클래스 중 **가장 드물게 사용**되고 있다.

지역 클래스는 **지역 변수를 선언할 수 있는 곳이라면 실질적으로 어디서든 선언할 수 있고 유효 범위도 지역변수와 같다.**
```
public class TestA {
    public void local() {
        class TestB { // 지역 클래스
            public void test() {
                System.out.println("지역 변수");
            }
        }
        TestB test = new TestB();
        test.test();
    }
    public static void main(String[] args) {
        TestA testA = new TestA();
        testA.local();
    }
}
```











