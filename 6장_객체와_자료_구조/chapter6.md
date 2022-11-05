# 6장 객체와 자료 구조

변수를 비공개(private)로 정의하는 이유는 남들이 변수에 의존하지 않게 만들고 싶어서다.

그렇다면 왜 조회/설정 함수를 당연하게 공개해 비공개 변수를 외부에 노출할까?

## 자료 추상화

목록 6-1과 목록 6-2의 차이를 살펴보자.

두 클래스 모두 2차원 점을 표현한다.

그런데 한 클래스는 구현을 외부로 노출하고 다른 클래스는 구현을 완전히 숨긴다.

```swift
// 목록 6-1 구체적인 Point 클래스
public class Point {
    let x: Double
    let y: Double
}
```

```swift
// 목록 6-2 추상적인 Point 클래스
public protocol Point {
    func getX() -> Double
    func getY() -> Double
    func setCartesian(x: Double, y: Double)
    func getR() -> Double
    func getTheta() -> Double
    func setPolar(r: Double, theta: Double)
}
```

정말 멋지게도, 목록 6-2는 점이 직교좌표계를 사용하는지 극좌표계를 사용하는지 알 길이 없다.

둘다 아닐지도 모른다! 그럼에도 불구하고 **인터페이스(프로토콜)는 자료 구조를 명백하게 표현한다.**

사실 목록 6-2는 자료 구조 이상을 표현한다. 클래스 메소드가 접근 정책을 강제한다. 좌표를 읽을 때는 각 값을 개별적으로 읽어야 한다. 하지만 좌표를 설정할 때는 두 값을 한꺼번에 설정해야 한다.

반면 목록 6-1은 확실히 직교좌표계를 사용한다. 또한 개별적으로 좌표값을 읽고 설정하게 강제한다. 목록 6-1은 구현을 노출한다. 변수를 private으로 선언하더라도 각 값마다 조회함수와 설정함수를 제공한다면 구현을 외부로 노출하는 셈이다.

변수 사이에 **함수라는 계층을 넣는다고 구현이 저절로 감춰지지는 않는다.** 구현을 감추려면 추상화가 필요하다! 그저 (형식 논리에 치우쳐) 조회 함수와 설정 함수로 변수를 다룬다고 클래스가 되지는 않는다. 그보다는 추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.

목록 6-3은 자동차 연료 상태를 구체적인 숫자 값으로 알려주고 목록 6-2는 자동차 연료 상태를 백분율이라는 추상적인 개념으로 알려준다.

```swift
// 목록 6-3 구체적인 Vehicle 클래스
public class Vehicle {
    func getFuelTankCapacityInGallons() -> Double
    func getGallonsOfGasoline() -> Double
}
```

```swift
// 목록 6-4 추상적인 Vehicle 클래스
public protocol Vehicle {
    func getPercentFuelRemaining() -> Double
}
```

목록 6-2, 목록 6-4가 목록 6-1, 목록 6-3보다 더 좋다. 자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다. **인터페이스나 조회/설정 함수만으로 추상화가 이뤄지지는 않는다.** 개발자는 객체가 포함하는 자료를 표현할 가장 좋은 방법을 심각하게 고민해야 한다. 아무 생각 없이 조회/설정 함수를 추가하는 방법이 가장 나쁘다.

## 자료/객체 비대칭

위 두 예제는 객체와 자료 구조 사이에 벌어진 차이를 보여준다. 객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다. 자료 구조는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다. 문단을 처음부터 다시 읽어보기 바란다. **두 정의는 본질적으로 상반된다.** 두 개념은 사실상 정반대다. 사소한 차이로 보일지 모르지만 **그 차이가 미치는 영향은 굉장하다.**

예를 들어, 목록 6-5를 살펴보자. 목록 6-5는 절차적인 도형 클래스다. Geometry 클래스는 세 가지 도형 클래스를 다룬다. 각 도형 클래스는 간단한 자료 구조다. 즉, 아무 메소드도 제공하지 않는다. 도형이 동작하는 방식은 Geometry 클래스에서 구현한다.

