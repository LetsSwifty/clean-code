
여기저기 흩어진 오류 처리 코드 때문에 실제 코드가 하는 일을 파악하기가 거의 불가능하다.<br>
상당수 코드 기반은 전적으로 오류 처리 코드에 좌우되기 때문에 깨끗한 코드와 오류 처리는 확실히 연관성이 있다.<br>
오류 처리 코드로 인해 프로그램 논리를 이해하기 어려워진다면 깨끗한 코드라 부르기 어렵다.<br>

## 오류 코드보다 예외를 사용하라

```swift
//목록 7-1
class DeviceController {
    func sendShutDown() -> Void {
        if handle != DevcieHandle.INVALID {
            if record.getStatus() != DEVICE_SUSPENDED {
                pauseDevice(handle)
                clearDeviceWorkQueue(handle)
                closeDevice(handle)
            } else {
                Log.error("Device suspended. Unable to shut down")
            }
        } else {
            Log.error("Invalid handle")
        }
    }
}
```

```swift
//목록 7-2
class DeviceController {
    func sendShutDown() -> Void {
        do {
            try shutDown()    
        } catch let e {
            Log.error(e)
        }
    }
    
    private func shutDown() throws {
        let handle = getHandle(DEV1)
        let record = retrieveDeviceRecord(handle)
        
        do {
            try pauseDevice(handle)
            try clearDeviceWorkQueue(handle)
            try closeDevice(handle)       
        } catch {
            ...
            throw ...
        }
        
    }
    
    private getHandle(id: DeviceId) -> DeviceHandle {
        ...
        throw DeviceShutDownError.error1
        ...
    }
    
    ...
}
```
7-1의 코드르 7-2로 바뀌면서 코드가 깔금해졌고, 코드 품질도 나아졌다.
디바이스를 종료하는 알고리즘과 오류를 처리하는 알고리즘을 분리했기 떄문이다.
이제는 각 개념을 독립적으로 살펴보고 이해할 수 있다.

## Try-Catch 문부터 작성하라

try-catch 문에서 try 블록에 들어가는 코드를 실행하면 어느 시점에서든 실행이 중단된 후 catch 블록으로 넘어갈 수 있다.

```swift
func pauseDevice(_ handle: DeviceHandle) throws {
    ...
    throw DeviceStateError.PAUSE
    ...
}
func clearDeviceWorkQueue(_ handle: DeviceHandle) throws {
    ...
    throw DeviceStateError.NEEDCLEARQUEUE
    ...
}
func closeDevice(_ handle: DeviceHandle) throws {
    ...
    throw DeviceStateError.NEEDCLOSE
    ...
}

enum DeviceStateError: Error {
    case pause
    case needClearQueue
    case needClose
}

// 확인된 예외를 사용하는 법 - 1 catch + enum
private func shutDown() {
    let handle = getHandle(DEV1)
    let record = retrieveDeviceRecord(handle)
        
    do {
        try pauseDevice(handle)
        try clearDeviceWorkQueue(handle)
        try closeDevice(handle)       
    } catch DeviceShutDownError.PAUSE {
        // pauseDevice(handle)에 대한 확인 된 에외 처리
    } catch {
        // 미확인 예외처리 - Swift 문법 상 필수로 생기는 영역
    }        
}

// 확인된 예외를 사용하는 법 - 2 - Switch
private func shutDown() {
    let handle = getHandle(DEV1)
    let record = retrieveDeviceRecord(handle)
        
    do {
        try pauseDevice(handle)
        try clearDeviceWorkQueue(handle)
        try closeDevice(handle)       
    } catch let e {
        // FIXME: case가 많아질 경우, 메소드로 분리 고려
        switch e {
        case DeviceStateError.pause:
            print("pause")
        case DeviceStateError.needClearQueue:
            print("needClearQueue")
        case DeviceStateError.needClose:
            print("needClose")
        default:
            print("default")
        }
    }        
}

// 확인된 예외를 사용하는 법 - 3 열겨형 + Switch
private func shutDown() {
    let handle = getHandle(DEV1)
    let record = retrieveDeviceRecord(handle)
        
    do {
        try pauseDevice(handle)
        try clearDeviceWorkQueue(handle)
        try closeDevice(handle)       
    } catch let e {
        guard let value: DeviceStateError = e as? DeviceStateError else { return }
        
        // FIXME: case가 많아질 경우, 메소드로 분리 고려
        switch value {
        case .pause:
            print("pause")
        case .needClearQueue:
            print("needClearQueue")
        case .needClose:
            print("needClose")
        @unknown default:
            // Default will never be executed
            // 열거형의 모든 case를 구현했기에 default가 필요없지만 예외가 추가될 가능성을 대비해서 @unknown default 정의
            print("default")
        }
    }        
}
```
catch 블록에서 예외 유형을 좁혀 실제로 발생하는 에러를 처리한다.

