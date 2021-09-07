---
title: Azure 通信服务中的故障排除
description: 了解如何收集在对通信服务解决方案进行故障排除时所需的信息。
author: manoskow
manager: chpalm
services: azure-communication-services
ms.author: manoskow
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 3cd19094b876203569df83cf3bc165968d9051a2
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257995"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Azure 通信服务中的故障排除

本文档将帮助你解决通信服务解决方案中可能会遇到的问题。 如果要排查短信故障，可以[使用事件网格启用送达报告](../quickstarts/telephony-sms/handle-sms-events.md)以捕获短信送达详情。

## <a name="getting-help"></a>获取帮助

我们鼓励开发人员提交问题、建议功能以及报告问题。 为帮助实现此目的，我们提供了一个[专用的支持和帮助选项页](../support.md)，其中列出了支持选项。

为了帮助你对某些类型的问题进行故障排除，系统可能会要求你提供以下信息：

* **MS-CV ID**：此 ID 用于对通话和消息进行故障排除。
* **呼叫 ID**：此 ID 用于标识通信服务呼叫。
* **短信 ID**：此 ID 用于标识短信。
* **调用日志**：这些日志包含可用于排查调用和网络问题的详细信息。


## <a name="access-your-ms-cv-id"></a>获取 MS-CV ID

在初始化 SDK 时，可以通过在 `clientOptions` 对象实例中配置诊断来获取 MS-CV ID。 可以为任何 Azure SDK（包括聊天、标识和 VoIP 呼叫）配置诊断。

### <a name="client-options-example"></a>客户端选项示例

以下代码片段演示的是诊断配置。 在启用诊断的情况下使用这些 SDK 时，诊断详细信息会发送到配置的事件侦听器：

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

## <a name="access-your-server-call-id"></a>访问服务器呼叫 ID
排查通话自动化 SDK 相关问题（如通话记录和通话管理问题）时，将需要收集服务器呼叫 ID。 可以使用 ```getServerCallId``` 方法收集此 ID。

#### <a name="javascript"></a>Javascript
```
callAgent.on('callsUpdated', (e: { added: Call[]; removed: Call[] }): void => {
    e.added.forEach((addedCall) => {
        addedCall.on('stateChanged', (): void => {
            if (addedCall.state === 'Connected') {
                addedCall.info.getServerCallId().then(result => {
                    dispatch(setServerCallId(result));
                }).catch(err => {
                    console.log(err);
                });
            }
        });
    });
});
```


## <a name="access-your-client-call-id"></a>访问客户端呼叫 ID

