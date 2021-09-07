---
title: 从命令行创建 C# 函数 - Azure Functions
description: 了解如何从命令行创建 C# 函数，然后将本地项目发布到 Azure Functions 中托管的无服务器实例。
ms.date: 08/15/2021
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
ms.openlocfilehash: c2344a13c1a3dc005d00933fdc182348be9bb0f2
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830601"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>快速入门：在 Azure 中从命令行创建 C# 函数

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

本文介绍如何使用命令行工具创建响应 HTTP 请求的 C# 函数。 在本地测试代码后，将代码部署到 Azure Functions 的无服务器环境。

本文支持创建两种类型的已编译 C# 函数： 

+ [进程内](create-first-function-cli-csharp.md?tabs=in-process) - 在与 Functions 主机进程相同的进程中运行。 若要了解详细信息，请参阅[使用 Azure Functions 开发 C# 类库函数](functions-dotnet-class-library.md)。
+ [隔离进程](create-first-function-cli-csharp.md?tabs=isolated-process) - 在单独的 .NET 工作进程中运行。 若要了解详细信息，请参阅[有关在 Azure 中的 .NET 5.0 上运行函数的指南](dotnet-isolated-process-guide.md)。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还提供了[基于 Visual Studio 代码的版本](create-first-function-vs-code-csharp.md)。

## <a name="configure-your-local-environment"></a>配置本地环境

在开始之前，必须满足以下条件：

[!INCLUDE [functions-cli-dotnet-prerequisites](../../includes/functions-cli-dotnet-prerequisites.md)]

+ 还需要一个具有有效订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

### <a name="prerequisite-check"></a>先决条件检查

验证你的先决条件，这取决于你是使用 Azure CLI 还是使用 Azure PowerShell 创建 Azure 资源：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 在终端或命令窗口中，运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `dotnet --list-sdks` 以检查是否安装了所需的版本。

+ 运行 `az --version` 以检查 Azure CLI 版本是否为 2.4 或更高版本。

+ 运行 `az login` 登录到 Azure 并验证活动订阅。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ 在终端或命令窗口中，运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `dotnet --list-sdks` 以检查是否安装了所需的版本。

+ 运行 `(Get-Module -ListAvailable Az).Version` 并验证版本是否为 5.0 或更高版本。 

+ 运行 `Connect-AzAccount` 登录到 Azure 并验证活动订阅。

---

## <a name="create-a-local-function-project"></a>创建本地函数项目

在 Azure Functions 中，有一个函数项目是一个或多个单独函数（每个函数响应特定的触发器）的容器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

1. 按如下所示运行 `func init` 命令，在名为 LocalFunctionProj 的文件夹中创建使用指定运行时的函数项目：  

    # <a name="in-process"></a>[进程内](#tab/in-process) 

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

    # <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

    ```csharp
    func init LocalFunctionProj --dotnet-isolated
    ```
    ---

1. 导航到项目文件夹：

    ```console
    cd LocalFunctionProj
    ```

    此文件夹包含项目的各个文件，其中包括名为 [local.settings.json](functions-develop-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 由于 *local.settings.json* 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 *.gitignore* 文件的源代码管理中排除。

1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数 (HttpExample) 的唯一名称，`--template` 参数指定该函数的触发器 (HTTP)。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` 创建 HttpExample.cs 代码文件。

### <a name="optional-examine-the-file-contents"></a>（可选）检查文件内容

如果需要，可以跳到[在本地运行函数](#run-the-function-locally)并稍后再检查文件内容。

#### <a name="httpexamplecs"></a>HttpExample.cs

从模板生成的函数代码取决于已编译的 C# 项目的类型。  

# <a name="in-process"></a>[进程内](#tab/in-process) 

*HttpExample.cs* 包含一个接收 `req` 变量中的请求数据的 `Run` 方法，该变量是使用 HttpTriggerAttribute  **修饰的 [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest)** ，用于定义触发器行为。

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

返回对象是 [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)，它将响应消息作为 [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) 或 [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) 返回。 

# <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

HttpExample.cs 包含一个接收 `req` 变量中的请求数据的 `Run` 方法，该变量是使用 HttpTriggerAttribute 修饰的 [HttpRequestData](/dotnet/api/microsoft.azure.functions.worker.http.httprequestdata) 对象，用于定义触发器行为。 由于采用独立的进程模型，`HttpRequestData` 是实际的 `HttpRequest` 的表示形式，而不是请求对象本身的表示形式。 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs":::

返回对象是一个 [HttpResponseData](/dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata) 对象，其中包含返回到 HTTP 响应的数据。 

---

要了解详细信息，请参阅 [Azure Functions HTTP 触发器和绑定](./functions-bindings-http-webhook.md?tabs=csharp)。

## <a name="run-the-function-locally"></a>在本地运行函数

1. 通过从 LocalFunctionProj  文件夹启动本地 Azure Functions 运行时主机来运行函数：

    ```
    func start
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
    
    >[!NOTE]  
    > 如果 HttpExample 未按如上所示出现，则可能是在项目的根文件夹外启动了主机。 在这种情况下，请按 **Ctrl**+**C** 停止主机，导航到项目的根文件夹，然后重新运行上一命令。

1. 将 `HttpExample` 函数的 URL 从此输出复制到浏览器：

    # <a name="in-process"></a>[进程内](#tab/in-process)
    
     在函数 URL 中，追加查询字符串 `?name=<YOUR_NAME>`，使完整的 URL 如 `http://localhost:7071/api/HttpExample?name=Functions`。 浏览器应显示回显查询字符串值的响应消息。 当你发出请求时，启动项目时所在的终端还会显示日志输出。

    # <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

    浏览到函数 URL，应会收到“欢迎使用Azure Functions”消息。

    ---

1. 完成后，请按 Ctrl+C 并选择 `y` 以停止函数主机 。

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. 在 Azure 中创建函数应用：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli/in-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令可在 Azure 创建函数应用。 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli/isolated-process)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime dotnet-isolated --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ``` 
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令可在 Azure 中创建函数应用。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/in-process)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location '<REGION>'
    ```

    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet 可在 Azure 中创建函数应用。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell/isolated-process)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet-isolated -FunctionsVersion 3 -Location '<REGION>'
    ```

    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet 可在 Azure 中创建函数应用。 

    ---
    
    在上一个示例中，请将 `<STORAGE_NAME>` 替换为在上一步骤中使用的帐户的名称，并将 `<APP_NAME>` 替换为适合自己的全局唯一名称。 `<APP_NAME>` 也是函数应用的默认 DNS 域。 
    
    此命令将创建一个函数应用，该应用在 [Azure Functions 消耗计划](consumption-plan.md)下指定的语言运行时中运行，根据本教程产生的用量，此操作是免费的。 该命令还会在同一资源组中预配关联的 Azure Application Insights 实例，可以使用它来监视函数应用和查看日志。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 该实例在激活之前不会产生费用。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

## <a name="invoke-the-function-on-azure"></a>在 Azure 上调用函数

由于函数使用 HTTP 触发器并且支持 GET 请求，因此可以通过向此函数的 URL 发出 HTTP 请求来调用它。 最简单的方法是在浏览器中执行此操作。  

# <a name="in-process"></a>[进程内](#tab/in-process) 

将 publish 命令的输出中显示的完整“调用 URL”复制到浏览器的地址栏，并追加查询参数 `?name=Functions`。 导航到此 URL 时，浏览器显示的输出应与本地运行函数时显示的输出类似。

# <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

将发布命令的输出中显示的完整调用 URL 复制到浏览器的地址栏。 导航到此 URL 时，浏览器显示的输出应与本地运行函数时显示的输出类似。

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>后续步骤

# <a name="in-process"></a>[进程内](#tab/in-process) 

> [!div class="nextstepaction"]
> [连接到 Azure 队列存储](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=in-process)

# <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

> [!div class="nextstepaction"]
> [连接到 Azure 队列存储](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp&tabs=isolated-process)

---