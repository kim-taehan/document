# software architecture
## software architecture 목표
> 소프트웨어 아키텍처의 목표는 필요한 시스템을 만들고 유지보수하는 데 투입되는 인력을 최소화하는 데 있다.

### 행위와 구조 
> 모든 소프트웨어 시스템은 이해관계자에서 서로 다른 두 가지 가치를 제공하는데 행위(behavior)와 구조(structure) 이다.  
> 행위는 이해관계자자의 시스템이 요구사항을 만족하도록 코드를 작성하는 것이고, 구조는 변경이 용이한 시스템의 아키텍처를 구성하는 일이다.

```text
[개인적인 생각: 일본의 잃어버린 10년]
10여년 동안 항상 행위를 위해서 일하는 코더로 살았던 것 같다.
핑계를 말해보면 주변 환경에서 선후배를 비롯한 모든 이해관계자들이 구조에 대해 신경을 쓰는 사람이 없었다.
항상 시간에 쫒기듯 기능 구현만을 위해서 개발을 진행하면서 기능 구현만 빠른 시간에 되면 개발 능력이 뛰어난 것으로 생각되었다.
일본의 잃어버린 10년처럼 제가 코더로 살았던 시간도 잃어버린 시간이라고 생각이 들어서 한동안 많이 힘들었다
```
> 소프트웨어 아키텍트는 시스템이 제공하는 특성이나 기능보다는 시스템의 구조에 더 중점을 둔다. 아키텍트는 이러한 특성과 기능을 개발하기 쉽고,
> 간편한게 수정할 수 있으면, 확장하기 쉬운 아키텍처를 만들어야 한다.

## 설계원칙 (SOLID)

### SRP: 단일 책임 원칙(Single Responseibility Principle)
> 각 소프트웨어 모듈은 변경의 이유가 하나여만 한다. 
```text
[개인적인 생각: 트레이드 오프]
개발을 수행하다 보면 공통 모듈(xxxUtils) 을 많이 만들게 된다. 그런데 공통 모듈에서 비지니스 영역이 일부분 포함되기도 한다.
이러한 부분은 조심을 해야될 것 같다. 분명 SRP 을 어기는 코드를 많이 만들었던 것 같다.
하지만 공통모듈을 만들지 말라는 이야기는 아닌듯 하다 공통모듈에서 관심사를 하나로 만들고 거기에만 집중하는 코드를 구성하는 습관을 가져야겠다.
```

### OCP: 개발-폐쇄 원칙(Open-Closed Principle)
> 소프트웨어 개체는 행위는 확장할 수 있어야 하지만, 이때 개체를 변경해서는 안된다.

#### HandlerAdapter: 제가 가장 좋아하는 DispatcherServlet 사용되는 인터페이스이다.
- HandlerAdapter 를 상속받은 클래스에서 지원하는 클래스를 찾아 그 클래스에서 역할을 넘기는 코드이다.
```java
public interface HandlerAdapter {

	boolean supports(Object handler);

	@Nullable
	ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception;
}
```

#### SimpleControllerHandlerAdapter: HandlerAdapter를 구현한 구현 클래스
- 여기서 다른 형태의 기능을 확장하고 싶을때 HandlerAdapter 구현한 클래스를 빈으로 등록만 하면 된다.
- 기존 소스는 전혀 수정없이 전혀 다른 형태는 handler를 추가할 수 있다.
```java
public class SimpleControllerHandlerAdapter implements HandlerAdapter {

	@Override
	public boolean supports(Object handler) {
		return (handler instanceof Controller);
	}

	@Override
	@Nullable
	public ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		return ((Controller) handler).handleRequest(request, response);
	}
}
```

### LSP: 리스코프 치환 원칙(Liskov substritution Principle)
> 상호 대체 가능한 구성요소를 이용해 소프트웨어 시스템을 만들 수 있으려면, 이들 구성요소는 반드시 서로 치환 가능해야 한다. 
> 업캐스팅된 상태에서 부모의 메서드를 사용해도 동작이 의도대로 흘러가야 하는 것을 의미

#### 직사각형 
```java
public class 직사각형{
	protected int x;
	protected int Y;
	public int getArea(){
		return x * y;
	}
	public void setX(int x) this.x = x;
}
```java
#### 직사각형 상속받은 정사각형
- 정사각형은 x, y 값이 동일하기에 정사각형에서 편리하게 사용하기 위해 y값도 같이 변경하였다.
- 하지만 이런 경우 LSP 를 위반하게 된다.
```
public class 정사각형 extends 직사각형{
	@overide
	public void setX(int x) this.x = x; this.y = y;
} 
```

### ISP: 인터페이스 분리 원칙(Interface Segregation Principle) 
- 인터페이스를 각각 사용에 맞게 끔 잘게 분리해야한다는 설계 원칙
- 인터페이스는 제약 없이 자유롭게 다중 상속(구현)이 가능하기 때문에, 분리할 수 있으면 분리하여 각 클래스 용도에 맞게 implements 하라는 설계 원칙이라고 이해하면 된다.
```text
[개인적인 생각]
Web Service 개발시 항상 하는 짓이 있다. XXXService 라는 비지니스 인터페이스를 만들고 이를 상속받은 XXXServiceImpl 이라는 클래스를 만든다.
XXXServiceImpl은 절대 다른 클래스로 변경되지 않는데도. Inteface를 만들고, '아 나 인터페이스 사용했네' 라는 생각을 하게 한다.
인터페이스와 추상클래스에서 가장 큰 장점이 다중 구현이 되는 것을 잃어버리지 말고, Inteface를 잘 쪼개서 설계하자.
```

### DIP: 의존 역전 원칙(Dependency Inversion Principle)
- 상위 모듈은 하위 모듈에 의존해서는 안 되고 둘 다 추상화에 의존해야 한다.
- 추상화는 세부 사항에 의존해서는 안 되고 세부사항(구체적인 구현)은 추상화에 의존해야 한다.
```text
[하지마라] (개인적으로 '하자마라' 시리즈가 참 좋다. 머리가 나빠도 이해를 못해도 그냥 외워서 안하면 된다.)
- 변동성이 큰 구체 클래스를 참조하지 말라.
- 변동성이 큰 구체 클래스로 부터 파생하지 말라
- 구체 함수를 오버라이드 하지 말라.
```

## GoF 디자인 패턴 
```text
1995년 GoF(Gang of Four)라고 불리는 Erich Gamma, Richard Helm, Ralph Johnson, John Vissides가 처음으로 디자인 패턴을 구체화하였다.
GoF의 디자인 패턴은 소프트웨어 공학에서 가장 많이 사용되는 디자인 패턴이다.
목적에 따라 분류할 시 생성 패턴 5개, 구조 패턴 7개, 행위 패턴 11개, 총 23개의 패턴으로 구성된다.
```
#### 아키텍처 공부한 내용을 같이 공유하라고 해서 공유합니다.
![image](https://github.com/kim-taehan/document/assets/52950400/3a22183c-7904-4c34-ad2d-7863792fc650)

#### GoF 패턴마다 샘플 작성하고 정리한 [github](https://github.com/kim-taehan/gof)
- https://github.com/kim-taehan/gof
- 각 패턴들에 대한 샘플 코딩과 설명을 정리한 내용입니다. 
