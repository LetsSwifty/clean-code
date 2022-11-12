# 10장 클래스

깨끗한 클래스를 알아보자.

## 클래스 체계

클래스에 대한 정의를 자바 관례를 따르면

1. 정적 공개(static public) 상수
2. 정적 비공개(static private) 변수
3. 비공개 인스턴스 변수
4. 공개 변수
5. 공개 함수
6. 비공개 함수는 자신을 호출하는 공개 함수 직후에

즉, 추상화 단계가 순차적으로 내려간다. 그래서 프로그램은 신문 기사처럼 읽힌다.

### 캡슐화
변수와 유틸리티 함수는 가능한 공개하지 않는 편이 낫지만 반드시 숨겨야 한다는 법칙도 없다. 때로는 변수나 유틸리티 함수를 `protected`로 선언해 테스트 코드에 접근을 허용하기도 한다. 우리에게 테스트는 아주 중요하다. 하지만 그 전에 비공개 상태를 유지할 온갖 방법을 강구한다. **캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.**

## 클래스는 작아야 한다!
클래스를 만들 때 첫 번째 규칙은 크기다. **클래스는 작아야 한다.** 두 번째 규칙도 크기다. **더 작아야 한다.** 클래스를 설계할 때도, 함수와 마찬가지로, **작게**가 기본 규칙이라는 의미다.

함수는 무릴적인 행 수로 크기를 측정했다. 클래스는 다른 척도를 사용한다. 클래스가 맡은 책임을 센다.

