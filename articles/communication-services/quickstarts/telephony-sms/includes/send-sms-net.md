---
title: 包含文件
description: include 文件
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 44cf84b1a9a045d1f9ecd6c6aaf3a8224ec273a6
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113111951"
---
通过使用通信服务 C# 短信 SDK 发送短信，开始使用 Azure 通信服务。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 适用于操作系统的最新版本 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。
- 启用短信的电话号码。 [获取电话号码](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `dotnet` 命令查看是否安装 .NET SDK。
- 若要查看与通信服务资源关联的电话号码，请登录到 [Azure 门户](https://portal.azure.com/)，找到通信服务资源，然后从左侧导航窗格中打开“电话号码”选项卡。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `SmsQuickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

```console
dotnet new console -o SmsQuickstart
```

将目录更改为新创建的应用文件夹，并使用 `dotnet build` 命令编译应用程序。

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>安装包

如果仍在应用程序目录中，可以使用 `dotnet add package` 命令安装适用于 .NET 包的 Azure 通信服务短信 SDK。

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

将 `using` 指令添加到 Program.cs 顶部以包括 `Azure.Communication` 命名空间。

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>对象模型

以下类和接口用于处理适用于 C# 的 Azure 通信服务短信 SDK 的某些主要功能。

| 名称                                       | 说明                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | 所有短信功能都需要此类。 使用订阅信息对其进行实例化，然后使用它发送短信。                           |
| SmsSendOptions | 此类提供用于配置传送报告的选项。 如果 enable_delivery_report 设置为 True，则在传送成功时将发出一个事件 |
| SmsSendResult               | 此类包含来自短信服务的结果。                                          |

## <a name="authenticate-the-client"></a>验证客户端

 在文本编辑器中打开 Program.cs，并将 `Main` 方法的主体替换为使用连接字符串初始化 `SmsClient` 的代码。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../../create-communication-resource.md#store-your-connection-string)。


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>发送 1:1 短信

若要将短信发送给单个收件人，请从 SmsClient 调用 `Send` 或 `SendAsync` 函数。 将此代码添加到 Program.cs 中的 `Main` 方法末尾：

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
应将 `<from-phone-number>` 替换为与通信服务资源关联的启用短信的电话号码，将 `<to-phone-number>` 替换为要向其发送消息的电话号码。

> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+14255550123）。

## <a name="send-a-1n-sms-message-with-options"></a>发送包含选项的 1: N 短信
若要将短信发送给收件人列表，请使用收件人电话号码列表从 SmsClient 调用 `Send` 或 `SendAsync` 函数。 你还可以传入可选参数，一个目的是指定是否应启用传送报告，另一个目的是设置自定义标记。

```csharp
Response<IReadOnlyList<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

你应将 `<from-phone-number>` 替换为与通信服务资源关联的启用短信的电话号码，并将 `<to-phone-number-1>` 和 `<to-phone-number-2>` 替换为要向其发送消息的电话号码

> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+14255550123）。

`enableDeliveryReport` 参数是一个可选参数，可用于配置传送报告。 这对于要在传送短信后发出事件的情况很有用。 请参阅[处理短信事件](../handle-sms-events.md)快速入门，了解如何为短信配置传送报告。

`Tag` 用于将标签应用到交付报告

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```console
dotnet run
```

## <a name="sample-code"></a>代码示例

可以从 [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS) 下载示例应用
