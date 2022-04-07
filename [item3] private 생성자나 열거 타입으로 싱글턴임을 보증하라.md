# [item3] private 생성자나 열거 타입으로 싱글턴임을 보증하라



싱글턴이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말합니다.

하지만 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워 질 수 있습니다.

싱글턴을 만드는 방식은 보통 둘 중 하나입니다. 두 방식 모두 생성자는 private으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련합니다.

<br>
<br>

>### 1번 방식 : public static final 필드 방식의 싱글턴

```java
public class Wise{
	public static final Wise INSTANCE = new Wise();
	private Wise(){}
}
```

1번 방식의 장점

- 해당 클래스가 싱글턴임이 API에 명백히 드러난다.
- 간결하다.

<br>
<br>

>### 2번 방식 : 정적 팩터리 방식의 싱글턴

```java
public class Wise{
	public static final Wise INSTANCE = new Wise();
	private Wise(){}
	public static Wise getInstance(){
		return INSTANCE;
	}
}
```

2번 방식의 장점

- 마음이 바뀌면 API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
    
    ( 유일한 인스턴스를 반환하던 펙터리 메서드가 호출하는 스레드별로 다른 인스턴스를 넘겨준다는 등)
    
- 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
- 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다.

<br>
<br>

>### 1, 2번 방식의 단점

둘 중 하나의 방식으로 만든 싱글턴 클래스를 직렬화하려면 단순히 Serializable을 구현한다고 선언하는 것만으로는 부족합니다. 

각 클래스를 직렬화한 후 역직렬화할 때 새로운 인스턴스를 만들어서 반환하기 때문입니다.

이를 해결하기 위해서는 readResolve() 메서드를 사용합니다.

모든 인스턴스 필드를 일시적(transient) 선언하고, 역직렬화 시 반드시 호출되는 readResolve 메서드로 인스턴스를 리턴하도록 수정합니다.

```java
private Object readResolve(){
	return INSTANCE;
}
```

<br>
<br>

>### 3번 방식 : 원소가 하나인 열거 타입을 선언하는 방식

```java
public enum Wise{
	INSTANCE;

	public String getName(){
		return "Wise";
	}
}
```

```java
System.out.println(Wise.INSTANCE.getName(); // -> Wise
System.out.println(Wise.INSTANCE.name(); // enum 객체의 공통 메소드도 사용 가능 -> INSTANCE
```

장점

- 더 간결하고 추가 노력 없이 직렬화할 수 있고, 더 복잡한 상황이나 리플렉션 공격에도 제 2의 인스턴스가 생기는 일을 완벽하게 막아준다.
- **대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.**

단점

- 만드려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다
    
    (열거 타입이 다른 인터페이스를 구현하도록 선언할 수는 있다.)