목록 10-1은 SuperDashboard라는 클래스로, 공개 메서드 수가 대략 70개 정도다. SuperDashboard 클래스를 `만능 클래스`라 부르는 개발자가 있을지도 모르겠다.
```swift
// 목록 10-1 너무 많은 책임
public class SuperDashBoard: JFrame, MetaDataUser {
    public func getCustomizerLanguagePath() -> String
    ...
    ...
    ...
    // 엄청나게 많은 함수...
}


하지만 만약 SuperDashboard가 목록 10-2와 같이 메서드 몇 개만 포함한다면?
```swift
// 목록 10-2 충분히 작을까?
public class SuperDashBoard: JFrame, MetaDataUser {
    public func getLastFocusedComponent() -> Component {}
    public setLastFocused(lastFocused: Component) {}
    public getMajorVersionNumber() -> Int {}
    public getMinorVersionNumber() -> Int {}
    public getBuildNumber() -> Int {}
}
```

메서드 다섯 개 정도면 괜찮다고 생각하지 않은가? 하지만 여기서는 아니다. SuperDashboard는 메서드 수가 작음에도 불구하고 **책임**이 너무 많다.

클래스 이름은 해당 클래스 책임을 기술해야 한다. 실제로 작명은 클래스 크기를 줄이는 첫 번째 관문이다. 간결한 이름이 떠오르지 않는다면 필경 클래스 크기가 너무 커서 그렇다. 클래스 이름이 모호하다면 필경 클래스 책임이 너무 많아서다. 예를 들어, 클래스 이름에 Processor, Manager, Super 등과 같이 모호한 단어가 있다면 클래스에다 여러 책임을 떠안겼다는 증거다.

또한 클래스 설명은 if, and, or, but을 사용하지 않고서 25단어 내외로 가능해야 한다.

### 단일 책임 원칙(SRP)
**단일 책임 원칙(SRP)**: 클래스나 모듈을 **변경할 이유**가 하나, 단 하나뿐이어야 한다는 원칙이다. SRP는 **책임**이라는 개념을 정의하며 적절한 클래스 크기를 제시한다. 클래스는 책임, 즉 변경할 이유가 하나여야 한다는 의미다.

겉보기에 작아보이는 목록 10-2 SuperDashboard는 변경할 이유가 두 가지다. 

1. SuperDashboard는 소프트웨어 버전 정보를 추적한다. 그런데 버전 정보는 소프트웨어를 출시할 때마다 달라진다.
2. SuperDashboard는 자바 스윙 컴포넌트를 관리하다. 즉, 스윙 코드를 변경할 때마다 버전 번호가 다랄진다.

책임, 즉 변경할 이유를 파악하려 애쓰다 보면 코드를 추상화하기도 쉬워진다. 더 좋은 추상화가 더 쉽게 떠오른다. SuperDashboard에서 버전 정보를 다루는 메서드 세 개를 따로 빼내 `Version`이라는 독자적인 클래스를 만든다. `Version` 클래스는 다른 앱에서 재사용하기 아주 쉬운 구조다!

```swift
// 목록  10-3 단일 책임 클래스
public class Version {
    public getMajorVersionNumber() -> Int {}
    public getMinorVersionNumber() -> Int {}
    public getBuildNumber() -> Int {}
}
```

SRP는 객체 지향 설계에서 더욱 중요한 개념이다. 또한 이해하고 지키기 수월한 개념이기도 하다. 하지만 이상하게도 SRP는 클래스 설계자가 가장 무시하는 규칙 중 하나다. 우리는 수 많은 책임을 떠안은 클래스를 꾸준하게 접한다. 왜일까?

소프트웨어를 돌아가게 만드는 활동과 소프트웨어를 깨끗하게 만드는 활동은 완전히 별개다. 그러니 우리는 소프트웨어를 돌아가게 만들고 **깨끗하고 체계적인 소프트웨어**라는 다음 관심사로 전환을 하자!

많은 개발자가 클래스를 잘게 쪼개면 보기 너무 힘든거 아니냐? 라는 말을 하지만 **작은 클래스가 많은 시스템이든 큰 클래스가 몇 개뿐인 시스템이든 돌아가는 부품은 그 수가 비슷하다.** 어느 시스템이든 익힐 내용은 그 양이 비슷하다. 그러므로 고민할 질문은 다음과 같다. 

> "도구 상자를 어떻게 관리하고 싶은가? 작은 서랍을 많이 두고 기능과 이름이 명확한 컴포넌트를 나눠 넣고 싶은가? 아니면 큰 서랍 몇 개를 두고 모두를 던져 넣고 싶은가?" 

규모가 어느 수준에 이르는 시스템은 논리가 많고도 복잡하다. 이런 복잡성을 다루려면 체계적인 정리가 필수다. 그래야 개발자가 무엇이 어디에 있는지 쉽게 찾는다. 그래야 (변경을 가할 때) 직접 영향이 미치는 컴포넌트만 이해해도 충분하다. 큼직한 다목적 클래스 몇 개로 이뤄진 시스템은 (변경을 가할 때) 당장 알 필요가 없는 사실까지 들이밀어 독자를 방해한ㄷ.

강조하는 차원에서 한번 더 적으면 **작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다.** 작은 클래스는 각자 맡은 책임이 하나며, 변경할 이유가 하나며, 다른 작은 클래스와 협력해 시스템에 필용한 동작을 수행한다.

### 응집도(Cohesion)
클래스는 인스턴스 변수 수가 작아야 한다. 각 클래스 메서드는 클래스 인스턴스 변수를 하나 이상 사용해야 한다. 일반적으로 메서드가 변수를 더 많이 사용할수록 메서드와 클래스는 응집도가 더 높다. 모든 인스턴스 변수를 메서드마다 사용하는 클래스는 응집도가 가장 높다.

> 메서드가 변수를 많이 사용할 수록 응집도 ⬆️

일반적으로 이처럼 응집도가 가장 높은 클래스는 가능하지도 바람직하지도 않다. 그렇지만 우리는 응집도가 높은 클래스를 선호한다. 응집도가 높다는 말은 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 의미기 때문이다.

목록 10-4는 Stack를 구현한 코드다. 아래 클래스는 응집도가 아주 높다. size()를 제외한 다른 두 메서드는 두 변수를 모두 사용한다.

```swift
// 목록 10-4 응집도가 높은 클래스
public class Stack<T> {
    private var topOfStack: Int = 0
    var elements: Array<T> = LinkedList<T>()
    
    public func size() -> Int {
        return topOfStack
    }
    
    public func push(element: T) {
        topOfStack += 1
        elements.append(element)
    }
    
