# [item2] 생성자에 매개변수가 많다면 빌더를 고려하라

>### 핵심 정리
 

**생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 게 더 낫다.** 매개변수 중 다수가 필수가 아니거나 같은 타입이면 특히 더 그렇다. 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전하다.

<br>
<br>

>### 빌더 패턴이란?


빌더 패턴이란 GoF 디자인 패턴 중 생성 패턴에 해당한다.

복잡한 객체를, 생성하는 클래스와 표현하는 클래스 둘로 분리하여 동일한 생성 절차에서 서로 다른 표현 결과를 만들 수 있게 하는 패턴이다.

점층적 생성자 패턴과  자바빈즈 패턴의 장점만을 취한 패턴이다.

<br>
<br>

>### 점층적 생성자 패턴

```java
public class Book{
	private int isbn;     // 도서번호 (필수)
	private int price;    // 도서가격 (필수)
	private String title; // 도서제목 (필수)
	private String desc;  // 설명 (선택)
	private int rate;     // 평점 (선택)
	
	public Book(int isbn, int price, String title){
		this(isbn, price, title, "");
	}

	public Book(int isbn, int price, String title, String desc){
		this(isbn, price, title, desc, "");
	}

	public Book(int isbn, int price, String title, String desc, int rate){
		this.isbn = isbn;
		this.price = price;
		this.title = title;
		this.desc = desc;
		this.rate = rate;
	}
```

다음과 같이 필수 매개변수만 받는 생성자, 필수 매개변수와 선택 매개변수 1개를 받는 생성자 ... 형태로 선택 매개변수를 전부 다 받는 생성자까지 늘려가는 방식을 점층적 생성자 패턴이라 한다.

- 단점
    - 매개변수의 순서에 따라 원치않는 매개변수에도 값을 지정해 줄 필요가 있다.
    - 위의 코드에서 rate의 값을 설정해주고 싶다면 그 전의 선택 매개변수까지 전부 값을 지정해줘야 한다.
    
    ```java
    Book book = new Book(1,1000,"effective java","설정하기 싫은데 rate 때문에 설정",5);
    ```
    
    - 결국 매개변수 개수가 많아질 수록 클라이언트 코드를 작성하거나 읽기 어려워진다.

<br>
<br>

>### 자바빈즈 패턴
 

```java
public class Book{
	private int isbn     = -1;     // 도서번호 (필수) ; 기본값 없음
	private int price    = -1;     // 도서가격 (필수) ; 기본값 없음
	private String title = "";     // 도서제목 (필수) ; 기본값 없음
	private String desc  = "";     // 설명 (선택)
	private int rate     = 0;      // 평점 (선택)
	
	public Book(){}
	
	// 세터 메서드들	
	public void setIsbn(int isbn) {this.isbn = isbn;}
	public void setPrice(int price) {this.price = price;}
	public void setTitle(String title) {this.title = title;}
	public void setDesc(String desc) {this.desc = desc;}
	public void setRate(int rate) {this.rate = rate;}
}
```

다음과 같이 매개변수들을 기본값으로 설정한 후 모든 매개변수의 설정을 세터 메서드를 통해 해결한다. 따라서 점층적 생성자 패턴보다 인스턴스를 만들기 쉽고 읽기도 쉬워졌다.

- 단점
    - 객체 하나를 만들려면 메서드를 여러 개 호출해야한다.
    - 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.

<br>
<br>

>### 빌더 패턴
 

```java
public class Book{
	private int isbn;     // 도서번호 (필수) ; 기본값 없음
	private int price;    // 도서가격 (필수) ; 기본값 없음
	private String title; // 도서제목 (필수) ; 기본값 없음
	private String desc;  // 설명 (선택)
	private int rate;     // 평점 (선택)
	
	// 빌더 클래스 생성
	public static class Builder{
		// 필수 매개변수
		private int isbn;
		private int price;
		private String title;

		// 선택 매개변수 기본값 초기화
		private String desc = "";
		private int rate = 0;
		
		public Builder(int isbn, int price, String title){
			this.isbn = isbn;
			this.price = price;
			this.title = title;
		}
		
		public Builder desc(String desc){
			this.desc = desc;
			return this;
		}

		public Builder rate(int rate){
			this.rate = rate;
			return this;
		}
		
		public Book build(){
			return new Book(this);
		}
	}
	
	private Book(Builder builder){
		isbn = builder.isbn;
		price = builder.price;
		title = builder.title;
		desc = builder.desc;
		rate = builder.rate;
	}
}

// 사용하기
Book book = new Book.Builder(1,1000,"effective java").desc("so easy").rate(5).build();
```

빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다

이러한 방식을 메서드 호출이 흐르듯 연결된다는 뜻으로 (플루언트 API), (메서드 연쇄)라고 한다

- 장점
    - 매개변수가 많아 복잡한 경우 코드의 가독성을 높인다
    - 계층적으로 설계된 클래스와 함께 쓰기에 좋다
    - 자바빈즈의 경우 public settter 때문에 불변 클래스로 만들 수 없었지만 빌더를 이용하면 불변 클래스로 만들 수 있다.
- 단점
    - 성능에 민감한 상황에서는 빌더 생성비용 때문에 문제가 될 수 있다. (크진 않음)
    - 코드가 장황하다. (매개변수가 4개 이상은 되어야 값어치를 한다.)