```swift
// 목록 6-5 절차적인 도형
public class Square {
    public var topLeft: CGPoint
    public var side: CGFloat
}

public class Rectangle {
    public var topLeft: CGPoint
    public var height: CGFloat
    public var width: CGFloat
}

public class Circle {
    public var center: CGPoint
    public var radius: CGFloat
}

public class Geometry {
    public let PI: CGFloat = 3.141592653589793
    
    public func area(shape: Object) throws -> Double {
        if shape is Square {
            let s = shape as! Sqaure
            return s.side * s.side
        } else if shape is Rectangle {
            let r = shape as! Rectangle
            return r.height * r.width
        } else if shape is Circle {
            let c = shape as! Circle
            return PI * c.radius * c.radius
        }
        throw NoSuchShapeError
    }
}
```

위 코드가 절차적인 클래스가 비판한다면 맞는 말이다.

하지만 Geometry 클래스에 둘레 길이를 구하는 perimeter() 함수를 추가하고 싶다면? 도형 클래스는 아무 영향도 받지 않는다! 도형 클래스에 의존하는 다른 클래스도 마찬가지다! 반대로 새 도형을 추가하고 싶다면? Geometry 클래스에 속한 함수를 모두 고쳐야 한다. 두 조건은 완전히 정반대다.

이번에는 목록 6-6을 살펴보자. 객체 지향적인 도형 클래스다. 여기서 area()는 다형 메소드다. Geometry 클래스는 필요 없다. 그러므로 새 도형을 추가해도 기존 함수에 아무런 영향을 미치지 않는다. 반면 새 함수를 추가하고 싶다면 도형 클래스를 전부 고쳐야 한다.

```swift
// 목록 6-6 다형적인 도형
public class Square: Shape {
    private var topLeft: CGPoint
    private var side: CGFloat
    
    public func area() -> CGFloat {
        return side * side
    }
}

public class Rectangle: Shape {
    private var topLeft: CGPoint
    private var height: CGFloat
    private var width: CGFloat
    
    public func area() -> CGFloat {
        return height * width
    }
}

public class Circle: Shape {
    private var center: CGPoint
    private var radius: CGFloat
    public static let PI = 3.141592653589793
    
    public func area() -> CGFloat {
        return PI * radius * radius
    }
}
```

목록 6-5와 목록 6-6은 상호 보완적인 특질이 있다. 사실상 반대다! 그래서  객체와 자료 구조는 근본적으로 양분된다.

> (자료 구조를 사용하는) 절차적인 코드는 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다. 반면, 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.
> 

반대쪽도 참이다.

> 절차적인 코드는 새로운 자료 구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다. 객체 지향 코드는 새로운 함수를 추가하기 어렵다. 그러려면 모든 클래스를 고쳐야 한다.
> 

다시 말해, **객체 지향 코드에서 어려운 변경은 절차적인 코드에서 쉬우며, 절차적인 코드에서 어려운 변경은 객체 지향 코드에서 쉽다!**

분별있는 프로그래머는 모든 것이 객체라는 생각이 **미신**임을 잘 안다. 때로는 단순한 자료 구조와 절차적인 코드가 가장 적합한 상황도 있다.

## 디미터 법칙

모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙이다.

앞 절에서 봤듯이, 객체는 자료를 숨기고 함수를 공개한다. 즉, 객체는 조회 함수로 내부 구조를 공개하면 안 된다는 의미다. **그러면 내부 구조를 (숨기지 않고) 노출하는 셈이니까.**

좀 더 정확히 표현하자면, 디미터 법칙은 “클래스 C의 메소드 f는 다음과 같은 객체의 메소드만 호출해야 한다.”고 주장한다.

- 클래스 C
- f가 생성한 객체
- f 인수로 넘어온 객체
- C 인스턴스 변수에 저장된 객체

하지만 위 객체에서 허용된 메소드가 반환하는 객체의 메소드는 호출하면 안 된다. 다시 말해, **낯선 사람은 경계하고 친구랑만 놀라는 의미다.**