    public pop() throws -> T? {
        if topOfStack == 0 {
            throw PoppedWhenEmpty()
        }
        
        let element = elements.popLast()
        return element
    }
}
```

**함수를 작게, 매개변수 목록을 짭게**라는 전략을 따르다 보면 때때로 몇몇 메서드만이 사용하는 인스턴스 변수가 아주 많아진다. 이는 십중팔구 새로운 클래스로 쪼개야 한다는 신호다. 응집도가 높아지도록 변수와 메서드를 적절히 분리해 새로운 클래스 두세 개로 쪼개준다.

### 응집도를 유지하면 작은 클래스 여럿이 나온다
**큰 함수를 작은 함수 여럿으로 나누기만 해도 클래스 수가 많아진다.**

예를 들어, 변수가 아주 많은 큰 함수 하나가 있다. 큰 함수 일부를 작은 함수 하나로 빼내고 싶은데, 빼내려는 코드가 큰 함수에 정의된 변수 넷을 사용한다. 그렇다면 변수 네 개를 새 함수에 인수로 넘겨야 옳을까?

전혀 아니다! 만약 네 변수를 클래스 인스턴스 변수로 승격한다면 새 함수는 인수가 **필요없다.** 그 만큼 함수를 쪼개기 쉬워진다.([currying이 쉬워진다.](https://velog.io/@kmp1007s/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EC%9D%98-Currying))

불행히도 이렇게 하면 클래스가 응집력을 잃는다. 몇몇 함수만 사용하는 인스턴스 변수가 점점 더 늘어나기 때문이다. 그런데 잠깐만! 몇몇 함수가 몇몇 변수만 사용한다면 독자적인 클래스로 분리해도 되지 않는가? 당연한다. **클래스가 응집력을 잃는다면 쪼개라!**

그래서 큰 함수를 작은 함수 여럿으로 쪼개다 보면 종종 작은 클래스 여럿으로 쪼갤 기회가 생긴다. 그러면서 프로그램에 점점 더 체계가 잡히고 구조가 투명해진다.

> **좀 더 자세히 설명하는 예제에 대한 내용은 Java로 작성하겠습니다..!**

Literate Programming에 나오는 유서 깊은 예제를 보자. 공정하게 말해 커누스 교수가 짠 프로그램이 아니라 그가 짠 WEB 도구로 출력한 결과다. 이 프로그램을 예제로 사용하는 이유는 큰 함수를 작은 함수/클래스 여럿으로 쪼개보는 출발점으로 훌륭하기 때문이다.

```java
// 목록 10-5 PrintPrimes.java
package literatePrimes;

public class PrintPrimes {
    public static void main(String[] args) {
        final int M= 1000;
        final int RR = 50;
        final int CC = 4;
        final int Ww= 10; 
        final int ORDMAX = 30;
        int P[] = new int[M + 1];
        int PAGENUMBER;
        int PAGEOFFSET;
        int ROWOFFSET;
        int C; 
        int J;
        int K;
        boolean JPRIME; 
        int ORD;
        int SQUARE;
        int N;
        int MULT [] = new int [ORDMAX + 1];
        J = 1;
        K =1;
        P[1] = 2;
        ORD = 2; 
        SQUARE = 9;

        while (K < M) {
            do {
               J = J + 2;
               if (J == SQUARE) {
                  ORD = ORD + 1;
                  SQUARE = P [ORD] * P [ORD]; 
                  MULT (ORD - 1] = J;
                }
               
               N = 2;
               JPRIME = true;
               while (N < ORD & JPRIME) {
                while (MULT [N] < J)
                    MULT[N] =MULT[N] +P[N] +P[N]; 
                    if (MULTIN] == J)
                        JPRIME = false; 
                    N = N + 1;
                }
               } while (!JPRIME); 
               K= K + 1;
               P[K] = J;
            }
            {
            PAGENUMBER = 1;
            PAGEOFFSET = 1;
            while (PAGEOFFSET =< M) {
                System.out.println("The First " + M + " Prime Numbers --- Page " + PAGENUMBER) ;
            System.out.println("");
            for (ROWOFSET = PAGEOFFSET; ROWOFSET < PAGEOFSET + RR; ROWOFFSET++) {
                for C( = 0; C < CC; C++)
                    if (ROWOFFSET +C*RR<=M)
                        System.out.format ("%10d", P[ROWOFFSET + C * RR]) ;
                System.out.println("");
            }
            System.out.println("\f"); 
            PAGENUMBER = PAGENUMBER + 1; 
            PAGEOFFSET = PAGEOFFSET + R * CC;
            }
        }
    }
}
```

함수가 하나 뿐인 위 프로그램은 엉망진창이다. 들여쓰기가 심하고, 이상한 변수가 많고, 구조가 빡빡하게 결합되었다. 최소한 여러 함수로 나눠야 마땅하다.

목록 10-6에서 목록 10-8까지는 목록 10-5를 작은 함수와 클래스로 나눈 후 함수와 클래스와 변수에 좀 더 의미 있는 이름을 부여한 결과다.

```java
// 목록 10-6 리팩터링 버전
package literatePrimes;

