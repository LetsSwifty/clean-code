# 2장 의미있는 이름

의미 있는 이름을 붙여야 한다. 

**왜? 결국 독자(개발자)가 읽는 것이고 이해가 잘 되면 일의 능률이 오르니깐 말이다.**

## 의도를 분명히 밝혀라

축약어를 사용하지 말고 의도가 드러나게 변수 이름을 지어라. 다른 사람이 보기에도 의도가 분명한 변수 및 메서드 이름이라면 그렇지 않은 경우보다 훨씬 생산성이 증가하고 심적으로도 편안하다. **따라서 이름을 주의 깊게 살펴 더 나은 이름이 떠오르면 개선하자.**

```swift
let d: Int // 경과 시간(단위: 날짜)
// days?, duration? d 가 무엇을 정확히 모른다. 경과 시간이나 날짜라는 느낌이 안든다.
```
**따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.**

```swift
var elapsdTimeInDays: Int
var daysSinceCreation: Int
var daysSinceModification: Int
var fileAgeInDays: Int
```

**의도가 드러나는 이름을 사용하면 코드 이해와 변경이 쉬워진다.**

```swift
func getThem() -> Array<[Int]> {
	var list1 = Array<[Int]>()
	for x in self.theList {
		if x[0] == 4 {
			list1.append(x)
		}
	}
	return list1
}
```

코드의 단순성이 아니라 코드의 함축성이다. 다시말해, 코드 맥락이 코드 자체에 명시적으로 드러나지 않기 때문에 코드가 하는 일을 짐작하기 어렵다.

위 코드는 암암리에 독자가 다음과 같은 정보를 안다고 가정한다.

1. theList에 무엇이 들었는가?
2. theList에서 0번째 값이 어째서 중요한가?
3. 값 4는 무슨 의미인가?
4. 함수가 반환하는 리스트 list1을 어떻게 사용하는가?

위 코드 샘플엔 이와 같은 정보가 드러나지 않는다. 하지만 정보 제공을 **충분히 가능했었다.** 지뢰찾기 게임을 만든다고 가정하자. 그러면 theList가 게임판이라는 사실을 안다. theList를 gameBoard로 바꿔보자.

```swift
func getFlaggedCells() -> [[Int]] {
    var flaggedCells = [[Int]]()
    for cell in self.gameBoard {
        if cell[Self.STATUS_VALUE] == Self.FLAGGED {
            flaggedCells.append(cell)
        }
    }
    return flaggedCells
}
```

배열에서 0번째 값은 칸 상태를 뜻한다. 값 4는 깃발이 꽂힌 상태를 가리킨다. 각 개념에 이름만 붙여도 코드가 상당히 나아진다.

```swift
func getFlaggedCells() -> [Cell] {
    var flaggedCells = [Cell]()
    for cell in self.gameBoard {
        if cell.isFlagged {
            flaggedCells.append(cell)
        }
    }
    return flaggedCells
}
```

한 걸음 더 나아가, int 배열을 사용하는 대신, 칸을 간단히 클래스로 만들어도 되겠다. isFlagged라는 좀 더 명시적인 함수를 사용해 FALGGED라는 상수를 감춰도 좋겠다.

=> 단순히 이름만 고쳤는데도 함수가 하는 일을 이해하기 쉬워졌다. 이것이 **좋은 이름이 주는 (엄청난) 위력이다.**

## 그릇된 정보를 피하라
프로그래머는 코드에 그릇된 단서를 남겨서는 안된다.

예를 들어, hp, aix, sco는 변수이름으로 적합하지 않다. 유닉스 플랫폼이나 유닉스 변종을 가리키는 이름이기 때문이다.

직각삼각형의 빗변(hypotenuse)을 구현할 때는 hp가 훌륭한 약어로 보일지라도 hp라는 변수는 독자에게 그릇된 정보를 제공한다.

