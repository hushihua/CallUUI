# 乐马IM Api (无UI)

[![CI Status](https://img.shields.io/travis/adam/LMPush.svg?style=flat)](https://travis-ci.org/adam/LMPush)
[![Version](https://img.shields.io/cocoapods/v/LMPush.svg?style=flat)](https://cocoapods.org/pods/LMPush)
[![License](https://img.shields.io/cocoapods/l/LMPush.svg?style=flat)](https://cocoapods.org/pods/LMPush)
[![Platform](https://img.shields.io/cocoapods/p/LMPush.svg?style=flat)](https://cocoapods.org/pods/LMPush)

# 一：乐马IM Api使用入门

开发者的应用“乐马推送SDK”、“乐马IM Api SDK”或“乐马 IM UI SDK”服务，需要经过如下几个简单的步骤：

## 第 1 步：按照流程，接入“乐马推送SDK”
具体流程请 [ 点击这里 ](https://github.com/hushihua/LMPush/)

## 第 2 步：开发环境要求

Xcode 10 及以上
iOS 8.0 及以上

   

#  二：集成说明

## CocoaPods 集成（推荐）

支持 CocoaPods 方式和手动集成两种方式。我们推荐使用 CocoaPods 方式集成，以便随时更新至最新版本。

在 Podfile 中增加以下内容。
```
 pod 'LMPush'
 pod 'LIMApi'
 pod 'AWSS3'
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

1. 在 Framework Search Path 中加上 LMPush，LIMApi 的文件路径，手动地将 LMPush.framework，LIMApi.framework 添加到您的工程"Frameworks and Libraries"中。
LMPush，LIMApi用swift语言进行原生开发，关于Objective-C桥接的相关操作，请自己Baidu查找。

2. 在 Podfile 中增加以下内容。
```
 pod 'AWSS3'
```
  

# 三：在代码中引入

## 1.按照“乐马推送SDK”的“代码中引入”流程，接入相关代码

具体流程请 [点击这里](https://github.com/hushihua/LMPush/)



  
# 四：API相关说明

## 1. LIMManager
主要负责 im 服务使用前的注册，登录等用户相关信息的操作及前后台事件处理处im推送中的相关事件进行处理并分发的处理器。
  
 - 1.1 用户注册
注册操作一般由服务端逻辑进行实现，代码如下：
```swift
LIMManager.getInstance().login(userName: String, password: String) { (response:LMResponse<LIMUserInfo>) in
    if response.isSuccess == true{
        if let info:LIMUserInfo = response.data{
            //login success
        }
    }
}
```
  
- 1.2 用户登录
用户登录操作成功后，才能对后继的业务进行操作，代码如下：
```swift
LIMManager.getInstance().login(userName: String, password: String) { (response:LMResponse<LIMUserInfo>) in
    if response.isSuccess == true{
        if let info:LIMUserInfo = response.data{
            //login success
        }
    }
}
```
  
 - 1.3 用户资料更新，头像修改， 修改密码
该操作要在“用户登录”操作成功后才能使用（以下的函数中不再一一说明），代码如下：
```swift

// 更新用户资料
LIMManager.getInstance().updateUserInfo(info: LIMUserInfo) { (response:LMResponse<LIMUserInfo>) in
    if response.isSuccess == true{
        if let info:LIMUserInfo = response.data{
            //success
        }
    }
}

/**
 *  更新用户头像
 *  avatar:  图像上传成功后返回的 “文件名称” （非完整的url路径）
 */
LIMManager.getInstance().updateAvatar(avatar: String) { (response:LMResponse<LIMUserInfo>) in
    if response.isSuccess == true{
        if let info:LIMUserInfo = response.data{
            //success
        }
    }
}

//修改密码
LIMManager.getInstance().updatePassword(oldPassword:String, newPassword:String) { (response:LMResponse<LIMUserInfo>) in
    if response.isSuccess == true{
        if let info:LIMUserInfo = response.data{
            //success
        }
    }
}

```
  
- 1.4 退出登录
退出登录成功执行后，推送服务关闭，IM系统中的其它服务要进行重新进行登录操作后，才能成功执行。
```swift
LIMManager.getInstance().logout { (response:LMResponse<Bool>) in
    if response.isSuccess == true{
        //success
    }
}
```


## 2. 会话管理器 LIMSessionManager
- 获取会话列表
```swift
LIMSessionManager.getInstance().loadSessionList { (LMResponse<[LIMSessionInfo]>) in
    if response.isSuccess == true, let list:[LIMSessionInfo] = response.data{
        //success
    }
}
```
- 删除会话
```swift
IMSessionManager.getInstance().deleteSession(chatId: String) { (response:LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 会话置顶设置
```swift
LIMSessionManager.getInstance().toppingSession(chatId: String, pin: String) { (response:LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 会话免打扰设置
```swift
LIMSessionManager.getInstance().keepQuiet(chatId: String) { (response:LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 取消会话免打扰设置
```swift
LIMSessionManager.getInstance().cancelQuiet(chatId: String) { (response:LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 获取单个会话详情
```swift
LIMSessionManager.getInstance().loadSessionInfo(chatId: String) { (response:LMResponse<LIMSessionInfo>) in
    if response.isSuccess == true, let info:LIMSessionInfo = response.data{
        //success
    }
}
```

## 3. 好友关系管理器 LIMFriendshipManager
- 获取好友列表
```swift
LIMFriendshipManager.getInstance().loadFriendList { (LMResponse<[LIMFriendInfo]>) in
    if response.isSuccess == true, let list:[LIMFriendInfo] = response.data{
        //success
    }
}
```
- 获取审核的好友列表
```swift
LIMFriendshipManager.getInstance().loadReviewList { (LMResponse<[LIMFriendInfo]>) in
    if response.isSuccess == true, let list:[LIMFriendInfo] = response.data{
        //success
    }
}
```
- 申请添加好友
```swift
LIMFriendshipManager.getInstance().requestFriends(friendName: String) { (LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 审核好友
```swift
LIMFriendshipManager.getInstance().approveFriend(friendName: String, status: String) { (LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 删除好友
```swift
LIMFriendshipManager.getInstance().deleteFriend(friends: [String]) { (LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 添加好友 此接口直接添加成功，沒有審核。
```swift
LIMFriendshipManager.getInstance().addFriend(friends: [String]) { (LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
## 4. 群组管理器 LIMGroupManager
- 创建群组
```swift
LIMGroupManager.getInstance().createGroup(title: String, avatar: String, users: [String]) { (response:LMResponse<LIMSessionInfo>) in
    if response.isSuccess == true, let info:LIMSessionInfo = response.data{
        //success
    }
}
```
- 邀请加入群组
```swift
LIMGroupManager.getInstance().invint(chatId: String, users: [String]) { (response:LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 踢出群组
```swift
LIMGroupManager.getInstance().kick(chatId: String, users: [String]) { (response:LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 退出群组
```swift
LIMGroupManager.getInstance().quit(chatId: String, masterUser: String) { (response:LMResponse<Bool>) in
    if response.isSuccess == true, let re:Bool = response.data, re == true{
        //success
    }
}
```
- 更新群组资料
```swift
LIMGroupManager.getInstance().updateInfo(chatId: String, title: String, avatar: String) { (response:LMResponse<LIMSessionInfo>) in
    if response.isSuccess == true, let info:LIMSessionInfo = response.data{
        //success
    }
}
```

## 5. 聊天管理器 LIMChatManager
- 发送消息
```swift
LIMChatManager.getInstance().sendMessage(message: LIMMessageInfo) { (LMResponse<LIMMessageInfo>) in
    if response.isSuccess == true, let info:LIMMessageInfo = response.data{
        //success
    }
}
```
- 删除消息
```swift
LIMChatManager.getInstance().delete(messageIds: [String]) { (LMResponse<Bool>) in
    if response.isSuccess == true, let info:LIMSessionInfo = response.data{
        //success
    }
}
```
- 设置消息为已读
```swift
LIMChatManager.getInstance().markReaded(chatId: String, msgId: String, msgTime: String) { (LMResponse<Bool>) in
    if response.isSuccess == true, let info:LIMSessionInfo = response.data{
        //success
    }
}
```
- 检测消息版本，当消息列表，显示的消息是用本地缓存的时候，都需要检测一次
```swift
LIMChatManager.getInstance().checkVersion(chatId: String, ids: String) { (LMResponse<[LIMMessageInfo]>) in
    if response.isSuccess == true, let list:[LIMMessageInfo] = response.data{
        //success
    }
}
```
- 获取seq_id 之后的聊天记录
```swift
LIMChatManager.getInstance().loadSeqBelow(chatId: String, seq: Int, offset: Int, pageSize: Int) { (LMResponse<LIMMessageReponseInfo?>) in
    if response.isSuccess == true, let info:LIMMessageReponseInfo = response.data{
        //success
    }
}
```
- 获取聊天记录列表
```swift
LIMChatManager.getInstance().loadHistory(chatId: String, seq: Int, offset: Int, pageSize: Int) { (LMResponse<LIMMessageReponseInfo?>) in
    if response.isSuccess == true, let info:LIMMessageReponseInfo = response.data{
        //success
    }
}
```



