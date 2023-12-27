### **🔎**@Override

- 메서드 선언에만 달 수 있다.
- 이 애너테이션이 달렸다는 것은 상위 타입의 메서드를 재정의했음을 뜻한다.
- 일관되게 사용하면 여러 가지 악명 높은 버그들을 예방해준다.

코드40-1. 영어 알파벳 2개로 구성된 문자열을 표현하는 클래스 - 버그를 찾아보자.

```java
package Override;

import java.util.HashSet;
import java.util.Set;

public class Bigram {
    private final char first;
    private final char second;

    public Bigram(char first, char second) {
        this.first = first;
        this.second = second;
    }
		
		//@Override 애터네이션을 사용해서 재정의한다는 의도를 명시해야한다.
    public boolean equals(Bigram b) {
        return b.first == first && b.second == second;
    }
    public int hashCode() {
        return 31 * first + second;
    }

    public static void main(String[] args) {
        Set<Bigram> s = new HashSet<>();
        for (int i = 0; i < 10; i++)
            for(char ch = 'a'; ch<= 'z'; ch++)
                s.add(new Bigram(ch, ch));
        System.out.println(s.size());
    }
}
```

- main 메서드를 보면 똑같은 소문자 2개로 구성된 바이그램 26개를 10번 반복해 집합에 추가한 다음, 그 집합의 크기를 출력한다. Set은 중복을 허용하지 않으니 26이 출력될 거 같지만, 실제로는 260이 출력된다. 무언가 잘못됐다!

✔️잘못된 이유.

- equals를 재정의(overriding)한 게 아니라 다중정의(overloading) 해버렸다.
- Object의 equals를 재정의하려면 매개변수 타입을 Object로 해야만 하는데, 그렇게 하지 않은 것이다.(Bigram b 이렇게 되어있음) 그래서 Object의 equals는 == 연산자와 똑같이 객체 식별성만을 확인한다. 따라서 같은 소문자를 소유한 바이그램 10개 각각이 서로 다른 객체로 인식되고, 결국 260을 출력한 것이다.

✔️해결방법.

- @Override 애너테이션을 달아서 Object.equals를 재정의한다는 의도를 명시해야 한다.

```java
    @Override public boolean equals(Object o) {
				if (!(o instanceof Bigram))
						return false;
				Bigram b = (Bigram) o;
        return b.first == first && b.second == second;
    }
```

### ✔️상위 클래스의 매서드를 재정의하려는 모드 메서드에 @Override 애너테이션을 달자.

- 예외는 한 가지, 구체 클래스에서 상위 클래스의 추상 매서드를 재정의할 때는 굳이 @Override를 달지 않아도 된다.
- 구체 클래스인데 아직 구현하지 않은 추상메서드가 남아 있다면 컴파일러가 그 사실을 바로 알려주기 때문이다.
- 재정의 메서드에 모두 @Override를 일괄로 붙여도 되지만, 대부분의 IDE는 재정의할 메서드를 선택하면 @Override를 자동으로 붙여준다.
- 클래스뿐 아니라 인터페이스의 메서드를 재정의할 때도 사용할 수 있다.

### 📌 Reference
- https://jjingho.tistory.com/88