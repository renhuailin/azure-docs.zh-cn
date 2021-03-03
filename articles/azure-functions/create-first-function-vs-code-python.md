---
title: 使用 Visual Studio Code 创建 Python 函数 - Azure Functions
description: 了解如何创建 Python 函数，然后使用 Visual Studio Code 中的 Azure Functions 扩展将本地项目发布到 Azure Functions 中的无服务器托管。
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-python-uiex
ms.openlocfilehash: 3dfafd4412110d8d12a656fbae587087745677f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723718"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>快速入门：在 Azure 中使用 Visual Studio Code 创建 Python 函数

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

在本文中，我们使用 Visual Studio Code 来创建一个响应 HTTP 请求的 Python 函数。 在本地测试代码后，将代码部署到 Azure Functions 的无服务器环境。

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还有一个[基于 CLI 的版本](create-first-function-cli-python.md)。

## <a name="configure-your-environment"></a>配置环境

在开始之前，请确保已满足下列要求：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 版本 3.x。

+ [Azure Functions 支持的 Python 版本](supported-languages.md#languages-by-runtime-version)

+ 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。

+ Visual Studio Code 的 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。  

+ Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>创建本地项目

在本部分，你将使用 Visual Studio Code 在 Python 中创建一个本地 Azure Functions 项目。 稍后在本文中，你要将函数代码发布到 Azure。

1. 在活动栏中选择“Azure”图标，然后在“Azure:函数”区域中选择“创建新项目...”图标。

    ![选择“创建新项目”](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 为项目工作区选择目录位置，然后选择“选择”  。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 在这种情况下，请不要选择属于工作区内的项目文件夹。

1. 根据提示提供以下信息：

    + **选择函数项目的语言**：选择`Python`。

    + **选择一个 Python 别名以创建虚拟环境**：选择 Python 解释器的位置。  
    如果该位置未显示，请键入 Python 二进制文件的完整路径。  

    + **为项目的第一个函数选择模板**：选择`HTTP trigger`。

    + **提供函数名称**：键入 `HttpExample`。

    + **授权级别**：选择 `Anonymous`，这使任何人都可以调用你的函数终结点。 若要了解授权级别，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。

    + **选择打开项目的方式**：选择`Add to workspace`。

1. Visual Studio Code 将使用此信息生成一个包含 HTTP 触发器的 Azure Functions 项目。 可以在资源管理器中查看本地项目文件。 若要详细了解所创建的文件，请参阅[生成的项目文件](functions-develop-vs-code.md#generated-project-files)。

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

确认该函数可以在本地计算机上正确运行以后，可以使用 Visual Studio Code 将项目直接发布到 Azure。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

在本部分，你将在 Azure 订阅中创建一个函数应用和相关资源，然后部署代码。 

> [!IMPORTANT]
> 发布到现有函数应用将覆盖该应用在 Azure 中的内容。 

1. 在活动栏中选择“Azure”图标，然后在“Azure:函数”区域中，选择“部署到函数应用...”按钮。

    ![将项目发布到 Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. 根据提示提供以下信息：

    + **选择文件夹**：从工作区中选择一个文件夹，或浏览到包含函数应用的文件夹。   
    如果已打开有效的函数应用，则不会看到此信息。

    + **选择订阅**：选择要使用的订阅。  
    如果只有一个订阅，则不会看到此项。

    + **在 Azure 中选择函数应用**：选择`+ Create new Function App`。  
    （请不要选择本文中未介绍的 `Advanced` 选项。）

    + **输入函数应用的全局唯一名称**：键入在 URL 路径中有效的名称。 将对你键入的名称进行验证，以确保其在 Azure Functions 中是唯一的。 

    + **选择运行时**：选择你一直在本地运行的 Python 版本。 可以使用 `python --version` 命令来检查你的版本。

    + **选择新资源的位置**：为了获得更好的性能，请选择你附近的 [区域](https://azure.microsoft.com/regions/)。

    在 Azure 中创建单个资源时，扩展会在通知区域显示这些资源的状态。

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure 资源创建通知":::

1. 完成后，将使用基于函数应用名称的名称在订阅中创建以下 Azure 资源：

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    创建函数应用并应用了部署包之后，会显示一个通知。 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. 在此通知中选择“查看输出”以查看创建和部署结果，其中包括你创建的 Azure 资源。 如果错过了通知，请选择右下角的响铃图标以再次查看。

    ![创建完成通知](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>后续步骤

你已使用 [Visual Studio Code](functions-develop-vs-code.md?tabs=python) 通过简单的 HTTP 触发函数创建了函数应用。 在下一篇文章中，你将通过连接到 Azure 存储来扩展该函数。 若要详细了解如何连接到其他 Azure 服务，请参阅[将捆绑项添加到 Azure Functions 中的现有函数](add-bindings-existing-function.md?tabs=python)。 

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[存在问题？请告诉我们。](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
