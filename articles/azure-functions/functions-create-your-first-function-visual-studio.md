---
title: 快速入门：使用 Visual Studio 在 Azure 中创建第一个 C# 函数
description: 本快速入门将介绍如何使用 Visual Studio 创建 C# HTTP 触发的函数，并将其发布到在 .NET Core 3.1 上运行的 Azure Functions。
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/14/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, contperf-fy21q3-portal
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
zone_pivot_groups: runtime-version-programming-functions
ms.openlocfilehash: fb969f494c350d253d688d3a5379c30513aa64fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671238"
---
# <a name="quickstart-create-your-first-c-function-in-azure-using-visual-studio"></a>快速入门：使用 Visual Studio 在 Azure 中创建第一个 C# 函数

[!INCLUDE [functions-runtime-version-dotnet](../../includes/functions-runtime-version-dotnet.md)]

通过 Azure Functions，可以在 Azure 的无服务器环境中运行 C# 代码。 

在本文中，学习如何：

> [!div class="checklist"]
> * 使用 Visual Studio 创建 C# 类库项目。
> * 创建响应 HTTP 请求的函数。 
> * 在本地运行代码以验证函数行为。
> * 将代码项目部署到 Azure Functions。
 
::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
> [!NOTE]
> 目前，Azure Functions 4.0 处于预览阶段，Visual Studio 支持使用[进程内执行模式](functions-dotnet-class-library.md)创建只在 .NET 6 上运行的 C# 函数。
::: zone-end

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还提供了[基于 Visual Studio 代码的版本](create-first-function-vs-code-csharp.md)。

## <a name="prerequisites"></a>先决条件

