# CallU IM UI使用入门

[![CI Status](https://img.shields.io/travis/adam/LMPush.svg?style=flat)](https://travis-ci.org/adam/LMPush)
[![Version](https://img.shields.io/cocoapods/v/LMPush.svg?style=flat)](https://cocoapods.org/pods/LMPush)
[![License](https://img.shields.io/cocoapods/l/LMPush.svg?style=flat)](https://cocoapods.org/pods/LMPush)
[![Platform](https://img.shields.io/cocoapods/p/LMPush.svg?style=flat)](https://cocoapods.org/pods/LMPush)


#  一：集成说明

## CocoaPods 集成（推荐）

支持 CocoaPods 方式和手动集成两种方式。我们推荐使用 CocoaPods 方式集成，以便随时更新至最新版本。

在 Podfile 中增加以下内容。
```
 pod 'CallUUI'
```
执行以下命令，安装 LMPush。
```
 pod install
```
如果无法安装 SDK 最新版本，执行以下命令更新本地的 CocoaPods 仓库列表。
```
 pod repo update
```
 
## 手动集成（不推荐）

1. 在 Framework Search Path 中加上 CallUApi，CallUUI 的文件路径，手动地将 CallUApi.framework，CallUUI.framework 添加到您的工程"Frameworks and Libraries"中。
CallUApi，CallUUI用swift语言进行原生开发，关于Objective-C桥接的相关操作，请自己Baidu查找。

2. 在 Podfile 中增加以下内容。
```
 pod 'AWSS3'
 pod 'SDWebImage'
 pod 'SDWebImage/GIF'
 pod 'IQKeyboardManager'
 pod 'TSVoiceConverter'
 pod 'MJRefresh'
 pod 'MBProgressHUD'
 pod 'SnapKit'
 pod 'SQLite.swift'
 pod 'GoogleMaps'
 pod 'GooglePlaces'
 pod 'GooglePlacePicker'
```
  
# 二：API相关说明

## 1. 在AppDelegate中初始化
```swift

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    
    UIManager.getInstance().initSdk()                                                       //init sdk
    UIManager.getInstance().registerNotification(application: application, delegate: self)  //注册推送, 也可以不调用，自己代码编写

    return true
}

```
- 1.1 推送功能接入
如果你还需要接入推送功能，在下面的回调方法中要进行方法的注入，当APNS推送在接收到im相关消息时，会自动处理：

```swift

func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    UIManager.getInstance().application(application, didRegisterForRemoteNotificationsWithDeviceToken:deviceToken)
}

@available(iOS 10.0, *)
func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void){
    UIManager.getInstance().userNotificationCenter(center, willPresent:notification, withCompletionHandler:completionHandler)
}

@available(iOS 10.0, *)
func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void){
    UIManager.getInstance().userNotificationCenter(center, didReceive:response, withCompletionHandler:completionHandler)
    completionHandler()
}

func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
    UIManager.getInstance().application(application, didReceiveRemoteNotification:userInfo, fetchCompletionHandler:completionHandler)
}

```

- 1.2 用户登录
IM 平台的用登录操作一般由服务端进行调用，把IM 平台返回的信息在你当前系统的登陆操作完成后，一同返回到app。当然，我们也提供了app端的登录操作的封装代码以供使用 ：

 ```swift
 
 CUUserManager.getInstance().login(userName: "你的用户名", password: “你的密码”) { [weak self] (result: CUResponse<CULoginResponse>) in

 }
 
 ```
 
 当取得IM平台用户的登录信息后，要对 UI Sdk中的登录信息进行设置 
 
 ```swift
 
 UIManager.getInstance().setLoginResponse(response)
 
```
 
 PS:  UI Sdk 中不提供 “用记注册” 功能，注册一般由服务端完成，比如在进行自有系统注册流程过程中，同时对 IM 后台服务的注册接口进行调用，完成注册流程。
  
  - 1.3 设置 IM Sdk 通知回调方法：
  当你的app中，需要接收 IM相关的apns推送，要先完成 1.1中的相关代码调用，如果不需要，可以忽略如下内容。
  在 IM Sdk中，要处理两种推送消息的处理逻辑： “收到新的im消息”， “有新的好友请求”。 
  
   ```swift
   
   UIManager.getInstance().addNewMessageObserver(self, selector:#selector(onNewMessage(notification:)))
   UIManager.getInstance().addNewFriendObserver(self, selector:#selector(onNewFriend(notification:)))
   
  ```
  
  相关处理代码可参考如下：
  ```swift
  
  //apns点击通知 进入聊天页面
  @objc func onNewMessage(notification: Notification){
      if let chatId:String = notification.object as? String{
          let controller:CUIIMViewController = CUIIMViewController.instanceIMController()
          controller.chatId = chatId
          self.navigationController?.pushViewController(controller, animated: true)
      }
  }
  
  //apns点击通知 进入好友请求列表
  @objc func onNewFriend(notification:Notification){
      let controller:CUIFriendRequestViewController = CUIFriendRequestViewController.instanceFriendRequestViewController()
      self.navigationController?.pushViewController(controller, animated: true)
  }
  
 ```
  
  ## 2. 创建会话列表界面
  
  - 代码的方式创建：
  ```swift
  
  let viewController:CUISeeionsListViewController = CUISeeionsListViewController.CreateViewController()
  
 ````
  为了方便自定义，会话列表界面没有设置title，所以可以把取得的controller的view，进行本了页面的填充
  
  -storyboard方式引入：
  
  
  
  ## 3. 打开聊天界面
  
  ```swift
  
  let controller:CUIIMViewController = CUIIMViewController.CreaetViewController()
  
 ```

