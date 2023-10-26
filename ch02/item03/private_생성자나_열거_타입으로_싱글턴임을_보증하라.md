## 싱글턴(singleton)
- `정의`: 인스턴스를 오직 하나만 생성할 수 있는 클래스
- `예시`: 무상태 객체, 설계상 유일해야 하는 시스템 컴포넌트

#### 단점
- 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기 어렵다.
	- 싱글턴 패턴은 어플리케이션 내에서 전역적으로 접근 가능한 인스턴스를 생성하는데 이로 인해 어플리케이션 다른 부분에서 의도치 않게 싱글턴 객체의 상태를 변경할 수 있으며, 이로 인해 테스트 간에 의존성이 생길 수 있기 때문이다.

### ✔️ public static final 필드 방식의 싱글턴
```
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }

    public void leaveTheBuilding() { ... }
}
```
private 생성자는 Elvis.INSTANCE를 초기화할 때 딱 한 번만 호출된다.  
예외적으로, 권한이 있는 클라이언트가 리플렉션 API인 `AccessibleObject.setAccessible`을 사용하면 private 생성자를 호출 가능하다.  
이를 방지하기 위해 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지게 하면 된다. 

#### 장점
- 해당 클래스가 싱글턴임이 API에 명백히 드러난다
	- `public static` 필드가 `final`이니 절대로 다른 객체를 참조불가하다.
- 간결함

### ✔️ 정적 팩터리 방식의 싱글턴
```
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE; }

    public void leaveTheBuilding() { ... }
}
```
Elvis.getInstance는 항상 같은 객체의 참조를 반환하기에 제2의 Elvis 인스턴스는 결코 만들어지지 않는다.  
다만, 리플렉션을 통한 예외는 똑같이 적용된다.

#### 장점
- API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
```
public class Elvis {
    private static Elvis instance = null;

    private Elvis() { ... }

    public static Elvis getInstance() {
        if (instance == null) {
            instance = new Elvis();
        }
        return instance;
    }

    public void leaveTheBuilding() { ... }
}
```
위 코드에서 `getInstance()` 메서드는 instance 변수가 null인 경우에만 새로운 Elvis 인스턴스를 생성하고 반환한다.  
이로써 호출할 때마다 새로운 인스턴스를 반환하게 되어, **싱글턴이 아니게** 동작합니다.
위 방식은 싱글턴 패턴에서 `Lazy Initialization`이다.
> Lazy Initialization: 싱글턴 인스턴스를 필요한 시점에 생성하는 방식이며, 각 호출마다 새로운 인스턴스를 생성할 수 있다.

- 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
```
public class SingletonFactory {
    private static final Map<Class<?>, Object> instances = new HashMap<>();

    public static <T> T getInstance(Class<T> clazz) {
        if (!instances.containsKey(clazz)) {
            try {
                T instance = clazz.newInstance();
                instances.put(clazz, instance);
            } catch (InstantiationException | IllegalAccessException e) {
                throw new RuntimeException("Error creating instance", e);
            }
        }
        return clazz.cast(instances.get(clazz));
    }
}
```
위의 코드에서 `getInstance()` 메서드는 임의의 클래스 타입에 대한 싱글턴 객체를 반환할 수 있다.

- 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다.
	- `예시`: `Elvis::getInstance`를 Supplier<Elvis>로 사용하는 방식
```
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();

    private Elvis() { }

    public static Elvis getInstance() {
        return INSTANCE;
    }

	public void leaveTheBuilding() { ... }
}

public class SingletonFactory {
    public static Elvis createElvis() {
        return Elvis.getInstance();
    }
}

public class Example {
    public static void main(String[] args) {
        Supplier<Elvis> supplier = SingletonFactory::createElvis;

        Elvis elvis = supplier.get();
        elvis.leaveTheBuilding();
    }
}
```
위의 코드에서 `Supplier` 인터페이스를 사용하여 createElvis 메서드를 공급자로 사용한다.  
이로써 람다 표현식 또는 메서드 참조를 통해 인스턴스를 생성할 수 있습니다.  
즉, 코드의 가독성을 높이고 재사용성을 개선할 수 있다.  

### 가짜 인스턴스 생성을 방지하기 위한 방법
위의 둘 중 하나의 방식으로 만든 싱글턴 클래스를 직렬화한 뒤 역직렬화할 때마다 새로운 인스턴스가 만들어진다.
<details>
	<summary><b>왜 역직렬화 시 새로운 인스턴스가 만들어질까?</b></summary>
	<li>직렬화: 객체를 직렬화할 때, 객체의 상태가 이진 데이터로 변환된다. 이 이진 데이터는 파일에 저장하거나 네트워크를 통해 전송될 수 있다.</li>
	<li>역직렬화: 직렬화된 데이터를 다시 객체로 변환할 때, 객체의 상태가 이진 데이터에서 복원됩니다. 이 과정은 새로운 객체 인스턴스를 생성할 수 있습니다.</li>

	싱글턴 패턴은 특정 클래스가 단 하나의 인스턴스만 생성되도록 보장한다. 이를 위해 일반적으로 생성자를 private으로 만들고 정적 메서드를 통해 인스턴스에 접근하게 한다.
	하지만 직렬화 및 역직렬화는 이런 패턴을 우회할 수 있습니다. 직렬화는 클래스의 인스턴스 변수와 상태를 저장하고 복원하는 프로세스이기 때문에, 역직렬화 시에는 생성자가 호출되지 않고 객체의 상태가 복원된다.
</details>

#### readResolve() 메서드
```
//싱글턴임을 보장해주는 readResolve 메서드
protected Object readResolve() {
		//'진짜' Elvis를 반환하고, 가짜 Elvis는 가비지 컬렉터에 맡긴다.
        return INSTANCE;
    }
```

### ✔️ 열거 타입 방식의 싱글턴 - 바람직한 방법
```
public enum Elvis {
    INSTANCE;
    public void leaveTheBuilding() { ... }
}
```

#### 장점
- 열거 타입은 Java에서 기본적으로 싱글턴을 보장하는 타입이다. 따라서 열거 타입은 JVM에 의해 싱글턴으로 관리됩니다.
- public 필드 방식과 비슷하지만 더 간결하다.
- 추가 노력 없이 직렬화할 수 있다.
	- 심지어 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제 2의 인스턴스가 생기는 일을 완벽하게 막아준다.
- 대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.
	- 단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.
	- 열거 타입이 다른 인터페이스를 구현하도록 선언할 수는 있다.

--- 
### 📌 Reference
- [싱글톤(Singleton) 패턴이란?](https://tecoble.techcourse.co.kr/post/2020-11-07-singleton/)
- [싱글턴 패턴(Singleton Pattern)에 대하여](https://velog.io/@think2wice/Java-%EC%8B%B1%EA%B8%80%ED%84%B4-%ED%8C%A8%ED%84%B4%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B4%85%EC%8B%9C%EB%8B%A4)
- [Singleton Pattern](https://seokrae.gitbook.io/sr/java-1/design/creational/_singleton)