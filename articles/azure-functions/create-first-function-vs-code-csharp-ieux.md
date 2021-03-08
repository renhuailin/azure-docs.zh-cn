---
title: 使用 Visual Studio Code 创建 C# 函数 - Azure Functions
description: 了解如何创建 C# 函数，然后使用 Visual Studio Code 中的 Azure Functions 扩展将本地项目发布到 Azure Functions 中的无服务器托管。
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 69a057f92009fd712d39666be223f1cfd99d73b5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747307"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>快速入门：在 Azure 中使用 Visual Studio Code 创建 C# 函数

> [!div class="op_single_selector" title1="选择你的函数语言： "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [其他 (Go/Rust)](create-first-function-vs-code-other.md)

在本文中，我们使用 Visual Studio Code 来创建一个响应 HTTP 请求的基于 C# 类库的函数。 在本地测试代码后，将其部署到 Azure Functions 的 <abbr title="一种运行时计算环境，其中服务器的所有详细信息对应用程序开发人员来说都是透明的，这简化了部署和管理代码的过程。">无服务器</abbr> 环境 <abbr title="为应用程序提供低成本无服务器计算环境的 Azure 服务。">Azure Functions</abbr>.

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还有一个[基于 CLI 的版本](create-first-function-cli-csharp.md)。
    
## <a name="1-configure-your-environment"></a>1.配置环境

在开始之前，请确保已满足下列要求：

+ Azure <abbr title="用于维护 Azure 使用情况计费信息的配置文件。">account</abbr> 具有有效 <abbr title="在 Azure 中管理资源的基本组织结构，通常与组织中的个人或部门相关联。">订阅</abbr>. [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 版本 3.x。

+ 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。

+ Visual Studio Code 的 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。  

+ Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2.创建本地项目

在本部分，你将使用 Visual Studio Code 通过 C# 创建一个本地 <abbr title="一个逻辑容器，它用于一个或多个可同时部署和管理的单独函数。">Azure Functions 项目</abbr> 。 稍后在本文中，你要将函数代码发布到 Azure。

1. 选择活动栏中的 Azure 图标 <abbr title="Visual Studio Code 窗口左侧的垂直图标组。">活动栏</abbr>，然后在“Azure: 函数”区域中，选择“新建项目...”图标 。

    ![选择“创建新项目”](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 为项目工作区选择目录位置，然后选择“选择”  。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 在这种情况下，请不要选择属于工作区内的项目文件夹。

1. 根据提示提供以下信息：

    + **选择函数项目的语言**：选择`C#`。

    + **为项目的第一个函数选择模板**：选择`HTTP trigger`。

    + **提供函数名称**：键入 `HttpExample`。

    + **提供命名空间**：键入 `My.Functions`。

    + **授权级别**：选择 `Anonymous`，这使任何人都可以调用你的函数终结点。 若要了解授权级别，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。

    + **选择打开项目的方式**：选择`Add to workspace`。

1. Visual Studio Code 将使用此信息生成一个包含 HTTP 触发器的 Azure Functions 项目 <abbr title="调用函数代码的事件类型，例如 HTTP 请求、队列消息或特定时间。">触发器</abbr>. 可以在资源管理器中查看本地项目文件。 若要详细了解所创建的文件，请参阅[生成的项目文件](functions-develop-vs-code.md#generated-project-files)。

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

确认该函数可以在本地计算机上正确运行以后，可以使用 Visual Studio Code 将项目直接发布到 Azure。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5.将项目发布到 Azure

在本部分，你将在 Azure 订阅中创建一个函数应用和相关资源，然后部署代码。 

> [!IMPORTANT]
> 发布到现有函数应用将覆盖该应用在 Azure 中的内容。 

1. 在活动栏中选择“Azure”图标，然后在“Azure:函数”区域中，选择“部署到函数应用...”按钮。

    ![将项目发布到 Azure](./media/functions-create-first-function-vs-code/function-app-publish-project.png)

1. 根据提示提供以下信息：

    + **选择文件夹**：从工作区中选择一个文件夹，或浏览到包含函数应用的文件夹。 如果已打开有效的函数应用，则不会看到此信息。

    + **选择订阅**：选择要使用的订阅。 如果只有一个订阅，则不会看到此项。

    + **在 Azure 中选择函数应用**：选择`+ Create new Function App`。 （请不要选择本文中未介绍的 `Advanced` 选项。）

    + **输入函数应用的全局唯一名称**：键入在 URL 路径中有效的名称。 验证键入的名称，确保其在 Azure Functions 中 <abbr title="在全球各地的所有 Azure 客户使用的所有 Functions 项目中，该名称都是唯一的。 通常，可使用个人姓名/公司名称、应用程序名称和数字标识符的组合，例如 contoso-bizapp-func-20">是唯一的</abbr>. 

    + **选择新资源的位置**：为了获得更好的性能，请选择你附近的 [区域](https://azure.microsoft.com/regions/)。

    在 Azure 中创建单个资源时，扩展会在通知区域显示这些资源的状态。

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure 资源创建通知":::

1. 完成后，将使用基于函数应用名称的名称在订阅中创建以下 Azure 资源：

    + 一个 **资源组**：相关资源的逻辑容器。
    + 一个标准 **Azure 存储帐户**：用于维护项目的状态和其他信息。
    + 一个消耗计划：用于定义无服务器函数应用的基础主机。 
    + 一个函数应用：提供用于执行函数代码的环境。 可以通过函数应用将函数分组为逻辑单元，以便在同一托管计划中更轻松地管理、部署和共享资源。
    + 一个连接到函数应用的 Application Insights 实例：用于跟踪无服务器函数的使用情况。

    创建函数应用并应用了部署包之后，会显示一个通知。 

    > [!TIP]
    > 默认情况下，会根据你提供的函数应用名称创建函数应用所需的 Azure 资源。 默认情况下，还会在函数应用所在的新资源组中创建这些资源。 如果要自定义这些资源的名称或重复使用现有资源，则需要[使用高级创建选项来发布项目](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options)。


1. 在此通知中选择“查看输出”以查看创建和部署结果，其中包括你创建的 Azure 资源。 如果错过了通知，请选择右下角的响铃图标以再次查看。

    ![创建完成通知](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6.在 Azure 中运行函数

1. 返回到“Azure：函数”区域（位于侧栏中），然后展开你的订阅、新的函数应用和“函数”。 右键单击 (Windows) 或按 <kbd>Ctrl -</kbd> 单击 (macOS) `HttpExample` 函数，然后选择“立即执行函数...”。

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="立即在 Azure 的 Visual Studio Code 中执行函数":::

1. 在“输入请求正文”中，你将看到请求消息正文值 `{ "name": "Azure" }`。

    按 Enter 将此请求消息发送给函数。  

1. 当函数在 Azure 中执行并返回响应时，Visual Studio Code 中将引发通知。

## <a name="5-clean-up-resources"></a>5.清理资源

继续到[下一步](#next-steps)并 <abbr title="一种将函数与存储队列关联的方法，确保其可在队列上创建消息。">向函数添加 Azure 存储队列输出绑定，</abbr> 此时需要将所有资源保存到正确位置，以便根据已完成的工作进行操作。

否则，可以使用以下步骤删除函数应用及其相关资源，以免产生任何额外的费用。

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

若要详细了解 Functions 的费用，请参阅[估算消耗计划成本](functions-consumption-costs.md)。

## <a name="next-steps"></a>后续步骤

你已使用 Visual Studio Code 通过简单的 HTTP 触发函数创建了函数应用。 在下一篇文章中，你将添加输出绑定来扩展该函数 <abbr title="函数和其他资源之间的声明性连接。 输入绑定为函数提供数据；输出绑定将函数中的数据提供给其他资源。">binding</abbr>. 此绑定将 HTTP 请求中的字符串写入 Azure 队列存储队列中的消息。 

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
