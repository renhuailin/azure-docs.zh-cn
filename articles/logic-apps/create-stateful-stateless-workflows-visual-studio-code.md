---
title: 在 Visual Studio Code 中创建逻辑应用预览版工作流
description: 在 Visual Studio Code 中使用 Azure 逻辑应用（预览版）扩展构建并运行用于自动化和集成方案的工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 4010f7e2d0d20216107a45109056478694c940ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772498"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>在 Visual Studio Code 中使用 Azure 逻辑应用（预览版）扩展创建有状态和无状态工作流

> [!IMPORTANT]
> 此功能现为公共预览版，在提供时不附带服务级别协议，建议不要用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

有了 [Azure 逻辑应用预览版](logic-apps-overview-preview.md)，你可以在 Visual Studio Code 中使用 Azure 逻辑应用（预览版）扩展来创建和运行包含[有状态和无状态工作流](logic-apps-overview-preview.md#stateful-stateless)的逻辑应用，从而跨应用、数据、云服务和系统构建自动化和集成解决方案。 使用此新的逻辑应用类型，你可以构建多个工作流，它们由重新设计的 Azure 逻辑应用预览版运行时提供支持，后者为在各种托管环境（包括 Azure 和 Docker 容器）中的部署和运行提供可移植性、更佳的性能和灵活性。 若要详细了解新的逻辑应用类型，请参阅 [Azure 逻辑应用预览版概述](logic-apps-overview-preview.md)。

![屏幕截图显示了 Visual Studio Code、逻辑应用项目和工作流。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

在 Visual Studio Code 中，你可以从使用 Azure 逻辑应用（预览版）扩展创建项目开始，在该项目中，你可以在开发环境本地构建和运行你的逻辑应用的工作流。 不过，你也可以从 [在 Azure 门户中创建新的 **逻辑应用（预览版）** 资源](create-stateful-stateless-workflows-azure-portal.md)开始，这两种方法都提供在相同种类的托管环境中部署和运行逻辑应用的功能。

同时，你仍可创建原始逻辑应用类型。 虽然 Visual Studio Code 中的开发体验在原始的和新的逻辑应用类型之间所有不同，但你的 Azure 订阅可同时包含这两种类型。 你可查看和访问 Azure 订阅中所有已部署的逻辑应用，但这些应用已整理到其自己的类别和部分中。

本文介绍了如何通过使用 Azure 逻辑应用（预览版）扩展并执行以下概要任务，在 Visual Studio Code 中创建逻辑应用和工作流：

* 为你的逻辑应用和工作流创建项目。

* 添加触发器和操作。

* 在本地运行、测试、调试和查看运行历史记录。

* 查找用于访问防火墙的域名详细信息。

* 部署到 Azure，这包括选择启用 Application Insights。

* 在 Visual Studio Code 和 Azure 门户中管理已部署的逻辑应用。

* 启用无状态工作流的运行历史记录。

* 在部署后启用或打开 Application Insights。

* 部署到可在任何位置运行的 Docker 容器。

> [!NOTE]
> 有关当前已知问题的信息，请查看 [GitHub 中的逻辑应用公共预览版已知问题页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

## <a name="prerequisites"></a>先决条件

### <a name="access-and-connectivity"></a>访问和连接

* 能够访问 Internet，以便可以下载要求，从 Visual Studio Code 连接到你的 Azure 帐户，以及从 Visual Studio Code 发布到 Azure、Docker 容器或其他环境。

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 若要构建与本文中相同的示例逻辑应用，你需要一个使用 Microsoft 工作或学校帐户进行登录的 Office 365 Outlook 电子邮件帐户。

  如果你选择使用 [Azure 逻辑应用支持的其他电子邮件连接器](/connectors/)（例如 Outlook.com 或 [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)），则仍可以按照示例操作，一般的整体步骤相同，但你的用户界面和选项在某些方面可能会有所不同。 例如，如果你使用 Outlook.com 连接器，请改用你的个人 Microsoft 帐户进行登录。

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>存储要求

#### <a name="windows"></a>Windows

在使用 Windows 的情况下，如果在 Visual Studio Code 中在本地构建和运行逻辑应用项目，请按照以下步骤来设置 Azure 存储模拟器：

1. 下载并安装 [Azure 存储模拟器 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179)。

1. 你需要在本地安装 SQL DB（如果尚未安装），例如免费的 [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658)，以便模拟器可以运行。

   有关详细信息，请参阅[使用 Azure 存储模拟器进行开发和测试](../storage/common/storage-use-emulator.md)。

1. 在运行项目之前，请确保启动模拟器。

   ![屏幕截图显示了正在运行的 Azure 存储模拟器。](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS 和 Linux

在使用 macOS 或 Linux 的情况下，如果在 Visual Studio Code 中在本地构建和运行逻辑应用项目，请按照以下步骤来创建并设置 Azure 存储帐户。

> [!NOTE]
> 当前，Visual Studio Code 中的设计器不可用于 Linux OS，但你仍可构建、运行和部署对基于 Linux 的虚拟机使用逻辑应用预览版运行时的逻辑应用。 目前，你可在 Windows 或 macOS 上的 Visual Studio Code 中构建逻辑应用，然后将其部署到基于 Linux 的虚拟机上。

1. 登录到 [Azure 门户](https://portal.azure.com)，并[创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)，这是 [Azure Functions 的先决条件](../azure-functions/storage-considerations.md)。

1. 在存储帐户菜单上的“设置”下，选择“访问密钥”。 

1. 在“访问密钥”窗格中，查找并复制存储帐户的连接字符串，它应当类似于以下示例：

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![屏幕截图显示了 Azure 门户，其中显示了存储帐户访问密钥和复制的连接字符串。](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   有关详细信息，请查看[管理存储帐户密钥](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)。

1. 将连接字符串保存在一个安全的位置。 在 Visual Studio Code 中创建逻辑应用项目后，必须将该字符串添加到项目根目录级文件夹中的 **local.settings.json** 文件。

   > [!IMPORTANT]
   > 如果计划部署到 Docker 容器，还需要用到用于部署 Docker 文件的连接字符串。 对于生产方案，请确保护此类机密和敏感信息安全，例如，可以使用密钥保管库。
  
### <a name="tools"></a>工具

* [Visual Studio Code 1.30.1（2019 年 1 月）或更高版本](https://code.visualstudio.com/)（一个免费工具）。 此外，请为 Visual Studio Code 下载并安装以下工具（如果尚未安装）：

  * [Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) - 为 Visual Studio Code 中的所有其他 Azure 扩展提供单一的通用 Azure 登录和订阅筛选体验。

  * [C# for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) - 它使 F5 功能可以运行你的逻辑应用。

  * [Azure Functions Core Tools 3.0.3245 或更高版本](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) - 通过使用 Microsoft 安装程序 (MSI) 版本 `func-cli-3.0.3245-x*.msi`。

    这些工具包括为预览版扩展在 Visual Studio Code 中使用的 Azure Functions 运行时提供支持的同一运行时的版本。

    > [!IMPORTANT]
    > 如果你的安装版本早于这些版本，请先卸载该版本，或者确保 PATH 环境变量指向你下载并安装的版本。

  * [适用于 Visual Studio Code 的 Azure 逻辑应用（预览版）扩展](https://go.microsoft.com/fwlink/p/?linkid=2143167)。 此扩展提供了创建逻辑应用的功能，你可以在这些逻辑应用中构建在 Visual Studio Code 本地运行的有状态和无状态工作流，然后将这些逻辑应用直接部署到 Azure 或 Docker 容器。

    目前，在 Visual Studio Code 中可以同时安装有原始 Azure 逻辑应用扩展和公共预览版扩展。 尽管开发体验的某些方面在这两种扩展之间有所不同，但你的 Azure 订阅可以同时包含通过这两种扩展创建的逻辑应用类型。 Visual Studio Code 会显示你的 Azure 订阅中所有已部署的逻辑应用，但会按扩展名、“逻辑应用”和“Azure 逻辑应用(预览版)”将它们组织到不同的部分中。

    > [!IMPORTANT]
    > 如果你使用早期的专用预览版扩展创建了逻辑应用项目，则这些项目将不能与公共预览版扩展一起使用。 但是，你可以卸载专用预览版扩展，删除关联的文件并安装公共预览版扩展，然后迁移这些项目。 然后，在 Visual Studio Code 中创建一个新项目，并将以前创建的逻辑应用的 **workflow.definition** 文件复制到你的新项目中。 有关详细信息，请参阅[从专用预览版扩展进行迁移](#migrate-private-preview)。
    > 
    > 如果你使用早期的公共预览版扩展创建了逻辑应用项目，则可以继续使用这些项目，而无需执行任何迁移步骤。

    **若要安装 Azure 逻辑应用（预览版）扩展，请执行以下步骤：**

    1. 在 Visual Studio Code 中的左侧工具栏中，选择“扩展”。

    1. 在扩展搜索框中，输入 `azure logic apps preview`。 在结果列表中，选择“Azure 逻辑应用(预览版)”>“安装”。  

       安装完成后，该预览版扩展将显示在“扩展: 已安装”列表中。

       ![屏幕截图显示了 Visual Studio Code 的已安装扩展的列表，其中的“Azure 逻辑应用(预览版)”扩展带有下划线。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > 如果该扩展未显示在“已安装”列表中，请尝试重启 Visual Studio Code。

* 若要使用运行 JavaScript 的[“内联代码操作”操作](../logic-apps/logic-apps-add-run-inline-code.md)，请安装 [Node.js 版本 10.x.x、11.x.x 或 12.x.x](https://nodejs.org/en/download/releases/)。

  > [!TIP] 
  > 对于 Windows，请下载 MSI 版本。 如果你改用 ZIP 版本，则必须手动使 Node.js 可用：通过使用你的操作系统的 PATH 环境变量。

* 若要在 Visual Studio Code 本地运行基于 Webhook 的触发器和操作（例如[内置的 HTTP Webhook 触发器](../connectors/connectors-native-webhook.md)），则你需要[为回调 URL 设置转发](#webhook-setup)。

* 若要测试你在本文中创建的示例逻辑应用，你需要一个可将调用发送到请求触发器的工具，该触发器是示例逻辑应用中的第一个步骤。 如果没有此类工具，你可以下载、安装并使用 [Postman](https://www.postman.com/downloads/)。

* 如果你使用支持 [Application Insights](../azure-monitor/app/app-insights-overview.md) 的设置创建并部署逻辑应用，则可以选择为你的逻辑应用启用诊断日志记录和跟踪。 可以在 Visual Studio Code 中部署逻辑应用时或在部署后执行此操作。 你需要有一个 Application Insights 实例，但你可以在部署逻辑应用时[提前](../azure-monitor/app/create-workspace-resource.md)创建此资源，或在部署后创建此资源。

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>从专用预览版扩展进行迁移

使用 Azure 逻辑应用（专用预览版）扩展创建的任何逻辑应用项目都不能与公共预览版扩展一起使用。 不过，你可以通过执行以下步骤将这些项目迁移到新项目：

1. 卸载专用预览版扩展。

1. 在以下位置删除任何关联的扩展捆绑包和 NuGet 程序包文件夹：

   * **Microsoft.Azure.Functions.ExtensionBundle.Workflows** 文件夹 - 它包含以前的扩展捆绑包，并位于以下路径中的任一路径：

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * **microsoft.azure.workflows.webjobs.extension** 文件夹 - 它是专用预览版扩展的 [NuGet](/nuget/what-is-nuget) 缓存，并位于以下路径：

     `C:\Users\{userName}\.nuget\packages`

1. 安装 Azure 逻辑应用（预览版）扩展。

1. 在 Visual Studio Code 中创建新项目。

1. 将之前创建的逻辑应用的 **workflow.definition** 文件复制到新项目。

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>设置 Visual Studio Code

1. 若要确保正确安装所有扩展，请重新加载或重启 Visual Studio Code。

1. 确认 Visual Studio Code 会自动查找并安装扩展更新，以便你的预览版扩展可以获得最新更新。 否则，你必须手动卸载过时的版本并安装最新版本。

   1. 在“文件”菜单上，转到“首选项”>“设置”。  

   1. 在“用户”菜单上，转到“功能”>“扩展”。  

   1. 确认已选中“自动检查更新”和“自动更新”。

另外，默认情况下，将为逻辑应用预览版扩展启用并设置以下设置：

* **Azure 逻辑应用 V2: 项目运行时** - 它设置为版本 **~3**

  > [!NOTE]
  > 使用[“内联代码操作”操作](../logic-apps/logic-apps-add-run-inline-code.md)时需要此版本。

* **Azure 逻辑应用 V2: 试验性视图管理器** - 这将在 Visual Studio Code 中启用最新的设计器。 如果你在设计器中遇到问题（例如拖放项），请关闭此设置。

若要查找和确认这些设置，请执行以下步骤：

1. 在“文件”菜单上，转到“首选项”>“设置”。  

1. 在“用户”菜单上，转到“功能”>“扩展”>“Azure 逻辑应用(预览版)”。   

   例如，可以在此处查找“Azure 逻辑应用 V2: 项目运行时”设置，或者使用搜索框查找其他设置：

   ![屏幕截图显示了“Azure 逻辑应用(预览版)”扩展的 Visual Studio Code 设置。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

1. 在 Visual Studio Code 活动栏上，选择 Azure 图标。

   ![屏幕截图显示了 Visual Studio Code 活动栏和所选的 Azure 图标。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. 在 Azure 窗格中的“Azure: 逻辑应用(预览版)”下选择“登录到 Azure”。 出现“Visual Studio Code 身份验证”页面时，请使用 Azure 帐户登录。

   ![屏幕截图显示了 Azure 窗格和处于选定状态的 Azure 登录链接。](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   登录后，Azure 窗格将显示你的 Azure 帐户中的订阅。 如果你还有已公开发布的扩展，则使用该扩展创建的任何逻辑应用都将显示在“逻辑应用”部分中，而不是显示在“逻辑应用(预览版)”部分中。
   
   如果预期的订阅未显示，或者你希望该窗格仅显示特定订阅，请执行以下步骤：

   1. 在订阅列表中，将指针移动到第一个订阅旁边，直到“选择订阅”按钮（筛选器图标）出现。 选择“筛选”图标。

      ![屏幕截图显示了 Azure 窗格和选定的筛选器图标。](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      或者，在 Visual Studio Code 状态栏中，选择你的 Azure 帐户。 

   1. 当另一个订阅列表出现时，选择所需的订阅，然后确保选择“确定”。

<a name="create-project"></a>

## <a name="create-a-local-project"></a>创建本地项目

创建逻辑应用之前，请先创建一个本地项目，以便可以从 Visual Studio Code 管理、运行和部署逻辑应用。 基础项目类似于 Azure Functions 项目（也称为函数应用项目）。 但是，这些项目类型彼此独立，因此，逻辑应用和函数应用不能存在于同一项目中。

1. 在计算机上，创建一个空的本地文件夹以用于稍后将在 Visual Studio Code 中创建的项目。

1. 在 Visual Studio Code 中，关闭所有打开的文件夹。

1. 在 Azure 窗格中的“Azure: 逻辑应用(预览版)”旁边，选择“创建新项目”（显示了文件夹和闪电的图标）。

   ![屏幕截图显示了 Azure 窗格工具栏，其中的“创建新项目”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. 如果 Windows Defender 防火墙提示你为 `Code.exe`（这是 Visual Studio Code）和 `func.exe`（这是 Azure Functions Core Tools）授予网络访问权限，请选择“专用网络(如我的家庭或工作网络)”>“允许访问”。  

1. 浏览到你创建项目文件夹的位置，选择该文件夹，然后继续。

   ![屏幕截图显示了“选择文件夹”对话框，其中有一个新创建的项目文件夹并选定了“选择”按钮。](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. 从显示的模板列表中，选择“有状态工作流”或“无状态工作流”。 在此示例中，请选择“有状态工作流”。

   ![屏幕截图显示了工作流模板列表，其中选定了“有状态工作流”。](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. 为你的工作流提供一个名称，然后按 Enter。 此示例使用 `Fabrikam-Stateful-Workflow` 作为名称。

   ![屏幕截图显示了“创建新的有状态工作流(3/4)”框和工作流名称“Fabrikam-Stateful-Workflow”。](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code 将完成项目创建，并在代码编辑器中打开工作流的 **workflow.json** 文件。

   > [!NOTE]
   > 当系统提示你选择如何打开项目时，如果希望在当前 Visual Studio Code 窗口中打开项目，请选择“在当前窗口中打开”。 若要打开 Visual Studio Code 的新实例，请选择“在新窗口中打开”。

1. 在 Visual Studio 工具栏中，打开“资源管理器”窗格（如果尚未打开）。

   “资源管理器”窗格将显示你的项目，该项目现在包括自动生成的项目文件。 例如，该项目有一个显示了你的工作流名称的文件夹。 在该文件夹中，**workflow.json** 文件包含你的工作流的基础 JSON 定义。

   ![屏幕截图显示了“资源管理器”窗格，其中包含项目文件夹、工作流文件夹和“workflow.json”文件。](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. 如果你使用的是 macOS 或 Linux，请按照以下步骤设置对你的存储帐户的访问权限，这些步骤是在本地运行项目所必需的：

   1. 在你的项目的根文件夹中，打开 **local.settings.json** 文件。

      ![屏幕截图显示了“资源管理器”窗格和你的项目中的“local.settings.json”文件。](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. 将 `AzureWebJobsStorage` 属性值替换为之前保存的存储帐户连接字符串，例如：

      早于:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      晚于：

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

      > [!IMPORTANT]
      > 对于生产方案，请确保护此类机密和敏感信息安全，例如，可以使用密钥保管库。

   1. 完成时，请务必保存所做的更改。

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>启用内置的连接器创作

可以使用[预览版的扩展性框架](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)为所需的任何服务创建自己的内置连接器。 与 Azure 服务总线和 SQL Server 等内置连接器相似这些连接器可提供更高的吞吐量、低延迟和本地连接，并且原本就与预览版运行时在同一进程中运行。

目前仅可在 Visual Studio Code 中使用创作功能，该功能默认情况下不启用。 若要创建这些连接器，你首先需要将项目从基于扩展捆绑包 (Node.js) 转换为基于 NuGet 程序包 (.NET)。

> [!IMPORTANT]
> 此操作是不可撤消的单向操作。

1. 在“资源管理器”窗格中的你的项目根目录下，将鼠标指针移动到所有其他文件和文件夹下方的任何空白区域，打开快捷菜单，然后选择“转换为基于 Nuget 的逻辑应用项目”。

   ![屏幕截图显示了“资源管理器”窗格，其中显示了从项目窗口中的空白区域打开的项目快捷菜单。](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. 当出现提示时，确认项目转换。

1. 若要继续，请查看并遵循[随处运行的 Azure 逻辑应用 - 内置的连接器扩展性](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)一文中的步骤。

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>在设计器中打开工作流定义文件

1. 通过运行以下命令来检查计算机上安装的版本：

   `..\Users\{yourUserName}\dotnet --list-sdks`

   如果你有 .NET Core SDK 5.x，此版本可能会阻止你在设计器中打开逻辑应用的基础工作流定义。 不要卸载此版本，请在项目的根文件夹中创建一个 **global.json** 文件，让该文件引用你拥有的高于 3.1.201 的 .NET Core 运行时 3.x，例如：

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > 确保从 Visual Studio Code 中在项目的根文件夹中显式添加 **global.json** 文件。 否则，设计器将不会打开。

1. 展开你的工作流的项目文件夹。 打开 **workflow.json** 文件的快捷菜单，然后选择“在设计器中打开”。

   ![屏幕截图显示了“资源管理器”窗格和 workflow.json 文件的快捷方式窗口，该窗口中的“在设计器中打开”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. 从“在 Azure 中启用连接器”列表中，选择“使用 Azure 提供的连接器”，这包括 Azure 中可用并部署的所有托管连接器，而不仅仅是 Azure 服务的连接器。

   ![屏幕截图显示了“资源管理器”窗格，其中打开了“在 Azure 中启用连接器”列表，并且“使用 Azure 提供的连接器”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > 对于部署在 Azure 中的[托管连接器](../connectors/managed.md)，无状态工作流目前仅支持操作，不支持触发器。 尽管你可以选择在 Azure 中为无状态工作流启用连接器，但设计器不会显示任何托管连接器触发器供你选择。

1. 从“选择订阅”列表中，选择要用于你的逻辑应用项目的 Azure 订阅。

   ![屏幕截图显示了“资源管理器”窗格，其中显示了“选择订阅”框并选中了你的订阅。](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. 从资源组列表中，选择“创建新资源组”。

   ![屏幕截图显示了“资源管理器”窗格，其中显示了资源组列表，并且“创建新资源组”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. 为资源组提供一个名称，然后按 Enter。 本示例使用 `Fabrikam-Workflows-RG`。

   ![屏幕截图显示了“资源管理器”窗格和资源组名称框。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. 从位置列表中，找到并选择在创建资源组和资源时要使用的 Azure 区域。 此示例使用“美国中西部”。

   ![屏幕截图显示了“资源管理器”窗格，其中显示了位置列表，并且“美国中西部”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   执行此步骤后，Visual Studio Code 将打开工作流设计器。

   > [!NOTE]
   > 当 Visual Studio Code 启动工作流设计时 API 时，你可能会收到一条消息，指出启动可能需要几秒钟时间。 你可以忽略此消息，也可以选择“确定”。
   >
   > 如果设计器未打开，请查看故障排除部分中的[设计器未能打开](#designer-fails-to-open)。

   在设计器显示后，“选择操作”提示将显示在设计器中并且默认被选中，这将显示“添加操作”窗格。 

   ![屏幕截图显示了工作流设计器。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. 接下来，向工作流[添加触发器和操作](#add-trigger-actions)。

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>添加触发器和操作

打开设计器后，“选择操作”提示将显示在设计器中并且默认被选中。 现在，可以通过添加触发器和操作开始创建工作流了。

此示例中的工作流使用此触发器和以下操作：

* 内置的 [请求触发器](../connectors/connectors-native-reqres.md)：**当收到 HTTP 请求时**，该触发器将收到入站调用或请求，并创建其他服务或逻辑应用可以调用的终结点。

* [Office 365 Outlook 操作](../connectors/connectors-create-api-office365-outlook.md)：**发送电子邮件**。

* 内置的[响应操作](../connectors/connectors-native-reqres.md)：用于发送答复并将数据返回给调用方。

### <a name="add-the-request-trigger"></a>添加“请求”触发器

1. 在设计器旁边的“添加触发器”窗格中的“选择操作”搜索框下，确保已选中“内置”以便可以选择原本就运行的触发器。

1. 在“选择操作”搜索框中输入 `when a http request`，并选择名为“当收到 HTTP 请求时”的内置请求触发器。

   ![屏幕截图显示了工作流设计器和“添加触发器”窗格，该窗格中的“当收到 HTTP 请求时”触发器处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   当触发器出现在设计器上时，触发器的详细信息窗格将打开，以显示触发器的属性、设置和其他操作。

   ![屏幕截图显示了工作流设计器，其中“当收到 HTTP 请求时”触发器处于选中状态，并且触发器详细信息窗格已打开。](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > 如果未显示详细信息窗格，请确保在设计器上选择了触发器。

1. 如果需要从设计器中删除某个项，请[遵循从设计器中删除项的这些步骤](#delete-from-designer)。

### <a name="add-the-office-365-outlook-action"></a>添加 Office 365 Outlook 操作

1. 在设计器上，在你已添加的触发器下，选择“新建步骤”。

   设计器上将显示“选择操作”提示，并且“添加操作”窗格将重新打开，以便你可以选择下一个操作。

1. 在“添加操作”窗格中的“选择操作”搜索框下选择“Azure”，以便查找并选择在 Azure 中部署的托管连接器的操作。

   此示例选择并使用 Office 365 Outlook 操作“发送电子邮件(V2)”。

   ![屏幕截图显示了工作流设计器和“添加操作”窗格，该窗格中的 Office 365 Outlook 操作“发送电子邮件”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. 在操作的详细信息窗格中，选择“登录”，以便可以创建与电子邮件帐户的连接。

   ![屏幕截图显示了工作流设计器和“发送电子邮件(V2)”窗格，该窗格中的“登录”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. 如果 Visual Studio Code 提示你同意访问你的电子邮件帐户，请选择“打开”。

   ![屏幕截图显示了 Visual Studio Code 提示，该提示请求允许访问。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > 若要防止将来再出现提示，请选择“配置受信任的域”，以便将身份验证页添加为受信任的域。

1. 按照后续提示进行登录、允许访问并允许返回到 Visual Studio Code。

   > [!NOTE]
   > 如果在完成提示之前过去了太多的时间，则身份验证过程将超时并失败。 在这种情况下，请返回到设计器，然后重试登录以创建连接。

1. 如果 Azure 逻辑应用（预览版）扩展提示你同意访问你的电子邮件帐户，请选择“打开”。 按照后续提示来允许访问。

   ![屏幕截图显示了预览版扩展提示，该提示请求允许访问。](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > 若要防止将来再出现提示，请选择“不再针对此扩展询问”。

   在 Visual Studio Code 创建连接后，某些连接器会显示消息“`The connection will be valid for {n} days only`”。 此时间限制仅应用于在 Visual Studio Code 中创作逻辑应用时的持续时间。 部署之后，此限制不再存在，因为逻辑应用在运行时可以通过使用其自动启用的[系统分配的托管标识](../logic-apps/create-managed-service-identity.md)进行身份验证。 该托管标识与你在创建连接时使用的身份验证凭据或连接字符串不同。 如果你禁用了该系统分配的托管标识，则连接在运行时不起作用。

1. 在设计器中，如果“发送电子邮件”操作未显示为选中状态，请选择该操作。

1. 在操作的详细信息窗格中，在“参数”选项卡上提供操作所需的信息，例如：

   ![屏幕截图显示了工作流设计器，其中显示了 Office 365 Outlook 操作“发送电子邮件”的详细信息。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **To** | 是 | <*your-email-address*> | 电子邮件收件人，这可以是你自己的电子邮件地址（用于测试）。 此示例使用虚构的电子邮件 `sophiaowen@fabrikam.com`。 |
   | **主题** | 是 | `An email from your example workflow` | 电子邮件主题 |
   | **正文** | 是 | `Hello from your example workflow!` | 电子邮件正文内容 |
   ||||

   > [!NOTE]
   > 如果要在详细信息窗格中的“设置”、“静态结果”或“随后运行”选项卡上进行任何更改时，请确保在切换选项卡或将焦点更改为设计器之前选择“完成”以提交这些更改。 否则，Visual Studio Code 不会保留你所做的更改。 有关详细信息，请查看 [GitHub 中的逻辑应用公共预览版已知问题页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

1. 在设计器上，选择“保存”。

> [!IMPORTANT]
> 若要在本地运行使用基于 Webhook 的触发器或操作（例如[内置的 HTTP Webhook 触发器或操作](../connectors/connectors-native-webhook.md)）的工作流，则必须通过[为 Webhook 的回调 URL 设置转发](#webhook-setup)来启用此功能。

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>启用本地运行的 Webhook

当你将基于 Webhook 的触发器或操作（例如 **HTTP Webhook**）用于在 Azure 中运行的逻辑应用时，逻辑应用运行时将通过生成回调 URL 并将其注册到服务终结点来订阅该终结点。 然后，触发器或操作将等待服务终结点调用此 URL。 但是，在 Visual Studio Code 中工作时，生成的回调 URL 以 `http://localhost:7071/...` 开头。 此 URL 用于你的 localhost 服务器，该服务器是专用的，因此服务终结点无法调用此 URL。

若要在 Visual Studio Code 中本地运行基于 Webhook 的触发器和操作，你需要设置一个公共 URL，该 URL 将公开 localhost 服务器，并将来自服务终结点的调用安全地转发到 Webhook 回调 URL。 你可以使用转发服务和工具（例如 [**ngrok**](https://ngrok.com/)），它将打开到 localhost 端口的 HTTP 隧道，也可以使用你自己的工具。

#### <a name="set-up-call-forwarding-using-ngrok"></a>使用 **ngrok** 设置调用转发

1. 如果尚没有帐户，请 [注册一个 **ngrok** 帐户](https://dashboard.ngrok.com/signup)。 否则，请[登录到你的帐户](https://dashboard.ngrok.com/login)。

1. 获取你的个人身份验证令牌，你的 **ngrok** 客户端需要使用该令牌连接到你的帐户并向其进行身份验证。

   1. 若要查找你的[身份验证令牌页](https://dashboard.ngrok.com/auth/your-authtoken)，请在帐户仪表板菜单上展开“身份验证”，然后选择“你的身份验证令牌”。

   1. 从“你的身份验证令牌”框中，将令牌复制到一个安全位置。

1. 从 [**ngrok** 下载页](https://ngrok.com/download)或 [你的帐户仪表板](https://dashboard.ngrok.com/get-started/setup)，下载 **ngrok** 所需的版本，然后提取 .zip 文件。 有关详细信息，请参阅[步骤 1：解压缩以进行安装](https://ngrok.com/download)。

1. 在计算机上，打开你的命令提示符工具。 浏览到 **ngrok.exe** 文件所在的位置。

1. 通过运行以下命令将 **ngrok** 客户端连接到你的 **ngrok** 帐户。 有关详细信息，请参阅[步骤 2：连接你的帐户](https://ngrok.com/download)。

   `ngrok authtoken <your_auth_token>`

1. 通过运行以下命令，打开到 localhost 端口 7071 的 HTTP 隧道。 有关详细信息，请参阅[步骤 3：将其激发](https://ngrok.com/download)。

   `ngrok http 7071`

1. 从输出中，找到以下行：

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. 复制并保存具有以下格式的 URL：`http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>在应用设置中设置转发 URL

1. 在 Visual Studio Code 中的设计器上，添加 **HTTP + Webhook** 触发器或操作。

1. 当显示了主机终结点位置提示时，输入之前创建的转发（重定向）URL。

   > [!NOTE]
   > 如果忽略此提示，则会出现警告，指出必须提供转发 URL，因此请选择“配置”，然后输入 URL。 完成此步骤后，对于你可能添加的后续 Webhook 触发器或操作，该提示将不会再次显示。
   >
   > 若要使提示再次显示，请在项目的根级别打开 **local.settings.json** 文件的快捷菜单，然后选择“配置 Webhook 重定向终结点”。 现在，提示将会再次显示，以便你可以提供转发 URL。

   Visual Studio Code 将转发 URL 添加到项目根文件夹中的 **local.settings.json** 文件。 在 `Values` 对象中，现在会出现名为 `Workflows.WebhookRedirectHostUri` 的属性并且它设置为转发 URL，例如：
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

首次启动本地调试会话或在不调试的情况下运行工作流时，逻辑应用运行时会将工作流注册到服务终结点，并订阅该终结点以用于通知 Webhook 操作。 工作流下次运行时，运行时将不会注册或重新订阅，因为本地存储中已存在订阅注册。

如果工作流运行使用本地运行的基于 Webhook 的触发器或操作，则为该工作流运行停止调试会话时，不会删除现有的订阅注册。 若要注销，必须手动移除或删除订阅注册。

> [!NOTE]
> 在工作流开始运行后，终端窗口可能会显示类似于以下示例的错误：
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> 在这种情况下，请打开项目根文件夹中的 **local.settings.json** 文件，并确保该属性设置为 `true`：
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>管理用于调试的断点

在通过启动调试会话来运行和测试逻辑应用工作流之前，你可以在每个工作流的 **workflow.json** 文件中设置 [断点](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)。 无需进行其他设置。 

目前，断点仅可用于操作，不可用于触发器。 每个操作定义都包含以下断点位置：

* 请在显示了操作名称的行上设置开始断点。 当在调试会话过程中命中此断点时，你可以在操作的输入被评估前查看操作的输入。

* 请在显示了操作的右大括号 ( **}** ) 的行上设置结束断点。 当在调试会话过程中命中此断点时，你可以在操作完成运行之前查看操作的结果。

若要添加断点，请执行以下步骤：

1. 打开要调试的工作流的 **workflow.json** 文件。

1. 在要设置断点的行上，在左栏中选择该栏。 若要删除断点，请选择该断点。

   启动调试会话时，“运行”视图将显示在代码窗口的左侧，而“调试”工具栏将显示在顶部附近。

   > [!NOTE]
   > 如果“运行”视图未自动显示，请按 Ctrl+Shift+D。

1. 若要查看命中断点时可用的信息，请在“运行”视图中观察“变量”窗格。

1. 若要继续执行工作流，请在“调试”工具栏上选择“继续”（“播放”按钮）。

可以在工作流运行期间随时添加和删除断点。 但是，如果在运行开始后更新 **workflow.json** 文件，则断点不会自动更新。 若要更新断点，请重启逻辑应用。

有关一般信息，请参阅[断点 - Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)。

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>在本地运行、测试和调试

若要测试逻辑应用，请按照以下步骤启动调试会话，并查找由请求触发器创建的终结点的 URL。 稍后向该终结点发送请求时，你需要使用此 URL。

1. 若要更轻松地调试无状态工作流，你可以[启用该工作流的运行历史记录](#enable-run-history-stateless)。

1. 在 Visual Studio Code 活动栏上，打开“运行”菜单，然后选择“启动调试”(F5)。

   此时将打开“终端”窗口，以便你可以查看调试会话。

   > [!NOTE]
   > 如果收到错误 **运行 preLaunchTask“generateDebugSymbols”后存在错误**，请参阅故障排除部分中的 [调试会话未能启动](#debugging-fails-to-start)。

1. 现在，在请求触发器上查找终结点的回调 URL。

   1. 重新打开“资源管理器”窗格，以便可以查看你的项目。

   1. 从 **workflow.json** 文件的快捷菜单上，选择“概述”。

      ![屏幕截图显示了“资源管理器”窗格和 workflow.json 文件的快捷方式窗口，该窗口中的“概述”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. 查找 **回调 URL** 值，它类似于示例请求触发器的此 URL：

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![屏幕截图显示了工作流的概述页，其中包含回调 URL](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. 若要通过触发逻辑应用工作流来测试回调 URL，请打开 [Postman](https://www.postman.com/downloads/) 或你习惯用来创建和发送请求的工具。

   此示例使用 Postman 继续。 有关详细信息，请参阅 [Postman 入门](https://learning.postman.com/docs/getting-started/introduction/)。

   1. 在 Postman 工具栏上，选择“新建”。

      ![屏幕截图显示了 Postman，其中“新建”按钮处于选中状态](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. 在“新建”窗格上的“构建基块”下，选择“请求”。

   1. 在“保存请求”窗口中的“请求名称”下，为请求提供一个名称，例如 `Test workflow trigger`。

   1. 在“选择要保存到的集合或文件夹”下，选择“创建集合”。

   1. 在“所有集合”下，为要创建的用于组织你的请求的集合提供一个名称，按 Enter，然后选择“保存到 <*collection-name*>”。 此示例使用 `Logic Apps requests` 作为集合名称。

      此时将打开 Postman 的请求窗格，以便你可以将请求发送到请求触发器的回调 URL。

      ![屏幕截图显示了 Postman，其中包含打开的请求窗格](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. 返回到 Visual Studio Code。 在工作流的概述页中，复制“回调 URL”属性值。

   1. 返回到 Postman。 在请求窗格上的方法列表（当前显示了 **GET** 作为默认请求方法）旁边的地址栏中，粘贴之前复制的回调 URL，然后选择“发送”。

      ![屏幕截图显示了 Postman 和地址框中的回调 URL，并且“发送”按钮处于选中状态](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      示例逻辑应用工作流发送的电子邮件类似于以下示例：

      ![屏幕截图显示了示例中所述的 Outlook 电子邮件](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. 在 Visual Studio Code 中，返回到你的工作流的概述页。

   如果你创建了有状态工作流，则在发送的请求触发工作流后，概述页将显示工作流的运行状态和历史记录。

   > [!TIP]
   > 如果未显示运行状态，请尝试通过选择“刷新”来刷新概述页。 如果由于不符合条件或找不到数据而跳过了触发器，则不会发生运行。

   ![屏幕截图显示了工作流的概述页，其中显示了运行状态和历史记录](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | 运行状态 | 说明 |
   |------------|-------------|
   | **Aborted** | 由于外部问题（例如，系统中断或 Azure 订阅过期），运行已停止或未完成。 |
   | 已取消 | 运行已触发并已启动，但收到了取消请求。 |
   | **已失败** | 运行中的至少一个操作失败。 工作流中未设置后续操作来处理失败。 |
   | **正在运行** | 运行已被触发并正在进行，但如果运行由于[操作限制](logic-apps-limits-and-config.md)或[当前定价计划](https://azure.microsoft.com/pricing/details/logic-apps/)而被限制，也可能会显示此状态。 <p><p>**提示**：如果你设置了 [诊断日志记录](monitor-logic-apps-log-analytics.md)，则可以获取发生的任何限制事件的相关信息。 |
   | 成功 | 运行已成功。 如果有任何操作失败，工作流中的后续操作已处理了该失败。 |
   | **已超时** | 运行超时，因为当前持续时间超出了运行持续时间限制，该限制由 [ **“运行历史记录保留期(天)”** 设置](logic-apps-limits-and-config.md#run-duration-retention-limits)控制。 运行持续时间是使用运行开始时间和在该开始时间有效的运行持续时间限制来计算的。 <p><p>**注意**：如果运行的持续时间还超出了当前的运行历史记录保留期限制（该限制也由 [ **“运行历史记录保留期(天)”** 设置](logic-apps-limits-and-config.md#run-duration-retention-limits)控制），则每日清理作业会将该运行从运行历史记录中清除。 无论运行是超时还是完成，始终都将使用运行的开始时间和当前保留期限制来计算保留期。 因此，如果你减小进行中的某个运行的持续时间限制，则该运行将超时。但是，运行将保留或从运行历史记录中清除，具体取决于运行持续时间是否超出了保留期限制。 |
   | **正在等待** | 运行尚未启动或已暂停，例如，由于前一个工作流实例仍在运行。 |
   |||

1. 若要查看特定运行中每个步骤的状态以及步骤的输入和输出，请选择该运行对应的省略号 ( **...** ) 按钮，然后选择“显示运行”。

   ![屏幕截图显示了工作流的运行历史记录行，其中的省略号按钮和“显示运行”处于选中状态](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code 将打开监视视图并显示运行中每个步骤的状态。

   ![屏幕截图显示了工作流运行中的每个步骤及其状态](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > 如果某个运行失败，并且监视视图中的某个步骤显示了 `400 Bad Request` 错误，则此问题可能是由较长的触发器名称或操作名称导致的，这些名称太长会导致基础统一资源标识符 (URI) 超出默认字符数限制。 有关详细信息，请参阅 [400 错误请求](#400-bad-request)。

   下面是工作流中的每个步骤可以具有的可能状态：

   | 操作状态 | 图标 | 说明 |
   |---------------|------|-------------|
   | **Aborted** | ![“已中止”操作状态的图标][aborted-icon] | 由于外部问题（例如，系统中断或 Azure 订阅过期），操作已停止或未完成。 |
   | 已取消 | ![“已取消”操作状态的图标][cancelled-icon] | 操作正在运行，但收到了取消请求。 |
   | **已失败** | ![“已失败”操作状态的图标][failed-icon] | 操作已失败。 |
   | **正在运行** | ![“正在运行”操作状态的图标][running-icon] | 操作当前正在运行。 |
   | 已跳过 | ![“已跳过”操作状态的图标][skipped-icon] | 此操作已被跳过，因为与其紧邻的前一个操作失败。 操作有一个 `runAfter` 条件，该条件要求前一个操作成功完成，然后才能运行当前操作。 |
   | 成功 | ![“已成功”操作状态的图标][succeeded-icon] | 操作已成功。 |
   | **重试成功** | ![“重试成功”操作状态的图标][succeeded-with-retries-icon] | 操作已在一次或多次重试后成功。 若要查看重试历史记录，请在运行历史记录详细信息视图中选择该操作，以便可以查看输入和输出。 |
   | **已超时** | ![“已超时”操作状态的图标][timed-out-icon] | 操作由于超出了操作设置指定的超时限制而停止。 |
   | **正在等待** | ![“正在等待”操作状态的图标][waiting-icon] | 适用于正在等待来自调用方的入站请求的 Webhook 操作。 |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. 若要查看每个步骤的输入和输出，请选择要检查的步骤。

   ![屏幕截图显示了工作流中每个步骤的状态，以及展开的“发送电子邮件”操作中的输入和输出](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. 若要进一步查看该步骤的原始输入和输出，请选择“显示原始输入”或“显示原始输出”。

1. 若要停止调试会话，请在“运行”菜单上，选择“停止调试” (Shift + F5)。

<a name="return-response"></a>

## <a name="return-a-response"></a>返回响应

若要向向逻辑应用发送请求的调用方返回响应，可以为以请求触发器开头的工作流使用内置的[响应操作](../connectors/connectors-native-reqres.md)。

1. 在工作流设计器中，在“发送电子邮件”操作下选择“新建步骤”。

   设计器上将显示“选择操作”提示，并且“添加操作”窗格将重新打开，以便你可以选择下一个操作。

1. 在“添加操作”窗格中的“选择操作”搜索框下，确保已选中“内置”。 在搜索框中输入 `response`，然后选择“响应”操作。

   ![屏幕截图显示了工作流设计器，其中的“响应”操作处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   当“响应”操作显示在设计器上时，操作的详细信息窗格将自动打开。

   ![屏幕截图显示了工作流设计器，其中打开了“响应”操作的详细信息窗格并且“正文”属性设置为“发送电子邮件”操作的“正文”属性值。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. 在“参数”选项卡上，提供要调用的函数所需的信息。

   此示例返回“发送电子邮件”操作输出的“正文”属性值。

   1. 在“正文”属性框中单击，以便显示动态内容列表，并显示来自工作流中前面的触发器和操作的可用输出值。

      ![屏幕截图显示了“响应”操作的详细信息窗格，其中鼠标指针位于“正文”属性内，以便显示动态内容列表。](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. 在动态内容列表中的“发送电子邮件”下，选择“正文”。

      ![屏幕截图显示了打开的动态内容列表。 在列表中的“发送电子邮件”标题下，“正文”输出值处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      完成后，“响应”操作的“正文”属性现在设置为“发送电子邮件”操作的“正文”输出值。

      ![屏幕截图显示了工作流中每个步骤的状态，以及展开的“响应”操作中的输入和输出。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. 在设计器上，选择“保存”。

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>重新测试逻辑应用

对逻辑应用进行更新后，可以通过在 Visual Studio 中重新运行调试器并发送另一个请求来触发已更新的逻辑应用以运行另一个测试（类似于[在本地运行、测试和调试](#run-test-debug-locally)中的步骤）。

1. 在 Visual Studio Code 活动栏上，打开“运行”菜单，然后选择“启动调试”(F5)。

1. 在 Postman 或你用于创建和发送请求的工具中，发送另一个请求来触发你的工作流。

1. 如果你创建了有状态工作流，请在工作流的概述页上检查最新运行的状态。 若要查看该运行中每个步骤的状态、输入和输出，请选择该运行对应的省略号 ( **...** ) 按钮，然后选择“显示运行”。

   例如，下面是在使用“响应”操作更新示例工作流后执行的某个运行的分步状态。

   ![屏幕截图显示了更新后的工作流中每个步骤的状态，以及展开的“响应”操作中的输入和输出。](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. 若要停止调试会话，请在“运行”菜单上，选择“停止调试” (Shift + F5)。

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>查找用于防火墙访问的域名

在 Azure 门户中部署并运行你的逻辑应用工作流之前，如果你的环境具有限制流量的严格网络要求或防火墙，则必须为工作流中存在的任何触发器或操作连接设置权限。

若要查找这些连接的完全限定的域名 (FQDN)，请执行以下步骤：

1. 在你的逻辑应用项目中，打开 **connections.json** 文件（该文件是在你将第一个基于连接的触发器或操作添加到工作流后创建的）并查找 `managedApiConnections` 对象。

1. 针对你创建的每个连接，查找、复制 `connectionRuntimeUrl` 属性值并将其保存到一个安全的位置，以便可以使用此信息设置防火墙。

   此示例 **connections.json** 文件包含两个连接（一个 AS2 连接和一个 Office 365 连接），这些连接具有以下 `connectionRuntimeUrl` 值：

   * AS2：`"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365：`"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>部署到 Azure

从 Visual Studio Code 中，可以直接将项目发布到 Azure，这将使用新的“逻辑应用(预览版)”资源类型部署你的逻辑应用。 与 Azure Functions 中的函数应用资源类似，此新资源类型的部署要求你选择一个[托管计划和定价层](../app-service/overview-hosting-plans.md)（可以在部署期间进行此设置）。 有关托管计划和定价的详细信息，请参阅以下主题：

* [在 Azure 应用服务中纵向扩展](../app-service/manage-scale-up.md)
* [Azure Functions 的缩放和托管](../azure-functions/functions-scale.md)

你可以将逻辑应用作为新资源发布，这将自动创建任何必要的资源，例如 [Azure 存储帐户，这类似于函数应用要求](../azure-functions/storage-considerations.md)。 另外，你还可以将逻辑应用发布到以前部署的“逻辑应用(预览版)”资源，这将覆盖该逻辑应用。

### <a name="publish-to-a-new-logic-app-preview-resource"></a>发布到新的“逻辑应用(预览版)”资源

1. 在 Visual Studio Code 活动栏上，选择 Azure 图标。

1. 在“Azure: 逻辑应用(预览版)”窗格工具栏上，选择“部署到逻辑应用”。

   ![屏幕截图显示了“Azure: 逻辑应用(预览版)”窗格和窗格的工具栏，其中的“部署到逻辑应用”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. 在出现提示时，选择要用于逻辑应用部署的 Azure 订阅。

1. 从 Visual Studio Code 打开的列表中，从以下选项中进行选择：

   * **在 Azure 中创建新的逻辑应用(预览版)** （快速）
   * **在 Azure 中创建新的逻辑应用(预览版)(高级)**
   * 以前部署的“逻辑应用(预览版)”资源（如果存在）

   此示例将继续 **在 Azure 中创建新的逻辑应用(预览版)(高级)** 。

   ![屏幕截图显示了“Azure: 逻辑应用(预览版)”窗格，其中包含一个列表，并且列表中的“在 Azure 中创建新的逻辑应用(预览版)”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. 若要创建新的“逻辑应用(预览版)”资源，请执行以下步骤：

   1. 为新的逻辑应用提供一个全局唯一的名称，这是将用于“逻辑应用(预览版)”资源的名称。 本示例使用 `Fabrikam-Workflows-App`。

      ![屏幕截图显示了“Azure: 逻辑应用(预览版)”窗格，其中的提示要求为要创建的新逻辑应用提供一个名称。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. 为新逻辑应用选择一个 [托管计划](../app-service/overview-hosting-plans.md)，这可以是 [**应用服务计划**(专用)](../azure-functions/dedicated-plan.md) 或 [**高级**](../azure-functions/functions-premium-plan.md)。

      > [!IMPORTANT]
      > 消耗计划不受支持，也不可用于此资源类型。 你选择的计划将影响稍后可供你使用的功能和定价层。 有关详细信息，请查看以下主题： 
      >
      > * [Azure Functions 的缩放和托管](../azure-functions/functions-scale.md)
      > * [应用服务定价详细信息](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > 例如，高级计划用于访问网络功能（例如，以专用方式连接 Azure 虚拟网络以及与之集成），这与创建和部署逻辑应用时的 Azure Functions 类似。 
      > 有关详细信息，请查看以下主题：
      > 
      > * [Azure Functions 网络选项](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps Running Anywhere - Networking possibilities with Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)（随处运行的 Azure 逻辑应用 - Azure 逻辑应用预览版进行网络连接的可能性）

      此示例使用“应用服务计划”。

      ![屏幕截图显示了“Azure: 逻辑应用(预览版)”窗格，其中的提示要求选择“应用服务计划”或“高级”。](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. 创建一个新的应用服务计划或选择一个现有计划。 此示例选择“创建新的应用服务计划”。

      ![屏幕截图显示了“Azure: 逻辑应用(预览版)”窗格，其中的提示要求“创建新的应用服务计划”或选择一个现有应用服务计划。](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. 为应用服务计划提供一个名称，然后为该计划选择一个[定价层](../app-service/overview-hosting-plans.md)。 在此示例中，请选择“F1 免费”计划。

      ![屏幕截图显示了“Azure: 逻辑应用(预览版)”窗格，其中的提示要求选择一个定价层。](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. 为了获得最佳性能，请为部署查找并选择与你的项目相同的资源组。

      > [!NOTE]
      > 尽管可以创建或使用其他资源组，但这样做可能会影响性能。 如果你创建或选择其他资源组，但在出现确认提示后取消，则还会取消你的部署。

   1. 对于有状态工作流，请选择“创建新的存储帐户”或选择一个现有存储帐户。

      ![屏幕截图显示了“Azure: 逻辑应用(预览版)”窗格，其中的提示要求创建或选择一个存储帐户。](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. 如果逻辑应用的创建和部署设置支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，则可以选择为逻辑应用启用诊断日志记录和跟踪。 可以在 Visual Studio Code 中部署逻辑应用时或在部署后执行此操作。 你需要有一个 Application Insights 实例，但你可以在部署逻辑应用时[提前](../azure-monitor/app/create-workspace-resource.md)创建此资源，或在部署后创建此资源。

      若要立即启用日志记录和跟踪，请执行以下步骤：

      1. 选择一个现有 Application Insights 资源或“创建新的 Application Insights 资源”。

      1. 在 [Azure 门户](https://portal.azure.com)中，转到你的 Application Insights 资源。

      1. 在资源菜单中，选择“概述”。 查找并复制 **检测密钥** 值。

      1. 在 Visual Studio Code 中，在你的项目的根文件夹中打开 **local.settings.json** 文件。

      1. 在 `Values` 对象中，添加 `APPINSIGHTS_INSTRUMENTATIONKEY` 属性，并将值设置为检测密钥，例如：

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > 你可以检查触发器和操作名称是否正确显示在你的 Application Insights 实例中。
         >
         > 1. 在 Azure 门户中，转到你的 Application Insights 资源。
         >
         > 2. 在资源菜单中的“调查”下，选择“应用程序映射”。
         >
         > 3. 查看映射中显示的操作名称。
         >
         > 来自内置触发器的某些入站请求在应用程序映射中可能会显示为重复项。 
         > 这些重复项使用工作流名称（而非使用 `WorkflowName.ActionName` 格式）作为操作名称，并且源自 Azure Functions 主机。

      1. 接下来，你可以根据需要调整逻辑应用收集并发送到 Application Insights 实例的跟踪数据的严重性级别。

         每次发生工作流相关事件时（例如，当触发了某个工作流时，或者当某个操作运行时），运行时会发出各种跟踪。 这些跟踪涵盖工作流的生存期，包括但不限于以下事件类型：

         * 服务活动，例如启动、停止和错误。
         * 作业和调度程序活动。
         * 工作流活动，例如触发器、操作和运行。
         * 存储请求活动，例如成功或失败。
         * HTTP 请求活动，例如入站、出站、成功和失败。
         * 任何开发跟踪，例如调试消息。

         每个事件类型都分配有一个严重性级别。 例如，`Trace` 级别捕获最详细的消息，而 `Information` 级别捕获工作流中的常规活动，例如逻辑应用、工作流、触发器和操作何时开始和停止。 下表描述了各个严重性级别及其跟踪类型：

         | 严重性级别 | 跟踪类型 |
         |----------------|------------|
         | 严重 | 此类日志描述逻辑应用中无法恢复的故障。 |
         | 调试 | 此类日志可用于在开发期间进行调查，例如，入站和出站 HTTP 调用。 |
         | 错误 | 此类日志指示工作流执行中的失败，但不会指示逻辑应用中的一般失败。 |
         | 信息 | 此类日志跟踪逻辑应用或工作流中的常规活动，例如： <p><p>- 触发器、操作或运行何时开始和结束。 <br>- 逻辑应用何时启动或结束。 |
         | 跟踪 | 此类日志包含最详细的消息，例如，存储请求或调度程序活动，以及与工作流执行活动相关的所有消息。 |
         | 警告 | 此类日志在逻辑应用中突出显示一个异常状态，但不会阻止其运行。 |
         |||

         若要设置严重性级别，请在项目的根级别打开 **host.json** 文件，然后查找 `logging` 对象。 此对象控制逻辑应用中所有工作流的日志筛选，并遵循[用于日志类型筛选的 ASP.NET Core 布局](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)。

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         如果 `logging` 对象未包含包括 `Host.Triggers.Workflow` 属性的 `logLevel` 对象，请添加这些项。 将该属性设置为所需跟踪类型的严重性级别，例如：

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   完成部署步骤后，Visual Studio Code 将开始创建和部署发布逻辑应用所需的资源。

1. 若要查看和监视部署过程，请在“视图”菜单上选择“输出”。 从“输出”窗口的工具栏列表中，选择“Azure 逻辑应用”。

   ![屏幕截图显示了“输出”窗口，其中显示了工具栏列表中选择的“Azure 逻辑应用”，以及部署进度和状态。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Visual Studio Code 将逻辑应用部署到 Azure 后，将显示以下消息：

   ![屏幕截图显示了一条消息，它指示部署到 Azure 已成功。](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   恭喜，现在你的逻辑应用在 Azure 中处于活动状态，并已默认启用。

接下来，你可以了解如何执行以下任务：

* [向项目中添加一个空白工作流](#add-workflow-existing-project)。

* 使用 [Azure 门户](#manage-deployed-apps-portal)[管理 Visual Studio Code 中的已部署逻辑应用](#manage-deployed-apps-vs-code)。

* [在无状态工作流上启用运行历史记录](#enable-run-history-stateless)。

* [在 Azure 门户中为已部署的逻辑应用启用监视视图](#enable-monitoring)。

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>向项目中添加空白工作流

逻辑应用项目中可以有多个工作流。 若要向项目中添加空白工作流，请执行以下步骤：

1. 在 Visual Studio Code 活动栏上，选择 Azure 图标。

1. 在 Azure 窗格中，在“Azure: 逻辑应用(预览版)”旁边，选择“创建工作流”（用于 Azure 逻辑应用的图标）。

1. 选择要添加的工作流类型：“有状态”或“无状态”

1. 为你的工作流提供一个名称。

完成后，项目中会出现一个新的工作流文件夹，其中包含工作流定义的 **workflow.json** 文件。

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>在 Visual Studio Code 中管理已部署的逻辑应用

在 Visual Studio Code 中，你可以查看你的 Azure 订阅中所有已部署的逻辑应用（无论它们是原始的“逻辑应用”资源类型还是“逻辑应用(预览版)”资源类型），然后选择可帮助你管理这些逻辑应用的任务。 但是，若要访问这两种资源类型，需要使用适用于 Visual Studio Code 的 Azure 逻辑应用和 Azure 逻辑应用（预览版）扩展。

1. 在左侧工具栏上，选择 Azure 图标。 在“Azure: 逻辑应用(预览版)”窗格中，展开你的订阅，其中显示了该订阅的所有已部署逻辑应用。

1. 打开要管理的逻辑应用。 从逻辑应用的快捷菜单中，选择要执行的任务。

   例如，你可以选择的任务包括停止、启动、重启或删除已部署逻辑应用，等等。

   ![屏幕截图显示了 Visual Studio Code，其中包含打开的“Azure 逻辑应用(预览版)”扩展窗格以及已部署的工作流。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. 若要查看逻辑应用中的所有工作流，请展开你的逻辑应用，然后展开“工作流”节点。

1. 若要查看某个特定工作流，请打开该工作流的快捷菜单，然后选择“在设计器中打开”，这将以只读模式打开工作流。

   若要编辑工作流，可以使用以下选项：

   * 在 Visual Studio Code 中，在工作流设计器中打开项目的 **workflow.json** 文件，进行编辑，并将逻辑应用重新部署到 Azure。

   * 在 Azure 门户中，[找到并打开你的逻辑应用](#manage-deployed-apps-portal)。 查找、编辑和保存工作流。

1. 若要在 Azure 门户中打开已部署的逻辑应用，请打开逻辑应用的快捷菜单，然后选择“在门户中打开”。

   Azure 门户将在浏览器中打开，自动使你登录到门户（如果你已登录到 Visual Studio Code）并显示你的逻辑应用。

   ![屏幕截图显示了 Visual Studio Code 中的逻辑应用的 Azure 门户页。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   你还可以单独登录到 Azure 门户，使用门户搜索框查找你的逻辑应用，然后从结果列表中选择你的逻辑应用。

   ![屏幕截图显示了Azure 门户和搜索栏，后者包含了针对已部署逻辑应用的搜索结果（显示为选中状态）。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>在门户中管理已部署的逻辑应用

在 Azure 门户中，你可以查看你的 Azure 订阅中所有已部署的逻辑应用，无论它们是原始的“逻辑应用”资源类型还是“逻辑应用(预览版)”资源类型。 目前，在 Azure 中，每种资源类型都作为单独的类别进行组织和管理。 若要查找具有“逻辑应用(预览版)”资源类型的逻辑应用，请执行以下步骤：

1. 在 Azure 门户的搜索框中，输入 `logic app preview`。 当结果列表出现时，在“服务”下选择“逻辑应用(预览版)”。

   ![屏幕截图显示了 Azure 门户搜索框，其中包含“逻辑应用预览版”搜索文本。](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. 在“逻辑应用(预览版)”窗格中，查找并选择你从 Visual Studio Code 部署的逻辑应用。

   ![屏幕截图显示了 Azure 门户和 Azure 中部署的“逻辑应用(预览版)”资源。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure 门户将打开所选逻辑应用的单个资源页。

   ![屏幕截图显示了 Azure 门户中逻辑应用工作流的资源页。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. 若要查看此逻辑应用中的工作流，请在逻辑应用菜单上选择“工作流”。

   “工作流”窗格将显示当前逻辑应用中的所有工作流。 此示例显示了你在 Visual Studio Code 中创建的工作流。

   ![屏幕截图显示了“逻辑应用(预览版)”资源页，其中打开了“工作流”窗格并显示了已部署的工作流](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. 若要查看工作流，请在“工作流”窗格中选择该工作流。

   工作流窗格将打开，并显示更多信息和你可对该工作流执行的任务。

   例如，若要查看工作流中的步骤，请选择“设计器”。

   ![屏幕截图显示了所选工作流的“概述”窗格，而工作流菜单显示了选定的“设计器”命令。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   工作流设计器将打开并显示你在 Visual Studio Code 中构建的工作流。 你现在可以在 Azure 门户中对此工作流进行更改。

   ![屏幕截图显示了工作流设计器和从 Visual Studio Code 部署的工作流。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>在门户中添加另一个工作流

通过 Azure 门户，你可以将空白工作流添加到从 Visual Studio Code 部署的“逻辑应用(预览版)”资源中并在 Azure 门户中构建这些工作流。

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择已部署的“逻辑应用(预览版)”资源。

1. 在逻辑应用菜单中，选择“工作流”。 在“工作流”窗格上，选择“添加”。

   ![屏幕截图显示了所选逻辑应用的“工作流”窗格和工具栏，工具栏中的“添加”命令处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. 在“新建工作流”窗格中，提供工作流的名称。 选择“有状态”或“无状态” **>** “创建”。

   在 Azure 部署了“工作流”窗格中显示的新工作流后，选择该工作流，以便管理和执行其他任务，例如打开设计器或代码视图。

   ![屏幕截图显示了所选工作流，其中包含管理和查看选项。](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   例如，打开新工作流的设计器将显示一个空白画布。 现在，你可以在 Azure 门户中构建此工作流。

   ![屏幕截图显示了工作流设计器和一个空白工作流。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>为无状态工作流启用运行历史记录

若要更轻松地调试某个无状态工作流，可以为该工作流启用运行历史记录，并在完成后禁用运行历史记录。 对于 Visual Studio Code，请按照下面的步骤操作；如果使用的是 Azure 门户，请参阅[在 Azure 门户中创建有状态和无状态工作流](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)。

1. 在你的 Visual Studio Code 项目中，展开“workflow-designtime”文件夹，然后打开“local.settings.json”文件。

1. 添加 `Workflows.{yourWorkflowName}.operationOptions` 属性，并将值设置为 `WithStatelessRunHistory`，例如：

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS 或 Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. 若要在完成时禁用运行历史记录，请将 `Workflows.{yourWorkflowName}.OperationOptions` 属性设置为 `None`，或者删除此属性及其值。

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>在 Azure 门户中启用监视视图

将“逻辑应用(预览版)”资源从 Visual Studio Code 部署到 Azure 后，你可以通过使用 Azure 门户和该工作流的“监视器”体验来查看该资源中工作流的任何可用的运行历史记录和详细信息。 但是，必须先对该逻辑应用资源启用“监视器”视图功能。

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择已部署的“逻辑应用(预览版)”资源。

1. 在该资源的菜单上，在“API”下选择“CORS”。

1. 在“CORS”窗格上，在“允许的来源”下添加通配符 (*)。

1. 完成后，在“CORS”工具栏上选择“保存” 。

   ![屏幕截图显示了 Azure 门户，其中包含一个已部署的“逻辑应用(预览版)”资源。 在资源菜单中，“CORS”处于选定状态，并且“允许的来源”的一个新条目设置为通配符“*”字符。](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>在部署后启用或打开 Application Insights

在工作流执行过程中，你的逻辑应用会发出遥测数据以及其他事件。 使用此遥测数据可以更好地了解工作流的运行情况以及逻辑应用运行时如何以各种方式工作。 你可以使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 来监视工作流，该工具可提供近乎实时的遥测数据（实时指标）。 使用该数据来诊断问题、设置警报和构建图表时，此功能可帮助你更轻松地调查失败和性能问题。

如果逻辑应用的创建和部署设置支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，则可以选择为逻辑应用启用诊断日志记录和跟踪。 可以在 Visual Studio Code 中部署逻辑应用时或在部署后执行此操作。 你需要有一个 Application Insights 实例，但你可以在部署逻辑应用时[提前](../azure-monitor/app/create-workspace-resource.md)创建此资源，或在部署后创建此资源。

若要在已部署的逻辑应用上启用 Application Insights 或要查看 Application Insights 数据（如果已启用），请执行以下步骤：

1. 在 Azure 门户中，找到你的已部署逻辑应用。

1. 在逻辑应用菜单上，在“设置”下，选择“Application Insights” 。

1. 如果未启用 Application Insights，请在“Application Insights”窗格上选择“启用 Application Insights”。 在窗格更新后，选择底部的“应用”。

   如果启用了 Application Insights，请在“Application Insights”窗格上选择“查看 Application Insights 数据”。

在 Application Insights 打开后，你可以查看逻辑应用的各种指标。 有关详细信息，请查看以下主题：

* [Azure Logic Apps Running Anywhere - Monitor with Application Insights - part 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)（随处运行的 Azure 逻辑应用 - 使用 Application Insights 进行监视 - 第 1 部分）
* [Azure Logic Apps Running Anywhere - Monitor with Application Insights - part 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)（随处运行的 Azure 逻辑应用 - 使用 Application Insights 进行监视 - 第 2 部分）

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>部署到 Docker

可以使用 [.NET CLI](/dotnet/core/tools/) 将逻辑应用部署到作为托管环境的 [Docker 容器](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container)。 使用这些命令，你可以构建并发布你的逻辑应用的项目。 然后，你可以构建并运行 Docker 容器作为目标以用于部署你的逻辑应用。

如果你不熟悉 Docker，请查看以下主题：

* [什么是 Docker？](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [容器和 Docker 简介](/dotnet/architecture/microservices/container-docker-introduction/)
* [.NET 和 Docker 简介](/dotnet/core/docker/introduction)
* [Docker 容器、映像和注册表](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [教程：Docker 入门 (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>要求

* 你的逻辑应用用于部署的 Azure 存储帐户

* 构建 Docker 容器时使用的工作流的 Docker 文件

  例如，该示例 Docker 文件部署逻辑应用并指定连接字符串，其中包含访问 Azure 存储帐户的密钥，可用来将逻辑应用公布到 Azure 门户。 若要查找此字符串，请参阅[获取存储帐户连接字符串](#find-storage-account-connection-string)。 更多信息，请参阅[编写 Docker 文件的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)。
  
  > [!IMPORTANT]
  > 对于生产方案，请确保护此类机密和敏感信息安全，例如，可以使用密钥保管库。 特别是对于 Docker 文件，请查看[用 BuildKit 生成映像](https://docs.docker.com/develop/develop-images/build_enhancements/)和[用 Docker 机密管理敏感数据](https://docs.docker.com/engine/swarm/secrets/)。

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>获取存储帐户连接字符串

你需要获取包含你的存储帐户的访问密钥的连接字符串，然后才能构建并运行你的 Docker 容器映像。 之前，为了在 macOS 或 Linux 上使用该扩展或者在将逻辑应用部署到 Azure 门户时，你创建了此存储帐户。

若要查找并复制此连接字符串，请执行以下步骤：

1. 在 Azure 门户中的存储帐户菜单上，在“设置”下选择“访问密钥”。  

1. 在“访问密钥”窗格中，查找并复制存储帐户的连接字符串，它应当类似于以下示例：

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![屏幕截图显示了 Azure 门户，其中显示了存储帐户访问密钥和复制的连接字符串。](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   有关详细信息，请查看[管理存储帐户密钥](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)。

1. 将连接字符串保存在一个安全的位置，以便可以将此字符串添加到用于部署的 Docker 文件。 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>查找存储帐户的主密钥

如果你的工作流包含一个请求触发器，则需要在构建并运行 Docker 容器映像后[获取触发器的回调 URL](#get-callback-url-request-trigger)。 对于此任务，你还需要指定用于部署的存储帐户的主密钥值。

1. 若要查找此主密钥，请在你的项目中打开 **azure-webjobs-secrets/{deployment-name}/host.json** 文件。

1. 找到 `AzureWebJobsStorage` 属性，然后复制此部分中的密钥值：

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. 将此密钥值保存在一个安全的位置，以便稍后使用。

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>构建并运行 Docker 容器映像

1. 通过使用你的 Docker 文件并运行以下命令，构建建 Docker 容器映像：

   `docker build --tag local/workflowcontainer .`

   有关详细信息，请参阅 [docker build](https://docs.docker.com/engine/reference/commandline/build/)。

1. 使用以下命令在本地运行容器：

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   有关详细信息，请参阅 [docker run](https://docs.docker.com/engine/reference/commandline/run/)。

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>获取请求触发器的回调 URL

对于使用请求触发器的工作流，通过发送以下请求来获取触发器的回调 URL：

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

`{trigger-name}` 值是请求触发器的名称，它显示在工作流的 JSON 定义中。 `{master-key}` 值是在你在 **azure-webjobs-secrets/{deployment-name}/host.json** 文件中为 `AzureWebJobsStorage` 属性设置的 Azure 存储帐户中定义的。 有关详细信息，请参阅[查找存储帐户主密钥](#find-storage-account-master-key)。

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>从设计器中删除项

若要从设计器中删除工作流中的某个项，请执行以下任一步骤：

* 选择该项，打开该项的快捷菜单 (Shift + F10)，然后选择“删除”。 若要确认，请选择“确定”。

* 选择该项，然后按 Delete 键。 若要确认，请选择“确定”。

* 选择该项，以便为该项打开详细信息窗格。 在窗格的右上角，打开省略号 ( **...** ) 菜单，然后选择“删除”。 若要确认，请选择“确定”。

  ![屏幕截图显示了设计器上的一个所选项，以及打开的详细信息窗格和选中的省略号按钮与“删除”命令。](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > 如果省略号菜单不可见，请充分扩展 Visual Studio Code 窗口，以使详细信息窗格在右上角显示省略号 ( **...** ) 按钮。

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>排查错误和问题

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>设计器未能打开

尝试打开设计器时出现以下错误：“无法启动工作流设计时”。 如果你以前尝试打开了该设计器，然后停用或删除了你的项目，则扩展捆绑包可能无法正确下载。 若要检查错误是否由此原因导致，请执行以下步骤：

  1. 在 Visual Studio Code 中，打开“输出”窗口。 从“视图”菜单中，选择“输出”。

  1. 从“输出”窗口的标题栏中的列表中，选择“Azure 逻辑应用(预览版)”以便查看来自扩展的输出，例如：

     ![屏幕截图显示了“输出”窗口，其中的“Azure 逻辑应用”处于选中状态。](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. 查看输出并检查是否显示了以下错误消息：

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   若要解决此错误，请删除位于 **...\Users\{your-username}\AppData\Local\Temp\Functions\ExtensionBundles** 位置的 **ExtensionBundles** 文件夹，然后重试在设计器中打开 **workflow.json** 文件。

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>对于以前创建的工作流，设计器选取器中缺少新的触发器和操作

Azure 逻辑应用预览版支持将内置操作用于 Azure 函数操作、Liquid 操作和 XML 操作（例如 **XML 验证** 和 **转换 XML**）。 但是，对于以前创建的逻辑应用，如果 Visual Studio Code 使用的是过时版本的扩展捆绑包 `Microsoft.Azure.Functions.ExtensionBundle.Workflows`，则这些操作可能不会出现在设计器选取器中供你选择。

此外，“Azure 函数操作”连接器和操作不会显示在设计器选取器中，除非你在创建逻辑应用时启用或选择了“使用 Azure 提供的连接器”。 如果你在创建应用时未启用 Azure 部署的连接器，则可以在 Visual Studio Code 中从你的项目启用它们。 打开 **workflow.json** 的快捷菜单并选择“使用 Azure 提供的连接器”。

若要修复过时的捆绑包，请按照以下步骤删除过时的捆绑包，这会使 Visual Studio Code 自动将扩展捆绑包更新到最新版本。

> [!NOTE]
> 此解决方案仅适用于使用 Visual Studio Code 通过 Azure 逻辑应用（预览版）扩展创建的逻辑应用，不适用于使用 Azure 门户创建的逻辑应用。 请参阅 [Azure 门户中的设计器缺少受支持的触发器和操作](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions)。

1. 保存所有不希望丢失的工作，然后关闭 Visual Studio。

1. 在你的计算机上，浏览到以下文件夹，其中包含现有捆绑包的带版本号的文件夹：

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 删除早期捆绑包的版本文件夹，例如，如果存在版本 1.1.3 的文件夹，请删除该文件夹。

1. 现在，浏览到以下文件夹，其中包含所需 NuGet 程序包的带版本号的文件夹：

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. 删除早期程序包的版本文件夹，例如，如果存在版本 1.0.0.8-preview 的文件夹，请删除该文件夹。

1. 重新打开 Visual Studio Code 和你的项目，在设计器中打开 **workflow.json** 文件。

缺少的触发器和操作现在将出现在设计器中。

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>触发器或操作中出现“400 错误请求”

当某个运行失败时，如果在监视视图中检查该运行，在名称较长的触发器或操作上可能会显示此错误，这些名称太长会导致基础统一资源标识符 (URI) 超出默认字符数限制。

若要解决此问题并调整较长的 URI，请按照以下步骤在计算机上编辑 `UrlSegmentMaxCount` 和 `UrlSegmentMaxLength` 注册表项。 [适用于 Windows 的 Http.sys 注册表设置](/troubleshoot/iis/httpsys-registry-windows)主题中介绍了这些项的默认值。

> [!IMPORTANT]
> 在开始之前，请务必保存你的工作。 此解决方案要求你在完成后重启计算机，以便使更改生效。

1. 在你的计算机上，打开“运行”窗口并运行 `regedit` 命令，这将打开注册表编辑器。

1. 在“User Account Control”框中选择“Yes”，以允许对计算机进行更改。

1. 在左侧窗格中，在“Computer”下面沿路径“HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters”展开节点，然后选择“Parameters”。

1. 在右侧窗格中，查找 `UrlSegmentMaxCount` 和 `UrlSegmentMaxLength` 注册表项。

1. 充分增大这些项的值，使 URI 可以容纳你要使用的名称。 如果这些项不存在，请执行以下步骤，将它们添加到“Parameters”文件夹：

   1. 从“Parameters”快捷菜单中，选择“新建” > “DWORD(32-位)值”。 

   1. 在出现的编辑框中，输入 `UrlSegmentMaxCount` 作为新的项名称。

   1. 打开新项的快捷菜单并选择“修改”。

   1. 在出现的“编辑字符串”框中，以十六进制或十进制格式输入所需的“数值数据”项值。 例如，十六进制的 `400` 等效于十进制的 `1024`。

   1. 若要添加 `UrlSegmentMaxLength` 项值，请重复这些步骤。

   在增大或添加这些项值后，注册表编辑器如以下示例所示：

   ![屏幕截图显示了注册表编辑器。](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. 准备就绪后，重启计算机，以使更改生效。

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>调试会话未能启动

尝试启动调试会话时，你收到错误 **运行 preLaunchTask“generateDebugSymbols”后存在错误**。 若要解决此问题，请编辑项目中的 **tasks.json** 文件以跳过符号生成。

1. 在你的项目中，展开“.vscode”文件夹，然后打开“tasks.json”文件。

1. 在下面的任务中，删除 `"dependsOn: "generateDebugSymbols"` 行以及上一行末尾的逗号，例如：

   早于:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   晚于：

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>后续步骤

我们希望你就 Azure 逻辑应用（预览版）扩展的使用体验提供反馈！

* 如果发现 bug 或问题，请[在 GitHub 中创建问题](https://github.com/Azure/logicapps/issues)。
* 如有问题、请求、意见和其他反馈，请[使用此反馈表单](https://aka.ms/lafeedback)。
