인스턴스 필드들을 모아두는 일 외에는 아무 목적도 없는 퇴보한 클래스를 작성하려할 때가 있다. 
```Java

class Point{
	public double x;
	public double y;
}
```
이 클래스는 데이터 필드에 직접 접근할 수 없으니 캡슐화의 이점을 제공하지 못한다. 이는 잘못히다. 객체지향 프로그래머는 이런 클래스를 싫어한다. 필드는 private로! getter를 추가해 사용한다.

```Java
class Point{
	private double x;
	private double y;

	public Point(double x, double y){this.x =x; this.y =y;}
	public getX(){return x;}
	public getY(){return y;}
}
```

퍼블릭 클래스라면 이 방법이 확실히 맞다. 패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있다.  퍼블릭 클래스가 필드를 공개하면 이를 사용하는 클라이언트가 생겨날 것이므로 내부표현 방식을 마음대로 바꿀 수 없게 된다. 

package-private 클래스 혹은 private 중첩 클래스라면, 데이터 필드를 노출한다고 해도 하등의 문제가 없다. 이를 유의해서 노출범위를 설정하는 것이 맞을 것이다. 

```Java

class Point{
	private static final int HOURS_PER_DAY = 24;
	private static final int MINUTES_PER_HOUR = 60;

	public final int hour;
	public final int minute;
	
	
}
```

하지만 여기서 필드를 불변으로 선언해 뒀다면, 직접 노출했을 때의 단점이 조금은 줄어든다. 
이 역시 좋은 생각은 아니다. API를 변경하지 않고는 표현방식을 바꿀 수 없고, 필드를 읽을 떄 부수작업을 수행할 수 없다는 단점은 여전하다 . 단 불변식은 보장할 수 있게 된다. 예컨대 다음 클래스는 각 인스턴스가 유효한 시간을 표현함을 보장한다. 

# 변경 가능성을 최소화하라

불변 클래스란 간단히 말해 , 