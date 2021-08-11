---
title: 包含文件
description: include 文件
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: ea5e7c4c502e7a31fddac78e1931d172acce7b9d
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113111952"
---
通过使用通信服务 Java 短信 SDK 发送短信，开始使用 Azure 通信服务。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/send-sms-quickstart) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java 开发工具包 (JDK)](/java/azure/jdk/) 8 或更高版本。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。
- 启用短信的电话号码。 [获取电话号码](../get-phone-number.md)。

### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `mvn -v` 以查看是否安装了 maven。
- 若要查看与通信服务资源关联的电话号码，请登录到 [Azure 门户](https://portal.azure.com/)，找到通信服务资源，然后从左侧导航窗格中打开“电话号码”选项卡。

## <a name="setting-up"></a>设置

### <a name="create-a-new-java-application"></a>创建新的 Java 应用程序

打开终端或命令窗口，并导航到要在其中创建 Java 应用程序的目录。 运行以下命令以从 maven-archetype-quickstart 模板生成 Java 项目。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

“生成”目标将创建一个与 artifactId 名称相同的目录。 在此目录下，src/main/java 目录包含项目源代码，src/test/java 目录包含测试源，pom.xml 文件是项目的项目对象模型 (POM)  。

### <a name="install-the-package"></a>安装包

在文本编辑器中打开 pom.xml 文件。 将以下依赖项元素添加到依赖项组。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>设置应用框架

在文本编辑器中打开 /src/main/java/com/communication/quickstart/App.java，添加 import 指令并删除 `System.out.println("Hello world!");` 语句：

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>对象模型

以下类和接口用于处理适用于 Java 的 Azure 通信服务短信 SDK 的某些主要功能。

| 名称                                                             | 说明                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | 此类创建 SmsClient。 向其提供终结点、凭据和 http 客户端。 |
| SmsClient                    | 所有短信功能都需要此类。 用其发送短信。                |
| SmsSendOptions               | 此类提供用于添加自定义标记和配置传送报告的选项。 如果 deliveryReportEnabled 设置为 true，则在传送成功时会发出一个事件 |        
| SmsSendResult                | 此类包含来自短信服务的结果。                                          |

## <a name="authenticate-the-client"></a>验证客户端

使用连接字符串实例化 `SmsClient`。 凭据是来自 Azure 门户的 `Key`。 了解如何[管理资源的连接字符串](../../create-communication-resource.md#store-your-connection-string)。 你还可以用任何实现 `com.azure.core.http.HttpClient` 接口的自定义 HTTP 客户端对此客户端进行初始化。

将以下代码添加到 `main` 方法中：

```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .buildClient();
```

你还可以使用 connectionString() 函数提供整个连接字符串，而不是提供终结点和访问密钥。
```java
// You can find your connection string from your resource in the Azure portal
String connectionString = "endpoint=https://<resource-name>.communication.azure.com/;accesskey=<access-key>";

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>发送 1:1 短信

若要将短信发送给单个收件人，请使用单个收件人的电话号码从 SmsClient 调用 `send` 方法。 你还可以传入可选参数，一个目的是指定是否应启用传送报告，另一个目的是设置自定义标记。

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```

你应将 `<from-phone-number>` 替换为与通信服务资源关联的启用短信的电话号码，以及将 `<to-phone-number>` 替换为要向其发送消息的电话号码

> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+14255550123）。

## <a name="send-a-1n-sms-message-with-options"></a>发送包含选项的 1: N 短信
若要将短信发送到收件人列表，请使用收件人电话号码列表调用 `send` 方法。 你还可以传入可选参数，一个目的是指定是否应启用传送报告，另一个目的是设置自定义标记。
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.sendWithResponse(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE).getValue();

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

你应将 `<from-phone-number>` 替换为与通信服务资源关联的启用短信的电话号码，以及将 `<to-phone-number-1>` 和 `<to-phone-number-2>` 替换为要向其发送消息的电话号码

> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+14255550123）。

`setDeliveryReportEnabled` 方法用于配置交付报告。 这对于要在传送短信后发出事件的情况很有用。 请参阅[处理短信事件](../handle-sms-events.md)快速入门，了解如何为短信配置传送报告。

`setTag` 方法用于将标签应用到交付报告。

## <a name="run-the-code"></a>运行代码

导航到包含 pom.xml 文件的目录，并使用 `mvn` 命令编译该项目。

```console

mvn compile

```

然后，生成包。

```console

mvn package

```

运行以下 `mvn` 命令以执行应用。

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```
