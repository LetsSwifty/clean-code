
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
        } catch {
            Log.error(error)
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


## Try-Catch 문부터 작성하라

## 미확인 예외를 사용하라

## 예외에 의미를 제공하라

## 호출자를 고려해 예외 클래스를 정의하라

## 정상 흐름을 정의하라

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
func getEmployes() -> [Employee]]? {
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

## 결론

