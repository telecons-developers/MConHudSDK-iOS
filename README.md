# MConHudSDK-iOS
<div align="left">
  <img src="https://img.shields.io/badge/Swift-FA7343?style=flat&logo=swift&logoColor=white"/>
  <img src="https://img.shields.io/badge/Swift-1.0-blue.svg"/>  
  <img src="https://img.shields.io/badge/iOS-15.0-blue.svg"/>  
  <img src="https://img.shields.io/badge/Release-1.0-blue.svg"/>  
  <img src="https://img.shields.io/badge/Platform-iOS-orange.svg"/>  
</div>

## Installation
Swift Package Manager 배포

## Authorization
Need Bluetooth Authorization Add in info.plist

```
Privacy - Bluetooth Always Usage Description
Privacy - Bluetooth Peripheral Usage Description
```

## Auth
AppKey 인증을 완료해 주세요.

```swift
MConHudSdk.shared.initialize(appKey: "appkey") { error in
  if let error = error {
    print("authorization fail \(error)")
    return
  }
  print("authorization success")
}
```

## Scan Device
```swift
// ViewController.swift
class ViewController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()
    // Set Scan delegate
    MConHudKit.shared.hudScanDelegate = self
    // The Scanner will be automatically terminated after timeoutSec.
    // The Scanner will NOT be terminated if you pass nil to timeoutSec.
    MConHudKit.shared.startScanPeripheral(timeoutSec: 7)
  }
}
extension ViewController: MConHudScanDelegate {
    func scanPeripheral(peripherals: [MConHudPeripheral]) {
        peripherals.forEach { item in
            print(item.name)    // Device Name
            print(item.uuid)    // Device Identifier UUID
            print(item.rssi)    // Device Bluetooth rssi
            print(item.paired)  // Device Bluetooth Connect Status (Not BLE Connection)
        }
    }

    func scanTimeOut() {
        print("Scan time out. Scan end.")
    }
    func error(error: MConHudKitError) {
        print(error)
    }
}
```

## Bluetooth Connect Device
```swift
// peripheral is MConHudPeripheral
MConHudKit.shared.connectPeripheral(peripheral: peripheral)
```

```swift
// Connect Result
extension ViewController: MConHudScanDelegate {
    func connectPeripheralResult(peripheral: MConHudPeripheral) {
        // Connect Success
    }

    func error(error: MConHudKitError) {
        // Connect Fail
        print(error)
    }
}
```

## Bluetooth Disconnect Device
Bluetooth connection will be disconnected once turn off HUD device's power or call the following code. 

```swift
MConHudKit.shared.disconnectPeripheral(peripheral: peripheral)
```

You are to receive a disconnection signal through disconnectedPeripheral when the connection is terminated.

```swift
extension ViewController: MConHudScanDelegate {
    func disconnectedPeripheral() {
        print("disconnected")
    }
}
```

## Turn By Turn Message
방향과 잔여거리를 전송합니다.

```swift
let turnByTurnCode: TurnByTurnCode = .STRAIGHT
// distance unit is meter.
let distance = 200
MConHudSdk.shared.sendTurnByTurnInfo(tbtCode: .STRAIGHT, distance: distance)
```

## Safety Message
Provide an array of camera types to be flashed, along with the remaining distance in meters

```swift
let safetyCodes = [SafetyCode.TRAFFIC_MONITORING_AREA]
MConHudSdk.shared.sendSafetyInfo(safetyCodes: safetyCodes, remainDistance: 100)
```

If multiple safety indicators need to be displayed simultaneously, provide the value for safetyCode in the form of an array.

```swift
let safetyCodes = [SafetyCode.TRAFFIC_MONITORING_AREA, SafetyCode.SPEED_BREAKER]
MConHudSdk.shared.sendSafetyInfo(safetyCodes: safetyCodes, remainDistance: 100)
```

## Speeding Message
과속 상태를 표현하는 메세지 입니다.  
provide the speed limit and the speeding condition values to convey the message

```swift
MConHudSdk.shared.sendSpeedingInfo(limitSpeed: 70, isSpeeding: true)
```

## Car Speed Message
자차 속도 메세지 입니다.  
Speed is used to represent the vehicle's velocity as an integer type.

```swift
let speed = 100
MConHudSdk.shared.sendCarSpeed(speed: speed)
```

## Fuel Message
연료타입 메세지 입니다.

```swift
MConHudSdk.shared.sendFuel(fuelCode: .FUEL_PUMP)
```
## Hud Brightness
Change the brightness of HUD

```swift
// has a LOW, MEDIUM, HIGH
let brightnessLevel: BrightnessLevel = .LOW
MConHudSdk.shared.sendHudBrightnessLevel(brightnessLevel: brightnessLevel)
```

Fetch the current status information for HUD's brightness status.

```swift
class ViewController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()

    MConHudSdk.shared.hudDelegate = self
  }
}

extension ViewController: MConHudDelegate {
    func receiveHudBrightnessLevel(brightnessLevel: MConHudSDK.BrightnessLevel) {
        print("receiveHudBrightnessLevel \(brightnessLevel)")
    }
}
```


