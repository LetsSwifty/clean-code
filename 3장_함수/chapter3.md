# 3장 함수

## 작게 만들어라

함수를 만드는 첫째 규칙은 ‘작게!’다. 함수를 만드는 둘째 규칙은 ‘더 작게!’다.

```swift
//  목록 3-3
static func renderPageWithSetupAndTeardowns(pageData: PageData, isSuite: Bool) throws -> String {
    if isTestPage(pageData) {
        includeSetupAndTeardownPages(pageData, isSuite)
    }
    return pageData.getHtml()
}
```

### 블록과 들여쓰기

if 문 / else 문 / while 문 등에 들어가는 블록은 한 줄이어야 한다는 의미이다.
한 줄이 되면 바깥을 감싸는 함수(enclosing function)가 작아질 뿐 아니라,
블록 안에서 호출하는 함수 이름을 적절히 짓는다면, 코드를 이해하기도 쉬워진다.
**중첩 구조가 생길만큼 함수가 커져서는 안 된다는 뜻이다.**
함수에서 들여쓰기 수준은 1단이나 2단을 넘어서면 안 된다.

## 한가지만 해라!

목록 3-1은 버퍼 생성, 페이지를 가져오고, 상속된 페이지 검색, 경로를 렌더링, 불가사의한 문자열을 덧붙이고, HTML을 생성한다.
반면 3-3은 한 가지만 처리한다.
설정 페이지와 해제 페이지를 테스트 페이지에 넣는다.

**함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한가지만을 해야 한다.**
목록 3-3은 한 가지만 하는가? 세 가지를 한다고 주장할 수도 있다.

1. 페이지가 테스트 페이지인지 판단한다.
2. 그렇다면 설정 페이지와 해제 페이지를 넣는다.
3. 페이지를 HTML로 렌더링한다.

위 세 단계는 지정된 함수 이름 아래에서 추상화 수준이 하나다.
함수는 간단한 `func`문단으로 기술할 수 있다.
이와같이, 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한 가지 작업만 한다.
우리가 함수를 만드는 이유는 **큰 개념을 다음 추상화 수준에서 여려 단계로 나눠 수행**하기 위해서가 아니던가.
따라서, 함수가 ‘한 가지’만 하는지 판단하는 방법이 하나 더 있다.
단순히 다른 표현이 아닌 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈이다.


## 함수 당 추상화 수준은 하나로!

함수가 확실히 ‘한 가지’ 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.
한 함수 내에서 추상화 수준을 섞으면 코드를 읽는 사람이 헷갈린다.
근본 개념과 세부사항을 구분하기 어려운 탓이다.
근본 개념과 세부사항을 뒤섞기 시작하면, 깨진 유리창처럼 사람들이 함수에 세부사항을 점점 더 추가한다.

### 위에서 아래로 코드 읽기: **내려가기** 규칙

코드는 위에서 아래로 이야기처럼 읽혀야 좋다.
  
한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.
 
즉, 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계씩 낮아진다.    
이것을 **내려가기** 규칙이라 부른다.
추상화 수준이 하나인 함수를 구현하기란 쉽지 않다. 그렇지만 매우 중요한 규칙이다.
핵심은 짧으면서도 ‘한 가지’만 하는 함수다.
위에서 아래로 `func` 문단을 읽어내려 가듯이 코드를 구현하면 추상화 수준을 일관되게 유지하기가 쉬워진다.

## Switch

`switch`문은 작게 만들기 어렵다.

본질적으로 switch 문은 N가지를 처리한다. 불행하게도 switch 문을 완전히 피할 방법은 없다.
물론 다형성을 이용한다.


## 서술적인 이름을 사용하라

함수 이름을 정할 때는 여러 단어가 쉽게 읽히는 명명법을 사용한다. 그런 다음, 여러 단어를 사용해 함수 기능을 잘 표한하는 이름을 선택한다.
이름을 붙일 때는 일관성이 있어야 한다.
`includeSetupAndTeardownPages, includeSetupPages, includeSuiteSetupPages, includeSetupPage` 등이 좋은 예시이다.

## 함수 인수

함수에서 이상적인 인수 개수는 0개(무항)다. 다음은 1개(단항)고, 다음은 2개(이항)다.
3개(삼항)는 가능한 피하는 편이 좋다. 4개(다항) 이상은 특별한 이유가 필요하다. 특별한 이유가 있어도 사용하면 안 된다.

### 많이 쓰는 단항 형식

함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 두 가지다.
1. 인수에 질문을 던지는 경우다. `func fileExists(”MyFile”) -> Bool {}` 이 좋은 예다.
2. 인수를 뭔가로 변환해 결과를 반환하는 경우다. `func fileOpen(”MyFile”) -> InputStream {}`은  String 형의 파일 이름을 InputStream으로 변환한다