排除语音或视频通话故障时，可能会要求提供 `call ID`。 可以通过 `call` 对象的 `id` 属性访问：

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.startCall` or `callAgent.join` methods
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
// `call` is an instance of a call created by `callAgent.startCall(…)` or `callAgent.join(…)` methods
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

Azure 通信服务呼叫 SDK 在内部依赖于 [@azure/logger](https://www.npmjs.com/package/@azure/logger) 库来控制日志记录。
使用 `@azure/logger` 包中的 `setLogLevel` 方法配置日志输出：

```javascript
import { setLogLevel } from '@azure/logger';
setLogLevel('verbose');
const callClient = new CallClient();
```

可以通过重写 `AzureLogger.log` 方法，使用 AzureLogger 从 Azure SDK 重定向日志记录输出：如果要将日志重定向到控制台以外的位置，这可能很有用。

```javascript
import { AzureLogger } from '@azure/logger';
// redirect log output
AzureLogger.log = (...args) => {
  console.log(...args); // to console, file, buffer, REST API..
};
```

# <a name="ios"></a>[iOS](#tab/ios)

在针对 iOS 系统进行开发时，日志存储在 `.blog` 文件中。 请注意，不能直接查看日志，因为它们已加密。

可以通过打开 Xcode 访问它们。 转到“窗口”>“设备和模拟器”>“设备”。 选择你的设备。 在“已安装的应用”下，选择应用程序，并单击“下载容器”。

此操作将为你提供 `xcappdata` 文件。 右键单击该文件，然后选择“显示包内容”。 然后你将看到 `.blog` 文件，可以将其附加到 Azure 支持请求。

# <a name="android"></a>[Android](#tab/android)

在针对 Android 系统进行开发时，日志存储在 `.blog` 文件中。 请注意，不能直接查看日志，因为它们已加密。

在 Android Studio 上，从模拟器和设备中选择“查看”>“工具窗口”>“设备文件资源管理器”，导航到设备文件资源管理器。 `.blog` 文件将位于应用程序的目录中，例如 `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`。 可以将此文件附加到支持请求。

---

## <a name="enable-and-access-call-logs-windows"></a>启用并访问调用日志 (Windows)

在针对 Windows 系统进行开发时，日志存储在 `.blog` 文件中。 请注意，不能直接查看日志，因为它们已加密。

可以通过查看应用的本地数据保留位置来访问这些数据。 有多种方法可以确定 UWP 应用的本地数据保存位置，以下步骤只是其中一种方法：
1. 打开 Windows 命令提示符（Windows 键 + R）
2. 键入 `cmd.exe`
3. 键入 `where /r %USERPROFILE%\AppData acs*.blog`
4. 请检查应用程序的应用 ID 是否与上一个命令返回的应用 ID 匹配。
5. 通过输入 `start ` 以及步骤 3 返回的路径，打开包含日志的文件夹。 例如：`start C:\Users\myuser\AppData\Local\Packages\e84000dd-df04-4bbc-bf22-64b8351a9cd9_k2q8b5fxpmbf6`
6. 请将所有 `*.blog` 和 `*.etl` 文件附加到 Azure 支持请求。


## <a name="calling-sdk-error-codes"></a>呼叫 SDK 错误代码

Azure 通信服务呼叫 SDK 使用以下错误代码，你可以通过这些错误代码来排查呼叫问题。 呼叫结束后，这些错误代码通过 `call.callEndReason` 属性公开。

| 错误代码 | 说明 | 采取的操作 |
| -------- | ---------------| ---------------|
| 403 | 被禁止/身份验证失败。 | 确保通信服务令牌有效且未过期。 |
| 404 | 找不到呼叫。 | 确保要呼叫的电话（或要加入的电话）存在。 |
| 408 | 呼叫控制器超时。 | 等待来自用户终结点的协议消息的呼叫控制器超时。 确保客户端已连接且可用。 |
| 410 | 本地媒体堆栈或媒体基础结构错误。 | 确保在受支持的环境中使用最新的 SDK。 |
| 430 | 无法将消息传递到客户端应用程序。 | 确保客户端应用程序正在运行且可用。 |
| 480 | 未注册远程客户端终结点。 | 确保远程终结点可用。 |
| 481 | 无法处理传入呼叫。 | 通过 Azure 门户提交支持请求。 |
| 487 | 呼叫被取消、本地被拒绝，由于终结点不匹配问题而结束，或无法生成媒体产品/服务。 | 预期行为。 |
| 490、491、496、487、498 | 本地终结点网络问题。 | 检查网络。 |
| 500、503、504 | 通信服务基础结构错误。 | 通过 Azure 门户提交支持请求。 |
| 603 | 全球呼叫被远程通信服务参与者拒绝 | 预期行为。 |

## <a name="chat-sdk-error-codes"></a>聊天 SDK 错误代码

Azure 通信服务聊天 SDK 使用以下错误代码，你可以通过这些错误代码来排查聊天问题。 错误代码通过错误响应中的 `error.code` 属性公开。

| 错误代码 | 说明 | 采取的操作 |
| -------- | ---------------| ---------------|
| 401 | 未授权 | 确保通信服务令牌有效且未过期。 |
| 403 | 禁止 | 确保请求的发起方有权访问该资源。 |
| 429 | 请求过多 | 确保客户端应用程序以用户友好的方式处理此方案。 如果此错误持续存在，请提交支持请求。 |
| 503 | 服务不可用 | 通过 Azure 门户提交支持请求。 |

## <a name="related-information"></a>相关信息
- [日志和诊断](logging-and-diagnostics.md)
- [度量值](metrics.md)
