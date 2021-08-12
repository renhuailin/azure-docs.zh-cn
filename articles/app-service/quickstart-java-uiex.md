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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 79cf3c2764035ef2e8c4418d172ba991a72bf3ff
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113285901"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>快速入门：在 Azure 应用服务中创建 Java 应用

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。  本快速入门介绍如何将 [Azure CLI](/cli/azure/get-started-with-azure-cli) 与[用于 Maven 的 Azure Web 应用插件](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)配合使用来部署 .jar 文件或 .war 文件。 

我们还提供了本文的 IDE 版本。 请查看 [Azure Toolkit for IntelliJ 快速入门](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)或 [Azure Toolkit for Eclipse 快速入门](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)。

## <a name="1-prepare-your-environment"></a>1.准备环境

在开始之前，必须满足以下条件：

+ 一个 <abbr title="用于维护 Azure 使用情况计费信息的配置文件。">Azure 帐户</abbr> （具有有效订阅） <abbr title="在 Azure 中管理资源的基本组织结构，通常与组织中的个人或部门相关联。">订阅</abbr>. [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure CLI](/cli/azure/install-azure-cli)。

+ [Java 开发人员工具包](/azure/developer/java/fundamentals/java-support-on-azure)版本 8 或 11。

+ [Apache Maven](https://maven.apache.org) 3.0 或更高版本。

Maven 插件会使用 Azure CLI 中的帐户凭据来部署到应用服务。 在继续操作之前[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。 有关详细信息，请参阅 [Maven 插件的身份验证](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)。

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2.创建 Java 应用

# <a name="java-se"></a>[Java SE](#tab/javase)

克隆 [Spring Boot 入门](https://github.com/spring-guides/gs-spring-boot)示例项目。

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

将目录更改为已完成项目。

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

在 Cloud Shell 提示符下，执行以下 Maven 命令来创建一个名为 `helloworld` 的新应用：

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

然后，将工作目录更改为项目文件夹：

```bash
cd helloworld
```

---

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3.配置 Maven 插件

运行下面的 Maven 命令来配置部署。 此命令将帮助你设置应用服务操作系统、Java 版本和 Tomcat 版本。

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. 当系统提示“订阅”选项时，通过在行首输入数字来选择适当的 `Subscription`。
1. 当系统提示“Web 应用”选项时，按 Enter 接受默认选项 `<create>` 或选择一个现有应用。
1. 当系统提示 OS 选项时，通过输入 `3` 选择 Windows 。
1. 当系统提示“定价层”选项时，通过输入 `2` 选择 B2 。
1. 按 Enter 使用默认的 Java 版本 Java 8。
1. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    摘要输出将类似于下面所示的代码片段。

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 当系统提示“订阅”选项时，通过在行首输入数字来选择适当的 `Subscription`。
1. 当系统提示“Web 应用”选项时，按 Enter 接受默认选项 `<create>` 或选择一个现有应用。
1. 当系统提示 OS 选项时，通过输入 `3` 选择 Windows 。
1. 当系统提示“定价层”选项时，通过输入 `2` 选择 B2 。
1. 按 Enter 使用默认的 Java 版本 Java 8。
1. 按 Enter 使用默认 Web 容器 Tomcat 8.5。
1. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    摘要输出将类似于下面所示的代码片段。

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. 当系统提示“订阅”选项时，通过在行首输入数字来选择适当的 `Subscription`。
1. 当系统提示“Web 应用”选项时，按 Enter 接受默认选项 `<create>` 或选择一个现有应用。
1. 当系统提示 OS 选项时，按 Enter 键选择 Linux 。
1. 当系统提示“定价层”选项时，通过输入 `2` 选择 B2 。
1. 按 Enter 使用默认的 Java 版本 Java 8。
1. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 当系统提示“订阅”选项时，通过在行首输入数字来选择适当的 `Subscription`。
1. 当系统提示“Web 应用”选项时，按 Enter 接受默认选项 `<create>` 或选择一个现有应用。
1. 当系统提示 OS 选项时，按 Enter 键选择 Linux 。
1. 当系统提示“定价层”选项时，通过输入 `2` 选择 B2 。
1. 按 Enter 使用默认的 Java 版本 Java 8。
1. 按 Enter 使用默认 Web 容器 Tomcat 8.5。
1. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end

如果需要，可以直接在 `pom.xml` 中修改应用服务的配置。 下面列出了一些常见配置：

属性 | 必选 | 说明 | 版本
---|---|---|---
`<schemaVersion>` | false | 指定配置架构的版本。 支持的值是：`v1`、`v2`。 | 1.5.2
`<subscriptionId>` | false | 指定订阅 ID。 | 0.1.0+
`<resourceGroup>` | true | Azure <abbr title="相关 Azure 资源的逻辑容器，可将其作为一个单元进行管理。">资源组</abbr> Web 应用的 Azure 资源组。 | 0.1.0+
`<appName>` | 是 | Web 应用的名称。 | 0.1.0+
`<region>` | 是 | 指定将托管 Web 应用的区域；默认值为“westeurope”。 [支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=app-service)部分中列出了所有有效区域。 | 0.1.0+
`<pricingTier>` | false | Web 应用的定价层。 生产工作负载的默认值为 P1V2，而 B2 是建议用于 Java 开发/测试的最低值 。 [了解详细信息](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | 是 | 运行时环境配置，可以在[此处](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)查看详细信息。 | 0.1.0+
`<deployment>` | 是 | 部署配置，可以在[此处](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)查看详细信息。 | 0.1.0+

请注意 `<appName>` 和 `<resourceGroup>` 的值（在示例输出中分别为 `helloworld-1590394316693` 和 `helloworld-1590394316693-rg`），后面会使用它们。

[报告问题](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4.部署应用

使用以下命令将 Java 应用部署到 Azure。

```bash
mvn package azure-webapp:deploy
```

部署完成后，应用程序会在 `http://<appName>.azurewebsites.net/` 处准备就绪。 在本地 web 浏览器中打开 url。 应看到显示以下内容：

![在 Azure 应用服务中运行的示例应用](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

祝贺你！ 现已将第一个 Java 应用部署到应用服务。

[报告问题](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5.清理资源

删除 Java 应用及其相关资源以避免产生任何后续成本。

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

此命令可能需要花费一点时间运行。

<br>
<hr/>

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