여러 계정을 그룹으로 묶을 때, 실제 List가 아니라면, `accountList`라 명명하지 않는다. 계정을 담는 컨테이너가 실제 List가 아니라면 프로그래머에게 그릇된 정보를 제공하는 셈이다.

그러므로 `accountGroup`, `bunchOfAccounts`, 아니면 단순히 Accounts라 명명한다.

서로 흡사한 이름을 사용하지 않도록 주의한다. 한 모듈에서 `XYZControllerForEfficientHandlingOfStrings`라는 이름을 사용하고, 조금 떨어진 모듈에서 `XYZControllerForEfficientStorageOfStrings`라는 이름을 사용한다면? 차이를 알아챘는가? 두 단어는 겁나게 비슷하다.

유사한 개념은 유사한 표기법을 사용한다. 이것도 **정보**다. 일관성이 떨어지는 표기법은 **그릇된 정보**다. IDE에서 자동 완성 기능을 사용할 때 각 개념 차이가 명백히 드러난다면 굉장히 유용해진다. 

## 의미있게 구분하라

컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하는 프로그래머는 **스스로 문제를 일으킨다.**

* 예를 들어, 동일한 범위 안에서는 다른 두 개념에 같은 이름을 사용하지 못한다. 그래서 프로그래머가 한쪽 이름을 마음대로 바꾸고 싶은 유혹에 빠지고 철자만 살짝 바꾼다. 나중에 철자 오류를 고치는 순간 컴파일이 불가능한 상황에 빠진다.

* 컴파일러를 통과할지라도 연속된 숫자를 덧붙이거나 불용어(noise word)를 추가하는 방식은 적절하지 못하다.
* 연속적인 숫자를 덧붙인 이름(a1, a2, ..., aN)은 아무런 정보를 제공하지 못하는 이름일 뿐이다.

```swift
func copyChars(a1: [Character], a2: [Character]) {
    var a1 = a1, a2 = a2
    for i in 0..<a1.count {
        a2[i] = a1[i]
    }
}
```
인자를 `source`, `destination`이라 이름 지으니 확실히 명확하다.

```swift
func copyChars(source: [Character], destination: [Character]) {
    var source = source, destination = destination
    for i in 0..<source.count {
        destination[i] = source[i]
    }
}
```

* 불용어를 추가한 이름 역시 아무런 정보도 제공하지 못한다. `Product`라는 클래스가 있다고 가정하자. 다른 클래스를 `ProductInfo` 혹은 `ProductData`라 부른다면 개념을 구분하지 않은 채 이름만 달리한 경우다. Info나 Data는 a, an, the와 마찬가지로 의미가 불분명한 불용어다.

* 불용어는 중복이다.
	* `NameString` -> `Name`
	* `Customer`, `CustomerObject` 둘의 차이를 알곘는가? 차이를 구분하도록 중복되지 않게 이름을 짓자

## 발음하기 쉬운 이름을 사용하라
여기 문단은 제목만 봐도 예제코드는 굳이 따로 적을 필요도 Swift로 변경하지 않아도 충분히 이해할 수 있다고 생각되어 생략...

## 검색하기 쉬운 이름을 사용하라
문자 하나를 사용하는 이름과 상수는 텍스트 코드에서 쉽게 눈이 띄지 않는다는 문제점이 있다. 이런 관점에서 긴 이름이 짧은 이름보다 좋다. 검색하기 쉬운 이름이 상수보다 좋다.
* `MAX_CLASSES_PER_STUDENT` 과 숫자 7을 비교해보라. 무엇이 더 검색하기 쉬운가?
* 아래 코드 중 두번째 코드가 훨씬 검색하기 쉽다. **위 코드에서 sum이 별로 유용하진 않으나 최소한 검색이 가능하다(이름 때문에 함수가 길어지는 것보다 검색하기 쉬운 이름을 갖는 것이 훨씬 유용하다).**