드물게 사용하는 이벤트
이벤트 함수는 입력 인수만 있고, 출력 인수는 없다.
`passwordAttemptFailedNtimes(attempts: Int)`가 좋은 예다.
이벤트라는 사실이 코드에 명확히 드러나야 한다.

### 플래그인수
플래그 인수는 추하다.

### 이항 함수

인수가 2개인 함수는 인수가 1개인 함수보다 이해하기 어렵다. 
예를 들어 `writefield(name)`는 `writeField(outputStream, name)`보다 이해하기 쉽다.
물론 이 함수가 적절한 경우도 있다. `Point p = new Point(0, 0)`가 좋은 예다. 
직교 좌표계 점은 일반적으로 인수 2개를 취한다. 하지만 여기서 인수 2개는 한 값을 표현하는 두 요소다. 
두 요소에는 자연적인 순서(x,y)도 있다.

### 삼항 함수

인수가 3개인 함수는 인수가 2개인 함수보다 훨씬 더 이해하기 어렵다.
순서, 주춤, 무시로 야기되는 문제가 두 배 이상 늘어난다. 그래서 삼항 함수를 만들 때는 신중히 고려하라 권고한다.

### 인수 객체

인수가 2-3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어 본다.

예를 들어, 다음 두 함수를 살펴보자.

```swift
func makeCircle(x: Double, y: Double, radius: Double)
func makeCircle(center: Point, radius: Double)
```

객체를 생성해 인수를 줄이는 방법이 눈속임이라 여겨질지 모르지만, 그렇지 않다.
위 예제에서 x와 y를 묶었듯이 변수를 묶어 넘기려면 이름을 붙여야 하므로 결국은 개념을 표현하게 된다.

## 동사와 키워드

함수의 의도나 인수의 순서와 의도를 제대로 표현하려면 좋은 함수 이름 이 필수다.
단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.
예를 들어, `write(name)`은 누구나 곧바로 이해한다. 좀 더 나은 이름은 `writeField(name)`이다. 그러면 이름이 필드라는 사실이 분명히 드러난다.
마지막 에제는 함수 이름에 **키워드**를 추가하는 형식이다. 즉, 함수 이름에 인수 이름을 넣는다.

예를 들어, `assertEquals`보다 `assertExpectedEqualsActual(expected, actual)`이 더 좋다.
그러면 인수 순서를 기억할 필요가 없어진다.

## 부수 효과(Side Effect)를 일으키지 마라!

많은 경우 시간적인 결합(temporal coupling)이나 순서 종속성(order dependency)을 초래한다.
목록 3-6을 살펴보자. 부수 효과(Side Effect)를 일으킨다.


```swift
//목록 3-6
class UserValidator {

    private var cryptographer: Cryptographer

    func checkPassword(userNmae: String, password: String) -> Bool {

        let user: User = UserGateway.findByName(userName)
        if user != nil {
            let codedPhrase: String = user.getPhraseEncodeByPassword()
            let phrase: String = cryptographer.decrypt(codedPhrase, password)
            if phrase == "Valid Password" {
                Session.init()
		return true
            } 
        }
	return false
    }
}
```

여기서 함수가 일으키는 부수 효과(Side Effect)는 `Session.init()` 호출이다.
`checkPassword` 함수 이름만 봐서는 **유효한 비밀번호** 일때 세션을 초기화한다는 사실이 드러나지 않는다.
만약 시간적인 결합이 필요하다면 함수 이름에 분명히 명시한다.
함수가 ‘한 가지’만 한다는 규칙을 위반하지만, 굳이 바꾼다면
목록 3-6은 `checkPasswordAndInitializeSession` 이라는 이름이 훨씬 좋다.

## 명령과 조회를 분리하라!

함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야 한다. 둘 다 하면 안 된다.
객체 상태를 변경하거나 아니면 객체 정보를 반환하거나 둘 중 하나다


```swift
//둘 다 하는 경우
func set(attribute: String, value: String) -> Bool
.
.
if set("userName","unclebob")
```

set이라는 함수 이름을 setAndCheckifExists라고 바꾸는 방밥도 있지만 진짜 해결책은 명령과 조회를 분리해 혼란을 애초에 뿌리뽑는 방법이다.

```swift
if attributeExists("userName") {

    setAttribute("userName", unclebob)
}
```

## 오류 코드보다 예외를 사용하라!

명령 함수에서 오류 코드를 반환하는 방식은 명령/조회 분리 규칙을 미묘하게 위반한다.
자칫하면 if문에서 명령을 표현식으로 사용하기 쉬운 탓이다.

`if deletePage(page) == E_OK`

