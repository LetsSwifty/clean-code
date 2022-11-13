# 9장 단위 테스트

1997년도만 해도 TDD(Test Driven Development) 라는 개념을 아무도 몰랐다. 대다수 개발자에게 단위 테스트란 자기 프로그램이 ‘돌아간다’는 사실만 확인하는 일회성 코드에 불과했다. 대게는 간단한 드라이버 프로그램을 구현해, 공들여 짠 클래스와 메서드를 테스트하는 코드를 수동으로 실행했다.

```java
void Timer::ScheduleCommand(Command* theCommand, int milliseconds)
```

위 코드는 작가가 구현한 C++ 기반 타이머 코드이다.

ScheduleCommand 함수는 milliseconds 후에 theCommand 라는 메서드를 실행시켰다.

문제는 이 함수를 테스트하는 방법이었다.

앞에 말했던거와 같이, 키보드로 입력받는 간단한 드라이버 프로그램을 만들었다. 사용자가 키를 누를때마다 그 키를 출력하는 명령을 ScheduleCommand함수의 인자로 넘겼다. 그런다음 키보드를 5초마다 한번씩 눌러 실행시켜 그 결과를 확인했다.

지금이라면 꼬치꼬치 따지며 코드가 제대로 도는지 확인하는 테스트 코드를 작성했을 것이다.

표준 타이밍 함수를 호출하는 대신 운영체제에서 코드를 분리했을 것이다.

타이밍 함수를 직접 구현해 시간을 완전히 통제했을 것이다.

부울 플래그를 설정하는 명령을 ScheduleCommand 함수로 넘겨 시간을 올바른 값으로 바꾸는 부울 값이 false에서 true 로 변하는지 확인했을 것이다.

테스트 케이스를 모두 구현하고 통과한 후에는 이 코드를 사용할 사람들에게도 공개하고, 테스트 코드와 이 코드를 소스 패키지로 확실하게 묶어 체크인 했을 것이다.

애자일과 TDD 덕택에 단위 테스트를 자동화하는 프로그래머들이 이미 많아졌으며, 점점 더 늘어나는 추세다. 하지만 우리 분야에 테스트를 추가하려고 급하게 서두르는 와중에 많은 프로그래머들이 제대로 된 케이스를 작성해야 한다는 좀더 중요한 사실을 놓친다.

## TDD 법칙 세가지

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

이 세가지 규칙을 따르면 개발과 테스트가 대략 30초 주기로 묶인다. 테스트 코드와 실제 코드가 함께 나올뿐더러 테스트 코드가 실제 코드보다 불과 몇 초 전에 나온다

하지만, 실제 코드와 맞먹을 정도로 방대한 테스트 코드는 심각한 관리 문제를 유발하기도 한다.

## 깨끗한 테스트 코드 유지하기

새 버전을 출시할 때마다 팀이 테스트 케이스를 유지하고 보수하는 비용도 늘어난다. 점차 테스트 코드는 개발자 사이에서 가장 큰 불만이 되고, 문제가 생겼을 경우 테스트코드를 비난한다. 결국 테스트 슈트를 폐기하게 된다.

하지만, 테스트 슈트가 없으면 개발자는 자신이 수정한 코드가 제대로 도는지 확인할 수 없다. 테스트 슈트가 없으면 이쪽을 수정해도 저쪽이 안전하다는 사실을 검증하지 못한다. 그래서 결함율이 높아지기 시작한다. 결함이 많아지면 개발자는 더이상 코드를 정리하지 못한다.

이를 초래한 원인은 테스트 코드를 막 짜도 좋다고 허용한 원인이였다.

이 이야기의 교훈은 다음과같다.

<aside>
💡 테스트 코드는 실제 코드 못지 않게 중요하다. 테스트 코드를 작성할때에는 사고와 설계와 주의가 필요하다. 실제 코드 못지 않게 깨끗하게 짜야한다.
</aside>

**테스트는 유연성, 유지보수성, 재사용성을 제공한다.**

코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목이 바로 단위 테스트이다. 테스트 케이스가 없다면 모든 변경이 잠정적인 버그다. 아무리 잘 짜진 코드라도, 테스트 케이스가 없으면 개발자는 변경을 주저한다. 버그가 숨어들까 두렵기 때문이다.

