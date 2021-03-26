---
title: 从命令行创建 C# 函数 - Azure Functions
description: 了解如何从命令行创建 C# 函数，然后将本地项目发布到 Azure Functions 中托管的无服务器实例。
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a78abea5bcc5925cb2e137d918c7217ae92b118e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044317"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>快速入门：在 Azure 中从命令行创建 C# 函数

> [!div class="op_single_selector" title1="选择你的函数语言： "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

在本文中，我们使用命令行工具创建一个响应 HTTP 请求的基于 C# 类库的函数。 在本地测试代码之后，将其部署到 Azure Functions 的 <abbr title="一种运行时计算环境，其中服务器的所有详细信息对应用程序开发人员来说都是透明的，这简化了部署和管理代码的过程。">无服务器</abbr> 环境 <abbr title="为应用程序提供低成本无服务器计算环境的 Azure 服务。">Azure Functions</abbr>.

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还提供了[基于 Visual Studio 代码的版本](create-first-function-vs-code-csharp.md)。

## <a name="1-prepare-your-environment"></a>1.准备环境

+ 获取具有有效订阅的 Azure <abbr title="用于维护 Azure 使用情况计费信息的配置文件。">account</abbr> 帐户 <abbr title="在 Azure 中管理资源的基本组织结构，通常与组织中的个人或部门关联。">订阅</abbr>. [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ 安装 [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

+ 安装 [Azure Functions Core Tools](functions-run-local.md#v2) 版本 3.x。

+ 使用 <abbr title="一组跨平台命令行工具，用于在本地开发计算机中使用 Azure 资源，是使用 Azure 门户的替代方法。">Azure CLI</abbr> 或 <abbr title="一个 PowerShell 模块，提供用于在本地开发计算机中使用 Azure 资源的命令，是使用 Azure 门户的替代方法。">Azure PowerShell</abbr> 创建 Azure 资源：

    + [Azure CLI](/cli/azure/install-azure-cli) 版本 2.4 或更高版本。

    + [Azure PowerShell](/powershell/azure/install-az-ps) 版本 5.0 或更高版本。

---

### <a name="2-verify-prerequisites"></a>2.验证先决条件

验证你的先决条件，这取决于你是使用 Azure CLI 还是使用 Azure PowerShell 创建 Azure 资源：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 在终端或命令窗口中，运行 `func --version` 以查看 <abbr title="用于在本地计算机上使用 Azure Functions 的命令行工具集。">Azure Functions Core Tools</abbr> 的版本是否为 3.x。

+ 运行 `az --version` 以检查 Azure CLI 版本是否为 2.4 或更高版本。

+ 运行 `az login` 以登录到 Azure 并验证有效订阅。

+ 运行 `dotnet --list-sdks` 以检查是否安装了 .NET Core SDK 版本 3.1.x

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `(Get-Module -ListAvailable Az).Version` 并验证版本是否为 5.0 或更高版本。 

+ 运行 `Connect-AzAccount` 以登录到 Azure 并验证有效订阅。

+ 运行 `dotnet --list-sdks` 以检查是否安装了 .NET Core SDK 版本 3.1.x

---

## <a name="3-create-a-local-function-project"></a>3.创建本地函数项目

在本部分，你将使用 C# 创建一个本地 <abbr title="一个逻辑容器，它用于一个或多个可同时部署和管理的单独函数。">Azure Functions 项目</abbr> 。 项目中的每个函数都响应特定的 <abbr title="调用函数代码的事件，例如 HTTP 请求、队列消息或特定时间。">触发器</abbr>.

1. 运行 `func init` 命令，在名为 LocalFunctionProj 的文件夹中创建使用指定运行时的函数项目：  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. 运行“cd LocalFunctionProj”以导航到 <abbr title="此文件夹包含项目的各个文件，其中包括名为 local.settings.json 和 host.json 的配置文件。 由于 local.settings.json 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 .gitignore 文件的源代码管理中排除。">项目文件夹</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. 使用以下命令将函数添加到项目：
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    `--name` 参数是函数的唯一名称 (HttpExample)。

    `--template` 参数指定函数的触发器 (HTTP)。

    
    <br/>   
    <details>  
    <summary><strong>可选：HttpExample.cs 的代码</strong></summary>  
    
    *HttpExample.cs* 包含一个接收 `req` 变量中的请求数据的 `Run` 方法，该变量是使用 HttpTriggerAttribute  **修饰的 [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest)** ，用于定义触发器行为。

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    返回对象是 [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)，它将响应消息作为 [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) 或 [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) 返回。 要了解详细信息，请参阅 [Azure Functions HTTP 触发器和绑定](./functions-bindings-http-webhook.md?tabs=csharp)。  
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4.在本地运行函数

1. 通过从 LocalFunctionProj  文件夹启动本地 Azure Functions 运行时主机来运行函数：

    ```
    func start
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

    <br/>
    <details>
    <summary><strong>在输出中看不到 HttpExample</strong></summary>

    如果 HttpExample 未出现，则可能是在项目的根文件夹外启动了主机。 在这种情况下，请按 <kbd>Ctrl+C</kbd> 停止主机，导航到项目的根文件夹，然后重新运行上一命令。
    </details>

1. 将此输出中 HttpExample 函数的 URL 复制到浏览器，并追加查询字符串 ?name=<YOUR_NAME>，使完整 URL 类似于 http://localhost:7071/api/HttpExample?name=Functions  。 浏览器应会显示类似于 Hello Functions 的消息：

    ![在浏览器中本地运行函数后的结果](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)


1. 选择 <kbd>Ctrl+C</kbd>，然后选择 <kbd>y</kbd> 以停止函数主机。

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5.创建函数的支持性 Azure 资源

在将函数代码部署到 Azure 之前，需要通过使用以下命令创建一个 <abbr title="相关 Azure 资源的逻辑容器，可将其作为一个单元进行管理。">资源组</abbr>、一个 <abbr title="包含所有 Azure 存储数据对象的帐户。 存储帐户为存储数据提供唯一的命名空间。">存储帐户</abbr>和一个 <abbr title="在 Azure 中托管无服务器函数的云资源，它提供运行函数的基础计算环境。">函数应用</abbr> ：

1. 请登录到 Azure（如果尚未这样做）：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


    ---

1. 在 `westeurope` 区域中创建名为 `AzureFunctionsQuickstart-rg` 的资源组。 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```

    [az group create](/cli/azure/group#az-group-create) 命令可创建资源组。 通常在附近的 <abbr title="对在其中分配资源的特定 Azure 数据中心的地理引用。">region</abbr> 中创建资源组和资源（使用从 `az account list-locations` 命令返回的可用区域）。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```


    ---

    不能在同一资源组中托管 Linux 和 Windows 应用。 如果名为 `AzureFunctionsQuickstart-rg` 的现有资源组有 Windows 函数应用或 Web 应用，必须使用其他资源组。
    
1. 在资源组和区域中创建常规用途 Azure 存储帐户：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    将 `<STORAGE_NAME>` 替换为适合自己且 <abbr title="在全球各地的所有 Azure 客户使用的所有存储帐户中，该名称都是唯一的。 例如，可使用个人姓名/公司名称、应用程序名称和数字标识符的组合，例如 contosobizappstorage20">在 Azure 存储中唯一的名称</abbr>. 名称只能包含 3 到 24 个数字和小写字母字符。 `Standard_LRS` 指定 [Functions 支持](storage-considerations.md#storage-account-requirements)的常规用途帐户。


1. 在 Azure 中创建函数应用。
将“<STORAGE_NAME>”替换为上一步中的名称。
将“<APP_NAME>”替换为全局唯一名称。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    将 `<STORAGE_NAME>` 替换为上一步中使用的帐户的名称。

    将 `<APP_NAME>` 替换为 <abbr title="该名称在全球各地的所有 Azure 客户中都必须是唯一的。 例如，可使用个人姓名/组织名称、应用程序名称和数字标识符的组合，例如 contoso-bizapp-func-20。">唯一名称</abbr>. `<APP_NAME>` 也是函数应用的默认 DNS 域。 

    <br/>
    <details>
    <summary><strong>Azure 上预配的资源的成本是多少？</strong></summary>

    此命令将创建一个函数应用，该应用在 [Azure Functions 消耗计划](consumption-plan.md)下指定的语言运行时中运行，根据本教程产生的用量，此操作是免费的。 该命令还会在同一资源组中预配关联的 Azure Application Insights 实例，可以使用它来监视函数应用和查看日志。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 该实例在激活之前不会产生费用。
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6.将函数项目部署到 Azure


将“func azure funtionapp publish <APP_NAME>”复制到终端，用应用的名称替换 `<APP_NAME>` 。
**运行**

```console
func azure functionapp publish <APP_NAME>
```

`publish` 命令显示类似于以下输出的结果（为简洁起见，示例中的结果已截断）：

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7.在 Azure 上调用函数

将 `publish` 命令的输出中显示的完整调用 URL 复制到浏览器的地址栏。 追加查询参数 &name=Functions 。 

![在 Azure 上运行函数后浏览器中的输出](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8.清理资源

如果继续执行[后续步骤](#next-steps)并添加 Azure 存储队列输出 <abbr title="函数和其他资源之间的声明性连接。 输入绑定为函数提供数据；输出绑定向其他资源提供来自函数的数据。">binding</abbr>，请将所有资源保存到正确位置，以便根据已完成的工作进行操作。

否则，请使用以下命令删除资源组及其包含的所有资源，以免产生额外的费用。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
