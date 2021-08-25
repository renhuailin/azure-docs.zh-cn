---
title: 快速入门：在自定义容器中创建基于 Azure Arc 的函数应用
description: 通过在自定义 Linux 容器中部署第一个函数应用，开始在 Azure Arc 上使用 Azure Functions。
ms.topic: quickstart
ms.date: 05/11/2021
ms.openlocfilehash: 48940e70e1678e4426efb34e40b3b16b742e4f5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746739"
---
# <a name="create-your-first-function-on-azure-arc-using-a-custom-container-preview"></a>使用自定义容器创建第一个基于 Azure Arc 的函数（预览版）

在本快速入门中，你将创建一个在自定义容器中运行的 Azure Functions 项目，并从 Docker Hub 帐户将其部署到[支持 Azure Arc 的 Kubernetes 群集](../azure-arc/kubernetes/overview.md)。 若要了解详细信息，请参阅 [Azure Arc 上的应用服务、Functions 和逻辑应用](../app-service/overview-arc-integration.md)。此方案仅支持在 Linux 上运行的函数应用。   

> [!NOTE]
> 对在支持 Arc 的 Kubernetes 群集上运行函数的支持目前处于预览阶段。  

## <a name="prerequisites"></a>先决条件

在本地计算机上：

# <a name="c"></a>[C\#](#tab/csharp)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.0.3245 或更高版本。
+ [Azure CLI](/cli/azure/install-azure-cli) 版本 2.4 或更高版本。
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/) 版本 12。 还支持 Node.js 版本 10。
+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.0.3245 或更高版本。
+ [Azure CLI](/cli/azure/install-azure-cli) 版本 2.4 或更高版本。
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions 支持的 Python 版本](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.0.3245 或更高版本。
+ [Azure CLI](/cli/azure/install-azure-cli) 版本 2.4 或更高版本。
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>创建本地函数项目

在 Azure Functions 中，函数项目是一个或多个单独函数的上下文，每个函数响应特定的触发器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

1. 按如下所示运行 `func init` 命令，在名为 LocalFunctionProj 的文件夹中创建使用指定运行时的函数项目：  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet --docker
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript --docker
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
    func init LocalFunctionProj --python --docker
    ```
    ---

    `--docker` 选项生成该项目的 `Dockerfile`，其中定义了适合用于 Azure Functions 和所选运行时的自定义容器。

1. 导航到项目文件夹：

    ```console
    cd LocalFunctionProj
    ```

    此文件夹包含项目的其他 Dockerfile 文件，其中包括名为 [local.settings.json](functions-develop-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 默认情况下，local.settings.json 文件会从 .gitignore 文件的源代码管理中排除。  此排除是因为该文件可以包含从 Azure 下载的机密。

1. 打开生成的 `Dockerfile` 并找到基础映像的 `3.0` 标记。 如果有 `3.0` 标记，请将其替换为 `3.0.15885` 标记。 例如，在 JavaScript 应用程序中，Docker 文件应该修改为包含 `FROM mcr.microsoft.com/azure-functions/node:3.0.15885`。 此版本的基础映像支持部署到已启用 Azure Arc 的 Kubernetes 群集。 

1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数 (HttpExample) 的唯一名称，`--template` 参数指定该函数的触发器 (HTTP)。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  

## <a name="build-the-container-image-and-test-locally"></a>生成容器映像并在本地测试

项目根目录中的 Dockerfile 描述了在容器中运行函数应用的最低环境要求。 Azure Functions 支持的基础映像的完整列表可以在 [Azure Functions 基础映像页](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。

1. 在项目根文件夹中运行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令，并提供名称 `azurefunctionsimage` 和标记 `v1.0.0`。   

    以下命令将为容器生成 Docker 映像。

    ```console
    docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
    ```

    在此示例中，将 `<DOCKER_ID>` 替换为 Docker Hub 帐户 ID。 该命令完成后，可在本地运行新容器。
    
1. 若要测试生成，请使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令并添加端口参数 `-p 8080:80`，在本地容器中运行映像。

    ```console
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```

    同样，将 `<DOCKER_ID` 替换为 Docker ID 并添加端口参数 `-p 8080:80`。

1. 映像在本地容器中运行后，请浏览到 `http://localhost:8080/api/HttpExample?name=Functions`，其中应该会显示与先前相同的“hello”消息。 由于 HTTP 触发的函数使用匿名授权，因此，即使函数在容器中运行，你仍可以调用该函数。 在本地容器中运行时，将强制执行函数访问密钥设置。 如果调用函数时遇到问题，请确保[对函数的访问](functions-bindings-http-webhook-trigger.md#authorization-keys)已设置为匿名。  

验证容器中的函数应用后，按 Ctrl+C 停止 Docker 。

## <a name="push-the-image-to-docker-hub"></a>将映像推送到 Docker Hub

Docker Hub 是托管映像并提供映像和容器服务的容器注册表。 若要共享映像（包括将映像部署到 Azure），必须将其推送到注册表。

1. 如果尚未登录到 Docker，请使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令登录（将 `<docker_id>` 替换为你的 Docker ID）。 此命令会提示输入用户名和密码。 如果出现“登录成功”消息，则表示已登录。

    ```console
    docker login
    ```
    
1. 登录后，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令将映像推送到 Docker Hub（同样，请将 `<docker_id>` 替换为你的 Docker ID）。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. 首次推送映像可能需要几分钟时间，具体取决于网络速度（推送后续的更改要快得多）。 在等待过程中，可以继续学习下一部分，并在另一个终端中创建 Azure 资源。

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>创建 Azure 资源 

你需要创建另外两个资源，然后才能将容器部署到新的应用服务 Kubernetes 环境：

- 工具当前所需的[存储帐户](../storage/common/storage-account-create.md)，它不是环境的一部分。
- 一个函数应用：提供用于运行容器的上下文。 函数应用在应用服务 Kubernetes 环境中运行，并映射到本地函数项目。 函数应用可将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

> [!NOTE]
> 函数应用以专用（应用服务）计划在应用服务 Kubernetes 环境中运行。 如果在没有现有计划的情况下创建函数应用，则系统会为你创建计划。  

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
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```
---

在此示例中，请将 `<CUSTOM_LOCATION_ID>` 替换为你为应用服务 Kubernetes 环境确定的自定义位置的 ID。 另请将 `<STORAGE_NAME>` 替换为上一步中使用的帐户的名称，将 `<APP_NAME>` 替换为适合你的全局唯一名称，并将 `<DOCKER_ID>` 替换为 Docker Hub ID。 

*deployment-container-image-name* 参数指定用于函数应用的映像。 可以使用 [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) 命令查看用于部署的映像的相关信息。 还可以使用 [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) 命令从另一映像进行部署。

首次创建函数应用时，它会从 Docker Hub 拉取初始映像。 也可以从 Docker Hub [启用到 Azure 的持续部署](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure)。  

若要了解如何在映像中启用 SSH，请参阅[启用 SSH 连接](functions-create-function-linux-custom-image.md#enable-ssh-connections)。

### <a name="set-required-app-settings"></a>设置所需的应用设置

运行以下命令，为存储帐户连接字符串创建应用设置：

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv)
az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
```

必须在本地计算机上在 Cloud Shell 或 Bash 中运行此代码。 将 `<STORAGE_NAME>` 替换为存储帐户的名称，并将 `<APP_NAME>` 替换为函数应用的名称。  

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