하지만 테스트 케이스가 있다면 이야기가 다르다. 아키텍쳐나 설계가 부실하고 엉망인 코드라도 별다른 우려 없이 코드를 수정할 수 있다. 테스트 커버리지가 높을수록 공포는 줄어든다.

즉, 테스트 케이스가 있으면 변경이 쉬워지기 때문에 유연성, 유지보수성, 재사용성이 보장이 된다.

## 깨끗한 테스트 코드

깨끗한 테스트 코드를 만들려면 가독성~~(책에서 3번이나 강조했다)~~이 중요하다.

가독성은 실제 코드보다 테스트 코드에 더더욱 중요하다.

(깨끗하지 못한 테스트 코드에 유형에 대해 설명하였기 때문에, 책에 나와있는 java 코드 그대로 작성하였다.)

```java
public void testGetPageHieratchyAsXml() throws Exception {
  crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
}
```

이 테스트 케이스에는 중복되는 코드가 너무 많다.

addPage() 와, assertSubString() 를 여러번 사용해서 테스트 코드의 표현력이 떨어진다

```java
public void testGetPageHieratchyAsXmlDoesntContainSymbolicLinks() throws Exception {
  WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  PageData data = pageOne.getData();
  WikiPageProperties properties = data.getProperties();
  WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
  symLinks.set("SymPage", "PageTwo");
  pageOne.commit(data);

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
  assertNotSubString("SymPage", xml);
}
```

PathParser는 문자열을 pagePath 인스턴스로 변환한다. 이코드는 테스트와 무관하며, 테스트 코드의 의도만 흐린다.

```java

public void testGetDataAsHtml() throws Exception {
    crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");
    request.setResource("TestPageOne");
    request.addInput("type", "data");
    Responder responder = new SerializedPageResponder();
    SimpleResponse response = (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
    String xml = response.getContent();
    assertEquals("text/xml", response.getContentType());
    assertSubString("test page", xml);
    assertSubString("<Test", xml);
}
```

responder객체를 생성하는 코드와 response를 수집해 변환하는 코드 역시 잡음에 불과하다.

이 테스트 코드들은 모두 읽는 사람을 고려하지 않는다. 불쌍한 독자들은 온갖 잡다하고 무관한 코드를 이해한 후에야 간신히 테스트 내용을 이해할 수 있다.

그럼, 아래 코드를 살펴보자.

각각 위 코드들을 개선한 코드로, 동일한 테스트를 수행한다. 달라진점은 좀 더 이해하기 쉬워진 것이다.

```java
public void testGetPageHierarchyAsXml() throws Exception {
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    submitRequest("root", "type:pages");
    assertResponseIsXML();
    assertResponseContains("<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}

public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
    WikiPage page = makePage("PageOne");
    makePages("PageOne.ChildOne", "PageTwo");

    addLinkTo(page, "PageTwo", "SymPage");

    submitRequest("root", "type:pages");

    assertResponseIsXML();
    assertResponseContains("<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
    assertResponseDoesNotContain("SymPage");
}

public void testGetDataAsXml() throws Exception {
    makePageWithContent("TestPageOne", "test page");

    submitRequest("TestPageOne", "type:data");

    assertResponseIsXML();
    assertResponseContains("test page", "<Test");
}
```

위에 여러개 있었던 addPage 와 assertSubString 함수가 각각 makePages, assertResponseContains 함수로 변경되었다.

테스트시 불필요한 reponder, response를 만들지 않는다.

이외에도

`assertEquals("text/xml", response.getContentType());`

위 코드가

`assertResponseIsXML();`

로 간단히 표현이 된 등 잡다한 코드를 줄였다.

BUILD-OPERATE-CHECK 패턴이 위와 같은 테스트 구조에 적합하다.

각 테스트는 명확히 세 부분으로 나눠진다.

testGetPageHierarchyAsXml() 는 테스트 자료를 만들고,

testSymbolicLinksAreNotInXmlPageHierarchy() 는 테스트 자료를 조작하며,

testGetDataAsXml() 는 조적한 결과가 올바른지 확인한다.

**도메인에 특화된 테스트 언어**

위에 작성한 수정된 테스트 코드는 도메인에 특화된 언어(DSL)로, 테스트 코드를 구현하는 기법을 보여준다.

이런 테스트 API 는 처음부터 설계된 API 가 아니다. 잡다하고 세세한 코드들을 계속 리팩터링 하다가 진화된 것이다. 숙련된 개발자라면 자기 코드를 좀 더 간결하고 표현력이 풍부한 코드로 리팩터링해야 마땅하다.

