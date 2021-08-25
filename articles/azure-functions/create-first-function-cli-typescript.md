---
title: 通过命令行创建 TypeScript 函数 - Azure Functions
description: 了解如何通过命令行创建 TypeScript 函数，然后将本地项目发布到 Azure Functions 中的无服务器托管。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: d9c9f95de61581614ffe48c60f8928775f208b77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738575"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>快速入门：在 Azure 中通过命令行创建 TypeScript 函数

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

在本文中，我们使用命令行工具创建一个响应 HTTP 请求的 TypeScript 函数。 在本地测试代码后，将代码部署到 Azure Functions 的无服务器环境。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还提供了[基于 Visual Studio 代码的版本](create-first-function-vs-code-typescript.md)。

## <a name="configure-your-local-environment"></a>配置本地环境

在开始之前，必须满足以下条件：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) 版本 3.x。

+ 以下用于创建 Azure 资源的工具之一：

    + [Azure CLI](/cli/azure/install-azure-cli) 版本 2.4 或更高版本。

    + [Azure PowerShell](/powershell/azure/install-az-ps) 版本 5.0 或更高版本。

+ [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。

### <a name="prerequisite-check"></a>先决条件检查

验证你的先决条件，这取决于你是使用 Azure CLI 还是使用 Azure PowerShell 创建 Azure 资源：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 在终端或命令窗口中，运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `az --version` 以检查 Azure CLI 版本是否为 2.4 或更高版本。

+ 运行 `az login` 登录到 Azure 并验证活动订阅。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ 在终端或命令窗口中，运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `(Get-Module -ListAvailable Az).Version` 并验证版本是否为 5.0 或更高版本。 

+ 运行 `Connect-AzAccount` 登录到 Azure 并验证活动订阅。

---

## <a name="create-a-local-function-project"></a>创建本地函数项目

在 Azure Functions 中，有一个函数项目是一个或多个单独函数（每个函数响应特定的触发器）的容器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

1. 按如下所示运行 `func init` 命令，在名为 LocalFunctionProj 的文件夹中创建使用指定运行时的函数项目：  

    ```console
    func init LocalFunctionProj --typescript
    ```

1. 导航到项目文件夹：

    ```console
    cd LocalFunctionProj
    ```
    
    此文件夹包含项目的各个文件，其中包括名为 [local.settings.json](functions-develop-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 由于 *local.settings.json* 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 *.gitignore* 文件的源代码管理中排除。

1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数 (HttpExample) 的唯一名称，`--template` 参数指定该函数的触发器 (HTTP)。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    
    `func new` 创建一个与函数名称匹配的、包含项目所选语言适用的代码文件的子文件夹，以及一个名为 *function.json* 的配置文件。

### <a name="optional-examine-the-file-contents"></a>（可选）检查文件内容

如果需要，可以跳到[在本地运行函数](#run-the-function-locally)并稍后再检查文件内容。

#### <a name="indexts"></a>index.ts

*index.ts* 导出一个根据 *function.json* 中的配置触发的函数。

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

对于 HTTP 触发器，该函数将接收 *function.json* 中定义的 **HttpRequest** 类型的变量 `req` 中的请求数据。 在 *function.json* 中定义为 `$return` 的返回对象是响应。 

#### <a name="functionjson"></a>function.json

*function.json* 是一个配置文件，其中定义了函数的输入和输出 `bindings`，包括触发器类型。 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

每个绑定都需要一个方向、类型和唯一的名称。 HTTP 触发器具有类型为 [`httpTrigger`](functions-bindings-http-webhook-trigger.md) 的输入绑定和类型为 [`http`](functions-bindings-http-webhook-output.md) 的输出绑定。

## <a name="run-the-function-locally"></a>在本地运行函数

1. 通过从 LocalFunctionProj  文件夹启动本地 Azure Functions 运行时主机来运行函数：

    ```console
    npm install
    npm start
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
    > 如果 HttpExample 未按下面所示出现，则可能是在项目的根文件夹外启动了主机。 在这种情况下，请按 **Ctrl**+**C** 停止主机，导航到项目的根文件夹，然后重新运行上一命令。

1. 将此输出中 `HttpExample` 函数的 URL 复制到浏览器，并追加查询字符串 `?name=<your-name>`，使完整 URL 类似于 `http://localhost:7071/api/HttpExample?name=Functions`。 浏览器应会显示类似于 `Hello Functions` 的消息：

    ![在浏览器中本地运行函数后的结果](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    当你发出请求时，启动项目时所在的终端还会显示日志输出。

1. 准备就绪后，按 **Ctrl**+**C** 并选择 `y` 停止函数主机。

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. 在 Azure 中创建函数应用：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令可在 Azure 中创建函数应用。 如果使用的是 Node.js 10，请同时将 `--runtime-version` 更改为 `10`。
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet 可在 Azure 中创建函数应用。 如果使用的是 Node.js 10，请将 `-RuntimeVersion` 更改为 `10`。

    ---
        
    在上一个示例中，请将 `<STORAGE_NAME>` 替换为在上一步骤中使用的帐户的名称，并将 `<APP_NAME>` 替换为适合自己的全局唯一名称。 `<APP_NAME>` 也是函数应用的默认 DNS 域。 
    
    此命令将创建一个函数应用，该应用在 [Azure Functions 消耗计划](consumption-plan.md)下指定的语言运行时中运行，根据本教程产生的用量，此操作是免费的。 该命令还会在同一资源组中预配关联的 Azure Application Insights 实例，可以使用它来监视函数应用和查看日志。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 该实例在激活之前不会产生费用。

## <a name="deploy-the-function-project-to-azure"></a>将函数项目部署到 Azure

使用 Core Tools 将项目部署到 Azure 之前，请从 TypeScript 源文件创建一个生产就绪的 JavaScript 文件版本。

1. 使用以下命令来准备要部署的 TypeScript 项目：

    ```console
    npm run build:production 
    ```

1. 准备好所需的资源后，便可以使用 [func azure functionapp publish](functions-run-local.md#project-file-deployment) 命令将本地函数项目部署到 Azure 中的函数应用。 在以下示例中，请将 `<APP_NAME>` 替换为你的应用的名称。

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    如果看到错误“找不到名为...的应用”，请等待几秒后重试，因为在执行上一 `az functionapp create` 命令后，Azure 可能尚未完全初始化该应用。
    
    publish 命令显示类似于以下输出的结果（为简洁起见，示例中的结果已截断）：
    
    <pre>
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
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
