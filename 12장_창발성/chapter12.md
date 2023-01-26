# 12장 창발성

## 창발적 설계로 깔끔한 코드를 구현하자
착실하게 따르기만 하면 우수한 설계가 나오는 간단한 규칙 네가지를 알아보자. 켄트 백은 다음 규칙을 따르면 설계는 "단순하다"고 말한다.  

아래 목록은 중요도 순이다.  
  
1. 모든 테스트를 실행한다.
2. 중복을 없앤다.
3. 프로그래머 의도를 표현한다.
4. 클래스와 메서드의 수를 최소로 줄인다.

### 1. 모든 테스트를 실행하라
무엇보다 먼저, 설계는 의도한 대로 돌아가는 시스템을 내놓아야햔다. 문서로는 시스템을 완벽히 설계했지만 시스템이 의도한대로 돌아가는지 검증할 간단한 방법이 없다면, 문서 작성을 위해 투자한 노력에 대한 가치는 인정받기 힘들다.  
논란의 여지는 있지만, 테스트가 불가능한 시스템은 절대 출시하면 안된다.  
**테스트가 가능한 시스템을 만들려고 애쓰면 설계 품질이 더불어 높아진다.** 크기가 작고 목적 하나만 수행하는 클래스가 나온다. SRP를 준수하는 클래스는 테스트가 훨씬 더 쉽다. 테스트 케이스가 많을수록 개발자는 테스트가 쉽게 코드를 작성한다. 따라서 철저한 테스트가 가능한 시스템을 만들면 더 나은 설계가 얻어진다.  
결합도가 높으면 테스트 케이스를 작성하기 어렵다. 그러므로 테스트 케이스를 많이 작성할수록 개발자는 DIP와 같은 원칙을 적용하고 의존성 주입, 인터페이스, 추상화 등과 같은 도구를 사용해 결합도를 낮춘다.  
"테스트 케이스를 만들고 계속 돌려라"라는 간단하고 단순한 규칙을 따르면 시스템은 낮은 결합도와 높은 응집력이라는, 객체 지향 방법론이 지향하는 목표를 저절로 달성한다.

### 리팩터링
테스트 케이스를 모두 작성했다면 이제 코드와 클래스를 정리해도 괜찮다. 구체적으로는 코드를 점진적으로 리팩터링 해나간다. 코드를 몇 줄 추가할 때마다 잠시 멈추고 설계를 조감한다. 새로 추가하는 코드가 설계 품질을 낮춘다면 깔끔히 정리한 후 테스트 케이스를 돌려 기존 기능을 깨뜨리지 않았다는 사실을 확인한다. 테스트 케이스가 있다면 시스템이 깨질까 걱정할 필요가 없다.  
리팩터링 단계에서는 소프트웨어 설계 품질을 높이는 기법이라면 무엇이든 적용해도 괜찮다. 응집도를 높이고 결합도를 낮추고, 관심사를 분리하고, 시스템 관심사를 모듈로 나누고, 함수와 클래스의 크기를 줄이고, 더 나은 이름을 선택하는 등의 더 나은 기법을 동원한다. 또한 이 단계에서는 단순한 설계 규칙 중 나머지 3개(2,3,4번)를 적용하는 단계이기도 하다.

### 2. 중복을 없애라
우수한 설계에서 중복은 커다란 적이다. 중복은 추가 작업, 추가 위험, 불필요한 복잡도를 뜻한다.  
중복은 여러 가지 형태로 표출된다. 구현 중복도 중복의 한 형태이다.
예시를 살펴보자.  

``` swift
// 따로 구현
func size() -> Int {}
func isEmpty() -> Bool {}

// size 메서드 이용
func isEmpty() -> Bool {
    return 0 == size()
}
```

각 메서드를 따로 구현하는 방법도 있지만, `isEmpty` 메서드에서 `size` 메서드를 이용하면 코드를 중복해 구현할 필요가 없어진다.

다음 예시를 살펴보자.
``` swift
public func scaleToOneDimension(_ desiredDimension: Float, _ imageDimension: Float) {
    if abs(desiredDimension - imageDimension) < errorThreshold {
        return
    }
    
    var scalingFactor: Float = desiredDimension / imageDimension
    scalingFactor = Float(floor(scalingFactor * 100) * 0.01)
    
    var newImage: RenderedOp = ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor)
    image.dispose()
    image = newImage
}

public func rotate(_ degrees: Int) {
    var newImage: RenderedOp = ImageUtilities.getRotatedImage(image, degrees)
    image.dispose()
    image = newImage
}
```
`scaleToOneDimension` 메서드와 `rotate` 메서드를 살펴보면 일부 코드가 동일하다. 다음과 같이 코드를 정리해 중복을 제거한다.

``` swift
public func scaleToOneDimension(_ desiredDimension: Float, _ imageDimension: Float) {
    if abs(desiredDimension - imageDimension) < errorThreshold {
        return
    }
    
    var scalingFactor: Float = desiredDimension / imageDimension
    scalingFactor = Float(floor(scalingFactor * 100) * 0.01)
    replaceImage(ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor))
}

public func rotate(_ degrees: Int) {
    replaceImage(ImageUtilities.getRotatedImage(image, degrees))
}

private func replaceImage(_ newImage: RenderedOp) {
    image.dispose()
    image = newImage
}
```
공통적인 코드를 새 메서드로 뽑고 보니 클래스가 SRP를 위반한다. 그러므로 새로 만든 `replaceImage` 메서드를 다른 클래스로 옮겨 새 메서드의 가시성을 높이자. 그러면 다른 팀원이 새 메서드를 좀 더 추상화해 다른 맥락에서 재사용할 기회를 포착할 수도 있다.  
이런 소규모 재사용은 시스템 복잡도를 극적으로 줄여준다. 소규모 재사용을 제대로 익혀야 대규모 재사용이 가능하다. `Template Method` 패턴은 고차원의 중복을 제거할 목적으로 자주 사용하는 기법이다. 예시를 살펴보자.  