public class PrimePrinter {
    public static void main(String[] args) {
        final int NUMBER_OF_PRIMES = 1000;
        int [] primes = PrimeGenerator.generate(NUMBER_OF_PRIMES);
        
        final int ROWS_PER_PAGE = 50;
        final int COLUMNS_PER_PAGE = 4; 
        RowColumnPagePrinter tablePrinter = new RowColumnPagePrinter(ROWS_PER_PAGE,
                                                                     COLUMNS_PER_PAGE,
                                                                     "The First " + NUMBER_OF_PRIMES + " Prime Numbers");

        tablePrinter.print(primes); 
    }
}
```

``` java
// 목록 10-7 RowColumnPagePrinter.java
package literatePrimes;

import java.io.PrintStream;

public class RowColumnPagePrinter { 
    private int rowsPerPage;
    private int columnsPerPage;
    private int numbersPerPage; 
    private String pageHeader;
    private PrintStream printStream;

    public RowColumnPagePrinter(int rowsPerPage,
                                int columnsPerPage,
                                String pageHeader) { 
        this.rowsPerPage = rowsPerPage;
        this.columnsPerPage = columnsPerPage;
        this.pageHeader = pageHeader;
        numbersPerPage = rowsPerPage * columnsPerPage; 
        printStream = System.out;
    }

    public void print(int data[]) { 
        int pageNumber = 1;
        for (int firstIndexOnPage = 0;
             firstIndexOnPage < data. length;
             firstIndexOnPage += numbersPerPage) { 
            int lastIndexOnPage = Math.min(firstIndexOnPage + numbersPerPage - 1, 
                                           data. length - 1);
            printPageleader (pageHeader, pageNumber); 
            printPage(firstIndexOnPage, lastIndexOnPage, data); 
            printStream.println("\f");
            pageNumber++;
        }
    }

    private void printPage(int firstIndexOnPage, 
                           int lastIndexOnPage,
                           int[] data) { 
        int firstIndex0fLastRownPage = firstIndexOnPage + rowsPerPage - 1;
        for (int firstIndexInRow = firstIndexOnPage;
             firstIndexInRow <= firstIndex0fLastRowOnPage; 
             firstIndexInRow++) {
            printRow(firstIndexInRow, lastIndexOnPage, data); 
            printStream.println('*');
        }
    }

    private void printRow(int firstIndexInRow, 
                          int lastIndexOnPage,
                          int[] data) {
        for (int column = 0; column < columnsPerPage; column++) {
            int index = firstIndexInRow + column * rowsPerPage;
            if (index <= lastIndexOnPage) 
                printStream. format ("%10d", data[index]);
        }
    }

    private void printPageHeader (String pageHeader, int pageNumber) {
        printStream.printIn(pageHeader + " --- Page " + pageNumber);
        printStream.println(""); 
    }

    public void setOutput (PrintStream printStream) { 
        this.printStream = printStream;
    }
}
```

```java
// 목록 10-8 PrimeGenerator.java 
package literatePrimes;

import java.til.ArrayList;