## 미확인 예외를 사용하라

안정적인 소프트웨어를 제작하는 요소로 확인된 예외가 반드시 필요하지는 않다는 사실이 분명해졌다.<br>
C#, C++, Python, Ruby 등 확인된 예외를 지원하지않는다. 하지만 안정적인 소프트웨어를 구현하기에 무리가 없다.<br>
확인된 오류가 치리는 비용에 상응하는 이익을 제공하는지 따져봐야한다.<br>

확인된 예외는 개방 폐쇄 원칙 - OCP(Open Closed Principle)를 위반한다.<br>
하위 단계에서 코드를 변경하면 상위 단계 메서드 선언부를 전부 고쳐야 한다는 말이다.<br>

결과적으로 최하위 단계부터 최상위 단계까지 연쇄적인 수정이 일어난다.<br>

## 예외에 의미를 제공하라

전후 상황을 충분히 덧붙인다면, 오류가 발생한 원인과 위치를 찾기가 쉬워진다.<br>
오류 메시지에 정보를 담아 예외와 함께 던진다. 실패한 연산 이름과 실패 유형도 언급한다.

## 호출자를 고려해 예외 클래스를 정의하라

```swift
let port = ACMEPort(12)
do {
    try port.open()
} catch DataException.DeviceResponse {
    reportPortError(error)
    Log.error("Device response exeception", error)
} catch DataException.ATM1212UnlockedException {
    reportPortError(error)
    Log.error("Unlock exception", error)
} catch DataException.GMXError {
    reportPortError(error)
    Log.error("Device response exception")
}
```

```swift
class LocalPort {
    private innerPort: ACMEPort
    init(innerPort: ACMEPort) {
        self.innerPort = innerPort
    }
    
    func open() {
        do {
           try port.open()
        } catch DataException.DeviceResponse {
            throw portDeviceFailure(error)
        } catch DataException.ATM1212UnlockedException {
            throw portDeviceFailure(error)
        } catch DataException.GMXError {
            throw portDeviceFailure(error)
        }
    }
}

let port = LocalPort(12)
do {
    try port.open()
} catch DataError.PortDeviceFailure {
    reportError(error)
    Log.error(error)
}
```

LocalPort 클래스처럼 ACMEPort를 감싸는 클래스는 매우 유용하다.<br>
외부 API를 감싸면 외부 라이브러리와 프로그램 사이에서 의존성이 크게 줄어든다.
## 정상 흐름을 정의하라

때로는 중단이 적합하지 않는 때도 있다.

식비를 비용으로 청구했다면 청구한 식비를 총계에 더한다.
식비를 비용으로 청구하지 않았다면 일일 기본 식비를 총계에 더한다.<br>
예외가 논리를 따라가기 어렵게 만든다.<br>

