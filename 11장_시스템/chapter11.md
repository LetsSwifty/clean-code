# 11장 시스템

시스템 수준에서도 깨끗함을 유지하는 방법 <br>
<pre> 복장성은 죽음이다. 개발자에에서 생기를 앗아가며, 제품을 계획하고 재작하고 테스트하기 어렵게 만든다."
- 레이 오지R, 마이크로소프트 최고 기술 책임자(CTO) </pre>

## 시스템 제작과 시스템 사용을 분리하라

우선 **제작(construction)** 은 **사용(use)** 과 아주 다르다는 사실을 명심한다.

소프트웨어 시스템은 (애플리케이션 객체를 제작하고 의존성을 서로 연결하는 준비 과정과 (준비 과정 이후에 이어지는) 런타임 로직을 분리해야 한다. <br>
시작 단계는 모든 애플리케이션이 풀어야 한 **관심사(concern)** 다. 이것이 이 장에서 우리가 맨 처음 살펴볼 **관심사(concern)** 다. <br>
**관심사 분리**는 우리 분야에서 가장 오래되고 가장 중요한 설계 기법 중 하나다. <br>
불행히도 대다수 애플리케이션은 시작 단계라는 관심사를 분리하지 않는다. <br>
준비 과정 코드를 주먹구구식으로 구현할 뿐만 아니라 런타임 로직과 마구 뒤섞는다. 다음이 전형적인 예다. <br>

```swift
func getService() -> Service {
	if let service = service {
    	return service
    }
    return MyServiceImpl(...)
}
```
이것이 초기화 지연(Lazy Initialization) 혹은 계산 지연(Lazy Evaluation)이라는 기법이다. <br>
장점은 여러 가지다. 우선, 실제로 필요할 때까지 객체를 생성하지 않으므로 불필요한 부하가 걸리지 않는다. 따라서 애플리케이션을 시작하는 시간이 그만큼 빨라진다. <br>

둘째, 어떤 경우에도 null포인터를 반환하지 않는다. <br>
하지만 `getService` 메서드가 `MyServiceImpl`과 (위에서는 생략한) 생성자 인수에 명시적으로 의존한다. 런타임 로직에서 `MyServiceImpl` 객체를 전혀 사용 하지 않더라도 의존성을 해결하지 않으면 컴파일이 안 된다. <br>
테스트도 문제다. `MyServiceImpl`이 무거운 객체라면 단위 테스트에서 `getService` 메서드를 호출하기 전에 적절한 **테스트 전용 객체**(TEST DOUBLE이나 MOCK OBIECT)를 service 필드에 할당해야 한다. <br>
또한 일반 런타임 로직에 다 객체 생성 로직을 섞어놓은 탓에 (service가 null인 경로와 null이 아닌 경로 등) 모든 실행 경로도 테스트해야 한다. <br>
책임이 둘이라는 말은 매서드가 작업을 두 가지 이상 수행한다는 의미다.<br>
즉, 작게나마 단일 책임 원칙, SRP을 깬다는 말이다. <br>
무엇보다 MyServiceImpl이 모든 상황에 적합한 객체인지 모른다는 사실이 가장 큰 우려다.그렇다고 getService 메서드를 포함한 클래스가 전체 문맥을 알 필요가 있을까? <br>
과연 이 시점에서 어떤 객제로 사용할지 알 수나 있을까? 현실적으로 한 객체 유형이 모든 문맥에 적합할 가능성이 있을까? <br>
모듈성은 저조하며 대개 중복이 심각하다. <br>
체계적이고 탄탄한 시스템을 만들고 싶다면 혼히 쓰는 좀스럽고 **손쉬운** 기법으로 모듈성을 깨서는 절대로 안 된다. 객체를 생성하거나 의존성을 연결할 때도 마찬가지다. <br>
설정 논리는 일반 실행 논리와 분리해야 모듈성이 높아진다. <br>
또한 주요 의존성을 해소하기 위한 방식, 즉 전반적이며 일관적인 방식도 필요하다. <br>

## Main 분리

시스템 생성과 시스템 사용을 분리하는 한 가지 방법으로, 생성과 관련한 코드는 모두 main이나 main이 호출하는 모듈로 옮기고, 나머지 시스템은 모든 객체가 생성되었고 모든 의존성이 연결되었다고 가정한다. <br>
(그림 11-1 참조.) <br>
<img width="500" src="https://user-images.githubusercontent.com/50406861/201718204-4fd24465-e67c-474f-b9a7-b6379224d4a9.jpg"/> <br>


제이 흐름은 따라가기 쉽다. <br>
main 함수에서 객체를 생성한 후 애플리케이션에 넘긴다. <br>
애플리케이션은 객체를 사용할 뿐이다. <br>
모든 화살표가 main 쪽에서 애플리케이션 쪽을 향한다. <br>
즉, 애플리케이션은 main이나 객체가 생성되는 과정을 전혀 모른다는 뜻이다. 단지 모든 객체가 직절히 생성되었다고 가정한다. <br>

### 팩토리

객체가 생성되는 **시점** 을 애플리케이션이 결정할 필요도 생긴다. <br>
예를 들어, 주문처리 시스템에서 애플리케이션은 `LineItem` 인스턴스를 생성해 `Order`에 추가한다. 이때는 **ABSTRACT FACTORY** 패턴을 사용한다. <br>
그러면 `LineItem`을 생성하는 시점은 애플리케이션이 결정하지만 `LineItem`을 생성하는 코드는 애플리케이션이 모른다. (그림 11-2 참조) <br>
<img width="500" src="https://user-images.githubusercontent.com/50406861/201720307-a9fc8a7a-c1f8-4c19-994d-4424368ffba6.jpg"/>

여기서도 마찬가지로 모든 의존성이 `main`에서 `OrderProcessing` 애플리케이션 으로 향한다. 
즉, `OrderProcessing` 애플리케이션은 `LineItem` 생성되는 구체적인 방법을 모른다.
그 방법은 `main` 쪽에 있는 `LineltemFactoryImplementation` 이 안다. 
그럼에도 `OrderProcessing` 애플리케이션은 `LineItem` 인스턴스가 생성 되는 시점을 완벽하게 통제하며, 필요하다면 `OrderProcessing` 애플리케이션에서만 사용하는 생성자 인수도 넘길 수 있다.

<image width="900" src="https://user-images.githubusercontent.com/50406861/202003744-106cc2ce-02a8-4a49-8a08-c7ca35ea8b5a.png"/>

```swift
import Foundation

// Abstract Product에 대한 객체를 생성하도록 유도하는 Protocol
protocol AbstractFactory {
    func createProductA() -> AbstractProduct
    func createProductB() -> AbstractProduct
}

// 실제로 객체를 생성하는 Class
class ConcreateFactory1: AbstractFactory {
    func createProductA() -> AbstractProduct {
        return ProductA1()
    }
    
    func createProductB() -> AbstractProduct {
        return ProductB1()
    }
}

// 실제로 객체를 생성하는 Class
class ConcreateFactory2: AbstractFactory {
    func createProductA() -> AbstractProduct {
        return ProductA2()
    }
    
    func createProductB() -> AbstractProduct {
        return ProductB2()
    }
}

protocol AbstractProduct {
    func useProduct()
}

class AbstractProductA: AbstractProduct {
    func useProduct() {}
}

class ProductA1: AbstractProductA {
    override func useProduct() {
        print("ProductA1")
    }
}

class ProductA2: AbstractProductA {
    override func useProduct() {
        print("ProductA2")
    }
}

class AbstractProductB: AbstractProduct {
    func useProduct() {}
}

class ProductB1: AbstractProductB {
    override func useProduct() {
        print("ProductB1")
    }
}

class ProductB2: AbstractProductB {
    override func useProduct() {
        print("ProductB2")
    }
}

let factory1 = ConcreateFactory1()
let productA1 = factory1.createProductA()
let productB1 = factory1.createProductB()

let factory2 = ConcreateFactory2()
let productA2 = factory2.createProductA()
let productB2 = factory2.createProductB()

productA1.useProduct()
productA2.useProduct()
productB1.useProduct()
productB2.useProduct()
```
### 의존성 주입

사용과 제작을 분리하는 강력한 메커니즘 하나가 **의존성 주입 (Dependency Injection)** 이다.
의존성 주입은 **제어 역전(Inversion of Control)** 기법을 의존성 관리에 적용한 메커니즘이다. 제어 역전에서는 한 객체가 맡은 보조 책임을 새로운 객체에게 전적으로 떠넘긴다. 
새로운 객체는 넘겨받은 책임만 맡으므로 **단일 책임 원칙(SRP)** 을 지키게 된다. 의존성 관리 맥락에서 객체는 의존성 자체를 인스턴스로 만드는 책임은 지지 않는다.
대신에 이런 책임을 다른 전담 메커니즘에 넘겨야만 한다. 그렇게 함으로써 제어를 역전한다. 초기 설정은 시스템 전체에서 필요하므로 대개 책임질 메커니즘으로 'main' 루틴이나 **특수 컨테이너**를 사용한다.
JNDI 검색은 의존성 주입을 부분적으로 구현한 기능이다. 객체는 디렉터리 서버에 이름을 제공하고 그 이름에 일치하는 서비스를 요청한다.
```java MyService myService = (MyService)(jndiContext.lookup ("NameOfMyService");```
호출하는 객체는 (반환되는 객체가 적절한 인터페이스를 구현하는 한) 실제로 반환되는 객체의 유형을 제어하지 않는다. 대신 호출하는 객체는 의존성을 능동적으로 해결한다.
진정한 의존성 주입은 여기서 한 걸음 더 나간다. 클래스가 의존성을 해결하려 시도하지 않는다. 클래스는 완전히 수동적이다. 
대신에 의존성을 주입하는 방법으로 `설정자(setter)` 메서드나 생성자 인수를 (혹은 둘 다를) 제공한다. 
DI 컨테이너는 (대개 요청이 들어올 때마다) 필요한 객체의 인스턴스를 만든 후 생성자 인수나 설정자 메서드를 사용해 의존성을 설정한다. 
실제로 생성되는 객체 유형은 설정 파일에서 지정하거나 특수 생성 모듈에서 코드로 명시한다.
스프링 프레임워크는 가장 널리 알려진 자바 DI 컨데이너를 제공한다. 객체 사이 의존성은 XML 파일에 정의한다. 그리고 자바 코드에서는 이름으로 특정한 객체를 요청한다. 예제는 잠시 후에 살펴본다.
그러나 초기화 지연으로 얻는 장점은 포기해야 하는 걸까? 이 기법은 DI를 사용하더라도 때론 여전히 유용하다. 
먼저 대다수 DI 컨테이너는 필요한 때까지는 객체를 생성하지 않고, 대부분은 계산 지연이나 비슷한 최적화에 쓸 수 있도록 팩토리를 호출하거나 프록시를 생성하는 방법을 제공한다.
즉, 계산 지연 기법이나 이와 유사한 최적화 기법에서 이런 메커니즘을 사용할 수 있다.

## 테스트 주도 시스템 아키텍처 구축

코드 수준에서 아키텍처 관심사를 분리할 수 있다면, 진정한 테스트 주도 아키텍처 구축이 가능해진다. 

그때그때 새로운 기술을 채택해 단순한 아키텍처를 복잡한 아키텍처로 키워갈 수도 있다.

처음에 쏟아 부은 노력을 버리지 않으려는 심리적 저항으로 인해, 그리고 처음 선택한 아키텍처가 향후 사고 방식에 미치는 영향으로 인해, 변경을 쉽사리 수용하지 못하는 탓이다.

소프트웨어 역시 나름대로 형체가 있지만, 소프트웨어 구조가 관점을 효과적으로 분리한다면, 극적인 변화가 경제적으로 가능 하다.

다시 말해, '아주 단순하면서도' 멋지게 분리된 아키텍처로 소프트웨어 프로젝트를 진행해 결과물을 재빨리 출시한 후, 기반 구조를 추가하며 조금씩 확장해 나가도 괜찮다는 말이다. 

세계 최대 웹 사이트들은 고도의 자료 캐싱, 보안, 가상화 등을 이용해 아주 높은 가용성과 성능을 효율적이고도 유연하게 달성했다.

설계가 최대한 분리되어 각 추상화 수준과 범위에서 코드가 적당히 단순하기 때문이다.

프로젝트를 시작할 때는 일반적인 범위, 목표, 일정은 물론이고 결과로 내놓을 시스템의 일반적인 구조도 생각해야 한다.

하지만 변하는 환경에 대처해 진로를 변경할 능력도 반드시 유지해야 한다.

지금까지 한 이야기를 요약하면 다음과 같다.

<pre>최선의 시스템 구조는 각기 POJO (또는 다른) 객체로 구현되는 모듈화된 관심사 영역(도메인)으로 구성된다. 
이렇게 서로 다른 영역은 해당 영역 코드에 최소한의 영향을 미치는 관점이나 유사한 도구를 사용해 통합한다. 이런 구조 역시 코드와 마찬가지로 테스트 주도 기법을 적용할 수 있다.</pre>

## 의사 결정을 최적화하라

모듈을 나누고 관심사를 분리하면 지연적인 관리와 결정이 가능해진다. 아주 큰 시스템에서는 한 사람이 모든 결정을 내리기 어렵다. <br>
가능한 마지막 순간까지 결정을 미루는 방법이 최선이라는 사실을 까먹곤 한다.
게으르거나 무책임해서가 아니다. 최대한 정보를 모아 최선의 결정을 내리기 위해서다. 
성급한 결정은 불충분한 지식으로 내린 결정이다. 너무 일찍 결정하면 고객 피드백을 더 모으고, 프로젝트를 더 고민하고, 구현 방안을 더 탐험할 기회가 사라진다.
<pre>관심사를 모듈로 분리한 POJO 시스템은 기민함을 제공한다. 이런 기민함 덕택에 최신 정보에 기반해 최선의 시점에 최적의 결정을 내리기가 쉬워진다. 또한 결정의 복잡성도 줄어든다.</pre>

## 시스템은 도메인 특화 언어가 필요하다

대다수 도메인과 마찬가지로, 건축 분야 역시 필수적인 정보를 명료하고 정확하 게 전달하는 어휘, 관용구, 패턴이 풍부하다.
소프트웨어 분야에서도 최근 들어 DSL(Domain- Specific Language)이 새롭게 조명 받기 시작했다. 
DSL은 간단한 스크립트 언어나 표준 언어로 구현한 API를 가리킨다. DSL로 짠 코드는 도메인 전문가가 작성한 구조적인 산문처럼 읽힌다.
좋은 DSI은 도메인 개념과 그 개념을 구현한 코드 사이에 존재하는 '의사소통 간극'을 줄여준다.
애자일 기법이 팀과 프로젝트 이해관계자 사이에 의사소통 간 극을 줄여주듯이 말이다. 도메인 전문가가 사용하는 언어로 도메인 논리를 구현 하면 도메인을 잘못 구현할 가능성이 줄어든다.
효과적으로 사용한다면 DSL은 추상화 수준을 코드 관용구나 디자인 패턴 이상으로 끌어올린다. 그래서 개발자가 적절한 추상화 수준에서 코드 의도를 표현 할 수 있다.
<pre>도메인 특화 언어(Domain-Specific Language, DSL)를 사용하면 고차원 정책에서 저차원 세부사항에 이르기까지 모든 추상화 수준과 모든 도메인을 POJO로 표현할 수 있다 </pre>

## 결론

시스템 역시 깨끗해야 한다. 깨끗하지 못한 아키텍처는 도메인 논리를 흐리며 기민성을 떨어뜨린다. 도메인 놀리가 흐려지면 제품 품질이 떨어진다.
버그가 숨이들기 쉬워지고, 스토리를 구현하기 어려워지는 탓이다. 기민성이 떨어지면 생산성이 낮아져 TDD가 제공하는 장점이 사라진다.
모든 추상화 단계에서 의도는 명학히 표현해야 한다. 그러려면 POJO를 작성하고 관점 혹은 관점과 유사한 메커니즘을 사용해 각 구현 관심사를 분리해야한다.
시스템을 설계하든 개별 모듈을 설계하든, 실제로 **돌아가는 가장 단순한 수단** 을 사용해야 한다는 사실을 명심하자.