public class PrimeGenerator {
    private static intl] primes;
    private static ArrayList<Integer> multiples0fPrimeFactors;
    
    protected static int[] generate(int n) {
        primes = new int[n];
        multiplesOfPrimeFactors = new ArrayList<Integer>(); set2AsFirstPrime(); checkOddNumbersForSubsequentPrimes();
        return primes; 
    }
    
    private static void set2AsFirstPrime() { 
        primes [0] = 2;
        multiples0fPrimeFactors. add(2);
    }

    private static void checkOddNumbersForSubsequentPrimes() { 
        int primeIndex = 1;
        for (int candidate = 3;
             primeIndex < primes. length; 
             candidate += 2) {
            if (isPrime(candidate)) 
                primes[primeIndex++] = candidate;
        }
    }

    private static boolean isPrime(int candidate) {
        if (isLeastRelevantMultiple0fNextLargerPrimeFactor (candidate)) {
            multiples0fPrimeFactors.add(candidate); 
            return false;
        }
        return isNotMultiple0fAnyPreviousPrimeFactor (candidate);
    }

    private static boolean isLeastRelevantMultiple0fNextLargerPrimeFactor (int candidate) {
        int nextLargerPrimeFactor = primes [multiples0fPrimeFactors.size()];
        int leastRelevantMultiple = nextLargerPrimeFactor * nextLargerPrimeFactor;
        return candidate == leastRelevantMultiple; 
    }

    private static boolean isNotMultiple0fAnyPreviousPrimeFactor (int candidate) {
        for (int n = 1; n < multiples0fPrimeFactors.size(); n++) { 
            if (isMultiple0fNthPrimeFactor (candidate, n))
                return false; 
        }
        return true; 
    }

    private static boolean isMultiple0fNthPrimeFactor(int candidate, int n) { 
        return candidate = = smallest0ddNthMultipleNotLessThanCandidate (candidate, n); 
    }

    private static boolean isMultiple0fNthPrimeFactor(int candidate, int n) { 
        return candidate = = smallest0ddNthMultipleNotLessThanCandidate (candidate, n); 
    }

    private static int smallestOddNthMultipleNotLessThanCandidate(int candidate,int n) {
        int multiple = multiples0fPrimeFactors.get (n); 
        while (multiple < candidate)
            multiple += 2 * primes[n]; 
        multiples0fPrimefactors.set(n, multiple);
        return multiple
    } 
}
```

가장 먼저 눈에 띄는 변화가 프로그램이 길어졌다는 사실이다. 한 쪽을 조금 넘겼던 프로그램이 거의 세 쪽으로 늘어났다. 길이가 늘어난 이유는 여러가지다.

1. 리팩터링한 프로그램은 좀 더 길고 서술적인 변수 이름을 사용한다.
2. 리팩터링한 프로그램은 코드에 주석을 추가하는 수단으로 함수 선언과 클래스 선언을 활용한다.
3. 가독성을 높이고자 공백을 추가하고 형식을 맞추었다.

원래 프로그램은 세 가지 책임으로 나눠졌다. PrimePrinter 클래스는 main 함수 하나만 포함하며 실행 환경을 책임진다. 호출 방식이 달라지면 클래스도 바뀐다. 예를 들어, 프로그램을 SOAP 서비스로 바꾸려면 PrimePrinter 클래스를 고쳐준다.

RowColumnPagePrinter 클래스는 숫자 목록을 주어진 행과 열에 맞춰 페이지에 출력하는 방법을 안다. 출력하는 모양새를 바꾸려면 RowColumnPagePrinter 클래스를 고쳐준다.

PrimeGenerator 클래스는 소수 목록을 생성하는 방법을 안다. 코드를 살펴보면 알겠지만, 객체로 인스턴스화하는 클래스가 아니다. 단순히 변수를 선언하고 감추려고 사용하는 유용한 공간일 뿐이다. 소수를 계산하는 알고리즘이 바뀐다면 PrimeGenerator 클래스를 고쳐준다.

재구현이 아니다! 프로그램을 처음부터 다시 짜지 않았다. 실제로 두 프로그램을 자세히 살펴보면 알고리즘과 동작 원리가 동일하다는 사실을 눈치챌 것이다.

가장 먼저, 원래 프로그램의 정확한 동작을 검증하는 테스트 슈트를 작성했다. 그런 다음, 한 번에 하나씩 수 차례에 걸쳐 조금씩 코드를 변경했다. 코드를 변경할 때마다 테스트를 수행해 원래 프로그램과 동일하게 동작하는지 확인했다. 조금씩 원래 프로그램을 정리한 결과 최종 프로그램이 얻어졌다.

(리팩터링을 하려면 테스트 슈트가 필요하며 변경 시마다 문제가 없는지 테스트를 돌린다... 메모...)

## 변경하지 쉬운 클래스
대다수 시스템은 지속적인 변경이 가해진다. 그리고 뭔가 변경할 때마다 시스템이 의도대로 동작하지 않을 위험이 따른다. 깨끗한 시스템은 클래스를 체계적으로 정리해 변경에 수반하는 위험을 낮춘다.

목록 10-9는 주어진 메타 자료로 적절한 SQL 문자열을 만드는 Sql 클래스다. 아직 미완성이라 update 문과 같은 일부 SQL 기능을 지원하지 않는다. 언젠가 update 문을 지원할 시점이 오면 클래스에 **손대어** 고쳐야 한다. 문제는 코드에 **손대면** 위험이 생긴다는 사실이다. 어떤 변경이든 클래스에 손대면 다른 코드를 망가뜨릴 잠정적인 위험이 존재한다. 그래서 테스트도 완전히 다시 해야 한다.

```swift
// 목록 10-9 변경이 필요해 손대야 하는 클래스
public class Sql {
    let table: String
    let columns: Array<Column>
    
