---
title: 参考 - 适用于 Azure Web PubSub 服务的 .NET 服务器 SDK
description: 该参考介绍适用于 Azure Web PubSub 服务的 .NET 服务器 SDK
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 4bb4c5d90958a31e26c39ce9d0fe7e22bec769e2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113557"
---
# <a name="net-server-sdk-for-azure-web-pubsub-service"></a>适用于 Azure Web PubSub 服务的 .NET 服务器 SDK

此库可用于执行以下操作。 有关此处使用的术语的详细信息，请参阅[主要概念](#key-concepts)部分。

- 将消息发送到中心和组。 
- 将消息发送到特定用户和连接。
- 将用户和连接整理到组中。
- 关闭连接
- 授予、撤销、检查现有连接的权限

[源代码][code] |
[包][package] |
[API 参考文档][api] |
[产品文档](https://aka.ms/awps/doc) |
[示例][samples_ref]

## <a name="getting-started"></a>入门
### <a name="install-the-package"></a>安装包

从 [NuGet](https://www.nuget.org/) 安装客户端库：

```PowerShell
dotnet add package Azure.Messaging.WebPubSub --prerelease
```

### <a name="prerequisites"></a>先决条件

- 一个 [Azure 订阅][azure_sub]。
- 现有 Azure Web PubSub 服务实例。

### <a name="authenticate-the-client"></a>验证客户端

若要与服务交互，需要创建 WebPubSubServiceClient 类的实例。 为了实现此目的，需要使用连接字符串或密钥，可在 Azure 门户中对其进行访问。

### <a name="create-a-webpubsubserviceclient"></a>创建 `WebPubSubServiceClient`

```csharp
var serviceClient = new WebPubSubServiceClient(new Uri("<endpoint>"), "<hub>", new AzureKeyCredential("<access-key>"));
```

## <a name="key-concepts"></a>关键概念

[!INCLUDE [Termsc](includes/terms.md)]

## <a name="examples"></a>示例

### <a name="broadcast-a-text-message-to-all-clients"></a>向所有客户端广播文本消息

```C# Snippet:WebPubSubHelloWorld
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll("Hello World!");
```

### <a name="broadcast-a-json-message-to-all-clients"></a>向所有客户端广播 JSON 消息

```C# Snippet:WebPubSubSendJson
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

serviceClient.SendToAll(RequestContent.Create(
        new
        {
            Foo = "Hello World!",
            Bar = 42
        }),
        ContentType.ApplicationJson);
```

### <a name="broadcast-a-binary-message-to-all-clients"></a>向所有客户端广播二进制消息

```C# Snippet:WebPubSubSendBinary
var serviceClient = new WebPubSubServiceClient(new Uri(endpoint), "some_hub", new AzureKeyCredential(key));

Stream stream = BinaryData.FromString("Hello World!").ToStream();
serviceClient.SendToAll(RequestContent.Create(stream), ContentType.ApplicationOctetStream);
```

## <a name="troubleshooting"></a>疑难解答

### <a name="setting-up-console-logging"></a>设置控制台记录
如果想要更深入地了解针对服务发出的请求，还可以轻松[启用控制台记录](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging)。

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp
[code]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/Azure.Messaging.WebPubSub/src
[package]: https://www.nuget.org/packages/Azure.Messaging.WebPubSub
[api]: /dotnet/api/azure.messaging.webpubsub

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]
