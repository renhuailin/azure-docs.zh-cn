---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 41f8fc2adefd29bb7cfda31e3dbaa83de599b154
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837687"
---
## <a name="install-the-sdk"></a>安装 SDK

找到项目级别 build.gradle，确保将 `mavenCentral()` 添加到 `buildscript` 和 `allprojects` 下的存储库列表中
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
然后，在模块级别的 build.gradle 中，将以下行添加到 dependencies 部分

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}
```

### <a name="initialize-the-required-objects"></a>初始化所需的对象

若要创建 `CallAgent` 实例，必须对 `CallClient` 实例调用 `createCallAgent` 方法。 这将异步返回 `CallAgent` 实例对象。
`createCallAgent` 方法采用 `CommunicationUserCredential` 作为参数来封装[访问令牌](../../../../quickstarts/access-tokens.md)。
若要访问 `DeviceManager`，必须先创建一个 callAgent 实例，然后可使用 `CallClient.getDeviceManager` 方法获取 DeviceManager。

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
若要为主叫方设置显示名称，请使用以下替代方法：

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```