```swift
for j in 0..<34 {
    s += (t[j]*4)/5;
}
```
```swift
let realDaysPerIdealDay = 4
let workDaysPerWeek = 5
var sum = 0
for j in 0 ..< numberOfTasks {
    let realTaskDays = taskEstimate[j] * realDaysPerIdealDay
    let realTaskWeeks = (realTaskDays / workDaysPerWeek)
    sum += realTaskWeeks
}
```
## 인코딩을 피하라
* 문제 해결에 집중하는 개발자에게 인코딩은 **불필요한 정신적 부담**이다.
* 인코딩된 이름을 쓰지말자. 인코딩된 이름을 과거에 썼던 이유는 당시에는 컴파일러가 타입을 점검하지 않았으므로 프로그래머에게 타입을 필요할 단서가 필요해서 썼던거지, 지금은 IDE 가 타입을 기억하고 강제하므로 인코딩된 이름을 사용할 필요가 없다.
* 대개 새로운 개발자가 익힐 코드 양은 상당히 많다. 여기다 **인코딩 언어까지 익히라는 요구는 비합리적이다.** 발음하기도 어려우며 오타가 생기기도 쉽다.

### 헝가리안 표기법
과거에는 타입을 표시하기 위해 사용하였지만 최근에는 변수를 선언한 위치와 사용하는 위치가 멀지 않기도 하고, IDE는 코드를 커마일하지 않고도 타입 오류를 감지할 정도로 발전했다.

따라서, 헝가리안 표기법이나 기타 인코딩 방식은 오히려 방해가 되므로 사용하지 않는다!

```swift
let phoneString: PhoneNumber
```
### 멤버 변수 접두어
이제 멤버 변수에 `m_`이라는 접두어를 붙일 필요도 없다.

```swift
class Part {
	private var m_dsc: String
	
	func setName(name: String) {
		m_dsc = name
	}
}
```

```swift
class Part {
	var description: String
	
	func setDescription(description: String) {
		self.description = description
	}
}
```

### 인터페이스 클래스와 구현 클래스
자바의 방식과 스위프트의 방식과 다르므로 스위프트만 정리하였습니다.

* **Action enablers** : 각각의 주어진 타입에 정의된 동작을 하도록 함. 일반적으로 Equatable 처럼 "able"로 끝나는 이름을 가짐
* **Requirement definitions** : Numeric, Sequence 처럼 특정한 종류의 객체가 되기 위한 요구사항을 정함
* **Type conversion** : CustomStringConvertible, ExpressibleByStringLiteral 처럼 다양한 타입이 다른 타입으로 변환 가능하거나 원시 값 또는 리터럴을 통해 표현할 수 있음을 선언하는데 사용
* **Abstract interfaces** : 여러 타입이 구현할 수 있는 통합된 API의 역할을 함. 이를 통해 원하는 대로 구현을 교체하거나, 코드를 캡슐화 하거나, mock된 객체를 테스트에서 사용할 수 있음

## 자신의 기억력을 자랑하지 마라
URL 변수인데 이름을 r 이라고 하고 짓지 말자. 

자신의 기억력이 좋다고 자신하며 r이라고 짓지 말자. 똑똑한 프로그래머와 전문가 프로그래머 사이에서 나타나는 차이점 하나만 들자면, 전문가 프로그래머는 명료함이 최고라는 사실을 이해한다. 

전문가 프로그래머는 자신의 능력을 좋은 방향으로 사용해 남들이 이해하는 코드를 내놓는다.

## 클래스 이름
클래스 이름과 객체 이름은 명사나 명사구가 적합하다. `Customer`, `WikiPage` 등이 좋은 예다.

Manager, Processor, Data, Info 등과 같은 단어는 피하고, 동사는 사용하지 않는다.

## 메소드 이름
메소드 이름은 동사나 동사구가 적합하다. `postPayment`, `deletePage` 등이 좋은 예다.

> Swift 의 경우에만 한합니다.

