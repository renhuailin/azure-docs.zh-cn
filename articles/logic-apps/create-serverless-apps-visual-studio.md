---
title: 使用 Visual Studio 创建示例无服务器应用
description: 利用 Azure 快速入门模板，在 Visual Studio 使用 Azure 逻辑应用和 Azure Functions 创建、部署和管理示例无服务器应用
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: df395fcffc1a7e675921f2ff397d488c301c703a
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114363784"
---
# <a name="create-an-example-serverless-app-with-azure-logic-apps-and-azure-functions-in-visual-studio"></a>在 Visual Studio 中使用 Azure 逻辑应用和 Azure Functions 创建示例无服务器应用

可以使用 Azure 中的服务和功能（例如 Azure 逻辑应用与 Azure Functions）快速创建、生成和部署基于云的“无服务器”应用。 使用 Azure 逻辑应用，可以通过低代码或无代码方法快速轻松地生成工作流，从而简化组合任务的协调。 可以集成不同的服务、云、本地或混合服务，而无需为这些交互编写代码、维护粘附代码或学习新的 API 或规范。 使用 Azure Functions 时，可以使用事件驱动模型加快开发速度。 可以通过自动运行自己的代码来使用响应事件的触发器。 可以使用绑定来无缝集成其他服务。

本文演示如何使用 Azure 快速入门模板，创建在多租户 Azure 中运行的示例无服务器应用。 该模板会创建一个 Azure 资源组项目，其中包含 Azure 资源管理器部署模板。 此模板定义基本的逻辑应用资源，其中预定义的工作流包括对定义的 Azure 函数的调用。 工作流定义包括以下组件：

* 接收 HTTP 请求的请求触发器。 若要启动此触发器，请将请求发送到触发器的 URL。
* 一个 Azure Functions 操作，可调用 Azure 函数（稍后可以定义该操作）。
* 一个响应操作，它返回包含函数结果的 HTTP 响应。

有关详细信息，请查看下列文章：