```swift
class ExpenseReportDAO {
    
    var mealExpenseForEmployee: [Int: Expense] = [:] 
    
    func getMeals(id: Int) throws -> Expense {
        
        if let expense = mealExpenseForEmployee[employeeID] {
            return  expense
        }
        
        
        throw ExpenseReportError.mealExpenseNotFound
    }
}

enum ExpenseReportError: Error {
    case mealExpenseNotFound
}

class Expense {
    
    func getTotal() -> Int {
        ...
        return 12000
    }
}

do {
    let expenses = try ExpenseReportDAO.getMeals(id: employee.getID())
    mealTotal += expenses.getTotal()
} catch {
    mealTotal += getMealPerDiem()
}
```

청구한 식비가 없다면 일일 기본 식비를 반환하는 DefaultDailyMealExpense 객체를 반환한다.
이를 특수 사례 패턴이라 부른다.

```swift
class ExpenseReportDAO {
    
    var mealExpenseForEmployee: [Int: Expense] = [:] 
    
    func getMeals(id: Int) -> Expense {
        
        if let expense = mealExpenseForEmployee[id] {
            return  expense
        }
        return  DefaultDailyMealExpense()
    }
}

class DefaultDailyMealExpense: Expense {
    
    func getTotal() -> Int {
        
        return 10000
    }
}

var total = 0
let expense = ExpenseReportDAO().getMeals(id: 0)
total += expense.getTotal()
```

## nil 반환하지 마라

오류를 유발하는 행위<br>
첫째, nil을 반환하는 습관이다.

```swift
func registerItem(item: Item) {
    if item != nil {
        let registry: ItemRegistry = peristentStore.getItemRegistry()
        if registry != nil {
            let existing: Item = registry.getItem(item.getID())
            if existing.getBillingPeriod().hasRetailOwner() {
                existing.register(item)
            }
        }
    }
}
```

위와 같은 코드는 나쁜 코드이다.<br>
`nil`을 반환하는 코드는 일거리를 늘리고, 호출자에게 문제를 떠넘긴다.<br>
`nil` 확인이 누란된 문제라 말하기 쉽다.<br>
하지만 `nil` 확인이 **너무 많아** 문제이다.<br>
`nil`을 반환하고픈 유혹이 든다면, 예외를 던지거나 특수 사례 객체를 반환하는 방식을 고려한다.<br>

```swift

// nil을 반환하는 메서드
func getEmployes() -> [Employee]? {
    ...
    return nil
    ...
}

let employees: [Employee] = getEmployes()
if employees != nil {
    for employ in employees {
        totalPay += employ.getPay()
    }
}
```
위에서 `getEmployees`는 `nil`도 반환한다. 하지만 반드시 `nil`을 반환할 필요가 있을까 ?<br>
`getEmployees`를 변경해 빈 리스트를 반환한다면 코드가 훨씬 깔끔해진다.

```swift
func getEmployes() -> [Employee] {
    ...
    if 직원이 없다면 {
        return Array<Employee>()
    }
    ...
}
```

## nil 전달하지 마라

메서드에서 nil을 반환하는 방식도 나쁘지만 메서드로 nil을 전달하는 방식은 더 나쁘다 !<br>
정상적인 인수로 nil을 기대하는 API가 아니라면 메서드로 nil을 전달하는 코드는 최대한 피한다.<br>

```swift
class MetricsCalculator {
    
    func xProjection(p1: Point, p2: Point) -> Double {
        return (p2.x - p1.x) * 1.5
    }
}

calculator.xProjection(p1: null, p2: Point(12, 13))
```


대다수의 프로그래밍 언어는 호출자가 실수로 넘기는 nil을 적절히 처리하는 방법이 없다.
그렇다면 애초에 nil을 넘기지 못하도록 금지하는 정책이 합리적이다.

## 결론

깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.
이 둘은 상충하는 목표가 아니기에, 오류 처리를 논리와 분리해 독자적이 사안으로 고려하면
튼튼하고 깨끗한 코드를 작성할 수 있다.