위 코드는 명사/형용사 혼란을 일으키지 않는 대신 여러 단계로 중첩되는 코드를 야기한다.
오류 코드를 반환하면 호출자는 오류 코드를 곧바로 처리해야 한다는 문제에 부딪힌다.

```swift
if deletePage(page) = ERROR.ok {
    if registry.deleteReference(page.name) = ERROR.ok {
        if (configkeys.deleteKey (page. name, makeKey()) = ERROR.ok {
            Log.error("page deleted")
        } else {
            Log.error("configkey not deleted")
        }
    }
    else {
        Log.error("deleteReference from registry failed")
    }
} else {
    Log.error("delete faile")
    return ERROR.unknown
}

// 반면 오류 코드 대신 예외를 사용하면 오류 처리 코드가 원래 코드에서 분리되
// 므로 코드가 깔끔해진다.

func delete(page: Page) {
    do {
        try deletePageAndAllReferences(page: page)
    catch let e {
        os_log("%@", e) // .OK가 출력됩니다.
    }
}

func deletePageAndAllReferences(page: Page) throws {
    deletePage(page: page)
    registry.deleteReference(page.name)
    configKeys.deleteKey(page.name.makeKey())

    throw ExampleError.OK
}
```

## Try/Catch 블록 뽑아내기

try/catch 블록은 원래 추하다. 
코드 구조에 혼란을 일으키며, 정상 동작과 오류 처리 동작을 뒤섞는다. 
그러므로 try/catch 블록을 별도 함수로 뽑아내는 편이 좋다.


위에서 delete 함수는 모든 오류를 처리한다. 그래서 코드를 이해하기 쉽다.
실제로 페이지를 제거하는 함수는 `deletePageAndAllReference`다.
`deletePageAndAllReferences` 함수는 예외를 처리하지 않는다.
정상 동작과 오류 처리 동작을 분리하면 코드를 이해하고 수정하기 쉬워진다.

## 오류 처리도 한 가지 작업이다

함수는 ‘한 가지’ 작업만 해야 한다. 오류 처리도 ‘한 가지’ 작업에 속한다.
그러므로 오류를 처리하는 함수는 오류를 처리해야 마땅하다.
함수에 키워드 `try`가 있다면 함수는 `try` 문으로 시작해 `do-catch`문으로 끝나야 한다는 말이다.

## Error 의존성 자석

오류 코드를 반환한다는 이야기는, 클래스든 열거형 변수든, 어디선가 오류 코드를 정의한다는 뜻이다.

```swift
enum ExampleError: Error {
case OK
case INVALID
case NO_SUCH
case LOCKED
case OUT_OF_RESOURCES
case WATTING_FOR_EVENT
}
```

위와 같은 Error 열거형은 의존성 자석이다. 
Error Enum case나 값이 변한다면 Error Enum을 사용하는 클래스 전부를 다시 컴파일하고 다시 배치해야 한다.
그래서 Error 열겨형 변경이 어려워진다.
오류 코드 대신 예외를 사용하면 새 예외는 do-catch 문에서 핸들링 할 수 있다.
따라서 재컴파일/재배치 없이도 새 예외 클래스를 추가할 수 있다.


## 반복하지 마라!

목록 3-1을 주의해서 읽어보면 네번이나 반복되는 알고리즘이 보인다.
다른 코드와 섞이면서 모양새가 조금씩 달라진 탓에 중복이 금방 드러나지는 않는다.
그래도 중복은 문제다. 코드 길이가 늘어날 뿐 아니라 알고리즘이 변하면 네 곳이나 손봐야 하니까.
게다가 어느 한곳이라도 빠뜨리는 바람에 오류가 발생할 확률도 네 배나 높다.
많은 원칙과 기법이 중복을 없애거나 제어할 목적으로 나왔다.

## 함수를 어떻게 짜죠?

소프트웨어를 짜는 행위는 어느 글짓기와 비슷하다. 

내가 함수를 짤 때도 마찬가지다. 처음에는 길고 복잡하다. 들여쓰기 단계도 많고 중복된 루프도 많다. 인수 목록도 아주 길다. 이름은 즉흥적이고 코드는 중복된다. 
하지만 나는 그 서투른 코드를 빠짐없이 테스트하는 단위 테스트 케이스도 만든다.
그런 다음 나는 코드를 다듬고, 함수를 만들고, 이름을 바꾸고, 중복을 제거한다. 메서드를 줄이고 순서를 바꾼다. 때로는 전체 클래스를 쪼개기도 한다. 이 와중에도 코드는 항상 단위 테스트를 통과한다

## 결론

여기서 설명한 규칙을 따른다면 길이가 짧고, 이름이 좋고, 체계가 잡힌 함수가 나올 것이다.
