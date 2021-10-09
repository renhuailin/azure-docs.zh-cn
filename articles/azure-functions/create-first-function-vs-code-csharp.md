---
title: 使用 Visual Studio Code 创建 C# 函数 - Azure Functions
description: 了解如何创建 C# 函数，然后使用 Visual Studio Code 中的 Azure Functions 扩展将本地项目发布到 Azure Functions 中的无服务器托管。
ms.topic: quickstart
ms.date: 09/14/2021
ms.custom: devx-track-csharp
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-csharp-ieux
zone_pivot_groups: runtime-version-programming-functions
ms.openlocfilehash: 4ebb5e0ff94669cb273cda31b63cd16dd448c104
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577135"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>快速入门：在 Azure 中使用 Visual Studio Code 创建 C# 函数

[!INCLUDE [functions-runtime-version-dotnet](../../includes/functions-runtime-version-dotnet.md)]

在本文中，你将使用 Visual Studio Code 来创建响应 HTTP 请求的 C# 函数。 在本地测试代码后，将代码部署到 Azure Functions 的无服务器环境。

::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]    
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
> [!NOTE]
> 目前，Visual Studio Code 支持使用[进程内执行模式](functions-dotnet-class-library.md)创建只在 .NET 6 上运行的 C# 函数。
::: zone-end

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还有一个[基于 CLI 的版本](create-first-function-cli-csharp.md)。

## <a name="configure-your-environment"></a>配置环境

在开始之前，请确保已满足下列要求：

::: zone pivot="programming-runtime-functions-v3"
# <a name="in-process"></a>[进程内](#tab/in-process)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 版本 3.x。

+ 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。

+ 适用于 Visual Studio Code 的 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。  

+ 适用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

# <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

+ [.NET 5.0 SDK](https://dotnet.microsoft.com/download)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)。 生成过程所需。

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 版本 3.x。

+ 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。

+ 适用于 Visual Studio Code 的 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。  

+ 适用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

---
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
+ [.NET 6.0 SDK](https://dotnet.microsoft.com/download/dotnet/6.0)

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 版本 4.x。

+ 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。

+ 适用于 Visual Studio Code 的 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。  

+ 适用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。
::: zone-end

还需要一个具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>创建本地项目

在本部分，你将使用 Visual Studio Code 在 C# 中创建一个本地 Azure Functions 项目。 稍后在本文中，你要将函数代码发布到 Azure。

1. 在活动栏中选择“Azure”图标，然后在“Azure:函数”区域中选择“创建新项目...”图标。

    ![选择“创建新项目”](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 为项目工作区选择目录位置，然后选择“选择”  。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 在这种情况下，请不要选择属于工作区内的项目文件夹。

1. 根据提示提供以下信息：

    ::: zone pivot="programming-runtime-functions-v3"
    # <a name="in-process"></a>[进程内](#tab/in-process) 

    |Prompt|选择|
    |--|--|
    |选择函数项目的语言|选择 `C#`。|
    | **选择 .NET 运行时** | 选择 `.NET Core 3.1 LTS`。|
    |为项目的第一个函数选择模板|选择 `HTTP trigger`。|
    |提供函数名称|键入 `HttpExample`。|
    |**提供命名空间** | 键入 `My.Functions`。 |
    |**授权级别**|选择 `Anonymous`，这使任何人都可以调用你的函数终结点。 若要了解授权级别，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。|
    |选择打开项目的方式|选择 `Add to workspace`。|

    # <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

    |Prompt|选择|
    |--|--|
    |选择函数项目的语言|选择 `C#`。|
    | **选择 .NET 运行时** | 选择 `.NET 5.0 Isolated`。|
    |为项目的第一个函数选择模板|选择 `HTTP trigger`。|
    |提供函数名称|键入 `HttpExample`。|
    |**提供命名空间** | 键入 `My.Functions`。 |
    |**授权级别**|选择 `Anonymous`，这使任何人都可以调用你的函数终结点。 若要了解授权级别，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。|
    |选择打开项目的方式|选择 `Add to workspace`。|

    ---
    ::: zone-end
    ::: zone pivot="programming-runtime-functions-v4"
    |Prompt|选择|
    |--|--|
    |选择函数项目的语言|选择 `C#`。|
    | **选择 .NET 运行时** | 选择 `.NET 6.0`。<sup>*</sup>|
    |为项目的第一个函数选择模板|选择 `HTTP trigger`。|
    |提供函数名称|键入 `HttpExample`。|
    |**提供命名空间** | 键入 `My.Functions`。 |
    |**授权级别**|选择 `Anonymous`，这使任何人都可以调用你的函数终结点。 若要了解授权级别，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。|
    |选择打开项目的方式|选择 `Add to workspace`。|

    <sup>*</sup>如果不将 `.NET 6` 视为运行时选项，请确保已安装 4.x 版本的 Azure Functions Core Tools。
    ::: zone-end
    
1. Visual Studio Code 将使用此信息生成一个包含 HTTP 触发器的 Azure Functions 项目。 可以在资源管理器中查看本地项目文件。 若要详细了解所创建的文件，请参阅[生成的项目文件](functions-develop-vs-code.md#generated-project-files)。

[!INCLUDE [functions-run-function-test-local-vs-code-csharp](../../includes/functions-run-function-test-local-vs-code-csharp.md)]

确认该函数可以在本地计算机上正确运行以后，可以使用 Visual Studio Code 将项目直接发布到 Azure。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>后续步骤

你已使用 [Visual Studio Code](functions-develop-vs-code.md?tabs=csharp) 通过简单的 HTTP 触发函数创建了函数应用。 在下一篇文章中，你将通过连接到 Azure Cosmos DB 或 Azure 队列存储来扩展该函数。 若要详细了解如何连接到其他 Azure 服务，请参阅[将捆绑项添加到 Azure Functions 中的现有函数](add-bindings-existing-function.md?tabs=csharp)。 

::: zone pivot="programming-runtime-functions-v3"
# <a name="in-process"></a>[进程内](#tab/in-process) 

> [!div class="nextstepaction"]
> [连接 Azure Cosmos DB](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=in-process)
> [连接 Azure 队列存储](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=in-process)

# <a name="isolated-process"></a>[隔离进程](#tab/isolated-process)

> [!div class="nextstepaction"]
> [连接 Azure Cosmos DB](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=isolated-process)
> [连接 Azure 队列存储](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=isolated-process)

---
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
> [!div class="nextstepaction"]
> [连接 Azure Cosmos DB](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp&tabs=in-process)
> [连接 Azure 队列存储](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp&tabs=in-process)
::: zone-end

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