**이중 표준**

테스트 API 코드에 적용하는 표준은 실제 코드에 적용하는 표준과 확실히 다르다.

단순하고, 간결하고, 표현력이 풍부해야 하지만, 실제 코드만큼 효율적일 필요는 없다. 실제 환경과 테스트 환경은 요구사항이 판이하게 다르기 때문이다.

아래는 온도가 '급격하게 떨어지면' 경보, 온풍기, 송풍기가 모두 가동되는지 확인하는 코드이다.

```java
@Test
public void turnOnLoTempAlarmAtThreashold() throws Exception {
    hw.setTemp(WAY_TOO_COLD);
    controller.tic();
    assertTrue(hw.heaterState());
    assertTrue(hw.blowerState());
    assertFalse(hw.coolerState());
    assertFalse(hw.hiTempAlarm());
    assertTrue(hw.loTempAlarm());
}
```

위 코드는 세세한 사항이 아주 많다.
온도가 떨어지는게 assertTrue 를 만족하는지, 쿨러 상태가 assertFalse 를 만족하는지 하나하나 체크하는 과정은 읽기가 어렵고 복잡하다.

그래서 아래와 같이 수정해서 가독성을 높였다.

```java
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
    wayTooCold();
    assertEquals("HBchL", hw.getState());
}
```

위에서 봤던 의미를 모르는 tic 함수는 보이지 않는다.
assertEquals 에 이상한 문자열을 주목해보면, 대문자는 ‘켜짐’ 이고, 소문자는 ‘꺼짐’ 을 의미한다.

“HBchL” 은 각각 heater, blower, cooler, hi-temp-alarm, lo-temp-alarm 이다.

비록 위 방식이 그릇된 정보를 피하라는 규칙을 위반하는거에 가깝지만, 여기서는 적절하게 쓰인것처럼 보인다.

대소문자의 의미만 안다면, 빠르게 코드의 의미를 파악할 수 있다.

아래 테스트 코드도 마찬가지이다. 우리는 대소문자의 의미를 알기때문에, 각 테스트함수의 의미를 이해할 수 있다.

```java
@Test
public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
    tooHot();
    assertEquals("hBChl", hw.getState());
}

@Test
public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
    tooCold();
    assertEquals("HBchl", hw.getState());
}

@Test
public void turnOnHiTempAlarmAtThreshold() throws Exception {
    wayTooHot();
    assertEquals("hBCHl", hw.getState());
}

@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
    wayTooCold();
    assertEquals("HBchL", hw.getState());
}
```

자 이제 넘어가서 위에서 쓰인 getState() 를 봐보자.

코드가 그리 효율적이지 못하다. 효율을 높이려면 StringBuffer가 더 적합하다.

```java
public String getState() {
    String state = "";
    state += heater ? "H" : "h";
    state += blower ? "B" : "b";
    state += cooler ? "C" : "c";
    state += hiTempAlarm ? "H" : "h";
    state += loTempAlarm ? "L" : "l";
    return state;
}
```

필자는 실제 코드에서도 크게 무리가 아니라면 StringBuffer을 피한다고 한다.
이 코드는 임베디드 시스템에 탑제된다. 그래서 컴퓨터 자원가 메모리가 제한적일 가능성이 높다. 하지만 대부분 경우에서 테스트 자원이 제한적일 가능성은 낮다.

이것이 이 챕터에서 설명하는 이중 표준에 본질이다.

실제 환경에서는 절대로 안되지만, 테스트 환경에서는 전혀 문제없는 방식이 있다. 대게 메모리나 CPU 효율과 관련 있는 경우이다. 코드의 깨끗함과는 무관하게, 메모리와 CPU 효율을 높이기 위한 투자를 많이 하지 않는다.

### 테스트 당 assert 하나

테스트코드는 함수 한개에 assert 문 단 하나만 사용해야 한다는 얘기가 있다. 물론 이럴 경우 결론이 하나기 때문에 코드를 이해하기 쉽고 빠르다는 장점이 있다.

하지만 아래 예제를 살펴보자

```java
public void testGetPageHierarchyAsXml() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

    whenRequestIsIssued("root", "type:pages");

    thenResponseShouldBeXML();
}

public void testGetPageHierarchyHasRightTags() throws Exception {
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

    whenRequestIsIssued("root", "type:pages");

    thenResponseShouldContain("<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}
```