``` swift
public class VacationPolicy {
    public func accrueUSDivisionVacation() {
        // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
        // ...
        // 휴가 일수가 미국 최소 법정 일수를 만족하는지 학인하는 코드
        // ...
        // 휴가 일수를 급여 대장에 적용하는 코드
        // ...
    }
    
    public func accrueEUDivisionVacation() {
        // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
        // ...
        //  휴가일수가 유럽연합 최소 법정 일수를 만족하는지 학인하는 코드
        // ...
        // 휴가일수를 급여 대장에 적용하는 코드
        // ...
    }
}

// Template Method 사용
public class VacationPolicy{
    public func accrueVacation() {
        calculateBaseVacationHours()
        alterForLegalMinimums()
        applyToPayroll()
    }
    
    private func calculateBaseVacationHours() {}
    fileprivate func  alterForLegalMinimums() {}
    private func applyToPayroll() {}

}

class USVacationPolicy: VacationPolicy {
    override func alterForLegalMinimums() {
        // 미국 최소 법정 일수를 사용한다.
    }
}

class EUVacationPolicy: VacationPolicy {
    override func alterForLegalMinimums() {
        // 유럽연합 최소 법정 일수를 사용한다.
    }
}
```
하위 클래스는 중복되지 않는 정보만을 제공해 `accrueVacation` 알고리즘에서 빠진 구멍을 메운다.

### 3. 표현하라
아마 우리 대다수는 엉망인 코드를 접한 경험, 내놓은 경험이 있으리라. 자신이 이해하는 코드를 짜기는 쉽다. 하지만 나중에 코드를 유지보수할 사람이 짜는 사람만큼이나 깊게 이해할 가능성은 희박하다.  
소프트웨어 프로젝트 비용 중 대다수는 장기적인 유지보수에 들어간다. 코드를 변경하면서 버그의 싹을 심지 않으려면 유지보수 개발자가 시스템을 제대로 이해해야한다. 하지만 시스템이 점차 복잡해지면서 유지보수 개발자가 시스템을 이해하느라 보내는 시간이 늘어나고 코드를 오해할 가능성도 점차 커진다. 그러므로 **개발자는 코드의 의도를 분명히 표현해야한다.** 그래야 결함이 줄어들고 유지보수 비용이 적게 든다.  
이를 위해 첫째, **좋은 이름을 선택한다.** 
둘째, **함수와 클래스의 크기를 가능한 줄인다.** 작은 함수와 클래스는 이름 짓기도 쉽고, 구현하기도 쉽고, 이해하기도 쉽다.  
셋째, **표준 명칭을 사용한다.** 예를 들어, 디자인 패턴은 의사소통과 표현력 강화가 주요 목적이다. 클래스가 Command, Visitor와 같은 패턴을 사용해 구현된다면 클래스 이름에 패턴 이름을 넣어준다. 그러면 다른 개발자가 설계 의도를 이해하기 쉬워진다.  
넷째, **단위 테스트 케이스를 꼼꼼히 작성한다.** 테스트 케이스는 소위 "예제로 보여주는 문서"이다. 다시 말해, 잘 만든 테스트 케이스를 읽어보면 클래스의 기능이 한눈에 들어온다.  

하지만 표현력을 높이는 가장 중요한 방법은 노력이다. 흔히 코드만 돌린 후 다음 문제로 직행하는 사례가 너무 흔하다. 나중에 읽을 사람을 고려해 조금이라도 읽기 쉽게 만들려는 충분한 고민은 거의 찾기 힘들다. 하지만 나중에 읽을 사람은 바로 자신일 가능성이 높다는 것을 명심하자.  
그러므로 함수와 클래스에 조금 더 시간을 투자하여, 더 나은 이름을 선택하고 큰 함수를 작은 함수 여럿으로 나누고 조금 더 주의를 기울이자. 

### 4. 클래스와 메서드 수를 최소로 줄여라
중복을 제거하고, 의도를 표현하고, SRP를 준수한다는 기본적인 개념도 극단으로 치달으면 득보다 실이 많아진다. 클래스와 메서드의 크기를 줄이고자 조그만 클래스와 메서드를 수없이 만드는 사례도 없지 않다. 따라서 이 규칙은 함수와 클래스 수를 가능한 줄이라고 제안한다.  
때로는 무의미하고 독단적인 정책 탓에 함수와 클래스 수가 늘어나기도 한다. 클래스마다 무조건 인터페이스를 생성하라고 요구하는 경우, 자료 클래스와 동작 클래스는 무조건 분리해야한다고 주장하는 개발자가 그 예이다.  
목표는 함수와 클래스의 크기를 작게 유지하면서 동시에 시스템 크기도 작게 유지하는 데에 있다. 하지만 이 규칙은 해당 챕터의 설계 규칙 네가지 중 가장 우선순위가 낮다. 다시 말해, 클래스와 함수 수를 줄이는 것도 중요하지만 테스트 케이스를 만들고 중복을 제거하고 의도를 표현하는 작업이 더 중요하다는 것이다.

## 결론
경험을 대신할 단순한 개발 기법은 당연히 없다. 하지만 이 책에서 소개하는 기법은 저자들이 수십 년 동안 쌓은 경험의 정수다. 앞서 이야기한 설계 규칙을 따른다면 우수한 기법과 원칙을 단번에 활용할 수 있다.