# MConHudSDK-iOS
<div align="left">
  <img src="https://img.shields.io/badge/Swift-FA7343?style=flat&logo=swift&logoColor=white"/>
  <img src="https://img.shields.io/badge/version-1.0-blue.svg"/>  
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

```swift
let safetyCodes = [SafetyCode.TRAFFIC_MONITORING_AREA, SafetyCode.SPEED_BREAKER]
MConHudSdk.shared.sendSafetyInfo(safetyCodes: safetyCodes,        // Provide the types of cameras to be flashed in the form of an array.
                                             remainDistance: 100  // Remaining distance (m)
)
```




