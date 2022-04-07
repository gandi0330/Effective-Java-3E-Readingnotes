# [item1] 생성자 대신 정적 팩토리 메서드를 고려하라

>### 핵심 정리

**정적 팩터리 메서드와 public 생성자는 각자 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋다. 그렇다고 하더라도 정적 팩터리를 사용하는 게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고치자.**

<br>
<br>


>### 정적 팩토리 메서드란?

클래스 인스턴스 생성을 생성자가 아닌 메서드에서 생성하며 이 메서드를 정적 팩토리 메서드라 한다.


<br>
<br>



>### 정적 팩토리 메서드의 장점

1. 이름을 가질 수 있다.
     생성자의 매개변수와 생성자 만으로 반환될 객체의 특성을 제대로 설명할 수 없기 때문에 이름을 사용하면 객체의 특성을 쉽게 설명할 수 있다.

1. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.
     반복되는 요청에 같은 객체를 반환하는 식으로 생성비용이 큰 객체의 요청에 대비할 수 있다.
    
2. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
     부모 클래스의 정적 팩토리 메서드에서 자식 클래스의 객체를 반환할 수 있다.
    
3. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
4. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.


<br>
<br>

>### 정적 팩토리 메서드의 단점

1. 정적 팩토리 메서드만 제공하면 하위 클래스를 만들 수 없다.
2. 정적 팩토리 메서드는 프로그래머가 찾기 어렵다.

<br>
<br>

>### 정적 팩토리 메서드 네이밍 컨벤션

```java
//from : 매개변수 하나를 받아 인스턴스를 반환
Apple apple = Apple.from(id);

//of : 여러 매개변수를 받아 인스턴스를 반환
Apple apple = Apple.of(id,price);

//valueOf : from, of 보다 자세한 버전
Apple apple = Apple.valueOf(id,price);

//instance or getInstance : 인스턴스를 반환하지만 같은 인스턴스임을 보장 x
Apple apple = Apple.instance(price);

//create or newInstance : 매번 새로운 인스턴스를 생성해 반환
Apple apple = Apple.newInstance(id,price);

//getType : 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의
Apple apple = Fruit.getApple(id);

//newType : 다른 클래스에 매번 새로운 인스턴스를 반환
Apple apple = Fruit.newApple(id);

//type : getType과 newType의 간결한 버전
Apple apple = Fruit.apple(id);
```