1. 출력이 XML이다.
2. 특정 문자열을 포함한다.

이 두가지의 assert문을 하나로 병합하는 방식이 불리해 보인다. 이럴경우는, 테스트를 두개로 쪼개 각자가 한개의 assert 문을 수행하면 된다.

각 함수마다 given → when → then 이라는 관례를 사용해 코드를 읽기는 쉬워졌지만, 테스트를 분리했기 때문에 중복되는 코드가 많아진다.

TEMPLATE METHOD 패턴을 사용하면 중복을 제거할 수 있다. given/when 부분을 부모 클래스에 두고 then 부분을 자식 클래스에 두면 된다. 아니면 완전히 독자적인 테스트 클래스를 만들어 @Before 함수에 given/when 부분을 넣고 @Test 함수에 then 부분을 넣어도 된다.

<aside> 참고자료 : https://refactoring.guru/ko/design-patterns/template-method </aside>

**하지만, 이것저것 감안해 보면 결국 한 테스트 함수 안에 여러 assert 문을 사용하는 편이 좋다고 생각한다.**

**테스트 당 개념 하나**

어쩌면 "테스트 함수마다 한 개념만 테스트하라"는 규칙이 더 낫겠다. 이것저것 잡다한 개념을 연속으로 테스트하는 긴 함수는 피한다. 그러므로 아래 예시는 부적절한 코드 예시이다.

```java
public void testAddMonths() {
    SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

    SerialDate d2 = SerialDate.addMonths(1, d1);
    assertEquals(30, d2.getDayOfMonth());
    assertEquals(6, d2.getMonth());
    assertEquals(2004, d2.getYYYY());

    SerialDate d3 = SerialDate.addMonths(2, d1);
    assertEquals(31, d3.getDayOfMonth());
    assertEquals(7, d3.getMonth());
    assertEquals(2004, d3.getYYYY());

    SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1));
    assertEquals(30, d4.getDayOfMonth());
    assertEquals(7, d4.getMonth());
    assertEquals(2004, d4.getYYYY());
}
```

assert문이 여러개라는 것도 있지만 그건 큰 문제가 아니다. 한 테스트 함수에서 여러 개념을 테스트한다는 사실이 문제다.

그러므로 가장 좋은 테스트 규칙은

`개념 당 assert문 수를 최소로 줄이고, 테스트 함수 하나는 개념 하나만 테스트하라`

라고 할 수 있다.

## F.I.R.S.T

깨끗한 테스트는 다음 다섯 가지 규칙을 따르는데, 각 규칙에서 첫글자를 모아 First라고 한다.

### Fast (빠르게)

테스트는 빨라야 한다. 테스트는 빨리 돌아야 한다는 말이다.

테스트가 느리면 자주 돌릴 엄두를 못 낸다. 자주 돌리지 않으면 초반에지 못하고, 코드를 정리하지 못해 결국 코드 품질이 망가지기 시작한다.

### Independent(독립적으로)

각 테스트는 서로 의존하면 안된다. 한 테스트가 다음 테스트가 실행될 환경을 준비해서는 안된다. 각 테스트는 독립적으로, 그리고 어떤 순서로 실행해도 괜찮아야 한다. 테스트가 서로에게 의존하면 하나가 실패할 때 나머지도 잇달아 실패하기 때문에 후반 테스트가 찾아 내야할 에러를 찾아내지 못한다.

### Repeatabele(반복가능하게)

테스트는 어떤 환경에서도 반복 가능해야 한다. 네트워크에 연결되지 않은 환경에서도 실행될 수 있어야 한다.

### Self-Validating(자가검증하는)

테스트는 Bool 값으로 결과를 내야한다. 성공 아니면 실패다. 통과 여부를 알려고 로그 파일을 읽게 만들어서도, 통과 여부를 볼라고 텍스트 파일 두개를 수작업으로 비교하게 만들어서도 안된다.

테스트가 스스로 T/F를 가늠하지 않는다면 판단은 주관적이 되며, 수작업으로 평가가 이뤄지게 된다.

### Timely(적시에)

테스트는 적시에 작성해야 한다.

단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다. 실제 코드를 구현한 다음에 테스트 코드를 만들면 실제 코드가 테스트하기 어렵다.
