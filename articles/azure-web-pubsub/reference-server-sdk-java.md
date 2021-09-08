---
title: 参考 - 适用于 Azure Web PubSub 服务的 Java 服务器 SDK
description: 本参考介绍适用于 Azure Web PubSub 服务的 Java 服务器 SDK
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: b36bf3715458fdf66d8c1a113e96e048bd414d0a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113172"
---
# <a name="java-server-sdk-for-azure-web-pubsub-service"></a>适用于 Azure Web PubSub 服务的 Java 服务器 SDK

使用该库可以：

- 将消息发送到中心和组。
- 将消息发送到特定用户和连接。
- 将用户和连接组织到组中。
- 关闭连接
- 授予/撤销/检查现有连接的权限

[源代码][source_code] | [API 参考文档][api] | [产品文档][product_documentation] | [示例][samples_readme]

## <a name="getting-started"></a>入门

### <a name="prerequisites"></a>先决条件

- [Java 开发工具包 (JDK)][jdk_link] 8 或更高版本。
- [Azure 订阅][azure_subscription]

### <a name="include-the-package"></a>包括包

[//]: # ({x-version-update-start;com.azure:azure-messaging-webpubsub;current})

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-webpubsub</artifactId>
    <version>1.0.0-beta.2</version>
</dependency>
```

[//]: # ({x-version-update-end})

### <a name="create-a-web-pubsub-client-using-connection-string"></a>使用连接字符串创建 Web PubSub 客户端

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .connectionString("{connection-string}")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-client-using-access-key"></a>使用访问密钥创建 Web PubSub 客户端

```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .endpoint("<Insert endpoint from Azure Portal>")
    .hub("chat")
    .buildClient();
```

### <a name="create-a-web-pubsub-group-client"></a>创建 Web PubSub 组客户端
```java
WebPubSubServiceClient webPubSubServiceClient = new WebPubSubClientBuilder()
    .credential(new AzureKeyCredential("{access-key}"))
    .hub("chat")
    .buildClient();
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("java");
```

## <a name="key-concepts"></a>关键概念

[!INCLUDE [Terms](includes/terms.md)]


## <a name="examples"></a>示例

### <a name="broadcast-message-to-entire-hub"></a>将消息广播到整个中心

```java
webPubSubServiceClient.sendToAll("Hello world!");
```

### <a name="broadcast-message-to-a-group"></a>将消息广播到组

```java
WebPubSubGroup javaGroup = webPubSubServiceClient.getGroup("Java");
javaGroup.sendToAll("Hello Java!");
```

### <a name="send-message-to-a-connection"></a>将消息发送到连接

```java
webPubSubServiceClient.sendToConnection("myconnectionid", "Hello connection!");
```

### <a name="send-message-to-a-user"></a>向用户发送消息
```java
webPubSubServiceClient.sendToUser("Andy", "Hello Andy!");
```

## <a name="troubleshooting"></a>故障排除

### <a name="enable-client-logging"></a>启用客户端日志记录
可以设置 `AZURE_LOG_LEVEL` 环境变量，以便查看在客户端库中生成的日志记录语句。 例如，设置 `AZURE_LOG_LEVEL=2` 会显示所有信息性消息、警告消息和错误日志消息。 日志级别可在此处找到：[日志级别][log_levels]。

### <a name="default-http-client"></a>默认的 HTTP 客户端
默认情况下，所有客户端库都使用 Netty HTTP 客户端。 添加上述依赖项会自动将客户端库配置为使用 Netty HTTP 客户端。 [HTTP 客户端 Wiki](/azure/developer/java/sdk/http-client-pipeline) 中详述了如何配置或更改 HTTP 客户端。

### <a name="default-ssl-library"></a>默认 SSL 库
默认情况下，所有客户端库均使用 Tomcat 原生 Boring SSL 库来为 SSL 操作启用原生级别性能。 Boring SSL 库是一个 uber jar，其中包含适用于 Linux/macOS/Windows 的原生库。与 JDK 内的默认 SSL 实现相比，它提供更好的性能。 有关详细信息（包括如何减小依赖项大小），请参阅 Wiki 的[性能优化][performance_tuning]部分。

[azure_subscription]: https://azure.microsoft.com/free
[jdk_link]: /java/azure/jdk
[source_code]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/webpubsub/azure-messaging-webpubsub/src
[product_documentation]: https://aka.ms/awps/doc
[samples_readme]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java
[log_levels]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/logging/ClientLogger.java
[performance_tuning]: https://github.com/Azure/azure-sdk-for-java/wiki/Performance-Tuning
[api]: /java/api/com.azure.messaging.webpubsub

## <a name="next-steps"></a>后续步骤

[!INCLUDE [next step](includes/include-next-step.md)]
