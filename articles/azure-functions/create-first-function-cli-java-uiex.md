---
title: 通过命令行创建 Java 函数 - Azure Functions
description: 了解如何通过命令行创建 Java 函数，然后将本地项目发布到 Azure Functions 中的无服务器托管。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1605bcc3a596276e8de99c45f290368691703c4f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752345"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>快速入门：在 Azure 中通过命令行创建 Java 函数

> [!div class="op_single_selector" title1="选择你的函数语言： "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

使用命令行工具创建响应 HTTP 请求的 Java 函数。 在本地测试代码，然后将代码部署到 Azure Functions 的无服务器环境。

完成本快速入门会在你的 Azure 帐户中产生最多几美分的费用 <abbr title="用于维护 Azure 使用情况计费信息的配置文件。">Azure 帐户</abbr>.

如果 Maven 不是你的首选开发工具，请查看面向使用 [Gradle](./functions-create-first-java-gradle.md)、[IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) 和 [Visual Studio Code](create-first-function-vs-code-java.md) 的 Java 开发人员的类似教程。

## <a name="1-prepare-your-environment"></a>1.准备环境

在开始之前，必须满足以下条件：

+ 具有有效订阅的 Azure 帐户 <abbr title="在 Azure 中管理资源的基本组织结构，通常与组织中的个人或部门关联。">订阅</abbr>. [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) 版本 3.x。

+ [Azure CLI](/cli/azure/install-azure-cli) 2.4 或更高版本。

+ [Java 开发人员工具包](/azure/developer/java/fundamentals/java-support-on-azure)版本 8 或 11。 `JAVA_HOME` 环境变量必须设置为正确版本的 JDK 的安装位置。

+ [Apache Maven](https://maven.apache.org) 3.0 或更高版本。

### <a name="prerequisite-check"></a>先决条件检查

+ 在终端或命令窗口中，运行 `func --version` 以查看 <abbr title="用于在本地计算机上使用 Azure Functions 的命令行工具集。">Azure Functions Core Tools</abbr> 的版本是否为 3.x。

+ 运行 `az --version` 以检查 Azure CLI 版本是否为 2.4 或更高版本。

+ 运行 `az login` 登录到 Azure 并验证活动订阅。

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2.创建本地函数项目

在 Azure Functions 中，函数项目是一个或多个单独函数的容器，每个函数响应特定的 <abbr title="调用函数代码的事件类型，例如 HTTP 请求、队列消息或特定时间。">触发器</abbr>. 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

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

    <br/>
    <details>
    <summary><strong>在 Java 11 上运行函数</strong></summary>

    如果希望函数在 Java 11 上运行，请使用 `-DjavaVersion=11`。 若要了解详细信息，请参阅 [Java 版本](functions-reference-java.md#java-versions)。
    </details>

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

<br/>
<details>
<summary><strong>在 LocalFunctionProj 文件夹中创建了哪些内容？</strong></summary>

此文件夹包含项目的各种文件，例如 Function.java、FunctionTest.java、和 pom.xml  。 还有一些名为 [local.settings.json](functions-develop-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 由于 *local.settings.json* 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 *.gitignore* 文件的源代码管理中排除。
</details>

<br/>
<details>
<summary><strong>Function.java 的代码</strong></summary>

Function.java 包含一个接收 `request` 变量中的请求数据的 `run` 方法，该变量是使用 [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) 注释修饰的 [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage)，用于定义触发器行为。 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

响应消息由 [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API 生成。

原型还会为函数生成单元测试。 更改函数以便在项目中添加绑定或新函数时，也需要修改 FunctionTest.java 文件中的测试。
</details>

<br/>
<details>
<summary><strong>pom.xml 的代码</strong></summary>

为托管应用而创建的 Azure 资源的设置在插件的 configuration 元素中使用生成的 pom.xml 文件中 `com.microsoft.azure` 的 groupId 定义。  例如，以下 configuration 元素指示基于 Maven 的部署在 `westus` 区域中的 `java-functions-group` 资源组内创建一个函数应用 <abbr title="对在其中分配资源的特定 Azure 数据中心的地理引用。">region</abbr>. 该函数应用本身在 Windows 上运行，后者托管在 `java-functions-app-service-plan` 计划（默认情况下是一个无服务器消耗计划）中。

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

若要控制在 Azure 中创建资源的方式，可以更改这些设置，例如，在初始部署之前将 `runtime.os` 从 `windows` 更改为 `linux`。 有关 Maven 插件支持的设置的完整列表，请参阅[配置详细信息](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)。
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3.在本地运行函数

1. 通过从 LocalFunctionProj 文件夹启动本地 Azure Functions 运行时主机来运行函数：

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    在输出的末尾，应显示以下行：

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    如果 HttpExample 未按如上所示出现，则可能是在项目的根文件夹外启动了主机。 在这种情况下，请按 <kbd>Ctrl+C</kbd> 停止主机，导航到项目的根文件夹，然后重新运行上一命令。

1. 将此输出中 `HttpExample` 函数的 URL 复制到浏览器，并追加查询字符串 `?name=<YOUR_NAME>`，使完整 URL 类似于 `http://localhost:7071/api/HttpExample?name=Functions`。 浏览器应会显示类似于 `Hello Functions` 的消息：

    ![在浏览器中本地运行函数后的结果](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    当你发出请求时，启动项目时所在的终端还会显示日志输出。

1. 完成后，请按 <kbd>Ctrl+C</kbd> 并选择 <kbd>y</kbd> 以停止函数主机。

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4.将函数项目部署到 Azure

首次部署函数项目时，会在 Azure 中创建一个函数应用和相关的资源。 为托管应用而创建的 Azure 资源的设置在 pom.xml 文件中定义。 在本文中，我们将接受默认值。

<br/>
<details>
<summary><strong>创建在 Linux 上运行的函数应用</strong></summary>

若要创建在 Linux 而不是 Windows 上运行的函数应用，请将 pom.xml 文件中的 `runtime.os` 元素从 `windows` 更改为 `linux`。 [这些区域](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)支持在消耗计划中运行 Linux。 不能在同一个资源组中同时创建在 Linux 上运行的应用和在 Windows 上运行的应用。
</details>

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

    部署会打包项目文件，并使用 [zip 部署](functions-deployment-technologies.md#zip-deploy)将其部署到新的函数应用。 此代码从 Azure 中的部署包运行。

<br/>
<details>
<summary><strong>在 Azure 中创建了哪些内容？</strong></summary>

+ 资源组。 命名为 java-functions-group。
+ 存储帐户。 Functions 所需。 此名称根据存储帐户名称要求随机生成。
+ 托管计划。 在 westus 区域中为函数应用提供无服务器托管。 名称为 java-functions-app-service-plan。
+ 函数应用。 函数应用是函数的部署和执行单元。 名称根据 artifactId 随机生成，其后面追加了一个随机生成的数字。
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5.在 Azure 上调用函数

由于函数使用 HTTP 触发器，因此，可以通过在浏览器中或使用 curl 等工具，向此函数的 URL 发出 HTTP 请求来调用它 <abbr title="用于生成对 URL 的 HTTP 请求的命令行工具；请参阅 https://curl.se/">curl</abbr>.

# <a name="browser"></a>[浏览器](#tab/browser)

将 `publish` 命令的输出中显示的完整调用 URL 复制到浏览器的地址栏，并追加查询参数 `&name=Functions`。 浏览器显示的输出应与本地运行函数时显示的输出类似。

![在 Azure 上运行函数后浏览器中的输出](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

结合“调用 URL”运行 [`curl`](https://curl.haxx.se/)，并追加参数 `&name=Functions`。 该命令的输出应是文本“Hello Functions”。

![使用 curl 在 Azure 上运行函数后的输出](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6.清理资源

如果继续执行[后续步骤](#next-steps)并添加 Azure 存储 <abbr title="Azure 存储中一种将函数与存储队列关联，以确保函数可在队列上创建消息的方法。">队列输出绑定</abbr>，请将所有资源保存到正确位置，以便根据已完成的工作进行操作。

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

<br>
<hr/>

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