* [无服务器计算：无服务器技术简介](https://azure.microsoft.com/overview/serverless-computing/)
* [关于 Azure 逻辑应用](logic-apps-overview.md)
* [关于 Azure Functions](../azure-functions/functions-overview.md)
* [Azure 无服务器产品/服务：概述如何使用 Azure 逻辑应用和 Azure Functions 构建基于云的应用和解决方案](logic-apps-serverless-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 下载并安装以下工具（如果尚未安装）：

  * [Visual Studio 2019、2017 或 2015（Community 或其他版本）](https://aka.ms/download-visual-studio)。 本快速入门使用免费的 Visual Studio Community 2019。

    > [!IMPORTANT]
    > 安装 Visual Studio 2019 或 2017 时，请务必选择“Azure 开发”工作负载。

  * [用于 .NET 的 Microsoft Azure SDK（2.9.1 或更高版本）](https://azure.microsoft.com/downloads/)。 详细了解[用于 .NET 的 Azure SDK](/dotnet/azure/intro)。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)。

  * 适用于所用 Visual Studio 版本的 Azure 逻辑应用工具。 可以[了解如何从 Visual Studio 内部安装此扩展](/visualstudio/ide/finding-and-using-visual-studio-extensions)，或者从 Visual Studio Marketplace 下载 Azure 逻辑应用的相应版本：

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)
    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)
    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    > [!IMPORTANT]
    > 完成安装后，请务必重启 Visual Studio。

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)以便可以本地调试 Azure 函数。 有关详细信息，请参阅[ Azure Functions Core Tools](../azure-functions/functions-run-local.md)。

* 请在使用嵌入式工作流设计器时访问 Internet。

  设计器需要 Internet 连接才能在 Azure 中创建资源，以及从工作流的[托管连接器](../connectors/managed.md)中读取属性和数据。 例如，如果使用 SQL 连接器，设计器会在服务器示例中检查可用的默认属性和自定义属性。

## <a name="create-a-resource-group-project"></a>创建资源组项目

若要开始，请为无服务器应用创建一个 Azure 资源组项目。 在 Azure 中，资源组是一个逻辑集合，用于整理整个应用的资源。 然后，便可以将这些资源作为单个资产进行管理和部署。 对于 Azure 中的无服务器应用，资源组包含 Azure 逻辑应用和 Azure Functions 中的资源。 有关详细信息，请参阅[资源管理器术语](../azure-resource-manager/management/overview.md#terminology)。

1. 打开 Visual Studio，然后使用 Azure 帐户登录（如有提示）。

1. 如果“开始”窗口打开，请选择“创建新项目”。

   ![显示 Visual Studio“开始”窗口的屏幕截图，其中选择了“创建新项目”。](./media/create-serverless-apps-visual-studio/start-window.png)

1. 如果“开始”窗口未打开，请在“文件”菜单中，选择“新建” > “项目”  。

   ![显示打开的“文件”菜单的屏幕截图，其中选择了“新建”菜单和“项目”选项。](./media/create-serverless-apps-visual-studio/create-new-project-visual-studio.png)

1. “创建新项目”窗口打开后，在搜索框中，选择适用于 C# 或 Visual Basic 的“Azure 资源组”项目模板 。 准备就绪后，选择“下一步”。 本示例将继续使用 C#。

   ![显示“创建新项目”窗口和搜索框的屏幕截图，其中选择了“资源组”和“Azure 资源组”项目模板。](./media/create-serverless-apps-visual-studio/start-window-find-project-template.png)

1. “配置新项目”窗口打开后，提供有关项目的信息，例如名称。 完成操作后，选择“创建”。

   ![显示“配置新项目”窗口和项目详细信息的屏幕截图。](./media/create-serverless-apps-visual-studio/start-window-create-new-project-details.png)

1. “选择 Azure 模板”窗口打开后，在“显示此位置的模板”列表中，选择“Azuew 快速入门 (github.com/Azure/azure-quickstart-templates)”作为模板位置  。

1. 在搜索框中输入 `logic-app-and-function-app`。 从结果中选择名为“quickstarts\microsoft.logic\logic-app-and-function-app”的模板。 完成后，请选择“确定”。

   ![显示“选择 Azure 模板”窗口的屏幕截图，其中选择了“Azure 快速入门”作为模板位置，并选择了“quickstarts\microsoft.logic\logic-app-and-function-app”。](./media/create-serverless-apps-visual-studio/select-template.png)

   Visual Studio 会创建资源组项目，包括项目的解决方案容器。

   ![显示已创建的项目和解决方案的屏幕截图。](./media/create-serverless-apps-visual-studio/create-serverless-solution.png)

1. 接下来，将解决方案部署到 Azure。

   > [!IMPORTANT]
   > 请确保完成部署步骤。 否则，将无法使用 Visual Studio 中的设计器打开、查看和编辑逻辑应用的工作流。

## <a name="deploy-your-solution"></a>部署解决方案

在使用 Visual Studio 中的设计器打开逻辑应用之前，必须将应用部署到 Azure。 然后，设计器可以创建与逻辑应用的工作流中使用的服务和资源的连接。

1. 在解决方案资源管理器中，从资源项目的快捷菜单中选择“部署” > “新建”。 

   ![显示“解决方案资源管理器”的屏幕截图，其中项目快捷菜单已打开、“部署”菜单已打开，并且选择了“新建”。](./media/create-serverless-apps-visual-studio/deploy.png)

1. “部署到资源组”窗口打开后，请按照以下步骤提供部署信息：

   1. 窗口检测到当前订阅后，确认 Azure 订阅或选择其他订阅（如果需要）。

   1. 在 Azure 中创建一个新的资源组。 从“资源组”列表中，选择“创建新资源组” 。

      ![显示“部署到资源组”窗口的屏幕截图，其中选择了“新建”。](./media/create-serverless-apps-visual-studio/create-resource-group-start.png)

   1. “创建资源组”窗口打开后，请提供以下信息：

      | 属性 | 说明 |
      |----------|-------------|
      | 资源组名称 | 为资源组提供的名称 |
      | 资源组位置 | 用于托管逻辑应用资源的 Azure 数据中心区域 |
      |||

      本示例通过在美国西部区域创建资源组来继续操作。

      ![显示“创建资源组”窗口的屏幕截图，其中包含新资源组的信息。](./media/create-serverless-apps-visual-studio/create-resource-group.png)

   1. 完成解决方案的创建和部署，例如：

      ![部署设置](./media/create-serverless-apps-visual-studio/deploy-to-resource-group.png)

1. 如果显示“编辑参数”窗口，请提供要用于已部署的逻辑应用资源和函数应用资源的资源名称，然后保存所做的更改。

   > [!IMPORTANT]
   > 请确保对逻辑应用和函数应用使用全局唯一名称。

   ![显示“编辑参数”窗口的屏幕截图，其中包含逻辑应用资源和函数应用资源的名称。](./media/create-serverless-apps-visual-studio/logic-function-app-name-parameters.png)

   Visual Studio 开始部署到资源组时，解决方案的部署状态会显示在 Visual Studio 的“输出”窗口中。 部署完成后，逻辑应用将在 Azure 门户中处于活动状态。

## <a name="open-and-edit-your-deployed-logic-app"></a>打开并编辑已部署的逻辑应用

1. 从解决方案资源管理器的“azuredeploy.json”文件的快捷菜单中，选择“使用逻辑应用设计器打开” 。

   ![显示“azuredeploy.js”快捷菜单的屏幕截图，其中选择了“使用逻辑应用设计器打开”。](./media/create-serverless-apps-visual-studio/open-designer.png)

   > [!TIP]
   > 如果 Visual Studio 2019 中没有此命令，请确认 Visual Studio 安装了最新的更新。

1. 工作流设计器打开后，可以通过编辑工作流或添加步骤来继续操作。 完成后，请记得保存对“azuredeploy.js”文件所做的更改。

   ![显示设计器中逻辑应用工作流的屏幕截图。](./media/create-serverless-apps-visual-studio/opened-logic-app-workflow.png)

## <a name="create-an-azure-functions-project"></a>创建 Azure Functions 项目

若要在解决方案中创建基于 C# 的 Azure 函数，请按照[快速入门：使用 Visual Studio 在 Azure 中创建第一个 C# 函数](../azure-functions/functions-create-your-first-function-visual-studio.md)来创建 C# 类库项目。 否则，若要使用其他语言创建 Azure Functions 项目和函数，请按照以下快速入门操作：

* [快速入门：在 Azure 中使用 Visual Studio Code 创建 Java 函数](../azure-functions/create-first-function-vs-code-java.md)
* [快速入门：在 Azure 中使用 Visual Studio Code 创建 JavaScript 函数](../azure-functions/create-first-function-vs-code-node.md)
* [快速入门：在 Azure 中使用 Visual Studio Code 创建 PowerShell 函数](../azure-functions/create-first-function-vs-code-powershell.md)
* [快速入门：在 Azure 中使用 Visual Studio Code 创建 Python 函数](../azure-functions/create-first-function-vs-code-python.md)
* [快速入门：在 Azure 中使用 Visual Studio Code 创建 TypeScript 函数](../azure-functions/create-first-function-vs-code-typescript.md)

## <a name="deploy-functions-from-visual-studio"></a>从 Visual Studio 部署函数

解决方案中的部署模板可以从 Git 存储库部署解决方案中存在的任何 Azure 函数，这些函数由“azuredeploy.json”文件中的变量指定。 如果在解决方案中创建并创作了 Functions 项目，可以将项目签入 Git 源代码管理（例如 GitHub 或 Azure DevOps），然后更新 azuredeploy.json 文件中的 `repo` 变量，让该模板部署 Azure 函数。

## <a name="manage-logic-apps-and-view-run-history"></a>管理逻辑应用和查看运行历史记录

如果有已在 Azure 中部署的逻辑应用，则可以从 Visual Studio 编辑、管理、查看运行历史记录，以及禁用这些应用。 有关详细信息，请参阅[使用 Visual Studio 管理逻辑应用](manage-logic-apps-with-visual-studio.md)。

## <a name="next-steps"></a>后续步骤

* 有关使用 Azure 逻辑应用和 Azure Functions 的其他示例，请尝试[教程：使用 Azure 逻辑应用、Azure Functions 和 Azure 存储自动执行任务来处理电子邮件](tutorial-process-email-attachments-workflow.md)