* init

```swift
// bad
let complex = Complex(23.0) 
// good
let complex = Complex(fromRealNumber: 23.0)
```

init 할때는 인자 레이블을 생략하기보다는 직접적으로 명시하여 생성자에 어느 값을 넣어야 하는지, 어느 값이 넣어지는지 명확하게 보여주도록 한다.

## 한 개념에 한 단어를 사용하라
추상적인 개념 하나에 단어 하나를 선택해 이를 고수하자.

똑같은 메서드를 클래스마다 fetch, retrieve, get 으로 제각각 부르면 혼란스럽다. 어느 클래스에서 어느 이름을 썼는지 기억하기 어렵다. 따라서 동일 기능의 메서드이면 하나의 이름만 사용하자.

마찬기지로, 동일 코드 기반에 controller, manager, driver를 섞어 쓰면 혼란스럽다. DeviceManager, ProtocolController는 근본적으로 어떻게 다른가? 어째서 둘 다 Controller가 아닌가? 어째서 둘 다 Manager 가 아닌가? **이름이 다르면 독자는 당연히 클래스도 다르고 타입도 다르다고 생각한다.** 따라서 한 개념에 한 단어만 사용하자.

## 말장난을 하지 마라(한 단어를 두 개념에 사용하지 마라)
한 단어를 두 가지 목적으로 사용하지 마라. 다른 개념에 같은 단어를 사용한다면 **그것은 말장난에 불과하다.**

`add`와 `append`

* `add`가 붙는 메소드는 기존 값 두개를 더하거나 이어서 새로운 값을 만든다고 가정하자. 근데 새로 작성하는 메소드는 집합에 값 하나를 추가한다. add라는 메소드가 많으므로 일관성을 지킨답시고 add를 붙이는 **말장난을 하지말고 다른 맥락의 insert나 append라는 이름을 사용하라.**

## 해법 영역에서 가져온 이름을 사용하라
기술 개념에는 기술 이름을 붙이는 게 가장 적합한 선택이다.
모든 이름을 문제 영역(domain)에서 가져오는 정책은 현명하지 못하다. 같은 이름(도메인용으로 사용되는 이름)을 다른 이름(기술용어로 사용되는 이름)으로 오해하던 동료들이 매번 물어봐야 하기 때문이다. 따라서 부담 갖지말고 코드를 읽는 사람도 프로그래머라는 사실을 명심하고, 전산용어, 알고리즘 이름, 패턴 이름, 수학 용어등을 사용하자(해도 괜찮다).

전산용어를 사용한 에:

* VISTOR 패턴에서 따온 `AccountVisitor`
* JobQueue 개념에서 따온 `enqueue(job: Job())`

내 생각: 기술 용어와 중복되지는 않지만 다른 예에서도 도메인이름을 사용하는 것이 적절하지 못한 경우가 있다. 예를 들어 애니메이션 되는 뷰가 있다고 해보자. 해당 뷰의 아이콘 이미지가 토끼라고 변수명이나 클래스명을 `Rabbit`으로 짓는건 현명하지 못하다. 나중에 거북이로 아이콘이 바뀌면 `Turtle`이라고 이름을 바꿀 것인가? 애니메이팅 되고 있는 뷰의 이름은 `AnimationView`가 적절할 것이다.

## 문제 영역에서 가져온 이름을 사용하라
적절한 프로그래밍 용어가 없다면 문제 영역(domain)에서 이름을 가져온다. 그러면 코드를 보수하는 프로그래머가 도메인 전문가에게 의미를 물어 파악할 수 있다.

## 의미있는 맥락을 추가하라
스스로 의미가 분명한 이름도 있다. 하지만 대다수 이름은 그렇지 못하고 주변의 맥락을 파악해야 의미를 알아차리는 경우다. 

**그래서 클래스, 함수, 이름 공간에 넣어 맥락을 부여한다. 모든 방법이 실패하면 마지막 수단으로 접두어를 붙인다.** 

