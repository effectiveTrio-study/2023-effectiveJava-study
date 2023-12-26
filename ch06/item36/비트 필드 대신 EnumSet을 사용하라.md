### **🔎** 비트 필드 열거 상수 - 구닥다리 기법

```java
package Enum;

public class Item36 {
    // 2의 거듭제곱 값을 할당한 정수 열거 패턴
    public static final int STYLE_BOLD          = 1 << 0; // 1
    public static final int STYLE_ITALIC        = 1 << 1; // 2
    public static final int STYLE_UNDERLINE     = 1 << 2; // 4
    public static final int STYLE_STRIKETHROUGH = 1 << 3; // 8

    public void applyStyles(int styles) {
        // 각 스타일에 해당하는 비트가 설정되어 있는지 확인하고 출력
        System.out.println("Bold: " + ((styles & STYLE_BOLD) != 0));
        System.out.println("Italic: " + ((styles & STYLE_ITALIC) != 0));
        System.out.println("Underline: " + ((styles & STYLE_UNDERLINE) != 0));
        System.out.println("Strikethrough: " + ((styles & STYLE_STRIKETHROUGH) != 0));
    }

    // 매개변수 styles는 0개 이상의 STYLE_ 상수를 비트별 OR 한 값이다.
    public static void main(String[] args) {
        Item36 item = new Item36();
        item.applyStyles(STYLE_BOLD | STYLE_ITALIC);
    }
}
```

✔️ **단점.**

- 비트 필드 값이 그대로 출력 되면 단순한 정수 열거 상수를 출력할 때보다 해석하기가 훨씬 어렵다.
- 비트 필드 하나에 녹아 있는 모든 원소를 순회하기도 까다롭다.
- 최대 몇 비트가 필요한지를 API작성 시 미리 예측해 적절한 타입(int or long)을 선택해야 한다.(API를 수정하지 않고는 비트 수를 더 늘릴 수 없기 때문이다.)

### **🔎** EnumSet

- EnumSet 클래스는 열거 타입 상수의 값으로 구성된 집합을 효과적으로 표현해준다.
- Set 인터페이스를 완벽히 구현
- 타입 안전하고 다른 어떤 Set 구현체와도 함께 사용할 수 있다.
- EnumSet의 내부는 비트 벡터로 구현되었다. 우너소가 총 64개 이하라면, 즉 대부분의 경우에는 EnumSet 전체를 long 변수 하나로 표현하여 비트 필드에 비견되는 성능을 보여준다. removeAll과 retainAll 같은 대량 작업은 비트를 효율적으로 처리할 수 있는 산술 연산을 써서 구현했다. 그러면서도 비트를 직접 다룰 때 흔히 겪는 오류들에서 해방된다. 난해한 작업을 EnumSet이 다 처리해주기 때문이다.

```java
public class Item36 {
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }

    //EnumSet을 넘기는 것이 가장좋다.
    public void applyStyles(Set<Style> styles) {
    }
    
    text.applyStyles(EnumSet.of(Style.BLOD, Style.ITALIC));
}
```

- 짧고 깔끔하고 안전하다

### 📌 Reference
- https://jjingho.tistory.com/83