::: zone pivot="programming-runtime-functions-v3"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/)。 请确保在安装过程中选择“Azure 开发”工作负载。 
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
+ [Visual Studio 2022 预览版 3 或更高版本](https://azure.microsoft.com/downloads/)。 请确保在安装过程中选择“Azure 开发”工作负载。 
::: zone-end

+ [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)。 如果还没有帐户，请在开始之前[创建免费帐户](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-a-function-app-project"></a>创建函数应用项目

::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]
::: zone-end

::: zone pivot="programming-runtime-functions-v4"
Visual Studio 中的 Azure Functions 项目模板创建了一个 C# 类库项目，该项目可发布到 Azure 中的函数应用。 可使用函数应用将函数分组为逻辑单元，以便更轻松地管理、部署、缩放和共享资源。

1. 在 Visual Studio 菜单中，选择“文件” > “新建” > “项目”。

1. 在“创建新项目”中，在搜索框中输入“functions”，选择“Azure Functions”模板，然后选择“下一步”。

1. 在“配置新项目”中，输入项目的“项目名称”，然后选择“创建”。 函数应用名称必须可以充当 C# 命名空间，因此请勿使用下划线、连字符或任何其他的非字母数字字符。

1. 对于“创建新的 Azure Functions 应用程序”设置，请使用下表中的值：

    | 设置      | 值  | 说明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET 版本** | **.NET 6** | 此值会创建一个函数项目，该项目在进程内使用 Azure Functions 运行时版本 4.x 运行。 Azure Functions 1.x 支持 .NET Framework。 有关详细信息，请参阅 [Azure Functions 运行时版本概述](./functions-versions.md)。   |
    | **函数模板** | **HTTP 触发器** | 此值会创建由 HTTP 请求触发的函数。 |
    | 存储帐户(AzureWebJobsStorage)  | 存储模拟器 | 由于 Azure 中的函数应用需要存储帐户，因此在将项目发布到 Azure 时会分配或创建一个存储帐户。 HTTP 触发器不使用 Azure 存储帐户连接字符串；所有其他触发器类型需要有效的 Azure 存储帐户连接字符串。  |
    | **授权级别** | **匿名** | 在未提供密钥的情况下，任何客户端都可以触发创建的函数。 通过此授权设置可以轻松测试新函数。 有关密钥和授权的详细信息，请参阅[授权密钥](./functions-bindings-http-webhook-trigger.md#authorization-keys)和 [HTTP 和 Webhook 绑定](./functions-bindings-http-webhook.md)。 |
    
    :::image type="content" source="../../includes/media/functions-vs-tools-create/functions-project-settings-v4.png" alt-text="Azure Functions 项目设置":::

    请确保将“授权级别”设置为“匿名”。  如果选择默认级别的 **函数**，需要在请求中提供 [函数密钥](./functions-bindings-http-webhook-trigger.md#authorization-keys)才能访问函数终结点。

1. 选择“创建”以创建函数项目和 HTTP 触发器函数。

::: zone-end

Visual Studio 将创建一个项目和一个包含 HTTP 触发器函数类型样本代码的类。 样本代码发送 HTTP 响应，其中包含来自请求正文或查询字符串的值。 `HttpTrigger` 属性指定该函数将由某个 HTTP 请求触发。

## <a name="rename-the-function"></a>重命名函数

`FunctionName` 方法属性设置函数的名称（默认情况下生成为 `Function1`）。 由于工具不允许在创建项目时覆盖默认函数名称，请花一点时间为函数类、文件和元数据创建更好的名称。

1. 在“文件资源管理器”中，右键单击 Function1.cs 文件并将其重命名为 `HttpExample.cs`。

1. 在代码中，将 Function1 类重命名为 `HttpExample`。

1. 在名为 `Run` 的 `HttpTrigger` 方法中，将 `FunctionName` 方法属性重命名为 `HttpExample`。 

函数定义现在应如以下代码所示：

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs" range="15-18"::: 

重命名函数以后，即可在本地计算机上对其进行测试。

## <a name="run-the-function-locally"></a>在本地运行函数

Visual Studio 与 Azure Functions Core Tools 集成，这样你便可使用完整的 Azure Functions 运行时在本地测试函数。  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

确认该函数可以在本地计算机上正确运行以后，即可将项目发布到 Azure。

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

必须在 Azure 订阅中有一个函数应用，然后才能发布项目。 Visual Studio 发布会在你首次发布项目时为你创建一个函数应用。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>在 Azure 中验证函数

1. 在 Cloud Explorer 中，应已选中新的函数应用。 如果未选中，请依次展开你的订阅>“应用服务”，并选择新的函数应用。

1. 右键单击函数应用，然后选择“在浏览器中打开”。 这会在默认的 Web 浏览器中打开函数应用的根，并显示指示函数应用正在运行的页面。 

    ::: zone pivot="programming-runtime-functions-v3"
    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="函数应用正在运行":::
    ::: zone-end
    
    ::: zone pivot="programming-runtime-functions-v4"
    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure-v4.png" alt-text="函数应用正在运行":::
    ::: zone-end

1. 在浏览器的地址栏中，将字符串 `/api/HttpExample?name=Functions` 追加到基 URL，然后运行请求。

    调用 HTTP 触发器函数的 URL 采用以下格式：

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

1. 转到此 URL，你会在浏览器中看到函数返回的对远程 GET 请求的响应，如下例所示：

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="浏览器中的函数响应":::

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他快速入门教程是在本文的基础上制作的。 如果打算使用后续的快速入门、教程或者在本快速入门中创建的任何服务，请勿清理这些资源。

Azure 中的资源是指函数应用、函数、存储帐户等。 这些资源可以组合到资源组中，删除该组即可删除组中的所有内容。 

已创建完成这些快速入门所需的资源。 这些资源可能需要付费，具体取决于[帐户状态](https://azure.microsoft.com/account/)和[服务定价](https://azure.microsoft.com/pricing/)。 

[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Visual Studio，在 Azure 中创建并发布了一个 C# 函数应用，其中包含一个简单的 HTTP 触发器函数。 

请转到下一篇文章，了解如何将一个 Azure 存储队列绑定添加到函数：
> [!div class="nextstepaction"]
> [将 Azure 存储队列绑定添加到函数](functions-add-output-binding-storage-queue-vs.md)

