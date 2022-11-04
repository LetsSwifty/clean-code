
여기저기 흩어진 오류 처리 코드 때문에 실제 코드가 하는 일을 파악하기가 거의 불가능하다.
상당수 코드 기반은 전적으로 오류 처리 코드에 좌우되기 때문에 깨끗한 코드와 오류 처리는 확실히 연관성이 있다.
오류 처리 코드로 인해 프로그램 논리를 이해하기 어려워진다면 깨끗한 코드라 부르기 어렵다.

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
a
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


## Try=Catch=Finally 문부터 작성하라

## 미확인 예외를 사용하라

## 예외에 의미를 제공하라

## 호출자를 고려해 예외 클래스를 정의하라

## 정상 흐름을 정의하라

## null를 반환하지 마라

## null를 전환하지 마라

## 결론