아래 코드는 디미터 법칙을 어기는 듯이 보인다. getOptions() 함수가 반환하는 객체의 getScratchDir() 함수를 호출한 후 getScratchDir() 함수가 반환하는 객체의 getAbsolutePath() 함수를 호출하기 때문이다.

`final String outputDir = txt.getOptions ().getScratchDir().getAbsolutePath()`

### 기차 충돌

흔히 위와 같은 코드를 **기차 충돌**이라 부른다. 여러 객차가 한 줄로 이어진 기차처럼 보이기 때문이다. 일반적으로 조잡하다 여겨지는 방식이므로 피하는 편이 좋다.

위 코드를 다음과 같이 나누는 편이 좋다.

```java
Options opts = txt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

그렇다면 이제 위의 코드 예제는 둘 다 디미터 법칙을 위반할까? 확실히 위 코드 형태로 구현된 함수는 `ctxt` 객체가 `Options`을 포함하며, `Options`가 `ScratchDir`을 포함하며, `ScratchDir`이 `AbsolutePath`를 포함한 다는 사실을 안다. 함수 하나가 아는 지식이 굉장히 많다. 위 코드를 사용하는 함수는 많은 객체를 탐색할 줄 안다는 말이다.

위 예제가 디미터 법칙을 위반하는 지 여부는 **`ctxt`, `Options`, `ScratchDir` 이 객체인지 아니면 자료 구조인지에 달렸다.** 객체라면 내부 구조를 숨겨야 하므로 확실히 디미터 법칙에 위반한다. 반면, 자료 구조라면 당연히 내부 구조를 노출하므로 디미터 법칙이 적용되지 않는다.

그런데 위 예제는 조회 함수를 사용하는 바람에 혼란을 일으킨다. 다음과 같이 바꾸면 디미터 법칙을 거론할 필요가 없어진다.

`final String outputDir = ctxt.options.scratchDir.absolutePath;`

**자료 구조는 무조건 함수 없이 공개 변수만 포함하고 객체는 비공개 변수와 공개 함수를 포함한다면, 문제는 훨씬 간단하리라.** 하지만 단순한 자료 구조에도 조회 함수와 설정 함수를 정의하라 요구하는 프레임워크와 표준이 존재한다.

### 잡종 구조

이런 혼란으로 말미암아 때때로 객체와 자료 구조가 절반으로 구성된 혼혈 잡종 구조가 나온다. 잡종 구조는 중요한 기능을 수행하는 함수도 있고, 공개 변수나 공개 조회/설정 함수도 있다. 공개 조회/설정 함수는 비공개 변수를 그대로 노출한다. 

이런 잡종 구조는 새로운 함수는 물론이고 새로운 자료 구조도 추가하기 어렵다. **양쪽 세상에서 단점만 모아놓은 구조다.** 그러므로 잡종 구조는 피하자!

### 구조체 감추기

만약 `ctxt`, `options`, `scratchDir`이 진짜 객체라면? 그렇다면 기차 충돌로 만들면 안된다. **객체라면 내부 구조를 감춰야 하니까.**

그렇다면 임시 디렉토리의 절대 경로를 어떻게 얻어야 좋을까?

다음 두 코드를 살펴보자.

`ctxt.getAbsolutePathofScratchDirectoryOption();`

`ctxt.getScratchDirectoryOption().getAbsolutePath()`

첫 번째 방법은 ctxt 객체에 공개해야 하는 메소드가 너무 많아진다. 두 번째 방법은 객체가 아니라 자료 구조를 반환한다고 가정한다. 어느 방법도 썩 내키지 않는다.

ctxt가 객체라면 **뭔가를 하라고** 말해야지 속을 드러내라고 말하면 안 된다. 임시 디렉토리의 절대 경로가 왜 필요할까? 절대 경로를 얻어 어디에 쓸려고? 다음은 같은 모듈에서 가져온 코드다.

```java
String outFile = outputDir +"/" +className.replace('.', '/') + "class"; 
FileOutputStream fout = new FileOutputStream(outFile); 
BufferedOutputStream bos = new BufferedOutputStream(fout);
```

위 코드를 살펴보면, 임시 디렉터리의 절대 경로를 얻으려는 이유가 임시 파일을 생성하기 위해서다.

그렇다면 ctxt 객체에 임시 파일을 생성하라고 시키면 어떨까?

```java
BufferedOutputStream bos = txt.createScratchFileStream(classFileName);
```

객체에게 맡기기에 적당한 임무로 보인다! ctxt 내부 구조를 드러내지 않으며. 모듈에서 해당 함수는 자신이 몰라야 하는 여러 객체를 탐색할 필요가 없다. 따라서 디미터 법칙을 위반하지 않는다.

## 자료 전달 객체

자료 구조체의 전형적인 형태는 공개 변수만 있고 함수가 없는 클래스다. 이런 자료 구조체를 때로는 자료 전달 객체(DTO: Data Transfer Object)라 한다. DTO는 굉장히 유용한 구조체다. (클린 아키텍처 예제에서도 DTO를 사용하죠)

특히 DB와 통신하거나 소켓에서 받은 메세지의 구문을 분석할 때 유용하다. 흔히 DTO는 DB에 저장된 가공되지 않은 정보를 애플리케이션 코드에서 사용할 객체로 변환하는 일련의 단계에서 가장 처음 사용하는 구조체다.

좀 더 일반적인 형태는 빈(bean) 구조다. 예제는 목록 6-7을 참조한다. 빈은 비공개 변수를 조회/설정 함수로 조작한다. 일종의 사이비 캡슐화(?)로, 일부 OO 순수주의자나 만족시킬 뿐 별다른 이익을 제공하지 않는다.

```swift
// 목록 6-7 Address.swift
public class Address {
    private let street: String
    private let streetExtra: String
    private let city: String
    private let state: String
    private let zip: String
    
