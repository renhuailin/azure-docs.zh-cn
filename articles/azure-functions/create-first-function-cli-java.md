---
title: 通过命令行创建 Java 函数 - Azure Functions
description: 了解如何通过命令行创建 Java 函数，然后将本地项目发布到 Azure Functions 中的无服务器托管。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-java-uiex
ms.openlocfilehash: 8f52ba7008b2a7b3f7734f652c92014e110e3df4
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287791"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>快速入门：在 Azure 中通过命令行创建 Java 函数

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

在本文中，我们使用命令行工具创建一个响应 HTTP 请求的 Java 函数。 在本地测试代码后，将代码部署到 Azure Functions 的无服务器环境。

如果 Maven 不是首选开发工具，请查看面向 Java 开发人员的类似教程：
+ [Gradle](./functions-create-first-java-gradle.md)
+ [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions)
+ [Visual Studio Code](create-first-function-vs-code-java.md)

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

## <a name="configure-your-local-environment"></a>配置本地环境

在开始之前，必须满足以下条件：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) 版本 3.x。

+ [Azure CLI](/cli/azure/install-azure-cli) 2.4 或更高版本。

+ [Java 开发人员工具包](/azure/developer/java/fundamentals/java-support-on-azure)版本 8 或 11。 `JAVA_HOME` 环境变量必须设置为正确版本的 JDK 的安装位置。     

+ [Apache Maven](https://maven.apache.org) 3.0 或更高版本。

### <a name="prerequisite-check"></a>先决条件检查

+ 在终端或命令窗口中，运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `az --version` 以检查 Azure CLI 版本是否为 2.4 或更高版本。

+ 运行 `az login` 登录到 Azure 并验证活动订阅。

## <a name="create-a-local-function-project"></a>创建本地函数项目

在 Azure Functions 中，有一个函数项目是一个或多个单独函数（每个函数响应特定的触发器）的容器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

1. 在空的文件夹中，运行以下命令以从 [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html) 生成 Functions 项目。 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```
    
    ---

    > [!IMPORTANT]
    > + 如果希望函数在 Java 11 上运行，请使用 `-DjavaVersion=11`。 若要了解详细信息，请参阅 [Java 版本](functions-reference-java.md#java-versions)。 
    > + 要完成本文中的步骤，`JAVA_HOME` 环境变量必须设置为正确版本的 JDK 的安装位置。

1. Maven 会请求你提供所需的值，以在部署上完成项目的生成。   
    系统提示时提供以下值：

    | Prompt | 值 | 说明 |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | 一个值，用于按照 Java 的[包命名规则](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)在所有项目中标识你的项目。 |
    | **artifactId** | `fabrikam-functions` | 一个值，该值是 jar 的名称，没有版本号。 |
    | **version** | `1.0-SNAPSHOT` | 选择默认值。 |
    | **package** | `com.fabrikam` | 一个值，该值是所生成函数代码的 Java 包。 使用默认值。 |

1. 键入 `Y` 或按 Enter 进行确认。

    Maven 在名为 artifactId 的新文件夹（在此示例中为 `fabrikam-functions`）中创建项目文件。 

1. 导航到项目文件夹：

    ```console
    cd fabrikam-functions
    ```

    此文件夹包含项目的各个文件，其中包括名为 [local.settings.json](functions-run-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 由于 *local.settings.json* 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 *.gitignore* 文件的源代码管理中排除。

### <a name="optional-examine-the-file-contents"></a>（可选）检查文件内容

如果需要，可以跳到[在本地运行函数](#run-the-function-locally)并稍后再检查文件内容。

#### <a name="functionjava"></a>Function.java
Function.java 包含一个接收 `request` 变量中的请求数据的 `run` 方法，该变量是使用 [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) 注释修饰的 [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage)，用于定义触发器行为。 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

响应消息由 [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API 生成。

#### <a name="pomxml"></a>pom.xml

为托管应用而创建的 Azure 资源的设置在插件的 configuration 元素中使用生成的 pom.xml 文件中 `com.microsoft.azure` 的 groupId 定义。 例如，以下 configuration 元素指示基于 Maven 的部署在 `westus` 区域中的 `java-functions-group` 资源组内创建一个函数应用。 该函数应用本身在 Windows 上运行，后者托管在 `java-functions-app-service-plan` 计划（默认情况下是一个无服务器消耗计划）中。

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

若要控制在 Azure 中创建资源的方式，可以更改这些设置，例如，在初始部署之前将 `runtime.os` 从 `windows` 更改为 `linux`。 有关 Maven 插件支持的设置的完整列表，请参阅[配置详细信息](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)。

#### <a name="functiontestjava"></a>FunctionTest.java

原型还会为函数生成单元测试。 更改函数以便在项目中添加绑定或新函数时，也需要修改 FunctionTest.java 文件中的测试。

## <a name="run-the-function-locally"></a>在本地运行函数

1. 通过从 LocalFunctionProj  文件夹启动本地 Azure Functions 运行时主机来运行函数：

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    在输出的末尾，应显示以下行：
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > 如果 HttpExample 未按如上所示出现，则可能是在项目的根文件夹外启动了主机。 在这种情况下，请按 **Ctrl**+**C** 停止主机，导航到项目的根文件夹，然后重新运行上一命令。

1. 将此输出中 `HttpExample` 函数的 URL 复制到浏览器，并追加查询字符串 `?name=<YOUR_NAME>`，使完整 URL 类似于 `http://localhost:7071/api/HttpExample?name=Functions`。 浏览器应显示回显查询字符串值的消息。 当你发出请求时，启动项目时所在的终端还会显示日志输出。

1. 完成后，请按 Ctrl+C 并选择 `y` 以停止函数主机 。

## <a name="deploy-the-function-project-to-azure"></a>将函数项目部署到 Azure

首次部署函数项目时，会在 Azure 中创建一个函数应用和相关的资源。 为托管应用而创建的 Azure 资源的设置在 [pom.xml 文件](#pomxml)中定义。 在本文中，我们将接受默认值。

> [!TIP]
> 若要创建在 Linux 而不是 Windows 上运行的函数应用，请将 pom.xml 文件中的 `runtime.os` 元素从 `windows` 更改为 `linux`。 [这些区域](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)支持在消耗计划中运行 Linux。 不能在同一个资源组中同时创建在 Linux 上运行的应用和在 Windows 上运行的应用。

1. 在部署之前，请使用 Azure CLI 或 Azure PowerShell 登录到 Azure 订阅。 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    使用 [az login](/cli/azure/reference-index#az_login) 命令登录到 Azure 帐户。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 登录到 Azure 帐户。

    ---

1. 使用以下命令将项目部署到新的函数应用。

    ```console
    mvn azure-functions:deploy
    ```
    
    这会在 Azure 中创建以下资源：
    
    + 资源组。 命名为 java-functions-group。
    + 存储帐户。 Functions 所需。 此名称根据存储帐户名称要求随机生成。
    + 托管计划。 在 westus 区域中为函数应用提供无服务器托管。 名称为 java-functions-app-service-plan。
    + 函数应用。 函数应用是函数的部署和执行单元。 名称根据 artifactId 随机生成，其后面追加了一个随机生成的数字。
    
    部署会打包项目文件，并使用 [zip 部署](functions-deployment-technologies.md#zip-deploy)将其部署到新的函数应用。 此代码从 Azure 中的部署包运行。

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>清理资源

若要继续执行[下一步](#next-steps)并添加 Azure 存储队列输出绑定，请保留所有资源，以备将来使用。

否则，请使用以下命令删除资源组及其包含的所有资源，以免产生额外的费用。

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
