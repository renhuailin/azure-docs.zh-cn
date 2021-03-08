---
title: 使用 Visual Studio Code 创建 Python 函数 - Azure Functions
description: 了解如何创建 Python 函数，然后使用 Visual Studio Code 中的 Azure Functions 扩展将本地项目发布到 Azure Functions 中的无服务器托管。
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 42f07b76cefed38aad53caba9ba35c74238540fe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031729"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>快速入门：在 Azure 中使用 Visual Studio Code 创建 Python 函数

> [!div class="op_single_selector" title1="选择你的函数语言： "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [其他 (Go/Rust)](create-first-function-vs-code-other.md)

在本文中，我们使用 Visual Studio Code 来创建一个响应 HTTP 请求的 Python 函数。 在本地测试代码之后，将其部署到 Azure Functions 的 <abbr title="一种运行时计算环境，其中服务器的所有详细信息对应用程序开发人员来说都是透明的，这简化了部署和管理代码的过程。">无服务器</abbr> 环境 <abbr title="为应用程序提供低成本无服务器计算环境的 Azure 服务。">Azure Functions</abbr>.

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还有一个[基于 CLI 的版本](create-first-function-cli-python.md)。

## <a name="1-prepare-your-environment"></a>1.准备环境

在开始之前，请确保已满足下列要求：

+ 具有有效订阅的 Azure <abbr title="用于维护 Azure 使用情况计费信息的配置文件。">account</abbr> 帐户 <abbr title="在 Azure 中管理资源的基本组织结构，通常与组织中的个人或部门关联。">订阅</abbr>. [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 版本 3.x。

+ Azure Functions (x64) 支持 [Python 3.8](https://www.python.org/downloads/release/python-381/)、[Python 3.7](https://www.python.org/downloads/release/python-375/)、[Python 3.6](https://www.python.org/downloads/release/python-368/)。

+ 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。

+ Visual Studio Code 的 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。  

+ Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2.<a name="create-an-azure-functions-project"></a>创建本地项目

1. 在活动栏中选择 Azure 图标 <abbr title="Visual Studio Code 窗口左侧的垂直图标组。">活动栏</abbr>然后，在“Azure: Functions”区域中，选择“新建项目...”图标 。

    ![选择“创建新项目”](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 为项目工作区选择目录位置，然后选择“选择”  。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 在这种情况下，请不要选择属于工作区内的项目文件夹。

1. 根据提示提供以下信息：

    + **选择函数项目的语言**：选择`Python`。

    + **选择一个 Python 别名以创建虚拟环境**：选择 Python 解释器的位置。 如果该位置未显示，请键入 Python 二进制文件的完整路径。  

    + **为项目的第一个函数选择模板**：选择`HTTP trigger`。

    + **提供函数名称**：键入 `HttpExample`。

    + **授权级别**：选择 `Anonymous`，这使任何人都可以调用你的函数终结点。 若要了解授权级别，请参阅[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。

    + **选择打开项目的方式**：选择`Add to workspace`。

<br/>
<details>
<summary><strong>无法创建函数项目？</strong></summary>

在创建本地 Functions 项目时，要解决的最常见的问题是：
* 未安装 Azure Functions 扩展。 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>在本地运行函数

1. 按 <kbd>F5</kbd> 启动函数应用项目。

1. 在“终端”面板中，可看到正在本地运行的函数的 URL 终结点。

    ![本地函数 VS Code 输出](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. 运行 Core Tools 后，转到“Azure:Functions”区域。 在“Functions”下，展开“本地项目” > “Functions”  。 右键单击 (Windows) 或按 <kbd>Ctrl -</kbd> 单击 (macOS) `HttpExample` 函数，然后选择“立即执行函数...”。

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="立即在 Visual Studio Code 中执行函数":::

1. 在“输入请求正文”中，你将看到请求消息正文值 `{ "name": "Azure" }`。 按 Enter 将此请求消息发送给函数。  

1. 当函数在本地执行并返回响应时，Visual Studio Code 中将引发通知。 函数执行的相关信息将显示在“终端”面板中。

1. 按 Ctrl + C 停止 Core Tools 并断开调试器的连接<kbd></kbd>。

<br/>
<details>
<summary><strong>无法在本地运行函数？</strong></summary>

在运行本地 Functions 项目时，要解决的最常见的问题是：
* 未安装 Core Tools。 
*  如果在 Windows 上运行时遇到问题，请确保用于 Visual Studio Code 的默认终端 shell 未设置为“WSL Bash”。 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4.登录 Azure

若要发布应用，请登录 Azure。 如果已登录，请转到下一部分。

1. 在活动栏中选择“Azure”图标，然后在“Azure: Functions”区域中选择“登录 Azure...” 。

    ![在 VS Code 中登录到 Azure](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. 在浏览器中出现提示时，请选择你的 Azure 帐户，并使用你的 Azure 帐户凭据登录 。

1. 成功登录后，关闭新的浏览器窗口，然后返回 Visual Studio Code。 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5.将项目发布到 Azure

首次部署代码时，需在 Azure 订阅中创建函数资源。

1. 在活动栏中选择“Azure”图标，然后在“Azure:函数”区域中，选择“部署到函数应用...”按钮。

    ![将项目发布到 Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. 根据提示提供以下信息：

    + **选择文件夹**：选择包含你的函数应用的文件夹。

    + **选择订阅**：选择要使用的订阅。 如果只有一个订阅，则不会看到此项。

    + **在 Azure 中选择函数应用**：选择`+ Create new Function App`。

    + **输入函数应用的全局唯一名称**：键入在 URL 路径中有效的名称。 将对你键入的名称进行验证，以确保其 <abbr title="该名称在全球各地的所有 Azure 客户中都必须是唯一的。 例如，可使用个人姓名/组织名称、应用程序名称和数字标识符的组合，例如 contoso-bizapp-func-20。">在 Azure 中是唯一的</abbr>. 

    + **选择运行时**：选择你一直在本地运行的 Python 版本。 可以使用 `python --version` 命令来检查你的版本。

    + **选择新资源的位置**：为了获得更好的性能，请选择你附近的[区域](https://azure.microsoft.com/regions/)。

    在 Azure 中创建单个资源时，扩展会在通知区域显示这些资源的状态。

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure 资源创建通知":::

1. 创建函数应用并应用了部署包之后，会显示一个通知。 选择“查看输出”以查看创建和部署结果。 

    ![创建完成通知](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>无法发布函数？</strong></summary>

在本部分中，创建了 Azure 资源，还将本地代码部署到了函数应用。 如果操作失败：

* 请查看输出中的错误信息。 此外，还可点击右下角的响铃图标查看输出。 
* 你是否已发布到现有的函数应用？ 该操作会在 Azure 中覆盖该应用的内容。
</details>


<br/>
<details>
<summary><strong>创建了哪些资源？</strong></summary>

完成后，将使用基于函数应用名称的名称在订阅中创建以下 Azure 资源： 
* **资源组**：资源组是同一区域中相关资源的逻辑容器。
* **Azure 存储帐户**：存储资源可维护项目的状态和其他信息。
* **消耗计划**：消耗计划定义无服务器函数应用的基础主机。
* **函数应用**：函数应用提供执行函数代码的环境并将函数分组为一个逻辑单元。
* **Application Insights**：Application Insights 跟踪无服务器函数的使用情况。

</details>

<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6.在 Azure 中运行函数

1. 返回“Azure: Functions”侧边栏，展开新的函数应用。
1. 展开“Functions”，然后右键单击 (Windows) 或按住 <kbd>Ctrl</kbd> 单击 (macOS) `HttpExample` 函数，并选择“立即执行函数...” 。

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="立即在 Azure 的 Visual Studio Code 中执行函数":::

1. 在“输入请求正文”中，你将看到请求消息正文值 `{ "name": "Azure" }`。

    按 Enter 将此请求消息发送给函数。  

1. 当函数在 Azure 中执行并返回响应时，Visual Studio Code 中将引发通知。

## <a name="7-clean-up-resources"></a>7.清理资源

继续进行[后续步骤](#next-steps)并向函数添加 <abbr title="一种将函数与存储队列关联，以确保函数可在队列上创建消息的方法。">Azure 存储队列输出绑定</abbr> 时，需要将所有资源保存到正确位置，以便根据已完成的工作进行操作。

否则，可以使用以下步骤删除函数应用及其相关资源，以免产生任何额外的费用。

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

若要详细了解 Functions 的费用，请参阅[估算消耗计划成本](functions-consumption-costs.md)。

## <a name="next-steps"></a>后续步骤

通过添加输出绑定来扩展该函数 <abbr title="函数和其他资源之间的声明性连接。 输入绑定为函数提供数据；输出绑定向其他资源提供来自函数的数据。">binding</abbr>. 此绑定将 HTTP 请求中的字符串写入 Azure 队列存储队列中的消息。 

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[存在问题？请告诉我们。](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
