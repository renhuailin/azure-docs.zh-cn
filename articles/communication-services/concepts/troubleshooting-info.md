---
title: Azure 通信服务中的故障排除
description: 了解如何收集在对通信服务解决方案进行故障排除时所需的信息。
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 775075765c8c8eaa94541c0f094c1f7743fe59d9
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886781"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Azure 通信服务中的故障排除

本文档将帮助你解决通信服务解决方案中可能会遇到的问题。 如果要排查短信故障，可以[使用事件网格启用送达报告](../quickstarts/telephony-sms/handle-sms-events.md)以捕获短信送达详情。

## <a name="getting-help"></a>获取帮助

我们鼓励开发人员在通信服务 [GitHub 存储库](https://github.com/Azure/communication)中提交问题、建议功能以及报告问题。 其他论坛包括：

* [Microsoft Q&A](/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

根据 Azure 订阅[支持计划](https://azure.microsoft.com/support/plans/)，你可以通过 [Azure 门户](https://azure.microsoft.com/support/create-ticket/)直接提交支持工单。

为了帮助你对某些类型的问题进行故障排除，系统可能会要求你提供以下信息：

* **MS-CV ID**：此 ID 用于对通话和消息进行故障排除。 
* **呼叫 ID**：此 ID 用于标识通信服务呼叫。
* **短信 ID**：此 ID 用于标识短信。
* **调用日志**：这些日志包含可用于排查调用和网络问题的详细信息。


## <a name="access-your-ms-cv-id"></a>获取 MS-CV ID

在初始化客户端库时，可以通过在 `clientOptions` 对象实例中配置诊断来获取 MS-CV ID。 可以为任何 Azure 客户端库配置诊断，包括聊天、管理和 VoIP 呼叫。

### <a name="client-options-example"></a>客户端选项示例

以下代码片段演示的是诊断配置。 当客户端库与启用的诊断一起使用时，诊断详细信息会发送到配置的事件侦听器：

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>获取呼叫 ID

当通过 Azure 门户删选与呼叫问题有关的支持请求时，系统可能会要求你提供所引用的呼叫 ID。 这可以通过呼叫客户端库来获取：

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>获取短信 ID

对于短信问题，可以从响应对象中收集消息 ID。

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>启用和访问调用日志




# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下代码可用于将 `AzureLogger` 配置为使用 JavaScript 客户端库将日志输出到控制台：

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

在针对 iOS 系统进行开发时，日志存储在 `.blog` 文件中。 请注意，不能直接查看日志，因为它们已加密。

可以通过打开 Xcode 访问它们。 转到“窗口”>“设备和模拟器”>“设备”。 选择你的设备。 在“已安装的应用”下，选择应用程序，并单击“下载容器”。 

此操作将为你提供 `xcappdata` 文件。 右键单击该文件，然后选择“显示包内容”。 然后你将看到 `.blog` 文件，可以将其附加到 Azure 支持请求。

# <a name="android"></a>[Android](#tab/android)

在针对 Android 系统进行开发时，日志存储在 `.blog` 文件中。 请注意，不能直接查看日志，因为它们已加密。

在 Android Studio 上，从模拟器和设备中选择“查看”>“工具窗口”>“设备文件资源管理器”，导航到设备文件资源管理器。 `.blog` 文件将位于应用程序的目录中，例如 `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`。 可以将此文件附加到支持请求。 
   

---


## <a name="related-information"></a>相关信息
- [日志和诊断](logging-and-diagnostics.md)
- [度量值](metrics.md)