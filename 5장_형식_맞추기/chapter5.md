# 5장 형식 맞추기

프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야 한다. 코드 형식을 맞추기 위한 간단한 규칙을 정하고, 착실히 따라야 한다.

팀으로 일한다면, 팀이 합의해 규칙을 정하고 모두가 그 규칙을 따라야 한다. 필요하다면 규칙을 자동으로 적용하는 도구를 활용한다.

### 형식을 맞추는 목적

코드 형식은 의사소통의 일환이다. 의사소통은 전문 개발자의 일차적인 의무다. 즉, 중요하다.

오늘 구현한 기능이 다음 버전에서 바뀔 확률은 아주 높다. 그래도, 오늘 구현한 코드의 가독성은 앞으로 바뀔 코드의 품질에 지대한 영향을 미친다. 원래 코드는 사라질지라도 개발자의 스타일과 규율은 사라지지 않는다.

그렇다면 원활한 소통을 장려하는 코드 형식은 무엇일까?

### 적절한 행 길이를 유지하라

일반적으로 큰 파일보다 작은 파일이 이해하기 쉽다.

JUnit, FitNesse 은 파일크기가 작은 프로젝트이다. 대부분 200줄 미만의 파일로 구성되어있다.

즉, 200줄 정도의 파일로도 커다란 시스템을 구축할 수 있다.

### 신문 기사처럼 작성하라

독자는 표제를 보고서 신문을 읽을지 말지를 결정한다. 신문의 첫 문단은 전체 기사 내용을 요약한다. 그리고 쭉 읽어 내려가면서 세세한 사실이 조금씩 드러난다.

소스파일도 신문 기사와 비슷하게 작성한다. 이름은 간단하면서도 설명이 가능하게 짓는다. 소스파일의 첫 부분은 고차원 개념과 알고리즘을 설명한다. 그리고 아래로 내려갈수록 의도를 세세하게 묘사한다. 마지막에는 가장 저차원 함수와 세부 내역이 나온다.

### 개념은 빈 행으로 분리하라

```swift
//
//  Chapter5.swift
//
//
//  Created by 김희진 on 2022/11/01.
//

import Foundation
import UIKit

protocol ChapterExDelegate {
    func boldWidget()
}

class Chapter5: ChapterExDelegate {

    let childBody = "<p>?</p>"

    func boldWidget() {
        if isMatchWithParent(child: childBody) {
            print("append </b> tag")
        }
    }

    func isMatchWithParent(child: String) -> Bool {
        if child.contains("</b>") {
            return true
        }
        return false
    }
}
```

우리는 코드를 읽으면서 빈행을 만나면, 새로운 개념이 시작된다는 것을 파악할 수 있다.

위의 코드에서 빈행을 완전히 빼버린 코드는 아래와 같다.

```swift
//
//  Chapter5.swift
//  Created by 김희진 on 2022/11/01.
//
import Foundation
import UIKit
protocol ChapterDelegate { func boldWidget() }
class Chapter5: ChapterDelegate {
    let childBody = "<p>?</p>"
    func boldWidget() {
        if isMatchWithParent(child: childBody) {
            print("append </b> tag")
        }
    }
    func isMatchWithParent(child: String) -> Bool {
        if child.contains("</b>") { return true }
        return false
    }
}
```

코드 가독성이 현저하게 떨어져 암호처럼 보인다.

### 세로 밀집도

줄바꿈이 개념을 분리한다면, 세로 밀집도는 연관성을 의미한다. 즉, 서로 밀접한 코드 행은 세로로 가까이 놓여야 한다. 그러면 눈을 별로 움직이지 않고도 연관성을 쉽게 파악할 수 있고 코드가 한눈에 들어온다.

### 수직 거리

같은 파일 내에서, 서로 밀접한 개념은 세로로 가까이 둬야 한다. 하지만 타당한 근거가 없다면 서로 밀접한 개념은 한 파일에 속해야 마땅하다. 이게 바로 protected 변수를 피해야 하는 이유중 하나이다.

연관성이 깊은 두 개념이 파일 내에서 멀리 떨어져 있으면 코드를 읽는 사람이 소스 파일과 클래스를 여기저기 뒤지게 된다.

**변수 선언**

변수는 사용하는 위치에 최대한 가까이 선언한다.

루프를 제어하는 변수는 흔히 루프 문 내부에 선언한다.

아주 드물지만, 다소 긴 함수에서는 블록 상단이나 루프 직전에 변수를 선언하는 사례도 있다.

**인스턴스 변수**

반면, 인스턴스 변수는 클래스의 맨 처음에 선언한다. 잘 설계한 클래스는 클래스 메서드가 인스턴스 변수를 사용하기 때문이다.

인스턴스 변수를 선언하는 위치는 언어마다 논쟁이 있다. C++ 에서는 인스턴스 변수를 클래스 마지막에 선언한다는 ‘가위규칙’ 을 적용한다.

하지만 자바에서는, 보통 클래스 맨 처음에 인스턴스 변수를 선언한다. 어디가 되었든, 잘 알려진 위치에 인스턴스 변수를 모아둔다는 것이 중요한다.

