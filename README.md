# MConHudSDK-iOS
<div align="left">
  <img src="https://img.shields.io/badge/Swift-FA7343?style=flat&logo=swift&logoColor=white"/>
  <img src="https://img.shields.io/badge/version-1.0-blue.svg"/>  
  <img src="https://img.shields.io/badge/Platform-iOS-orange.svg"/>  
</div>

# Installation
Swift Package Manager 배포

# Authorization
Need Bluetooth Authorization Add in info.plist

```
Privacy - Bluetooth Always Usage Description
Privacy - Bluetooth Peripheral Usage Description
```

# Auth
AppKey 인증을 완료해 주세요.

```
MConHudSdk.shared.initialize(appKey: "appkey") { error in
  if let error = error {
    print("authorization fail \(error)")
    return
  }
  print("authorization success")
}
```