    init(table: String, columns: [Column]) {
        self.table = table
        self.columns = columns
    }
    
    public func create() -> String {}
    public func insert(fields: [Object]) -> String {}
    public func selectAll() -> String {}
    public func findByKey(keyColumn: String, keyValue: String) -> String {}
    public func select(column: Column, pattern: String) -> String {}
    public func select(criteria: Criteria) -> String {}
    public func preparedInsert() -> String {}
    private func columnList(columns: [Column]) -> String {}
    private func valuesList(fields: [Object], final columns: [Columns]) -> String {}
    private func selectWithCriteria(criteria: String) -> String {}
    private func placeholderList(columns: [Columns]) -> String {}
}
```

새로운 SQL 문을 지원하려면 반드시 Sql 클래스에 손대야 한다. 또한 기존 SQL문 하나를 수정할 대도 반드시 Sql 클래스에 손대야 한다. 예를 들어, select 문에 내장된 select 문을 지원하려면 Sql 클래스를 고쳐야 한다. 이렇듯 변경할 이유가 두 가지이므로 Sql 클래스는 SRP를 위반한다.

단순히 구조적인 관점에서도 Sql은 SRP를 위반한다. 메서드를 쭉 훑어보면 `selectWithCriteria`라는 비공개 메서드가 있는데, 이 메서드는 select 문을 처리할 때만 사용한다.

경험에 의하면 클래스 일부에서만 사용되는 비공개 메서드는 코드를 개선할 잠재적인 여지를 서사한다. 하지만 실제로 개선에 뛰어드는 계기는 시슽메이 변경해서라야 한다.

목록 10-10과 같은 방법은 어떨까? 목록 10-9에 있던 공개 인터페이스를 각각 Sql 클래스에서 파생하는 클래스로 만들었다. valueList와 같은 비공개 메서드는 해당하는 파생 클래스로 옮겼다. 모든 파생 클래스가 공통으로 사용하는 비공개 메서드는 Where와 ColumnList라는 두 유틸리 클래스에 넣었다.

```swift
// 목록 10-10 닫힌 클래스 집함
public class Sql {
    let table: String
    let columns: Array<Column>
    
    init(table: String, columns: [Column]) {
        self.table = table
        self.columns = columns
    }
    
    public func generate() -> String {}
}

