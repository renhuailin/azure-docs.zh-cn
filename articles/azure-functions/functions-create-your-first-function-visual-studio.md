---
title: 快速入门：使用 Visual Studio 在 Azure 中创建第一个 C# 函数
description: 本快速入门将介绍如何使用 Visual Studio 创建 C# HTTP 触发的函数，并将其发布到在 .NET Core 3.1 上运行的 Azure Functions。
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 05/18/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, contperf-fy21q3-portal
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: 9b5d5d85d8df58a8e8c3e2626abaed75377e6025
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "122643075"
---
# <a name="quickstart-create-your-first-c-function-in-azure-using-visual-studio"></a>快速入门：使用 Visual Studio 在 Azure 中创建第一个 C# 函数

通过 Azure Functions，可以在 Azure 的无服务器环境中运行 C# 代码。 

在本文中，学习如何：

> [!div class="checklist"]
> * 使用 Visual Studio 创建 C# 类库 (.NET Core 3.1) 项目。
> * 创建响应 HTTP 请求的函数。 
> * 在本地运行代码以验证函数行为。
> * 将代码项目部署到 Azure Functions。 
 
完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。
 
创建的项目在 .NET Core 3.1 上运行。 如果想要创建在 .NET 5.0 上运行的项目，请参阅[使用 Azure Functions 开发和发布 .NET 5 函数](dotnet-isolated-process-developer-howtos.md)。

## <a name="prerequisites"></a>先决条件

+ [Visual Studio 2019](https://azure.microsoft.com/downloads/)。 请确保在安装过程中选择“Azure 开发”工作负载。 

+ [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)。 如果还没有帐户，请在开始之前[创建免费帐户](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-a-function-app-project"></a>创建函数应用项目

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio 将创建一个项目和一个包含 HTTP 触发器函数类型样本代码的类。 样本代码发送 HTTP 响应，其中包含来自请求正文或查询字符串的值。 `HttpTrigger` 属性指定该函数将由某个 HTTP 请求触发。 

## <a name="rename-the-function"></a>重命名函数

`FunctionName` 方法属性设置函数的名称（默认情况下生成为 `Function1`）。 由于工具不允许在创建项目时覆盖默认函数名称，请花一点时间为函数类、文件和元数据创建更好的名称。

1. 在“文件资源管理器”中，右键单击 Function1.cs 文件并将其重命名为 `HttpExample.cs`。

1. 在代码中，将 Function1 类重命名为 `HttpExample`。

1. 在名为 `Run` 的 `HttpTrigger` 方法中，将 `FunctionName` 方法属性重命名为 `HttpExample`。 

函数定义现在应如以下代码所示：

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs" range="13-18"::: 
 
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

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="函数应用正在运行":::

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

