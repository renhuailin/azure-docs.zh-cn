---
title: 快速入门：在 Azure Arc 上创建函数应用
description: 通过部署第一个函数应用，开始在 Azure Arc 上使用 Azure Functions。
ms.topic: quickstart
ms.date: 05/10/2021
ms.openlocfilehash: 3a9c92e500e79b68688ec526baa2a77b8df2ccef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723013"
---
# <a name="create-your-first-function-on-azure-arc-preview"></a>在 Azure Arc 上创建第一个函数（预览版）

在本快速入门中，你将创建一个 Azure Functions 项目，并将其部署到在[支持 Azure Arc 的 Kubernetes 群集](../azure-arc/kubernetes/overview.md)上运行的函数应用。 若要了解详细信息，请参阅 [Azure Arc 上的应用服务、Functions 和逻辑应用](../app-service/overview-arc-integration.md)。此方案仅支持在 Linux 上运行的函数应用。   

> [!NOTE]
> 对在支持 Arc 的 Kubernetes 群集上运行函数的支持目前处于预览阶段。  
>  
> 目前不支持将 PowerShell 函数项目发布到支持 Azure Arc 的 Kubernetes 群集。 如果需要将 PowerShell 函数部署到支持 Azure Arc 的 Kubernetes 群集，请[在容器中创建函数应用](create-first-function-arc-custom-container.md)。 

## <a name="prerequisites"></a>先决条件

在本地计算机上：

# <a name="c"></a>[C\#](#tab/csharp)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.0.3245 或更高版本。
+ [Azure CLI](/cli/azure/install-azure-cli) 版本 2.4 或更高版本。

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/) 版本 12。 还支持 Node.js 版本 10。
+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.0.3245 或更高版本。
+ [Azure CLI](/cli/azure/install-azure-cli) 版本 2.4 或更高版本。

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions 支持的 Python 版本](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.0.3245 或更高版本。
+ [Azure CLI](/cli/azure/install-azure-cli) 版本 2.4 或更高版本。

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>创建本地函数项目

在 Azure Functions 中，函数项目是用于部署和执行一个或多个单独函数的单元，每个函数响应特定的触发器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

1. 按如下所示运行 `func init` 命令，在名为 LocalFunctionProj 的文件夹中创建使用指定运行时的函数项目：  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript
    ```

    # <a name="python"></a>[Python](#tab/python)

    Python 需要虚拟环境，用于虚拟环境的命令包括 bash 和 Windows 命令行。
    
     + Bash： 

        ```bash
        python -m venv .venv
        source .venv/bin/activate
        ```
    
     + 命令行：

        ```cmd
        py -m venv .venv
        .venv\scripts\activate
        ```  

    现在，在虚拟环境中创建项目。 
    
    ```console
    func init LocalFunctionProj --python
    ```

    ---

1. 导航到项目文件夹：

    ```console
    cd LocalFunctionProj
    ```

    此文件夹包含项目的各个文件，其中包括名为 [local.settings.json](functions-develop-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 默认情况下，local.settings.json 文件会从 .gitignore 文件的源代码管理中排除。  此排除是因为该文件可以包含从 Azure 下载的机密。

1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数 (HttpExample) 的唯一名称，`--template` 参数指定该函数的触发器 (HTTP)。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  
[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>创建 Azure 资源 

你需要创建另外两个资源，然后才能将函数代码部署到新的应用服务 Kubernetes 环境：

- 工具当前所需的[存储帐户](../storage/common/storage-account-create.md)，它不是环境的一部分。
- 一个函数应用：提供用于执行函数代码的上下文。 函数应用在应用服务 Kubernetes 环境中运行，并映射到本地函数项目。 函数应用可将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

> [!NOTE]
> 函数应用以专用（应用服务）计划在应用服务 Kubernetes 环境中运行。 如果在没有现有计划的情况下创建函数应用，则系统会为你创建正确的计划。  

### <a name="create-storage-account"></a>创建存储帐户

使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令在资源组和区域中创建常规用途存储帐户：

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

> [!NOTE]  
> Azure Functions 工具目前需要存储帐户。 

在上一个示例中，将 `<STORAGE_NAME>` 替换为适合你且在 Azure 存储中唯一的名称。 名称只能包含 3 到 24 个数字和小写字母字符。 `Standard_LRS` 指定 [Functions 支持](storage-considerations.md#storage-account-requirements)的常规用途帐户。 `--location` 值是标准 Azure 区域。 

### <a name="create-the-function-app"></a>创建函数应用

运行 [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令以在环境中创建新的函数应用。

# <a name="c"></a>[C\#](#tab/csharp)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet 
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8
```
---

在此示例中，请将 `<CUSTOM_LOCATION_ID>` 替换为你为应用服务 Kubernetes 环境确定的自定义位置的 ID。 另请将 `<STORAGE_NAME>` 替换为上一步中使用的帐户的名称，并将 `<APP_NAME>` 替换为适合你的全局唯一名称。 

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

由于在支持 Azure Arc 的 Kubernetes 群集上完成完整部署可能需要一些时间，因此可能需要重新运行以下命令来验证已发布的函数：

```command
func azure functionapp list-functions
``` 

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

## <a name="next-steps"></a>后续步骤

你的函数应用已在支持 Arc 的应用服务 Kubernetes 环境的容器中运行，现在可以通过添加队列存储输出绑定来将其连接到 Azure 存储。

# <a name="c"></a>[C\#](#tab/csharp)  

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-javascript)

# <a name="python"></a>[Python](#tab/python)  

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

---