    init(street: String, streetExtra: String, city: String, state: String, zip: String) {
        self.street = street
        self.streetExtra = streetExtra
        self.city = city
        self.state = state
        self.zip = zip
    }
    
    public func getStreet() -> String {
        return street
    }
    
    public func getStreetExtra() -> String {
        return streetExtra
    }
    
    public func getCity() -> String {
        return city
    }
    
    public func getState() -> String {
        return state
    }
    
    public func getZip() -> String {
        return zip
    }
}
```

### 활성 레코드

활성 레코드는 DTO의 특수한 형태다. 공개 변수가 있거나 비공개 변수에 조회/설정 함수가 있는 자료 구조지만, 대개 save나 find와 같은 탐색 함수도 제공한다. 활성 레코드는 DB 테이블이나 다른 소스에서 자료를 직접 변환한 결과다.

불행히도 활성 레코드에 비즈니스 규칙 메소드를 추가해 이런 자료 구조를 객체로 취급하는 개발자가 흔하다. 하지만 이는 바람직하지 않다. 그러면 잡종 구조가 나오기 때문이다.

해결책은 당연하다. 활성 레코드는 자료 구조로 취급한다. 비즈니스 규칙을 담으면서 내부 자료를 숨기는 객체는 따로 생성한다.

## 결론

객체는 동작을 공개하고 자료를 숨긴다. 그래서 기존 동작을 변경하지 않으면서 새 객체 타입을 추가하기는 쉬운 반면, 기존 객체에 새 동작을 추가하기는 어렵다. 자료 구조는 별다른 동작 없이 자료를 노출한다. 그래서 기존 자료 구조에 새 동작을 추가하기는 쉬우나, 기존 함수에 새 자료 구조를 추가하기는 어렵다.

(어떤) 시스템을 구현할 때, 새로운 자료 타입을 추가하는 유연성이 필요하면 객체가 더 적합하다. 다른 경우로 새로운 동작을 추가하는 유연성이 필요하면 자료 구조와 절차적인 코드가 더 적합하다. **우수한 소프트웨어 개발자는 편견없이 이 사실을 이해해 직면한 문제에 최적인 해결책을 선택한다.**