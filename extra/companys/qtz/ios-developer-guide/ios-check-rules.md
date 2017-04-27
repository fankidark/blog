## iOS 审核自检规则


### ATS 支持

App Transport Security (ATS) 是 Apple 为增强 iOS App 网络通信安全提出的安全功能，适用于 iOS App 和 App Extension ；在启用 ATS 之后，它会强制应用通过 HTTPS （而不是 HTTP ）连接网络服务，在 2017-01-01 实施。

ATS 只对 `NSURLConnection/NSURLSession` 相关以及任何基于此的上层 API 起作用，使用 Apple 底层网络 API 或第三方网络库 API 不受限制。

Cocos2d-x 引擎中的 `openURL` 函数应该用到了 `NSURLConnection/NSURLSession` 函数，所以用到这个接口的域名需要支持 HTTPS ，其他的在 Lua 层使用的 `HTTPClient` ，底层使用的是 Curl ，所以不受影响。

检查点：

 - 在 info.plist 中设置 `NSAllowsArbitraryLoads` 为 NO
 - 使用 `openURL` 接口的URL一定要是 HTTPS
 - Curl 库支持 HTTPS


### 敏感信息提醒

iOS 10 增加了新的安全设定。如果要访问相机，相册，麦克风等需要在 info.plist 中添加提醒条目。

常用的敏感信息条目如下:

- `NSContactsUsageDescription` 通讯录
- `NSMicrophoneUsageDescription` 麦克风
- `NSPhotoLibraryUsageDescription` 相册
- `NSCameraUsageDescription` 相机
- `NSLocationAlwaysUsageDescription` 地理位置
- `NSLocationWhenInUseUsageDescription` 地理位置

[更多信息请参考 Apple QA](https://developer.apple.com/library/content/qa/qa1937/_index.html)