예를 들어, firstName, lastName, street, houseNumber, city, state, zipcode라는 변수가 있다. 변수를 훑어보면 주소라는 사실을 금방 알아챈다. 하지만 어느 메서드가 state(주)라는 변수하나만 사용하면 state(상태)와 헷갈릴 가능성이 농후하다.

**addr라는 접두어를 추가해** addrFirstName, addrLastName, addrState라 쓰면 맥락이 좀 더 분명해진다. 변수가 좀 더 큰 구조에 속하다는 사실이 적어도 독자(개발자)에게는 분명해진다. 

또 **Address라는 클래스를 생성하면 더 좋다.** 그러면 변수가 좀 더 큰 개념에 속하다는 사실이 컴파일러에게도 분명해진다(가독성을 위해서라도 응집성을 높이자).

> 맥락이 불분명한 변수

```swift
private func printGuessStatistics(candidate: Character, count: Int) {
    var number: String
    var verb: String
    var pluralModifier: String
    
    if(count == 0) {
        number = "no"
        verb = "are"
        pluralModifier = "s"
    } else if (count == 1) {
        number = "1"
        verb = "is"
        pluralModifier = ""
    } else {
        number = "\(count)"
        verb = "are"
        pluralModifier = "s"
    }
    
    let guessMessage = "There \(verb) \(number) \(candidate) \(pluralModifier)"
    print(guessMessage)
}
```

> 맥락이 분명한 변수

```swift
public class GuessStatisticsMessage {
    private var number: String
    private var verb: String
    private var pluralModifier: String
    
    init() {
        self.number = ""
        self.verb = ""
        self.pluralModifier = ""
    }
    
    private func createPluralDependentMessageParts(count: Int) {
        if(count == 0) {
            thereAreNoLetters()
        } else if(count == 1) {
            thereIsOneLetter()
        } else {
            thereAreManyLetters(count: count)
        }
    }
    
    private func thereAreManyLetters(count: Int) {
        self.number = "\(count)"
        self.verb = "are"
        self.pluralModifier = "s"
    }
        
    private func thereIsOneLetter() {
        self.number = "1"
        self.verb = "is"
        self.pluralModifier = ""
    }
    
    private func thereAreNoLetters() {
        self.number = "no"
        self.verb = "are"
        self.pluralModifier = "s"
    }
}
```

## 불필요한 맥락을 없애라
일반적으로 짧은 이름이 긴 이름보다 좋다. **단, 의미가 분명한 경우에 한해서다(즉, 의미가 분명하지 않은 대부분의 경우에는 긴 이름이 좋다).** 

accountAddress와 customerAddress 는 Address 클래스 인스턴스로는 좋은 이름이나 클래스 이름으로는 적합하지 못하다. Address는 클래스 이름으로 적합하다. 포트 주소, MAC 주소, 웹 주소를 구분해야 한다면 PostalAddress, MAC, URI라는 이름도 괜찮겠다.

* 불필요한 맥락이 포함된 경우들
	* 고급 휘발유 충전소(Gas Station Deluxe)라는 애플리케이션을 짠다고 모든 클래스 이름을 GSD라고 시작하는 경우<br>=> 진짜 끔찍하다. IDE에서 클래스 이름을 검색하려고 G를 누르면 모오든 클래스 이름이 열거될 것이기 때문이다. 보통 회사 이름을 줄이거나 도메인 이름을 약자로 접두사로 클래스 이름에 추가하던데 그러지 말자. 모두가 고생한다.

## 마치면서
여느 코드 개선 노력과 마찬가지로 이름 역시 나름대로 바꿨다가는 누군가 질책할지도 모른다. 그렇다고 코드를 개선하려는 노력을 중단해서는 안 된다. 우리는 프로그래머고 유지보수를 위해 높은 코드 품질을 지향해야 한다. 이름 잘 짓자!