**종속 함수**

한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다. 또한 가능하다면, 호출하는 함수를 호출되는 함수보다 먼저 배치하면 프로그램이 자연스럽게 읽혀 모듈 전체의 가독성이 높아진다.

```swift
class Chapter5 {
    func makeResponse(context: String, requset: Request) {
        let pageName = getPageNameOrDefault(request)

        loadPage(pageName)
    }

    func getPageNameOrDefault(request: Request) {
        return request.getResource()
    }

    func loadPage(_ name: String) {
        ....
    }
}
```

**개념적 유사성**

개념적인 친화도가 높은 코드는 서로 끌어당긴다. 친화도가 높을수록 코드를 가까이 배치한다.

한 함수가 다른 함수를 호출해 생기는 직접적인 종속성, 변수와 그 변수를 사용하는 함수도 친화도가 높은 예시이다.

외에도 비슷한 동작을 수행하는 일군의 함수도 친화도가 높다고 할 수 있다.

```swift
class Assert {
    public func assertTrue(message: String?, condition: Bool) -> Bool {
        if !condition {
            return false
        }
    }

    public func assertTrue(condition: Bool) -> Bool {
        assertTrue(message: nil, condition: condition)
    }
}
```

위 두 함수는 명명법이 똑같고 기본 기능이 유사하고 간단하다. 종속적인 관계가 서로 없더라도, 가까이 배치할 함수들이다.

**세로순서**

일반적으로 함수 호출 종속성은 아래 방향으로 유지한다. 이는 가장 중요한 개념을 먼저 표현하고, 세세한 사항은 가장 마지막에 표현하는 신문기사와 비슷한 형식이다. 즉, 호출되는 함수를 호출하는 함수보다 나중에 배치한다.

### 가로 형식 맞추기

7개의 큰 프로젝트를 조사한 결과, 10자 미만이 30%, 20~60자 사이가 40%에 달한다.

프로그래머는 명백하게 짧은 행을 선호한다.

필자는 행 길이를 120자 정도로 제한하고 있다.

**가로 공백과 밀집도**

가로는 공백을 사용해 밀접한 개념과 느슨한 개념을 표현한다.

```swift
func measureLine(_ line: String) {
	lineCount += 1
	var lineSize = lineCount.count
	lineWidthHistogram.addLine(lineSize, lineCount)
}
```

할당 연산자 “=” 를 강조하기 위해 앞뒤에 공백을 줬다. 공백을 넣으면 두가지 주요 요소가 확실히 나뉜다는 사실이 분명해진다.

반면, 함수명과 이어지는 괄호 사이에는 공백을 넣지 않았다. 함수와 인수는 서로 밀접하기 때문이다.

연산자 우선순위를 강조하기 위해서도 공백을 사용한다.

승수 사이는 공백이 없다. 곱셈은 우선순위가 가장 높다. 항 사이에는 공백이 들어간다. 덧셈과 뺄셈은 곱셈보다 우선순위가 낮다.

공백을 사용해서 위 내용을 더욱 강조할 수 있다.

```swift
func root1(a: Double, b: Double, c: Double) {
	return (-b + Math.sqrt(determinant()) / (2*a)
}

func determinant(a: Double, b: Double, c: Double) -> Double {
	return b*b - 4*a*c
}
```

**가로 정렬**

```swift
params["CxId"]                  = data.ResultData?.CxId                                                         // 간편인증 요청 후 받은 CxId 값
params["PrivateAuthType"]       = data.ResultData?.PrivateAuthType                                              // 간편인증 요청 후 받은 PrivateAuthType 값
```

실제로 내가 사용했던 코드이다. (ㅎㅎ..)

어셈블리어 시절에는 특정 구조를 강조하고자 가로 정렬을 사용했다.

하지만 이는 별로 유용하지 못하다. 정렬하지 않으면 오히려 결함을 찾기 쉽다.

### 들여쓰기

소스파일은 윤곽도와 계층이 비슷하다. 파일 전체에 적용되는 정보가 있고, 파일 내 개별 클래스에 적용되는 정보가 있고, 클래스 내 각 메서드에 적용되는 정보가 있고, 블록 내 블록에 재귀적으로 적용되는 정보가 있다.

이렇게 범위로 이뤄진 계층을 표현하기 위해 우리는 코드를 들여쓴다.

프로그래머는 왼쪽으로 코드를 맞춰 코드가 속하는 범위를 시각적으로 표현할 수 있다. 그럼 이 범위에서 저 범위로 이동이 쉬워진다. 또 파일의 구조가 한눈에 들어와 관계없는 if/while 문도 일일이 살펴보지 않아도 된다.

들여쓰기가 없다면 코드를 읽는건 거의 불가능하다.

### 팀 규칙

개발자로서 팀에 속한다면 자신이 선호해야 할 규칙은 바로 팀규칙이다.

개개인이 따로국밥처럼 맘대로 짜대는 코드는 피해야 한다.

팀과 함께 수현 스타일, 괄호의 위치, 들여쓰기는 몇자로 할지, 클래스 변수와 메서드 네이밍 규칙 등을 정해서 이를 따라야 한다.
