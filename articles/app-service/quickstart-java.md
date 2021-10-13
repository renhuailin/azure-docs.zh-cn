---
title: 快速入门：在 Azure 应用服务中创建 Java 应用
description: 在数分钟内将第一个 Java Hello World 部署到 Azure 应用服务。 可以通过 Maven 的 Azure Web 应用插件方便地部署 Java 应用。
keywords: azure, 应用服务, web 应用, windows, linux, java, maven, 快速入门
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: 5be4133f23764ab87e8c1fca3b176d680e1eeae1
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855605"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>快速入门：在 Azure 应用服务中创建 Java 应用

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。  本快速入门介绍如何将 [Azure CLI](/cli/azure/get-started-with-azure-cli) 与[用于 Maven 的 Azure Web 应用插件](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)配合使用来部署 .jar 文件或 .war 文件。 使用选项卡在 Java SE 和 Tomcat 说明之间切换。


> [!NOTE]
> 也可使用 IntelliJ 和 Eclipse 等常见 IDE 执行相同的操作。 请查看 [Azure Toolkit for IntelliJ 快速入门](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)或 [Azure Toolkit for Eclipse 快速入门](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)中的类似文档。


![在 Azure 应用服务中运行的示例应用](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>创建 Java 应用

# <a name="java-se"></a>[Java SE](#tab/javase)

克隆 [Spring Boot 入门](https://github.com/spring-guides/gs-spring-boot)示例项目。

```azurecli-interactive
git clone https://github.com/spring-guides/gs-spring-boot
```

将目录更改为已完成项目。

```azurecli-interactive
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

在 Cloud Shell 提示符下，执行以下 Maven 命令来创建一个名为 `helloworld` 的新应用：

```azurecli-interactive
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

然后，将工作目录更改为项目文件夹：

```azurecli-interactive
cd helloworld
```

# <a name="jboss-eap"></a>[JBoss EAP](#tab/jbosseap)

::: zone pivot="platform-windows"

JBoss EAP 仅适用于 Linux 版本的应用服务。 请选择本文顶部的“Linux”按钮，以查看 JBoss EAP 的快速入门说明。

::: zone-end
::: zone pivot="platform-linux"

克隆 Pet Store 演示应用程序。

```azurecli-interactive
git clone https://github.com/agoncal/agoncal-application-petstore-ee7.git
```

将目录更改为已克隆项目。

```azurecli-interactive
cd agoncal-application-petstore-ee7
```

::: zone-end

---

## <a name="configure-the-maven-plugin"></a>配置 Maven 插件

部署到 Azure 应用服务的过程会自动使用 Azure CLI 中的 Azure 凭据。 如果未在本地安装 Azure CLI，则 Maven 插件将使用 Oauth 或设备登录名进行身份验证。 有关详细信息，请参阅 [Maven 插件的身份验证](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)。

运行下面的 Maven 命令来配置部署。 此命令将帮助你设置应用服务操作系统、Java 版本和 Tomcat 版本。

```azurecli-interactive
mvn com.microsoft.azure:azure-webapp-maven-plugin:2.2.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. 如果系统提示“订阅”选项，通过在行首输入数字来选择适当的 `Subscription`。
2. 当系统提示“Web 应用”选项时，按 Enter 键选择默认选项 `<create>`。
3. 当系统提示 OS 选项时，通过输入 `1` 选择 Windows 。
4. 当系统提示“javaVersion”选项时，通过输入 `1` 选择“Java 8”。
5. 当系统提示“定价层”选项时，通过输入 `10` 选择“P1v2” 。
6. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    摘要输出将类似于下面所示的代码片段。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : centralus
    PricingTier : P1v2
    OS : Windows
    Java : Java 8
    Web server stack : Java SE
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 如果系统提示“订阅”选项，通过在行首输入数字来选择适当的 `Subscription`。
2. 当系统提示“Web 应用”选项时，按 Enter 键选择默认选项 `<create>`。
3. 当系统提示 OS 选项时，通过输入 `1` 选择 Windows 。
4. 当系统提示“javaVersion”选项时，通过输入 `1` 选择“Java 8”。
5. 当系统提示“webContainer”选项时，通过输入 `1` 选择“Tomcat 8.5” 。
6. 当系统提示“定价层”选项时，通过输入 `10` 选择“P1v2” 。
7. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    摘要输出将类似于下面所示的代码片段。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : centralus
    PricingTier : P1v2
    OS : Windows
    Java : Java 8
    Web server stack : Tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="jboss-eap"></a>[JBoss EAP](#tab/jbosseap)

JBoss EAP 仅适用于 Linux 版本的应用服务。 请选择本文顶部的“Linux”按钮，以查看 JBoss EAP 的快速入门说明。

---

::: zone-end
::: zone pivot="platform-linux"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. 当系统提示“订阅”选项时，通过在行首输入数字来选择适当的 `Subscription`。
1. 当系统提示“Web 应用”选项时，按 Enter 键选择默认选项 `<create>`。
1. 当系统提示 OS 选项时，按 Enter 键选择 Linux 。
2. 当系统提示“javaVersion”选项时，通过输入 `1` 选择“Java 8”。
3. 当系统提示“定价层”选项时，通过输入 `9` 选择“P1v2” 。
4. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : centralus
    PricingTier : P1v2
    OS : Linux
    Web server stack : Java SE
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 当系统提示“订阅”选项时，通过在行首输入数字来选择适当的 `Subscription`。
1. 当系统提示“Web 应用”选项时，按 Enter 键选择默认选项 `<create>`。
1. 当系统提示 OS 选项时，按 Enter 键选择 Linux 。
1. 当系统提示“javaVersion”选项时，通过输入 `1` 选择“Java 8”。
1. 当系统提示“webcontainer”选项时，请通过输入 `3` 选择“Tomcat 8.5” 。
1. 当系统提示“定价层”选项时，通过输入 `9` 选择“P1v2” 。
1. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : centralus
    PricingTier : P1v2
    OS : Linux
    Web server stack : Tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="jboss-eap"></a>[JBoss EAP](#tab/jbosseap)

1. 如果系统提示“订阅”选项，通过在行首输入数字来选择适当的 `Subscription`。
1. 当提示“Web 应用”选项时，按 Enter 接受默认选项 `<create>`。
1. 当系统提示 OS 选项时，按 Enter 键选择 Linux 。
1. 当系统提示“javaVersion”选项时，通过输入 `1` 选择“Java 8”。
1. 当系统提示“webContainer”选项时，请通过输入 `1` 选择“Jbosseap 7” 。
1. 当系统提示“定价层”选项时，通过输入 `1` 选择“P1v3” 
1. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : petstoreee7-1623451825408
    ResourceGroup : petstoreee7-1623451825408-rg
    Region : centralus
    PricingTier : P1v3
    OS : Linux
    Java : Java 8
    Web server stack: Jbosseap 7
    Deploy to slot : false
    Confirm (Y/N) [Y]: y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 01:01 min
    [INFO] Finished at: 2021-06-11T15:52:25-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

如果需要，可以直接在 `pom.xml` 中修改应用服务的配置。 下面列出了一些常见配置：

属性 | 必须 | 说明 | 版本
---|---|---|---
`<schemaVersion>` | false | 指定配置架构的版本。 支持的值是：`v1`、`v2`。 | 1.5.2
`<subscriptionId>` | false | 指定订阅 ID。 | 0.1.0+
`<resourceGroup>` | 是 | 用于 Web 应用的 Azure 资源组。 | 0.1.0+
`<appName>` | 是 | Web 应用的名称。 | 0.1.0+
`<region>` | false | 指定将托管 Web 应用的区域；默认值为 centralus。 [支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=app-service)部分中列出了所有有效区域。 | 0.1.0+
`<pricingTier>` | false | Web 应用的定价层。 生产工作负荷的默认值为 P1v2，而 B2 是建议用于 Java 开发/测试的最低值 。 [了解详细信息](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | false | 运行时环境配置，可以在[此处](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)查看详细信息。 | 0.1.0+
`<deployment>` | false | 部署配置，可以在[此处](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)查看详细信息。 | 0.1.0+

请注意 `<appName>` 和 `<resourceGroup>` 的值（在演示中相应地为 `helloworld-1590394316693` 和 `helloworld-1590394316693-rg`），后面会使用它们。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>部署应用

在 pom 文件中准备好所有配置后，只需要一个命令即可将 Java 应用部署到 Azure。

```azurecli-interactive
mvn package azure-webapp:deploy
```

::: zone pivot="platform-linux"

> [!NOTE]
> 对于 JBoss EAP，请运行 `mvn package azure-webapp:deploy -DskipTests` 以禁用测试，因为它需要在本地安装 Wildfly。 

::: zone-end

部署完成后，应用程序会在 `http://<appName>.azurewebsites.net/`（在演示中为 `http://helloworld-1590394316693.azurewebsites.net`）处准备就绪。 在本地 Web 浏览器中打开 url，你应该会看到

![在 Azure 应用服务中运行的示例应用](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

祝贺你！ 现已将第一个 Java 应用部署到应用服务。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，请通过门户删除资源组，或在 Cloud Shell 中运行以下命令来这样做：

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

此命令可能需要花费一点时间运行。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Java 连接到 Azure DB for PostgreSQL](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [设置 CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [定价信息](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [聚合日志和指标](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [纵向扩展](manage-scale-up.md)

> [!div class="nextstepaction"]
> [面向 Java 开发人员的 Azure 资源](/java/azure/)

> [!div class="nextstepaction"]
> [配置 Java 应用](configure-language-java.md)