public class CreateSql: Sql {
    init(table: String, columns: [Column]) {
        self.table = table
        self.columns = columns
    }
    
    override public func generate() -> String {}
}

// 이런식으로 각각 함수별로 클래스를 만듦
```

각 클래스는 극도로 단순한다. 클래스가 서로 분리되었기 때문이다.

목록 10-10처럼 재구성한 Sql 클래스는 세상의 모든 장점만 취한다! 우선 SRP를 지원한다. 여기다 객체 지향 설계에서 또 다른 핵심 원칙인 OCP도 지원한다. OCP란 클래스는 확장에 개방적이고 수정에 폐쇄적이어야 한다는 원칙이다. 우리가 재구성한 Sql 클래스는 파생 클래스를 생성하는 방식으로 새 기능에 개방적인 동시에 다른 클래스를 닫아놓는 방식으로 수정에 폐쇄적이다. 그저 UpdateSql 클래스를 제자리에 끼워 넣으면 끝난다.

새 기능을 수정하거나 기존 기능을 변경할 때 건드릴 코드가 최소인 시스템 구조가 바람직하다. 이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장할 뿐 기존 코드를 변경하지 않는다.

### 변경으로부터 격리
요구사항은 변하기 마련이다. 따라서 코드도 변하기 마련이다. 객체 지향 프로그래밍 입문에서 우리는 구체적인 클래스와 추상 클래스가 있다고 배웠다. 구체적인 클래스는 상세한 구현(코드)을 포함하며 추상 클래스는 개념만 포함한다고도 배웠다. 상세한 구현에 의존하는 클라이언트 클래스는 구현이 바뀌면 위험에 빠진다. 그래서 우리는 인터페이스와 추상 클래스를 사용해 구현이 미치는 영향을 격리한다.

상세한 구현에 의존하는 코드는 테스트가 어렵다. 예를 들어, Portfolio 클래스를 만든다고 가정하자. 그런데 Portfolio 클래스는 외부 TokyoStockExchange API를 사용해 포트폴리오 값을 계산한다. 따라서 우리 테스트 코드는 시세 변화에 영향을 받는다. 5분마다 값이 달라지는 API로 테스트 코드를 짜기한 쉽지않다.

Portfolio 클래스에서 TokyoStockExchange API를 직접 호출하는 대신 StockExchange라는 인터페이스를 생성한 후 메서드 하나를 선언한다.

```swift
public protocol StockExchange {
    func currentPrice(symbol: String) -> Money
}
```

다음으로 StockExchange 프로토콜을 구현하는 TokyoStockExchange 클래스를 구현한다. 또한 Portfolio 생성자를 수정해 StockExchange 참조자를 인수로 받는다.

```swift
public Portfolio {
    private exchange: StockExchange
    init(exchange: StockExchange) {
        self.exchange = exchange
    }

    // ...
}
```

이제 TokyoStockExchange 클래스를 흉내내는 테스트용 클래스를 만들 수 있다. 테스트용 클래스는 StockExchange 인터페이스를 구현하며 고정된 주가를 반환한다. 테스트에서 MS 주식 다섯 주를 구입한다면 테스트용 클래스는 주가로 언제나 100불을 반환한다. 우리 테스트용 클래스는 단순히 미리 정해놓은 표 값만 참조한다. 그러므로 우리는 전체 포트폴리오 총계가 500불인지 확인하는 테스트 코드를 작성할 수 있다.

테스트가 가능할 정도로 시스템의 결합도를 낮추면 유연성과 재사용성도 더욱 높아진다. 결합도가 낮다는 소리는 각 시스템 요소가 다른 요소로부터 그리고 변경으로부터 잘 격리되어 있다는 의미다. 시스템 요소가 서로 잘 격리되어 있으면 각 요소를 이해하기도 더 쉬워진다.

이렇게 결합도를 최소로 줄이면 자연스럽게 또 다른 클래스 설계 원칙인 DIP(Dependency Inversion Principle)를 따르는 클래스가 나온다. 본질적으로 DIP는 클래스가 상세한 구현이 아니라 추상화에 의존해야 한다는 원칙이다.