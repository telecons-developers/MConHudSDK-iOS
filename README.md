# MConHudSDK-iOS
<div align="left">
  <img src="https://img.shields.io/badge/Swift-FA7343?style=flat&logo=swift&logoColor=white"/>
  <img src="https://img.shields.io/badge/Platform-iOS_v15.0-orange.svg"/> 
  <img src="https://img.shields.io/badge/Swift-5-orange.svg"/>    
  <img src="https://img.shields.io/badge/Release-v1.0-blue.svg"/>  
</div>

## Table of Contents
- [Installation](#Installation)
- [Authorization](#Authorization)
- [Auth](#Auth)
- [Scan Device](#Scan-Device)
- [Bluetooth Connect Device](#Bluetooth-Connect-Device)
- [Bluetooth Disconnect Device](#Bluetooth-Disconnect-Device)
- [Turn By Turn Message](#Turn-By-Turn-Message)
- [Safety Message](#Safety-Message)
- [Speeding Message](#Speeding-Message)
- [Junction Views Message](#Junction-Views-Message)
- [Car Speed Message](#Car-Speed-Message)
- [Fuel Message](#Fuel-Message)
- [Clear Message](#Clear-Message)
- [Time Message](#Time-Message)
- [HUD Brightness](#HUD-Brightness)
- [HUD Buzzer](#HUD-Buzzer)
- [Firmware Infomation](#Firmware-Infomation)
- [Firmware Update](#Firmware-Update)

## Installation


## Authorization
Need Bluetooth Authorization Add in info.plist

```
Privacy - Bluetooth Always Usage Description
Privacy - Bluetooth Peripheral Usage Description
```

## Auth
Need to complete AppKey process.

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
    MConHudSdk.shared.hudScanDelegate = self
    // The Scanner will be automatically terminated after timeoutSec.
    // The Scanner will NOT be terminated if you pass nil to timeoutSec.
    MConHudSdk.shared.startScanPeripheral(timeoutSec: 7)
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
    func error(error: MConHudSdkError) {
        print(error)
    }
}
```

## Bluetooth Connect Device
```swift
// peripheral is MConHudPeripheral
MConHudSdk.shared.connectPeripheral(peripheral: peripheral)
```

```swift
// Connect Result
extension ViewController: MConHudScanDelegate {
    func connectPeripheralResult(peripheral: MConHudPeripheral) {
        // Connect Success
    }

    func error(error: MConHudSdkError) {
        // Connect Fail
        print(error)
    }
}
```

## Bluetooth Disconnect Device
Bluetooth connection will be disconnected once turn off HUD device's power or call the following code. 

```swift
MConHudSdk.shared.disconnectPeripheral(peripheral: peripheral)
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
Provide the direction (TBT) with the remaining distance.

```swift
let turnByTurnCode: TurnByTurnCode = .STRAIGHT
// distance unit is meter.
let distance = 200
MConHudSdk.shared.sendTurnByTurnInfo(tbtCode: turnByTurnCode, distance: distance)
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
Provide the status of over speeding messgae.
provide the speed limit and the speeding condition values to convey the message

```swift
MConHudSdk.shared.sendSpeedingInfo(limitSpeed: 70, isSpeeding: true)
```
## Junction Views Message

```swift
let junctionViewCodes = [JunctionViewCode.SERVICE_LANE]
MConHudSdk.shared.sendJunctionViewInfo(JunctionViewCodes: junctionViewCodes)
```
If multiple JunctionView indicators need to be displayed simultaneously, the value for JunctionViewCode should be provided as an array.

```swift
let junctionViewCodes = [JunctionViewCode.SERVICE_LANE, JunctionViewCode.UNDERPASS]
MConHudSdk.shared.sendJunctionViewInfo(JunctionViewCodes: junctionViewCodes)
```

## Car Speed Message
The message of the current vehicle speed.
Speed is used to represent the vehicle's velocity as an integer type.

```swift
let speed = 100
MConHudSdk.shared.sendCarSpeed(speed: speed)
```

## Fuel Message
The message of the fuel type.

```swift
MConHudSdk.shared.sendFuel(fuelCode: .FUEL_PUMP)
```

## Clear Message
Message of turn off

```swift
let clearCodes = [ClearCode.TURN_BY_TURN]
MConHudSdk.shared.sendClear(clearCodes: clearCodes)
```

If multiple Clear indicators need to be displayed simultaneously, the value for ClearCode should be provided as an array.

```swift
let clearCodes = [ClearCode.TURN_BY_TURN, ClearCode.SAFETY]
MConHudSdk.shared.sendClear(clearCodes: clearCodes)
```

If an 'All Clear' indication is required, the value for ClearCode should be provided as an array.

```swift
let clearCodes = [ClearCode.TURN_BY_TURN, ClearCode.SAFETY, ClearCode.SPEEDING, ClearCode.JUNCTION_VIEWS, ClearCode.GPS_SPEED, ClearCode.FUEL]
MConHudSdk.shared.sendClear(clearCodes: clearCodes)
```

## Time Message
Transmitting the current time as a String, utilizing the date and time format yyyyMMddHHmmss.

```swift
let yyyyMMddHHmmss = getCurrentDateTime()
MConHudSdk.shared.sendTime(yyyyMMddHHmmss: yyyyMMddHHmmss)

func getCurrentDateTime() -> String {
  let date = Date()
  let dateFormatter = DateFormatter()
  dateFormatter.dateFormat = "yyyyMMddHHmmss"
  return dateFormatter.string(from: date)
}
```

If the HUD requires time updates, events are delivered through [MConHudDelegate].

```swift
class ViewController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()

    MConHudSdk.shared.hudDelegate = self
  }
}

extension ViewController: MConHudDelegate {
    func receiveHudTime() {
        print("receiveHudTime")
    }

    ...
}
```

## HUD Brightness
Change the brightness of HUD

```swift
let brightnessLevel: BrightnessLevel = .LOW
MConHudSdk.shared.sendHudBrightnessLevel(brightnessLevel: brightnessLevel)
```

Fetch the current status information for HUD's brightness status.

```swift
class ViewController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()

    MConHudSdk.shared.hudDelegate = self
    MConHudSdk.shared.getHudBrightnessLevel()
  }
}

extension ViewController: MConHudDelegate {
    func receiveHudBrightnessLevel(brightnessLevel: BrightnessLevel) {
        print("receiveHudBrightnessLevel \(brightnessLevel)")
    }

    ...
}
```

## HUD Buzzer
Change the beep sound volume of HUD.

```swift
let buzzerLevel: BuzzerLevel = .LOW
MConHudSdk.shared.sendHudBuzzerLevel(buzzerLevel: buzzerLevel)
```

Fetch the current status information for HUD's buzzer status, retrieving only the on/off state and excluding the levels of MUTE, LOW, MEDIUM, HIGH.

```swift
class ViewController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()

    MConHudSdk.shared.hudDelegate = self
    MConHudSdk.shared.getHudBuzzerLevel()
  }
}

extension ViewController: MConHudDelegate {
    func receiveHudBuzzerStatus(buzzerStatus: BuzzerLevel) {
        print("receiveHudBuzzerStatus \(buzzerStatus)")
    }

    ...
}
```

## Firmware Infomation
[MConHudFirmwareDelegate] is to indicate the current information of the firmware
```swift
class ViewController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()

    MConHudSdk.shared.hudFirmwareDelegate = self
    MConHudSdk.shared.getFirmwareInfo()
  }
}

extension TbtTestController: MConHudFirmwareDelegate {
    func receiveFirmwareInfo(firmwareInfo: FirmwareInfo) {
        // modelName: Stirng, firmwareVersion : String
    }

  ...
}
```

## Firmware Update
```swift
MConHudSdk.shared.startFirmwareUpdate(
    firmwareFileURL: downloadFileURL,    // Firmware file path
    deviceName: "MHUD",                  // Scanned device name by BT for HUD
    version: "212201",                   // Scanned device name by BT for HUD
    fileSize: 794192,                    // Firmware file size to be updated
    crcValue: "0x5699"                   // checksum
)
```
[MConHudFirmwareDelegate] is to indicate the progress and complete status of the firmware update

```swift
class ViewController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()

    MConHudSdk.shared.hudFirmwareDelegate = self
  }
}

extension ViewController: MConHudFirmwareDelegate {
    func firmwareUpdate(progress: Int) {
        print("[MConHudFirmwareDelegate] prog : \(progress)")
    }
    
    func firmwareUpdateComplete() {
        print("[MConHudFirmwareDelegate] firmwareUpdateComplete")
    }
    
    func firmwareUpdateError(firmwareUpdateError: MConHudSdkError) {
        print("[MConHudFirmwareDelegate] \(firmwareUpdateError)")
    }

  ...
}
```
