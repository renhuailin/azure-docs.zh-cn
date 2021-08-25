---
title: 教程：通过在单一实例 Java Spring 应用中使用推送刷新来使用动态配置
titleSuffix: Azure App Configuration
description: 本教程介绍如何通过推送刷新动态更新 Java Spring 应用的配置数据
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: mametcal
ms.openlocfilehash: 1b4dbe38c1b70bb44eb1c280e56954c150a5a23a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739895"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-java-spring-app"></a>教程：通过在 Java Spring 应用中使用推送刷新来使用动态配置

应用程序配置 Java Spring 客户端库支持按需更新配置，不需让应用程序重启。 应用程序可以配置为使用以下两种方法中的一种或两种来检测应用配置更改。

- 轮询模型：这是使用轮询检测配置更改的默认行为。 设置的缓存值到期后，对 `AppConfigurationRefresh` 的 `refreshConfigurations` 的下一次调用将向服务器发送请求，以检查配置是否已更改，并根据需要拉取已更新的配置。

- 推送模型：这将使用[应用配置事件](./concept-app-configuration-event.md)检测配置更改。 应用程序配置设置为使用事件网格发送键值更改事件后，应用程序就可以通过 [Webhook](../event-grid/handler-event-hubs.md)，使用这些事件来优化保持配置更新所需的请求总数。

本教程演示如何在代码中使用推送刷新实现动态配置更新。 它建立在快速入门中介绍的应用之上。 请先完成[使用应用程序配置创建 Java Spring 应用](./quickstart-java-spring-app.md)，然后再继续。

你可以使用任何代码编辑器执行本教程中的步骤。 [Visual Studio Code](https://code.visualstudio.com/) 是 Windows、macOS 和 Linux 平台上提供的一个卓越选项。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置订阅以将配置更改事件从“应用程序配置”发送到“Webhook”
> * 将 Spring Boot 应用程序部署到应用服务
> * 设置 Java Spring 应用，以根据应用程序配置中的更改更新其配置。
> * 在应用程序中使用最新配置。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 受支持的 [Java 开发工具包 (JDK)](/java/azure/jdk)，版本为 8。
- [Apache Maven](https://maven.apache.org/download.cgi) 版本 3.0 或更高版本。
- 现有 Azure 应用程序配置存储。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="setup-push-refresh"></a>设置推送刷新

1. 打开 pom.xml 并使用以下依赖项更新文件。

   ```xml
           <dependency>
               <groupId>com.azure.spring</groupId>
               <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
               <version>2.0.0</version>
           </dependency>
   
           <!-- Adds the Ability to Push Refresh -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
   ```

1. 设置 [Maven 应用服务部署](../app-service/quickstart-java.md?tabs=javase)，以便可以通过 Maven 将应用程序部署到 Azure 应用服务。

   ```console
   mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
   ```

1. 打开 bootstrap.properties 并配置 Azure 应用程序配置推送刷新和 Azure 服务总线

   ```properties
   # Azure App Configuration Properties
   spring.cloud.azure.appconfiguration.stores[0].connection-string= ${AppConfigurationConnectionString}
   spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled= true
   spring.cloud.azure.appconfiguration.stores[0].monitoring.cacheExpiration= 30d
   spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key= sentinel
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.name= myToken
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.secret= myTokenSecret
   
   management.endpoints.web.exposure.include= "appconfiguration-refresh"
   ```

在将缓存值标记为“脏”之前会增加一个随机延迟，以减少潜在的限制。 缓存值被标记为“脏”之前的默认最长延迟时间为 30 秒。

> [!NOTE]
> 主令牌名应作为密钥存储在应用程序配置中，然后主令牌机密应作为“应用程序配置密钥保管库参考”来进行存储，以增加安全性。

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

事件网格 Webhook 需要在创建时进行验证。 可以按照本[指南](../event-grid/webhook-event-delivery.md)进行验证，也可以通过启动已配置了 Azure 应用程序配置 Spring Web 库（此过程将注册应用程序）的应用程序来验证。 要使用事件订阅，请按照以下两部分中的步骤操作。

1. 将环境变量设置为应用程序配置实例的连接字符串：

    #### <a name="windows-command-prompt"></a>[Windows 命令提示符](#tab/cmd)

    ```cmd
    setx AppConfigurationConnectionString <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

1. 运行以下命令以生成控制台应用：

   ```shell
    mvn package
   ```

1. 生成成功完成后，请运行以下命令以在本地运行应用：

    ```shell
    mvn spring-boot:deploy
    ```

## <a name="set-up-an-event-subscription"></a>设置事件订阅

1. 在 Azure 门户中打开“应用配置”资源，然后在 `Events` 窗格中单击 `+ Event Subscription`。

    :::image type="content" source="./media/events-pane.png" alt-text="事件窗格中有一个用于创建新订阅的选项。" :::

1. 输入 `Event Subscription` 和 `System Topic` 的名称。 默认情况下，设置了事件类型“键值修改”和“键值删除”，该设置可更改，方法是使用“筛选器”选项卡，选择发送“推送事件”的确切原因。

    :::image type="content" source="./media/create-event-subscription.png" alt-text="事件需要名称、主题和筛选器。" :::

1. 选择 `Endpoint Type` 作为 `Web Hook`，选择 `Select an endpoint`。

    :::image type="content" source="./media/event-subscription-webhook-endpoint.png" alt-text="“选择终结点”会创建一个用于输入终结点 URI 的新边栏选项卡。" :::

1. 终结点是应用程序 URI +“/actuator/appconfiguration-refresh?{your-token-name}={your-token-secret}”。 例如 `https://my-azure-webapp.azurewebsites.net/actuator/appconfiguration-refresh?myToken=myTokenSecret`

1. 单击 `Create` 以添加事件订阅。 选择 `Create` 后，将会向应用程序发送 Webhook 注册请求。 这由 Azure 应用程序配置客户端库接收、验证并返回有效响应。

1. 单击 `Events` 窗格中的 `Event Subscriptions` 以验证订阅是否已成功创建。

    :::image type="content" source="./media/event-subscription-view-webhook.png" alt-text="Webhook 显示在页面底部的表中。" :::

> [!NOTE]
> 订阅配置更改时，可以使用一个或多个筛选器来减少发送到应用程序的事件数。 这些筛选器可以配置为[事件网格订阅筛选器](../event-grid/event-filtering.md)或[服务总线订阅筛选器](../service-bus-messaging/topic-filters.md)。 例如，订阅筛选器可用于仅订阅以特定字符串开头的键的更改的事件。

## <a name="verify-and-test-application"></a>验证并测试应用程序

1. 应用程序运行以后，请使用 curl 测试该应用程序，例如  ：

   ```cmd
   curl -X GET http://localhost:8080
   ```

1. 打开 Azure 门户并导航到与应用程序关联的应用程序配置资源。 在“操作”下选择“配置资源管理器”并更新以下密钥值 ：

    | 密钥 | 值 |
    |---|---|
    | application/config.message | Hello - Updated |

1. 刷新浏览器页面，查看显示的新消息。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已启用 Java 应用，以通过应用程序配置动态刷新配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)