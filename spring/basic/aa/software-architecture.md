# software architecture
## 행위와 구조 
> 모든 소프트웨어 시스템은 이해관계자에서 서로 다른 두 가지 가치를 제공하는데 행위(behavior)와 구조(structure) 이다.  
> 행위는 이해관계자자의 시스템이 요구사항을 만족하도록 코드를 작성하는 것이고, 구조는 변경이 용이한 시스템의 아키텍처를 구성하는 일이다.

```text
[개인적인 생각: 잃어버린 10년]
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
#### 아키텍처 공부한 내용을 같이 이야기 해달라고 해서 공유합니다.
![image](https://github.com/kim-taehan/document/assets/52950400/3a22183c-7904-4c34-ad2d-7863792fc650)

#### GoF 패턴마다 샘플 작성하고 정리한 [github](https://github.com/kim-taehan/gof)
- https://github.com/kim-taehan/gof
- 각 패턴들에 대한 샘플 코딩과 설명을 정리한 내용입니다. 

## 컴포넌트 원칙
> 컴포넌트는 시스템의 구성 요소로 배포할 수 있는 가장 작은 단위다. (자바의 경우 jar 파일)

### 컴포넌트 응집도 원칙
#### 재사용/릴리즈 등가 원칙(Reuse/Relese Equivalence Principle, REP)
> 재사용 단위는 릴리스 단위와 같다
#### 공통 폐쇄 원칙 (Common Closure Principle, CCP)
> 동일한 이유로 동일한 시점에 변경되는 클래스를 같은 컴포넌트로 묶어라
#### 공통 재사용 원칙(Common Reuse Principle, CRP)
> 컴포넌트 사용자들은 필요하지 않은 것에 의존하게 강요하지 말라 

### 컴포넌트 결합
#### 의존성 비순환 원칙(Acyclic Dependencies Principles, ADP)
> 컴포넌트 의존성 그래프에 순환(cycle)이 있어서는 안 된다
#### 안정된 의존성 원칙 (Stable Dependencies Principles, SDP)
> 안정성의 방향으로(더 안정된 쪽에) 의존하라
#### 안정된 추상화 원칙(Stable Abstractions Principle, SAP)
> 컴포넌트 안정된 정도만큼만 추상화되어야 한다


## 아키텍처란
> 소프트웨어 아키텍트라면 코드에서 탈피하여 고수준의 문제에 집중해야 한다는 거짓말에 절대로 속아 넘어가서는 안된다.  
> 코드와 동떨어져서는 안된다.  
> 프로그래밍 작업을 맡을 뿐만 아니라 동시에 나머지 팀원들이 생산성을 극대화 할 수 있는 설계를 하도록 방향을 이끌어준다  

### 아키텍처의 주된 목적
```text
시스템의 생명주기를 지원하는 것
시스템을 쉽게 이해하고
쉽게 개발하며
쉽게 유지보수하고
쉽게 배포하게 해준다.
궁극적인 목표는 시스템의 수명과 관련된 비용은 최소화하고 프로그래머의 생산성은 최대화 하는데 있다.
```

## 테스트 코드
### 테스트를 고려한 설계
```text
테스트가 시스템의 설계와 잘 통합되지 않으면, 테스트는 깨지기 쉬워지고 시스템은 뻣뻣해져서 변경하기가 어려워진다.
물론 문제는 결합이다. 시스템에 강하게 결합된 테스트라면 시스템이 변경됐을때 테스트를 망가트린다.
깨지기 쉬운 테스트는 시스템을 뻣뻣하게 만든다는 부작용을 낳을때가 많다.
테스트를 고려해서 설계해야 한다. 이는 소프트웨어 설계의 첫번째 규칙인 변동성이 있는 것에 의존하면 안된다
```

```
[개인적인 생각: 보험 도메인과 테스트코드]
10년 동안 보험 도메인 기반의 SI를 하면서 테스트 코드를 작성하는 개발자를 보지 못했다.
그리고 현재에도 저와 친한 개발자들은 금융 도메인은 너무 복잡해 테스트 코드 작성하지 못한다고 말한다.
`클린 임베디드 아키텍처는 테스트하기 쉬운 임베디드 아키텍처다.` 
```

#### 아키텍처 공부한 내용을 같이 이야기 해달라고 해서 공유합니다.
![image](https://github.com/kim-taehan/document/assets/52950400/11ebc853-b558-4ff4-9895-568f94e5492c)


> 테스트 코드는 반드시 작성해야 된다고 생각하기 시작했다. 구조적으로 테스트 코드를 작성하기 힘들다면, 그 구조가 변경할려고 노력하는 것이 필요할 것 같다.

## 애자일 방법론
> 애자일 프로세스의 배경에는 소프트웨어 개발 자체가 다른 공학적인 프로세스와는 큰 차이가 있음을 인지하는 데에서부터 시작되었다. 이는 소프트웨어 위기의 원인과 해결방안을 찾는 데에서부터 시작되었다.
```text
애자일 개발 방법론은 계획을 통해서 주도해 나갔던 과거의 방법론과는 다르게 앞을 예측하며 개발을 하지 않고,
일정한 주기를 가지고 끊임없이 프로토 타입을 만들어내며 그때 그때 필요한 요구를 더하고 수정하여 하나의 커다란 소프트웨어를 개발해 나가는 adaptive style 이라고 할 수 있다
```

### 애자일 선언문
> 우리는 소프트웨어를 개발하고, 또 다른 사람의 개발을 도와주면서 소프트웨어 개발의 더 나은 방법들을 찾아가고 있다.
> 이 작업을 통해 우리는 다음을 가치있게 여기게 되었다:
```text
공정과 도구보다 개인과 상호작용을
포괄적인 문서보다 작동하는 소프트웨어를
계약 협상보다 고객과의 협력을
계획을 따르기보다 변화에 대응하기를
```

> 지나치게 계획에 의존하고 있으며 형식적인 절차를 따르기 때문에 시간도 오래걸리고 그 효율성이 크게 저하된다는 단점이 있다.


| 구분| 애자일(Agile) 방법론| 기존 방법론|
| --| --| --|
| 요구사항관리| 지속적인 요구사항 개발 및 변경수용| 초기 요구사항 수집 및 엄격한 변경관리|
| 계획 수립| 두단계 계획(찾은 계획 수립&갱신) 경험 기반 프로세스| 상세한 계획 수립(Up-Front) 계획 기반 프로세스|
| 설계| 적시(Just-in-time)설계| 상세한 사전(Up-Front) 설계|
| 문서화| 경량 프로세스 및 문서화보다 코드를 강조| 중량 프로세스 및 상세한 문서화 강조|
| 역할| 전체 팀워크를 중요시| 엄격한 역할 분리|

