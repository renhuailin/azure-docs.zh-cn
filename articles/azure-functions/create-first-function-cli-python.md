---
title: 通过命令行创建 Python 函数 - Azure Functions
description: 了解如何通过命令行创建 Python 函数，然后将本地项目发布到 Azure Functions 中的无服务器托管。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-python-uiex
ms.openlocfilehash: f08a035b9d095035fb108140af7c010bc9f049ea
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573951"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>快速入门：在 Azure 中通过命令行创建 Python 函数

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

在本文中，你将使用命令行工具创建响应 HTTP 请求的 Python 函数。 在本地测试代码后，将代码部署到 Azure Functions 的无服务器环境。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还提供了[基于 Visual Studio 代码的版本](create-first-function-vs-code-python.md)。

## <a name="configure-your-local-environment"></a>配置本地环境

在开始之前，必须满足以下条件：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) 版本 3.x。
  
+ 以下用于创建 Azure 资源的工具之一：

    + [Azure CLI](/cli/azure/install-azure-cli) 版本 2.4 或更高版本。

    + [Azure PowerShell](/powershell/azure/install-az-ps) 版本 5.0 或更高版本。

+ [Azure Functions 支持的 Python 版本](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>先决条件检查

验证你的先决条件，这取决于你是使用 Azure CLI 还是使用 Azure PowerShell 创建 Azure 资源：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 在终端或命令窗口中，运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `az --version` 以检查 Azure CLI 版本是否为 2.4 或更高版本。

+ 运行 `az login` 登录到 Azure 并验证活动订阅。

+ 运行 `python --version` (Linux/macOS) 或 `py --version` (Windows)，以检查 Python 版本是否报告 3.8.x、3.7.x 或 3.6.x。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ 在终端或命令窗口中，运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `(Get-Module -ListAvailable Az).Version` 并验证版本是否为 5.0 或更高版本。 

+ 运行 `Connect-AzAccount` 登录到 Azure 并验证活动订阅。

+ 运行 `python --version` (Linux/macOS) 或 `py --version` (Windows)，以检查 Python 版本是否报告 3.8.x、3.7.x 或 3.6.x。

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>创建并激活虚拟环境

在适当的文件夹中，运行以下命令以创建并激活一个名为 `.venv` 的虚拟环境。 请务必使用受 Azure Functions 支持的 Python 3.8、3.7 或 3.6。

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

如果 Python 未在 Linux 分发版中安装 venv 包，请运行以下命令：

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

所有后续命令将在这个已激活的虚拟环境中运行。 

## <a name="create-a-local-function-project"></a>创建本地函数项目

在 Azure Functions 中，有一个函数项目是一个或多个单独函数（每个函数响应特定的触发器）的容器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

1. 按如下所示运行 `func init` 命令，在名为 LocalFunctionProj 的文件夹中创建使用指定运行时的函数项目：  

    ```console
    func init LocalFunctionProj --python
    ```

1. 导航到项目文件夹：

    ```console
    cd LocalFunctionProj
    ```
    
    此文件夹包含项目的各个文件，其中包括名为 [local.settings.json](functions-run-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 由于 *local.settings.json* 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 *.gitignore* 文件的源代码管理中排除。

1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数 (HttpExample) 的唯一名称，`--template` 参数指定该函数的触发器 (HTTP)。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` 创建一个与函数名称匹配的、包含项目所选语言适用的代码文件的子文件夹，以及一个名为 *function.json* 的配置文件。

### <a name="optional-examine-the-file-contents"></a>（可选）检查文件内容

如果需要，可以跳到[在本地运行函数](#run-the-function-locally)并稍后再检查文件内容。

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* 包含一个根据 *function.json* 中的配置触发的 `main()` Python 函数。

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

对于 HTTP 触发器，该函数将接收 *function.json* 中定义的变量 `req` 中的请求数据。 `req` 是 [azure.functions.HttpRequest 类](/python/api/azure-functions/azure.functions.httprequest)的实例。 在 *function.json* 中定义为 `$return` 的返回对象是 [azure.functions.HttpResponse 类](/python/api/azure-functions/azure.functions.httpresponse)的一个实例。 要了解详细信息，请参阅 [Azure Functions HTTP 触发器和绑定](./functions-bindings-http-webhook.md?tabs=python)。

#### <a name="functionjson"></a>function.json

*function.json* 是一个配置文件，其中定义了函数的输入和输出 `bindings`，包括触发器类型。

如果需要，可以更改 `scriptFile` 以调用不同的 Python 文件。

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

每个绑定都需要一个方向、类型和唯一的名称。 HTTP 触发器具有类型为 [`httpTrigger`](functions-bindings-http-webhook-trigger.md) 的输入绑定和类型为 [`http`](functions-bindings-http-webhook-output.md) 的输出绑定。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>创建函数的支持性 Azure 资源

在将函数代码部署到 Azure 之前，需要创建三个资源：

- 一个资源组：相关资源的逻辑容器。
- 一个存储帐户：维护有关项目的状态和其他信息。
- 一个函数应用：提供用于执行函数代码的环境。 函数应用映射到本地函数项目，可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

使用以下命令创建这些项。 支持 Azure CLI 和 PowerShell。

1. 请登录到 Azure（如果尚未这样做）：

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
    
1. 使用 Azure CLI 时，可以启用 `param-persist` 选项，它自动跟踪所创建资源的名称。 要了解详细信息，请参阅 [Azure CLI 持久参数](/cli/azure/param-persist-howto)。  

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az config param-persist on
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    
    Azure PowerShell 不支持此功能。
    
    ---

1. 在 `westeurope` 区域中创建名为 `AzureFunctionsQuickstart-rg` 的资源组。 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [az group create](/cli/azure/group#az_group_create) 命令可创建资源组。 通常，你会在从 `az account list-locations` 命令返回的、离你近的某个可用区域中创建资源组和资源。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令可创建资源组。 通常，你会在从 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 返回的、离你近的某个可用区域中创建资源组和资源。

    ---

    > [!NOTE]
    > 不能在同一资源组中托管 Linux 和 Windows 应用。 如果名为 `AzureFunctionsQuickstart-rg` 的现有资源组有 Windows 函数应用或 Web 应用，必须使用其他资源组。

1. 在资源组和区域中创建常规用途存储帐户：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令可创建存储帐户。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet 可创建存储帐户。

    ---

    在上一个示例中，将 `<STORAGE_NAME>` 替换为适合你且在 Azure 存储中唯一的名称。 名称只能包含 3 到 24 个数字和小写字母字符。 `Standard_LRS` 指定 [Functions 支持](storage-considerations.md#storage-account-requirements)的常规用途帐户。
    
    在本快速入门中使用的存储帐户只会产生几美分的费用。

1. 在 Azure 中创建函数应用：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --os-type linux
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令可在 Azure 创建函数应用。 如果使用的是 Python 3.7 或 3.6，请将 `--runtime-version` 分别更改为 `3.7` 或 `3.6`。
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet 可在 Azure 中创建函数应用。 如果使用的是 Python 3.7 或 3.6，请将 `-RuntimeVersion` 分别更改为 `3.7` 或 `3.6`。

    ---
    
    在上一个示例中，将 `<APP_NAME>` 替换为适合自己的全局唯一名称。  `<APP_NAME>` 也是函数应用的默认 DNS 域。 
    
    此命令将创建一个函数应用，该应用在 [Azure Functions 消耗计划](consumption-plan.md)下指定的语言运行时中运行，根据本教程产生的用量，此操作是免费的。 该命令还会在同一资源组中预配关联的 Azure Application Insights 实例，可以使用它来监视函数应用和查看日志。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 该实例在激活之前不会产生费用。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

运行以下命令以在 Azure 门户中查看 Application Insights 中的准实时[流式处理日志](functions-run-local.md#enable-streaming-logs)：

```console
func azure functionapp logstream <APP_NAME> --browser
```

在单独的终端窗口或浏览器中，再次调用远程函数。 终端中显示了 Azure 中函数执行的详细日志。 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[存在问题？请告诉我们。](https://aka.ms/python-functions-qs-survey)
