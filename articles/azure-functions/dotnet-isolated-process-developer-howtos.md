---
title: 使用 Azure Functions 开发和发布 .NET 5 函数
description: 了解如何使用 .NET 5.0 创建和调试 C# 函数，然后将本地项目部署到 Azure Functions 中的无服务器托管。
ms.date: 05/03/2021
ms.topic: how-to
recommendations: false
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 1de1aaf8f293621d69534897add89c8a29a7916e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752394"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>使用 Azure Functions 开发和发布 .NET 5 函数 

本文介绍如何使用 .NET 5.0 来处理 C# 函数，后者在 Azure Functions 运行时的进程外运行。 你将了解如何在本地创建、调试这些 .NET 隔离进程函数，以及如何将其发布到 Azure。 在 Azure 中，这些函数在支持 .NET 5.0 的隔离进程中运行。 若要了解详细信息，请参阅[有关在 Azure 中的 .NET 5.0 上运行函数的指南](dotnet-isolated-process-guide.md)。

如果你不需要支持 .NET 5.0 或者在进程外运行函数，可以改为[创建 C# 类库函数](functions-create-your-first-function-visual-studio.md)。

## <a name="prerequisites"></a>先决条件

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="development-environment-vscode,development-environment-cli"  
+ [.NET 5.0 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.0.3381 或更高版本。

+ [Azure CLI](/cli/azure/install-azure-cli) 2.20 或更高版本。  
::: zone-end  
::: zone pivot="development-environment-vscode"  
+ 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。  

+ Visual Studio Code 的 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。  

+ Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 1.3.0 或更高版本。
::: zone-end  
::: zone pivot="development-environment-vs"  
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/)，版本 16.10 或更高版本。 安装必须包含“Azure 开发”或“ASP.NET 和 Web 开发”工作负载。  
::: zone-end  

## <a name="create-a-local-function-project"></a>创建本地函数项目

在 Azure Functions 中，有一个函数项目是一个或多个单独函数（每个函数响应特定的触发器）的容器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

::: zone pivot="development-environment-vscode"  
1. 在活动栏中选择“Azure”图标，然后在“Azure:函数”区域中选择“创建新项目...”图标。

    ![选择“创建新项目”](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 为项目工作区选择目录位置，然后选择“选择”  。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 在这种情况下，请不要选择属于工作区内的项目文件夹。

1. 根据提示提供以下信息：

    + **选择函数项目的语言**：选择`C#`。

    + **选择 .NET 运行时**：选择“`.NET 5 isolated`”。

    + **为项目的第一个函数选择模板**：选择`HTTP trigger`。

    + **提供函数名称**：键入 `HttpExample`。

    + **提供命名空间**：键入 `My.Functions`。

    + **授权级别**：选择 `Anonymous`，这使任何人都可以调用你的函数终结点。 若要了解授权级别，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。

    + **选择打开项目的方式**：选择`Add to workspace`。

1. Visual Studio Code 将使用此信息生成一个包含 HTTP 触发器的 Azure Functions 项目。 可以在资源管理器中查看本地项目文件。 若要详细了解所创建的文件，请参阅[生成的项目文件](functions-develop-vs-code.md#generated-project-files)。
::: zone-end  
::: zone pivot="development-environment-cli"  

1. 按如下所示运行 `func init` 命令，在名为 LocalFunctionProj 的文件夹中创建函数项目：  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    指定 `dotnetisolated` 将创建在 .NET 5.0 上运行的项目。


1. 导航到项目文件夹：

    ```console
    cd LocalFunctionProj
    ```

    此文件夹包含项目的各个文件，其中包括 [local.settings.json](functions-develop-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 配置文件。 由于 *local.settings.json* 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 *.gitignore* 文件的源代码管理中排除。

1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数 (HttpExample) 的唯一名称，`--template` 参数指定该函数的触发器 (HTTP)。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` 创建 HttpExample.cs 代码文件。
::: zone-end  

::: zone pivot="development-environment-vs"

1. 在 Visual Studio 菜单中，选择“文件” > “新建” > “项目”。

1. 在“创建新项目”中，在搜索框中输入“functions”，选择“Azure Functions”模板，然后选择“下一步”。

1. 在“配置新项目”中，输入项目的“项目名称”，然后选择“创建”。 函数应用名称必须可以充当 C# 命名空间，因此请勿使用下划线、连字符或任何其他的非字母数字字符。

1. 对于“创建新的 Azure Functions 应用程序”设置，请使用下表中的值：

    | 设置      | 值  | 说明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET 版本** | .NET 5（独立） | 此值创建在独立进程中在 .NET 5.0 上运行的函数项目。   |
    | **函数模板** | **HTTP 触发器** | 此值会创建由 HTTP 请求触发的函数。 |
    | 存储帐户(AzureWebJobsStorage)  | 存储模拟器 | 由于 Azure 中的函数应用需要存储帐户，因此在将项目发布到 Azure 时会分配或创建一个存储帐户。 HTTP 触发器不使用 Azure 存储帐户连接字符串；所有其他触发器类型需要有效的 Azure 存储帐户连接字符串。  |
    | **授权级别** | **匿名** | 在未提供密钥的情况下，任何客户端都可以触发创建的函数。 通过此授权设置可以轻松测试新函数。 有关密钥和授权的详细信息，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)和 [HTTP 和 Webhook 绑定](functions-bindings-http-webhook.md)。 |
    
    
    ![Azure Functions 项目设置](./media/dotnet-isolated-process-developer-howtos/functions-project-settings.png)

    请确保将“授权级别”设置为“匿名”。  如果选择默认级别的 **函数**，需要在请求中提供 [函数密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)才能访问函数终结点。

1. 选择“创建”以创建函数项目和 HTTP 触发器函数。

Visual Studio 将创建一个项目和一个包含 HTTP 触发器函数类型样本代码的类。 样板代码发送“欢迎使用 Azure Functions!” HTTP 响应。 `HttpTrigger` 属性指定该函数将由某个 HTTP 请求触发。 

## <a name="rename-the-function"></a>重命名函数

`FunctionName` 方法属性设置函数的名称（默认情况下生成为 `Function1`）。 由于工具不允许在创建项目时覆盖默认函数名称，请花一点时间为函数类、文件和元数据创建更好的名称。

1. 在“文件资源管理器”中，右键单击 Function1.cs 文件并将其重命名为 `HttpExample.cs`。

1. 在代码中，将 Function1 类重命名为 `HttpExample`。

1. 在名为 `Run` 的方法 `HttpTrigger` 中，将 `FunctionName` 方法属性重命名为 `HttpExample`，将值传递给 `GetLogger` 方法。

函数定义现在应如以下代码所示：

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-isolated/HttpExample.cs" range="9-15":::
 
重命名函数以后，即可在本地计算机上对其进行测试。

## <a name="run-the-function-locally"></a>在本地运行函数

Visual Studio 与 Azure Functions Core Tools 集成，这样你便可使用完整的 Azure Functions 运行时在本地测试函数。  

1. 若要运行函数，请在 Visual Studio 中按 <kbd>F5</kbd>。 你可能需要启用防火墙例外，这样工具才能处理 HTTP 请求。 在本地运行函数时，永远不会强制实施授权级别。

1. 从 Azure Functions 运行时输出复制函数的 URL 并运行请求。 函数成功运行且日志写入运行时输出时，会显示“欢迎使用 Functions”消息。 

1. 若要停止调试，请在 Visual Studio 中按 <kbd>Shift</kbd>+<kbd>F5</kbd>。

确认该函数可以在本地计算机上正确运行以后，即可将项目发布到 Azure。

::: zone-end

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>创建函数的支持性 Azure 资源

在将函数代码部署到 Azure 之前，需要创建三个资源：

- 一个[资源组](../azure-resource-manager/management/overview.md)：相关资源的逻辑容器。
- 一个[存储帐户](../storage/common/storage-account-create.md)：用于维护有关函数的状态和其他信息。
- 一个函数应用：提供用于执行函数代码的环境。 函数应用映射到本地函数项目，可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。

使用以下命令创建这些项。

1. 请登录到 Azure（如果尚未这样做）：

    ```azurecli
    az login
    ```

    使用 [az login](/cli/azure/reference-index#az_login) 命令登录到 Azure 帐户。

1. 在 `westeurope` 区域中创建名为 `AzureFunctionsQuickstart-rg` 的资源组：

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [az group create](/cli/azure/group#az_group_create) 命令可创建资源组。 通常，你会在从 `az account list-locations` 命令返回的、离你近的某个可用区域中创建资源组和资源。

1. 在资源组和区域中创建常规用途存储帐户：

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令可创建存储帐户。 

    在上一个示例中，将 `<STORAGE_NAME>` 替换为适合你且在 Azure 存储中唯一的名称。 名称只能包含 3 到 24 个数字和小写字母字符。 `Standard_LRS` 指定 [Functions 支持](storage-considerations.md#storage-account-requirements)的常规用途帐户。

1. 在 Azure 中创建函数应用：
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令可在 Azure 中创建函数应用。 
    
    在上一个示例中，请将 `<STORAGE_NAME>` 替换为在上一步骤中使用的帐户的名称，并将 `<APP_NAME>` 替换为适合自己的全局唯一名称。 `<APP_NAME>` 也是函数应用的默认 DNS 域。 
    
    此命令会创建一个函数应用，该应用运行 [Azure Functions 消耗计划](consumption-plan.md)中的 .NET 5.0。 就本文中的操作产生的用量来说，此计划应该是免费的。 此命令还在同一资源组中预配关联的 Azure Application Insights 实例。 使用该实例可以监视函数应用并查看日志。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 该实例在激活之前不会产生费用。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

:::zone pivot="development-environment-vs"

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

必须在 Azure 订阅中有一个函数应用，然后才能发布项目。 Visual Studio 发布会在你首次发布项目时为你创建一个函数应用。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>在 Azure 中验证函数

1. 在 Cloud Explorer 中，应已选中新的函数应用。 如果未选中，请依次展开你的订阅>“应用服务”，并选择新的函数应用。

1. 右键单击函数应用，然后选择“在浏览器中打开”。 这会在默认的 Web 浏览器中打开函数应用的根，并显示指示函数应用正在运行的页面。 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="函数应用正在运行":::

1. 在浏览器的地址栏中，将路径 `/api/HttpExample` 追加到基 URL，然后运行请求。

1. 转到此 URL，你会在浏览器中看到与本地运行时相同的响应。

:::zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

在本部分，你将在 Azure 订阅中创建一个函数应用和相关资源，然后部署代码。

> [!IMPORTANT]
> 发布到现有函数应用将覆盖该应用在 Azure 中的内容。


1. 在活动栏中选择“Azure”图标，然后在“Azure:函数”区域中，选择“部署到函数应用...”按钮。

    ![将项目发布到 Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. 根据提示提供以下信息：

    - **选择文件夹**：从工作区中选择一个文件夹，或浏览到包含函数应用的文件夹。 如果已打开有效的函数应用，则不会看到此提示。

    - **选择订阅**：选择要使用的订阅。 如果只有一个订阅，则不会看到此提示。

    - **在 Azure 中选择函数应用**：选择`- Create new Function App`。 （请不要选择本文中未介绍的 `Advanced` 选项。）
      
    - **输入函数应用的全局唯一名称**：键入在 URL 路径中有效的名称。 将对你键入的名称进行验证，以确保其在 Azure Functions 中是唯一的。
    
    - **选择运行时堆栈**：选择`.NET 5 (non-LTS)`。 
    
    - **选择新资源的位置**：为了获得更好的性能，请选择你附近的 [区域](https://azure.microsoft.com/regions/)。 
    
    在 Azure 中创建单个资源时，你会在通知区域中看到这些资源的状态。

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure 资源创建通知":::
    
1.  完成后，将使用基于函数应用名称的名称在订阅中创建以下 Azure 资源：
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    创建函数应用并应用了部署包之后，会显示一个通知。 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. 在此通知中选择“查看输出”以查看创建和部署结果，其中包括你创建的 Azure 资源。 如果错过了通知，请选择右下角的响铃图标以再次查看。

    ![创建完成通知](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>清理资源

你创建了完成本文所需的资源。 这些资源可能需要付费，具体取决于[帐户状态](https://azure.microsoft.com/account/)和[服务定价](https://azure.microsoft.com/pricing/)。 

::: zone pivot="development-environment-cli"  
请使用以下命令删除资源组及其包含的所有资源，以免产生额外的费用。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
请使用以下步骤删除函数应用及其相关资源，以免产生任何额外的费用。

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 .NET 隔离函数](dotnet-isolated-process-guide.md)

