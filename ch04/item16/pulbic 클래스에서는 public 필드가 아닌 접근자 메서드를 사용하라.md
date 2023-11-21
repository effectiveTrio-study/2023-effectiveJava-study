
**요약** <br>
> public 클래스는 절대 가변 필드를 직접 노출해서는 안 된다. <br>
하지만 package-private 클래스나private 중첩 클래스에서는 종종 필드를 노출하는 편이 나을 때도 있다.

## (접근자)Getter 와 변경자(Setter)를 활용한 캡슐화
이 클래스는 데이터 필드에 직접 접근해서 수정이 가능하여 캡슐화의 이점을 제공하지 못한다.
```
class Point{
    public double x;
    public double y;
}
```
## 
그럴때 사용하는게 접근저와 변경자다.<br>
클래스의 필드는 모두 private으로 바꾸고 public 접근자(getter), 변경자(setter)를 추가

```
class Point{
    private double x;
    private double y;

    public Point(double x, double y){
        this.x = x;
        this.y = y;
    }

    public double getX() { 
        return x; 
    }
    public double getY() { 
        return y; 
    }

    public void setX(double x){ 
        this.x = x; 
    }
    public void setY(double y){
        this.y = y; 
    }
}
```
패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공함으로 클래스 내부 표현 방식을 언제든 바꿀수 있는 유연성을 얻을 수 있다.
하지만 package-private클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 문제가 없다.

## 불변 필드를 노출한 public 클래스
```
public final class Time {
    private static final int HOURS_PER_DAY    = 24;
    private static final int MINUTES_PER_HOUR = 60;

    public final int hour;
    public final int minute;

    public Time(int hour, int minute) {
        if (hour < 0 || hour >= HOURS_PER_DAY)
            throw new IllegalArgumentException("Hour: " + hour);
        if (minute < 0 || minute >= MINUTES_PER_HOUR)
            throw new IllegalArgumentException("Min: " + minute);
        this.hour = hour;
        this.minute = minute;
    }

}
```
**public 클래스의 필드가 불변**이라면 직접 노출할 때의 단점이 조금은 줄어들지만, 여전히 좋은 생각은 아니다.<br>
API를 변경하지 않고는 표현 방식을 바꿀 수 없고, 필드를 읽을 때 부수 작업을 수행할 수 없다.<br>
단, 불변식은 보장할 수 있다.