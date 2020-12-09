---
title: 在 Visual Studio Code 中创建逻辑应用预览工作流
description: " (预览版) 扩展，为 Visual Studio Code 中的自动化和集成方案生成并运行工作流。"
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: cf8b6670817132c8c0c0a09194b9aca4bbc8073c
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922994"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>在 Azure 逻辑应用 Visual Studio Code 中创建有状态和无状态工作流 (预览版) 扩展

> [!IMPORTANT]
> 此功能现为公共预览版，在提供时不附带服务级别协议，建议不要用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 [Azure 逻辑应用预览](logic-apps-overview-preview.md)，可以通过使用 Azure 逻辑应用 (预览版) 扩展在 Visual Studio Code 中创建和运行包含有 [*状态* 和 *无状态* 工作流](logic-apps-overview-preview.md#stateful-stateless) 的逻辑应用，从而跨应用、数据、云服务和系统生成自动化和集成解决方案。 通过使用这一新的逻辑应用类型，你可以构建由重新设计的 Azure 逻辑应用预览运行时提供支持的多个工作流，该运行时提供可移植性、更好的性能和灵活性，可用于在各种宿主环境中进行部署和运行，而不仅限于 Azure，还支持 Docker 容器 若要了解有关新的逻辑应用类型的详细信息，请参阅 [Azure 逻辑应用的概述预览](logic-apps-overview-preview.md)。

![显示 Visual Studio Code、逻辑应用项目和工作流的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

在 Visual Studio Code 中，你可以首先创建一个在 *本地* 生成的项目，然后使用 Azure 逻辑应用 (预览版) 扩展在开发环境中运行逻辑应用的工作流。 尽管还可以通过 [创建新的 **逻辑应用 (预览)** Azure 门户中的资源](create-stateful-stateless-workflows-azure-portal.md)，但这两种方法都能让你在同一种类的宿主环境中部署和运行逻辑应用。

同时，你仍可创建原始逻辑应用类型。 尽管 Visual Studio Code 的开发体验在原始和新的逻辑应用类型之间有所不同，但 Azure 订阅可以同时包含这两种类型。 可以在 Azure 订阅中查看和访问所有已部署的逻辑应用，但这些应用会组织到它们自己的类别和分区中。

本文介绍如何使用 Azure 逻辑应用 (预览版) 扩展并执行以下高级任务，在 Visual Studio Code 中创建逻辑应用和工作流：

* 为逻辑应用和工作流创建项目。

* 添加触发器和操作。

* 在本地运行、测试、调试和查看运行历史记录。

* 部署到 Azure，其中包含（可选）启用 Application Insights。

* 在 Visual Studio Code 和 Azure 门户中管理已部署的逻辑应用。

* 启用无状态工作流的运行历史记录。

* 部署后启用或打开 Application Insights。

* 部署到可在任何位置运行的 Docker 容器。

> [!NOTE]
> 有关当前已知问题的信息，请查看 [GitHub 中的逻辑应用公共预览版已知问题页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

## <a name="prerequisites"></a>先决条件

### <a name="access-and-connectivity"></a>访问和连接

* 访问 internet，以便可以下载要求、从 Visual Studio Code 连接到 Azure 帐户，以及从 Visual Studio Code 发布到 Azure、Docker 容器或其他环境。

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 若要在本文中生成相同的示例逻辑应用，需要使用 Microsoft 工作或学校帐户登录的 Office 365 Outlook 电子邮件帐户。

  如果你选择使用 [Azure 逻辑应用支持的其他电子邮件连接器](/connectors/)（如 Outlook.com 或 [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)），仍可以按照示例操作，一般的整体步骤相同，但你的用户界面和选项在某些方面可能会有所不同。 例如，如果你使用 Outlook.com 连接器，则使用你的个人 Microsoft 帐户改为登录。

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>存储要求

#### <a name="windows-and-linux"></a>Windows 和 Linux

1. 下载并安装 [Azure 存储模拟器 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179)。

1. 若要运行仿真程序，需要安装本地 SQL DB，如免费的 [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658)。 有关详细信息，请参阅 [使用 Azure 存储模拟器进行开发和测试](../storage/common/storage-use-emulator.md)。

   > [!IMPORTANT]
   > 在打开设计器以生成工作流之前，请确保启动模拟器。 否则，你将收到一条消息 `Workflow design time could not be started` 。
   >
   > ![显示运行 Azure 存储模拟器的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos"></a>macOS

1. 登录到 [Azure 门户](https://portal.azure.com)，并 [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)，这是 [Azure Functions 的先决条件](../azure-functions/storage-considerations.md)。

1. [查找并复制存储帐户的连接字符串](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)，例如：

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![屏幕截图，显示具有存储帐户访问密钥和连接字符串的 Azure 门户。](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

1. 将字符串保存在一个安全的位置，以便稍后可以将该字符串添加到项目中的 **local.settings.js** 文件中，用于在 Visual Studio Code 中创建逻辑应用。

稍后尝试在逻辑应用中打开工作流的设计器时，会收到一条消息 `Workflow design time could not be started` 。 显示此消息后，你必须将存储帐户的连接字符串添加到项目中的两个 **local.settings.js** 文件中，然后重试打开设计器。

### <a name="tools"></a>工具

* [Visual Studio Code 1.30.1 (2019 年1月) 或更高版本](https://code.visualstudio.com/)（免费）。 此外，为 Visual Studio Code 下载并安装这些其他工具（如果尚未安装）：

  * [Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)，为 Visual Studio Code 中的所有其他 azure 扩展提供单个常见的 azure 登录和订阅筛选体验。

  * [C # for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)，使 F5 功能可以运行逻辑应用。

  * [Azure Functions Core Tools 3.0.2931 或更高版本，请](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) 使用 Microsoft INSTALLER (MSI) 。

    这些工具包括一个用于支持 Azure Functions 运行时的版本，在 Visual Studio Code 中，预览扩展使用该版本。

    > [!IMPORTANT]
    > 如果安装的版本早于这些版本，请先卸载该版本，或者确保路径环境变量指向你下载和安装的版本。

  * [适用于 Visual Studio Code 的 Azure 逻辑应用 (预览) 扩展](https://go.microsoft.com/fwlink/p/?linkid=2143167)。 此扩展提供了创建逻辑应用的功能，你可以在其中构建本地运行 Visual Studio Code 中的有状态和无状态工作流，然后将这些逻辑应用直接部署到 Azure 或 Docker 容器。

    目前，可以在 Visual Studio Code 中安装原始 Azure 逻辑应用扩展和公共预览版扩展。 尽管开发体验在扩展之间以某些方式有所不同，但你的 Azure 订阅可以包含你用扩展创建的逻辑应用类型。 Visual Studio Code 显示你的 Azure 订阅中的所有已部署逻辑应用，但会按扩展名称、 **逻辑应用** 和 **Azure 逻辑应用 (预览版)** 将它们组织到不同的部分。

    > [!IMPORTANT]
    > 如果使用早期的专用预览扩展创建了逻辑应用，则这些逻辑应用将不会使用公共预览版扩展。 但是，你可以在卸载专用预览扩展、删除关联的文件并安装公共预览版扩展后迁移这些逻辑应用。 然后，在 Visual Studio Code 中创建一个新项目，并将以前创建的逻辑应用的 **工作流定义** 文件复制到您的新项目中。 有关详细信息，请参阅 [从个人预览版扩展迁移](#migrate-private-preview)。

    若要安装 **Azure 逻辑应用 (预览)** 扩展，请执行以下步骤：

    1. 在 Visual Studio Code 的左侧工具栏中，选择 " **扩展**"。

    1. 在 "扩展" 搜索框中输入 `azure logic apps preview` 。 在结果列表中，选择 " **Azure 逻辑应用 (预览")** " **>** **安装**"。

       安装完成后，预览扩展将出现在 " **扩展：已安装** " 列表中。

       ![屏幕截图，显示 Visual Studio Code 安装的扩展列表，其中包含带下划线的 "Azure 逻辑应用 (预览版) " 扩展。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* 若要在 Visual Studio Code 中本地运行基于 webhook 的触发器和操作，例如 [内置 HTTP webhook 触发器](../connectors/connectors-native-webhook.md)，需要为 [回调 URL 设置转发](#webhook-setup)。

* 若要测试在本文中创建的示例逻辑应用，需要一个可将调用发送到请求触发器的工具，这是示例逻辑应用的第一个步骤。 如果没有此类工具，可以下载、安装和使用 [Postman](https://www.postman.com/downloads/)。

* 如果创建逻辑应用并将其部署为支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)的设置，则可以选择为逻辑应用启用诊断日志记录和跟踪。 在部署逻辑应用时，可以从 Visual Studio Code 或部署后执行此操作。 你需要有一个 Application Insights 实例，但你可以在部署逻辑应用时或部署后 [提前](../azure-monitor/app/create-workspace-resource.md)创建此资源。

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>从专用预览扩展迁移

使用 Azure 逻辑应用创建的任何逻辑应用 **(个人预览版)** 扩展将不会使用 PublicPreview 扩展。 不过，你可以通过执行以下步骤将这些逻辑应用迁移到新的 Visual Studio Code 项目：

1. 卸载专用预览扩展。

1. 在以下位置删除关联的扩展包和 NuGet 包文件夹：

   * **ExtensionBundle** 文件夹，其中包含以前的扩展捆绑，位于以下路径中的任一路径：

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`

   * 作为专用预览扩展插件的 [NuGet](/nuget/what-is-nuget)缓存，并位于此路径中，则为 **microsoft.**

     `C:\Users\{userName}\.nuget\packages`

1. **(预览) 扩展安装 Azure 逻辑应用**。

1. 在 Visual Studio Code 中创建一个新项目。

1. 将之前创建的逻辑应用的 **工作流定义** 文件复制到新项目中。

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>设置 Visual Studio Code

1. 若要确保正确安装所有扩展，请 Visual Studio Code 重新加载或重新启动。

1. 启用或确认 Visual Studio Code 会自动查找并安装扩展更新，以便你的 PublicPreview 扩展获取最新更新。 否则，你必须手动卸载过期版本并安装最新版本。

   若要检查此设置，请执行以下步骤：

   1. 在 " **文件** " 菜单上，中转到 " **首选项**" " **>** **设置**"。

   1. 在 " **用户** " 选项卡上，中转到 " **功能** **>** **扩展**"。

   1. 确认已选中 " **自动检查更新** 并 **自动更新** "。

1. 启用或确认这些扩展设置具有正确的选择：

   * **Azure 逻辑应用 V2： Panel 模式**
   * **Azure 逻辑应用 V2：项目运行时**

   1. 在 " **文件** " 菜单上，中转到 " **首选项**" " **>** **设置**"。

   1. 在 "**用户**" 选项卡上，中转到 " **>** **Extensions** **>** **Azure 逻辑应用 (预览")**。

   1. 确认以下扩展设置：

      * 在 **"Azure 逻辑应用 V2：面板模式**" 下，确认已选中 **"启用面板模式"** 。

      * 在 " **Azure 逻辑应用 V2：项目运行时**" 下，确认版本设置为 **~ 3**。

        > [!IMPORTANT]
        > 若要使用 macOS 和 Linux 当前不可用的 [内联代码操作操作](../logic-apps/logic-apps-add-run-inline-code.md)，则 **项目运行时** 设置需要版本3。

      ![显示 "Azure 逻辑应用 (预览版) " 扩展 Visual Studio Code 设置的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

1. 在 "Visual Studio Code" 活动栏上，选择 "Azure" 图标。

   ![显示 Visual Studio Code 活动栏和所选 Azure 图标的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. 在 Azure 窗格中的 " **azure：逻辑应用 (预览")** 中，选择 " **登录到 Azure**"。 出现 "Visual Studio Code 身份验证" 页面时，请用 Azure 帐户登录。

   ![显示 azure 窗格和 Azure 登录的所选链接的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   登录后，Azure 窗格将显示 Azure 帐户中的订阅。 如果还具有公开发布的扩展，可以在 " **逻辑应用** " 部分中找到使用该扩展创建的任何逻辑应用，而不是 " **逻辑应用 (预览")** 部分。
   
   如果预期的订阅未显示，或者您希望该窗格仅显示特定订阅，请执行以下步骤：

   1. 在 "订阅" 列表中，将指针移动到第一个订阅旁边，直到 " **选择订阅** " 按钮 (筛选器 "图标) 出现。 选择 "筛选器" 图标。

      ![显示 Azure 窗格和所选筛选器图标的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      或者，在 "Visual Studio Code" 状态栏中，选择 Azure 帐户。 

   1. 如果出现其他订阅列表，请选择所需的订阅，然后确保选择 **"确定"**。

<a name="create-project"></a>

## <a name="create-a-local-project"></a>创建本地项目

创建逻辑应用之前，请先创建一个本地项目，以便可以从 Visual Studio Code 管理和部署逻辑应用。 基础项目类似于 Azure Functions 项目，也称为函数应用项目。 但是，这些项目类型彼此独立，因此逻辑应用和函数应用不能存在于同一项目中。

1. 在计算机上，创建一个 *空* 的本地文件夹以用于稍后在 Visual Studio Code 中创建的项目。

1. 在 Visual Studio Code 中，关闭所有打开的文件夹。

1. 在 Azure 窗格中的 " **azure：逻辑应用 (预览")** 上，选择 " **新建项目** " (图标，该图标显示文件夹和闪电) 。

   ![显示选中了 "创建新项目" 的 Azure 窗格工具栏的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. 如果 Windows Defender 防火墙提示你授予的网络访问权限 `Code.exe` ，这是 Visual Studio Code 的，而对于 `func.exe` Azure Functions Core Tools，则选择 " **专用网络"，例如 "我的家庭" 或 "工作网络** **>** **允许访问**"。

1. 浏览到创建项目文件夹的位置，选择该文件夹，然后继续。

   ![显示 "选择文件夹" 对话框并选择新创建的项目文件夹和 "选择" 按钮的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. 从显示的 "模板" 列表中，选择有 **状态工作流** 或 **无状态工作流**。 此示例选择有 **状态工作流**。

   ![显示工作流模板列表的屏幕截图，其中选择了 "有状态工作流"。](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. 提供工作流的名称，然后按 Enter。 此示例使用 `Fabrikam-Stateful-Workflow` 作为名称。

   ![屏幕截图，显示 "创建新的有状态工作流 (3/3) " 框和 "Fabrikam-有状态的工作流" 作为工作流名称。](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code 完成创建项目，并打开工作流的文件 **workflow.js** 。

1. 在 Visual Studio 工具栏中，打开 "资源管理器" 窗格（如果尚未打开）。

   "资源管理器" 窗格显示你的项目，该项目现在包括自动生成的项目文件。 例如，项目有一个文件夹，其中显示工作流的名称。 在此文件夹中，文件中的 **workflow.js** 包含工作流的基础 JSON 定义。

   ![屏幕截图，显示包含项目文件夹、工作流文件夹和 "workflow.js" 文件的 "资源管理器" 窗格。](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>在设计器中打开工作流定义文件

1. 通过运行以下命令来检查计算机上安装的版本：

   `..\Users\{yourUserName}\dotnet --list-sdks`

   如果你已 .NET Core SDK 1.x，此版本可能会阻止你在设计器中打开逻辑应用的基础工作流定义。 不要卸载此版本，而是在项目的根级别创建一个 **global.js** 文件，该文件引用的 .net Core 运行时2.x 版本高于3.1.201，例如：

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   请确保从 Visual Studio Code 内将文件中的 **global.js** 显式添加到项目的根级别。 否则，设计器将不会打开。

1. 如果正在 Windows 或 Linux 上运行 Visual Studio Code，请确保 Azure 存储模拟器正在运行。 有关详细信息，请参阅 [先决条件](#prerequisites)。 如果在 macOS 上运行 Visual Studio Code，则继续执行下一步。

1. 展开工作流的项目文件夹。 打开文件快捷菜单上的 " **workflow.js** ，然后选择" **在设计器中打开**"。

   ![显示 "在设计器中打开" 的文件上的 workflow.js的 "资源管理器" 窗格和快捷窗口的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   如果收到错误消息 `Workflow design time could not be started` ，请检查以下情况：

   * **Windows 或 Linux**：确保 Azure 存储模拟器正在运行。 否则，请参阅 [疑难解答问题和错误](#troubleshooting)。

   * **macOS**：尝试以下解决方案，如果不成功，请参阅 [排查问题和错误](#troubleshooting)。

     1. 在项目中，打开文件的 **local.settings.js** ，可以在项目的根文件夹和 **designtime** 文件夹中找到这些文件。

        ![显示项目中的 "资源管理器" 窗格和 "local.settings.js" 的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

     1. 在每个文件中，找到 `AzureWebJobsStorage` 属性，例如：

        ```json
        {
           "IsEncrypted": false,
           "Values": {
              "AzureWebJobsStorage": "UseDevelopmentStorage=true",
              "FUNCTIONS_WORKER_RUNTIME": "dotnet"
            }
        }
        ```

      1. 将 `AzureWebJobsStorage` 属性值替换为之前从存储帐户保存的连接字符串，例如：

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet"
            }
         }
         ```

      1. 保存更改，并尝试在设计器中重新打开文件 **上的workflow.js** 。

1. 从 " **在 azure 中启用连接器** " 列表中，选择 " **使用 azure** 中的连接器"，此功能适用于 azure 中提供并部署的所有托管连接器，而不只是 azure 服务的连接器。

   ![屏幕截图显示 "在 Azure 中启用连接器" 列表，并选择 "使用 Azure 中的连接器"。](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > 无状态工作流目前只支持 [托管连接器](../connectors/apis-list.md#managed-api-connectors)的操作，这些 *操作* 部署在 Azure 中，而不支持触发器。 尽管你可以选择在 Azure 中为无状态工作流启用连接器，但设计器不会显示任何托管的连接器触发器供你选择。

1. 从 " **选择订阅** " 列表中，选择要用于逻辑应用项目的 Azure 订阅。

   ![显示 "选择订阅" 框和所选订阅的 "资源管理器" 窗格的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. 从 "资源组" 列表中，选择 " **新建资源组**"。

   ![显示 "资源组" 列表并选择 "创建新资源组" 的 "资源管理器" 窗格的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. 提供资源组的名称，然后按 Enter。 本示例使用 `Fabrikam-Workflows-RG`。

   ![显示 "资源管理器" 窗格和 "资源组名称" 框的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. 从 "位置" 列表中，找到并选择要在创建资源组和资源时使用的 Azure 区域。 此示例使用 **美国西部**。

   ![显示 "资源管理器" 窗格和 "位置" 列表并选择 "美国西部" 的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   执行此步骤后，Visual Studio Code 打开工作流设计器。

   > [!NOTE]
   > 当 Visual Studio Code 启动工作流设计时 API 时，会出现一条消息，指示启动可能需要几秒钟时间。 您可以忽略此消息，也可以选择 **"确定"**。

   设计器显示后，设计器中将显示 " **选择操作** " 提示，并在默认情况下选中，其中显示了 " **添加操作** " 窗格。

   ![显示工作流设计器的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. 接下来，向工作流 [添加触发器和操作](#add-trigger-actions) 。

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>添加触发器和操作

打开设计器后，设计器中将显示 " **选择操作** " 提示，并在默认情况下处于选中状态。 现在可以通过添加触发器和操作来开始创建工作流。

本示例中的工作流使用此触发器和以下操作：

* 内置 [请求触发器](../connectors/connectors-native-reqres.md)： **收到 HTTP 请求时**，接收入站调用或请求，并创建其他服务或逻辑应用可调用的终结点。

* [Office 365 Outlook 操作](../connectors/connectors-create-api-office365-outlook.md)**发送电子邮件**。

* 内置 [响应操作](../connectors/connectors-native-reqres.md)，用于发送答复并将数据返回给调用方。

### <a name="add-the-request-trigger"></a>添加“请求”触发器

1. 在设计器旁的 " **添加触发器** " 窗格的 " **选择操作** " 搜索框下，确保已选中 " **内置** "，以便可以选择本机运行的触发器。

1. 在 " **选择操作** " 搜索框中输入 `when a http request` ，并选择在 **收到 HTTP 请求时** 命名的内置请求触发器。

   ![显示工作流设计器的屏幕截图，并在选择 "收到 HTTP 请求时添加触发器 * *" 窗格。](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   当设计器上出现触发器时，触发器的详细信息窗格将打开，显示触发器的属性、设置和其他操作。

   ![显示工作流设计器的屏幕截图，其中选择了 "收到 HTTP 请求时" 触发器，并打开了触发器详细信息窗格。](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > 如果未显示详细信息窗格，请确保在设计器上选择了触发器。

1. 如果需要从设计器中删除项，请 [按照以下步骤从设计器中删除项](#delete-from-designer)。

### <a name="add-the-office-365-outlook-action"></a>添加 Office 365 Outlook 操作

1. 在设计器的已添加的触发器下，选择 " **新建步骤**"。

   设计器上将显示 " **选择操作** " 提示，并重新打开 " **添加操作** " 窗格，以便您可以选择下一个操作。

1. 在 " **添加操作** " 窗格中的 " **选择操作** " 搜索框下，选择 " **Azure** "，以便查找并选择在 azure 中部署的托管连接器的操作。

   此示例选择并使用 Office 365 Outlook 操作， **(V2) 发送电子邮件**。

   ![屏幕截图显示工作流设计器，并使用 Office 365 Outlook "发送电子邮件" 操作选择 "添加操作" * * 窗格。](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. 在操作的详细信息窗格中，选择 " **登录** "，以便可以创建与电子邮件帐户的连接。

   ![屏幕截图显示工作流设计器，并将 "发送电子邮件 (V2) * *" 窗格中选择 "登录"。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. 如果 Visual Studio Code 提示你同意访问你的电子邮件帐户，请选择 " **打开**"。

   ![屏幕截图，显示允许访问的 Visual Studio Code 提示。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > 若要防止将来出现提示，请选择 " **配置受信任的域** "，以便可以将 "身份验证" 页添加为受信任的域。

1. 按照后续提示进行登录、允许访问并允许返回 Visual Studio Code。

   > [!NOTE]
   > 如果在完成提示之前经过了太多的时间，则身份验证过程将超时并失败。 在这种情况下，请返回到设计器，然后重试登录以创建连接。

1. 如果 Azure 逻辑应用 (预览版) 扩展会提示你同意访问你的电子邮件帐户，请选择 " **打开**"。 遵循后续提示以允许访问。

   ![屏幕截图，显示允许访问的预览扩展提示。](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > 若要防止将来出现提示，请选择 " **不再询问此扩展**"。

   Visual Studio Code 创建连接后，某些连接器会显示消息 `The connection will be valid for {n} days only` 。 此时间限制仅适用于在 Visual Studio Code 中创作逻辑应用时的持续时间。 部署之后，此限制不再适用，因为逻辑应用可以在运行时通过使用其自动启用 [系统分配的托管标识](../logic-apps/create-managed-service-identity.md)进行身份验证。 此托管标识不同于在创建连接时使用的身份验证凭据或连接字符串。 如果禁用此系统分配的托管标识，则连接在运行时不起作用。

1. 在设计器中，如果 " **发送电子邮件** " 操作未显示选中状态，请选择该操作。

1. 在操作的详细信息窗格中的 " **参数** " 选项卡上，提供操作所需的信息，例如：

   ![显示工作流设计器的屏幕截图，其中包含 Office 365 Outlook "发送电子邮件" 操作的详细信息。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | 属性 | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **To** | 是 | <*your-email-address*> | 电子邮件收件人，可以是你的电子邮件地址，用于测试目的。 此示例使用虚构电子邮件 `sophiaowen@fabrikam.com` 。 |
   | **主题** | 是 | `An email from your example workflow` | 电子邮件主题 |
   | **正文** | 是 | `Hello from your example workflow!` | 电子邮件正文内容 |
   ||||

   > [!NOTE]
   > 如果要在 " **设置**"、" **静态结果**" 或 " **之后运行** " 选项卡的 "详细信息" 窗格中进行任何更改，请确保选择 " **完成** " 以在切换选项卡或将焦点更改为设计器之前提交这些更改。 否则，Visual Studio Code 不会保留你的更改。 有关详细信息，请查看 [GitHub 中的逻辑应用公共预览版已知问题页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

1. 在设计器中，选择 " **保存**"。

> [!IMPORTANT]
> 若要在本地运行使用基于 webhook 的触发器或操作的工作流，例如 [内置 HTTP webhook 触发器或操作](../connectors/connectors-native-webhook.md)，则必须通过 [为 WEBHOOK 的回调 URL 设置转发](#webhook-setup)来启用此功能。

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>启用本地运行的 webhook

当你将基于 webhook 的触发器或操作（如 **HTTP webhook**）用于在 Azure 中运行的逻辑应用时，逻辑应用运行时将通过生成该终结点并注册回调 URL 来订阅服务终结点。 然后，触发器或操作会等待服务终结点调用 URL。 但是，在 Visual Studio Code 中工作时，生成的回调 URL 以开头 `http://localhost:7071/...` 。 此 URL 适用于你的 localhost 服务器，这是专用的，因此服务终结点无法调用此 URL。

若要在 Visual Studio Code 中本地运行基于 webhook 的触发器和操作，需要设置一个公共 URL，该 URL 将公开 localhost 服务器，并将服务终结点中的调用安全地转发到 webhook 回调 URL。 可以使用转发服务和工具（如 [**ngrok**](https://ngrok.com/)）打开到 localhost 端口的 HTTP 隧道，也可以使用自己的工具。

#### <a name="set-up-call-forwarding-using-ngrok"></a>使用 **ngrok** 设置呼叫转发

1. 如果没有帐户，请 [注册 **ngrok** 帐户](https://dashboard.ngrok.com/signup)。 否则，请 [登录到你的帐户](https://dashboard.ngrok.com/login)。

1. 获取你的个人身份验证令牌，你的 **ngrok** 客户端需要连接到你的帐户并对其进行身份验证。

   1. 若要查找 " [身份验证令牌" 页](https://dashboard.ngrok.com/auth/your-authtoken)，请在帐户仪表板菜单上，展开 " **身份验证**"，然后选择 **Authtoken**。

   1. 从 " **Authtoken** " 框中，将令牌复制到一个安全位置。

1. 从 [ **ngrok** 下载页](https://ngrok.com/download)或 [你的帐户仪表板](https://dashboard.ngrok.com/get-started/setup)中，下载所需的 **ngrok** 版本，并提取 .zip 文件。 有关详细信息，请参阅 [步骤1：解压缩到安装](https://ngrok.com/download)。

1. 在计算机上，打开命令提示符工具。 浏览到 **ngrok.exe** 文件所在的位置。

1. 通过运行以下命令将 **ngrok** 客户端连接到 **ngrok** 帐户。 有关详细信息，请参阅 [步骤2：连接帐户](https://ngrok.com/download)。

   `ngrok authtoken <your_auth_token>`

1. 通过运行以下命令，打开到 localhost 端口7071的 HTTP 隧道。 有关详细信息，请参阅 [步骤3：启动它](https://ngrok.com/download)。

   `ngrok http 7071`

1. 从输出中找到以下行：

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. 复制并保存具有以下格式的 URL： `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>在应用设置中设置转发 URL

1. 在 Visual Studio Code 的项目根级别上，打开文件上的 **local.settings.js** 。

1. 在 `Values` 对象中，添加一个名为的属性 `Workflows.WebhookRedirectHostUri` ，并将值设置为之前创建的转发 URL，例如：

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

第一次在不调试的情况下启动本地调试会话或运行工作流时，逻辑应用运行时会将工作流注册到服务终结点，并订阅该终结点以通知 webhook 操作。 下次运行工作流时，运行时将不会注册或重新订阅，因为本地存储中已存在订阅注册。

当您停止使用本地运行的基于 webhook 的触发器或操作的工作流运行的调试会话时，不会删除现有的订阅注册。 若要注销，必须手动删除或删除订阅注册。

> [!NOTE]
> 工作流开始运行后，终端窗口可能会显示类似于以下示例的错误：
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> 在这种情况下，请在项目的根级别打开 **local.settings.js** 文件，并确保属性设置为 `true` ：
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>管理用于调试的断点

通过启动调试会话来运行和测试逻辑应用工作流之前，可以在每个工作流的 **workflow.js** 文件中设置 [断点](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)。 不需要其他设置。 

目前，只有操作而不是触发器支持断点。 每个操作定义都包含以下断点位置：

* 在显示操作名称的行上设置开始断点。 在调试会话过程中命中此断点时，你可以在计算操作之前查看其输入。

* 在显示操作的右大括号 (**}**) 的行上设置结束断点。 在调试会话过程中命中此断点时，可以在操作完成运行之前查看操作的结果。

若要添加断点，请执行以下步骤：

1. 打开要调试的工作流的 **workflow.js** 文件。

1. 在要设置断点的行上，在左栏中选择该列。 若要删除断点，请选择该断点。

   启动调试会话时，"运行" 视图将出现在代码窗口的左侧，而 "调试" 工具栏显示在顶部附近。

   > [!NOTE]
   > 如果 "运行" 视图未自动显示，请按 Ctrl + Shift + D。

1. 若要查看断点命中时可用的信息，请在 "运行" 视图中检查 " **变量** " 窗格。

1. 若要继续执行工作流，请在 "调试" 工具栏上，选择 " **继续** (播放" 按钮) "。 

可以在工作流运行期间随时添加和删除断点。 但是，如果在运行开始后更新文件 **上的workflow.js** ，则断点不会自动更新。 若要更新断点，请重新启动逻辑应用。

有关一般信息，请参阅 [断点-Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)。

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>在本地运行、测试和调试

若要测试逻辑应用，请按照以下步骤启动调试会话，并查找由请求触发器创建的终结点的 URL。 需要此 URL，以便以后可以向该终结点发送请求。

1. 若要更轻松地调试无状态工作流，可以 [启用该工作流的运行历史记录](#enable-run-history-stateless)。

1. 在 "Visual Studio Code" 活动栏上，打开 " **运行** " 菜单，然后选择 " **启动调试** " (F5) "。

   此时将打开 **终端** 窗口，以便您可以查看调试会话。

1. 现在，在请求触发器上查找终结点的回调 URL。

   1. 重新打开 "资源管理器" 窗格，以便可以查看项目。

   1. 从文件快捷菜单上的 " **workflow.js** " 中，选择 " **概述**"。

      ![显示 "概述" 选定的文件上的 "资源管理器" 窗格和快捷 workflow.js窗口的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. 查找 " **回调 URL** " 值，该 url 与示例请求触发器的 url 类似：

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![显示工作流的 "概述" 页的屏幕截图，其中包含回叫 URL](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. 若要通过触发逻辑应用工作流来测试回调 URL，请打开 [Postman](https://www.postman.com/downloads/) 或用于创建和发送请求的首选工具。

   此示例通过使用 Postman 继续进行。 有关详细信息，请参阅 [Postman 入门](https://learning.postman.com/docs/getting-started/introduction/)。

   1. 在 Postman 工具栏上，选择 " **新建**"。

      ![显示 Postman 并选择 "新建" 按钮的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. 在 " **新建** " 窗格的 " **构建基块**" 下，选择 " **请求**"。

   1. 在 " **保存请求** " 窗口中的 " **请求名称**" 下，提供请求的名称，例如 `Test workflow trigger` 。

   1. 在 " **选择要保存到的集合或文件夹**" 下，选择 " **创建集合**"。

   1. 在 "**所有集合**" 下，提供要创建的集合的名称以组织你的请求，按 enter，然后选择 "**保存到 <*集合名称* >**"。 此示例使用 `Logic Apps requests` 作为集合名称。

      此时将打开 Postman 的请求窗格，以便可以将请求发送到请求触发器的回调 URL。

      ![用打开的请求窗格显示 Postman 的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. 返回 Visual Studio Code。 在工作流的 "概述" 页中，复制 " **回调 URL** " 属性值。

   1. 返回到 Postman。 在 "请求" 窗格中，单击 "方法列表"， **当前显示为** 默认请求方法，粘贴之前在 "地址" 框中复制的回调 URL，然后选择 " **发送**"。

      ![屏幕截图，显示已选中 "发送" 按钮的 "地址" 框中的 Postman 和回叫 URL](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      示例逻辑应用工作流发送的电子邮件类似于以下示例：

      ![显示 Outlook 电子邮件的屏幕截图，如示例中所述](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. 在 Visual Studio Code 中，返回到工作流的 "概述" 页。

   如果创建了有状态的工作流，则在发送的请求触发工作流后，"概述" 页将显示工作流的运行状态和历史记录。

   > [!TIP]
   > 如果未显示运行状态，请尝试通过选择 " **刷新**" 来刷新 "概述" 页。 由于不符合条件或找不到任何数据而跳过的触发器不会运行。

   ![显示运行状态和历史记录的工作流的 "概述" 页的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | 运行状态 | 说明 |
   |------------|-------------|
   | **Aborted** | 由于外部问题（例如，系统中断或过期的 Azure 订阅），运行已停止或未完成。 |
   | 已取消 | 运行已触发并已启动，但收到了取消请求。 |
   | 失败 | 运行中的至少一个操作失败。 未设置工作流中的后续操作来处理失败。 |
   | **正在运行** | 运行已触发并正在进行中，但对于由于 [操作限制](logic-apps-limits-and-config.md) 或 [当前定价计划](https://azure.microsoft.com/pricing/details/logic-apps/)而受到限制的运行，也可能显示此状态。 <p><p>**提示**：如果设置 [诊断日志记录](monitor-logic-apps-log-analytics.md)，则可以获取发生的任何限制事件的相关信息。 |
   | 成功 | 运行成功。 如果任何操作失败，工作流中的后续操作会处理失败。 |
   | **已超时** | 运行超时，因为当前持续时间超过了运行持续时间限制，该限制由 " [**运行历史记录保持期（天**](logic-apps-limits-and-config.md#run-duration-retention-limits)）" 设置控制。 运行的持续时间是使用运行的开始时间和开始时间的运行持续时间限制来计算的。 <p><p>**注意**：如果此运行的持续时间还超出了当前 *运行历史记录保留限制*，而此限制也是由 " [**运行历史记录保持期（天**](logic-apps-limits-and-config.md#run-duration-retention-limits)）" 设置控制的，则每日清除作业将从运行历史记录中清除运行。 无论运行超时还是完成，始终都将使用运行的开始时间和 *当前* 保留限制来计算保持期。 因此，如果您缩短正在进行的运行的持续时间限制，则运行将超时。但是，运行会根据运行的持续时间是否超出保留限制，从运行历史记录中清除运行。 |
   | **正在等待** | 运行未启动或已暂停，例如，由于仍在运行的工作流实例较早而暂停。 |
   |||

1. 若要查看特定运行中每个步骤的状态和步骤的输入和输出，请选择该运行的 "省略号 (**...** ") 按钮，然后选择 " **显示运行**"。

   ![显示工作流的运行历史记录行并选中 "显示运行" 的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code 打开 "监视" 视图并显示运行中每个步骤的状态。

   ![显示工作流运行和状态的每个步骤的屏幕截图](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > 如果运行失败，并且 "监视" 视图中的步骤显示 `400 Bad Request` 错误，则此问题可能是由于较长的触发器名称或操作名称导致的，导致基础统一资源标识符 (URI) 超过默认字符限制。 有关详细信息，请参阅 ["400 请求错误"](#400-bad-request)。

   下面是工作流中每个步骤可以具有的可能状态：

   | 操作状态 | 图标 | 说明 |
   |---------------|------|-------------|
   | Aborted | !["中止" 操作状态的图标][aborted-icon] | 操作已停止或未完成，原因是外部问题，例如，系统中断或过期的 Azure 订阅。 |
   | 已取消 | !["已取消" 操作状态的图标][cancelled-icon] | 操作正在运行，但收到了取消请求。 |
   | 失败 | !["失败" 操作状态的图标][failed-icon] | 操作失败。 |
   | 正在运行 | !["正在运行" 操作状态的图标][running-icon] | 操作当前正在运行。 |
   | 已跳过 | !["跳过" 操作状态的图标][skipped-icon] | 此操作已被跳过，因为前一个操作失败。 操作具有要求在 `runAfter` 当前操作运行之前成功完成前面的操作的条件。 |
   | 成功 | !["成功" 操作状态的图标][succeeded-icon] | 操作成功。 |
   | 已成功重试 | !["已成功重试" 操作状态的图标][succeeded-with-retries-icon] | 操作成功，但仅在一个或多个重试后。 若要查看重试历史记录，请在 "运行历史记录详细信息" 视图中选择该操作，以便可以查看输入和输出。 |
   | 已超时 | !["超时" 操作状态的图标][timed-out-icon] | 由于操作的设置指定的超时限制，操作已停止。 |
   | 等待 | !["等待" 操作状态的图标][waiting-icon] | 适用于正在等待来自调用方的入站请求的 webhook 操作。 |
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

   ![屏幕截图，显示工作流中每个步骤的状态，以及展开的 "发送电子邮件" 操作中的输入和输出](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. 若要进一步查看该步骤的原始输入和输出，请选择 " **显示原始输入** " 或 " **显示原始输出**"。

1. 若要停止调试会话，请在 " **运行** " 菜单上，选择 " **停止调试** " (Shift + F5) 。

<a name="return-response"></a>

## <a name="return-a-response"></a>返回响应

若要向向逻辑应用发送请求的调用方返回响应，可以将内置 [响应操作](../connectors/connectors-native-reqres.md) 用于以请求触发器开头的工作流。

1. 在工作流设计器中的 " **发送电子邮件** " 操作下，选择 " **新建步骤**"。

   设计器上将显示 " **选择操作** " 提示，并重新打开 " **添加操作" 窗格** ，以便您可以选择下一个操作。

1. 在 " **添加操作** " 窗格中的 " **选择操作** " 搜索框下，确保已选中 " **内置** "。 在搜索框中，输入 `response` ，然后选择 " **响应** " 操作。

   ![显示工作流设计器并选择响应操作的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   当 **响应** 操作显示在设计器上时，操作的详细信息窗格将自动打开。

   ![显示工作流设计器的屏幕截图，其中打开了 "响应" 操作的详细信息窗格并将 "正文" 属性设置为 "发送电子邮件" 操作的 "正文" 属性值。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. 在 " **参数** " 选项卡上，提供要调用的函数所需的信息。

   此示例返回 "**发送电子邮件**" 操作的输出的 **正文** 属性值。

   1. 在 " **正文** " 属性框中单击，以便显示动态内容列表，并显示前面的触发器中的可用输出值以及工作流中的操作。

      ![屏幕截图显示 "响应" 操作的详细信息窗格，其中鼠标指针位于 "Body" 属性内，以便显示动态内容列表。](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. 在动态内容列表的 " **发送电子邮件**" 下，选择 " **正文**"。

      ![屏幕截图，显示打开的动态内容列表。 在列表中的 "发送电子邮件" 标头下，选择 "正文" 输出值。](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      完成后，"响应" 操作的 " **正文** " 属性将设置为 " **发送电子邮件** " 操作的 **正文** 输出值。

      ![屏幕截图，显示工作流中每个步骤的状态，以及展开的 "响应" 操作中的输入和输出。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. 在设计器中，选择 " **保存**"。

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>重新测试逻辑应用

在对逻辑应用进行更新后，可以通过在 Visual Studio 中重新运行调试器并发送另一个请求来触发已更新的逻辑应用（类似于 [本地运行、测试和调试](#run-test-debug-locally)中的步骤）来运行其他测试。

1. 在 "Visual Studio Code" 活动栏上，打开 " **运行** " 菜单，然后选择 " **启动调试** " (F5) "。

1. 在 Postman 或用于创建和发送请求的工具中，发送另一个请求来触发工作流。

1. 如果创建有状态工作流，则在工作流的 "概述" 页上，检查最近运行的状态。 若要查看该运行中每个步骤的状态、输入和输出，请选择该运行的 "省略号 (**...** ") 按钮，然后选择 " **显示运行**"。

   例如，下面是在使用响应操作更新示例工作流之后运行的分步状态。

   ![屏幕截图，显示更新的工作流中每个步骤的状态，以及展开的 "响应" 操作中的输入和输出。](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. 若要停止调试会话，请在 " **运行** " 菜单上，选择 " **停止调试** " (Shift + F5) 。

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>部署到 Azure

从 Visual Studio Code 中，可以直接将项目发布到 Azure，这会使用新的 **逻辑应用 (预览版)** 资源类型部署逻辑应用。 与 Azure Functions 中的函数应用资源类似，此新资源类型的部署要求你选择 [托管计划和定价层](../app-service/overview-hosting-plans.md)，你可以在部署期间进行设置。 有关托管计划和定价的详细信息，请参阅以下主题：

* [向上缩放 Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions 的缩放和托管](../azure-functions/functions-scale.md)

你可以将逻辑应用作为新资源发布，这将自动创建任何其他必要的资源，例如 [Azure 存储帐户，这与函数应用要求类似](../azure-functions/storage-considerations.md)。 或者，你可以将逻辑应用发布到以前部署的 **逻辑应用 (预览)** 资源，这将覆盖该逻辑应用。

### <a name="publish-to-a-new-logic-app-preview-resource"></a>发布到新的逻辑应用 (预览) 资源

1. 在 "Visual Studio Code" 活动栏上，选择 "Azure" 图标。

1. 在 **Azure：逻辑应用 (预览)** 窗格工具栏上，选择 " **部署到逻辑应用**"。

   ![屏幕截图，显示 "Azure：逻辑应用 (预览") "窗格和窗格工具栏，并选中" 部署到逻辑应用 "。](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. 从 Visual Studio Code 打开的列表中，从以下选项中进行选择：

   * **在 Azure 中创建新的逻辑应用 (预览)** (快速) 
   * **在 Azure Advanced 中创建新的逻辑应用 (预览)**
   * 以前部署的 **逻辑应用 (预览)** 资源（如果存在）

   此示例将继续 **创建新的逻辑应用 (预览版) Azure Advanced**。

   ![屏幕截图显示 "Azure：逻辑应用 (预览) " 窗格，其中包含一个列表，其中列出了 "在 Azure 中创建新的逻辑应用 (预览) "。](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. 若要创建新的 **逻辑应用 (预览)** 资源，请执行以下步骤：

   1. 为新的逻辑应用提供全局唯一的名称，此名称是 **逻辑应用 (预览)** 资源中使用的名称。 本示例使用 `Fabrikam-Workflows-App`。

      ![屏幕截图，显示 "Azure：逻辑应用 () 预览" 窗格，并提示提供要创建的新逻辑应用的名称。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. 为新逻辑应用选择托管计划，" [**应用服务计划**](../azure-functions/functions-scale.md#app-service-plan) " 或 " [**高级**](../azure-functions/functions-scale.md#premium-plan)"。 此示例选择 " **应用服务计划**"。

      ![屏幕截图显示 "Azure：逻辑应用 (预览") "窗格，并提示选择" 应用服务计划 "或" 高级 "。](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. 创建新的应用服务计划或选择现有计划。 此示例选择 " **创建新的应用服务计划**"。

      ![屏幕截图，显示 "Azure：逻辑应用 () 预览" 窗格，并提示 "创建新应用服务计划" 或选择现有的应用服务计划。](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. 为应用服务计划提供名称，然后为该计划选择一个 [定价层](../app-service/overview-hosting-plans.md) 。 此示例选择 **F1 免费** 计划。

      ![屏幕截图，显示 "Azure：逻辑应用 (预览") "窗格，并提示选择定价层。](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. 为了获得最佳性能，请查找并选择与部署项目相同的资源组。

      > [!NOTE]
      > 尽管可以创建或使用不同的资源组，但这样做可能会影响性能。 如果创建或选择其他资源组，但在出现确认提示后取消，则还会取消部署。

   1. 对于有状态工作流，选择 " **新建存储帐户** " 或 "现有存储帐户"。

      ![屏幕截图显示 "Azure：逻辑应用 (预览") "窗格，并提示创建或选择存储帐户。](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. 如果逻辑应用的创建和部署设置支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，则可以选择为逻辑应用启用诊断日志记录和跟踪。 在部署逻辑应用时，可以从 Visual Studio Code 或部署后执行此操作。 你需要有一个 Application Insights 实例，但你可以在部署逻辑应用时或部署后 [提前](../azure-monitor/app/create-workspace-resource.md)创建此资源。

      若要立即启用日志记录和跟踪，请执行以下步骤：

      1. 选择现有 Application Insights 资源或 **创建新 Application Insights 资源**。

      1. 在 [Azure 门户](htpps://portal.azure.com)中转到 Application Insights 资源。

      1. 在 "资源" 菜单中，选择 " **概述**"。 查找并复制 **检测密钥** 值。

      1. 在 Visual Studio Code 的项目根级别上，打开文件上的 **local.settings.js** 。

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
         > 您可以检查触发器和操作名称是否正确显示在您的 Application Insights 实例中。
         >
         > 1. 在 Azure 门户中转到 Application Insights 资源。
         >
         > 2. 在资源资源菜单的 " **调查**" 下，选择 " **应用程序映射**"。
         >
         > 3. 查看地图中显示的操作名称。
         >
         > 内置触发器的某些入站请求可能会在应用程序映射中显示为重复项。 
         > `WorkflowName.ActionName`这些重复项使用工作流名称作为操作名称 Azure Functions，而不是使用格式，而不是使用格式。

      1. 接下来，你可以根据需要调整逻辑应用收集并发送到 Application Insights 实例的跟踪数据的严重性级别。

         每次发生工作流相关事件时（例如，当触发工作流时，或者当操作运行时），运行时会发出各种跟踪。 这些跟踪涵盖工作流的生存期，包括但不限于以下事件类型：

         * 服务活动，如 "启动"、"停止" 和 "错误"。
         * 作业和调度程序活动。
         * 工作流活动，如触发器、操作和运行。
         * 存储请求活动，如成功或失败。
         * HTTP 请求活动，如入站、出站、成功和失败。
         * 即席开发跟踪，如调试消息。

         每个事件类型都分配给一个严重性级别。 例如， `Trace` 级别捕获最详细的消息，而 `Information` 级别捕获工作流中的常规活动，例如逻辑应用、工作流、触发器和操作的开始和停止时间。 下表描述了严重性级别及其跟踪类型：

         | 严重性级别 | 跟踪类型 |
         |----------------|------------|
         | 严重 | 描述逻辑应用中无法恢复的故障的日志。 |
         | 调试 | 可用于在开发期间进行调查的日志，例如，入站和出站 HTTP 调用。 |
         | 错误 | 指示在工作流执行过程中失败，但不会在逻辑应用中出现一般故障的日志。 |
         | 信息 | 跟踪逻辑应用或工作流中的常规活动的日志，例如： <p><p>-触发器、操作或运行开始和结束时。 <br>-逻辑应用启动或结束的时间。 |
         | 跟踪 | 包含最详细消息的日志，例如，存储请求或调度程序活动，以及与工作流执行活动相关的所有消息。 |
         | 警告 | 在逻辑应用中突出显示状态不正常的日志，但不会阻止其运行。 |
         |||

         若要设置严重性级别，请在项目的根级别，打开文件 **上的host.js** ，然后查找 `logging` 对象。 此对象控制逻辑应用中所有工作流的日志筛选，并遵循 [日志类型筛选的 ASP.NET Core 布局](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)。

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

         如果 `logging` 对象不包含 `logLevel` 包含属性的对象，则 `Host.Triggers.Workflow` 添加这些项。 将属性设置为所需跟踪类型的严重性级别，例如：

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

   完成部署步骤后，Visual Studio Code 开始创建和部署发布逻辑应用所需的资源。

1. 若要查看和监视部署过程，请在 " **视图** " 菜单上选择 " **输出**"。 从 "输出" 窗口的工具栏列表中，选择 " **Azure 逻辑应用**"。

   ![屏幕截图，显示 "输出" 窗口，其中包含在工具栏列表中选择的 "Azure 逻辑应用" 以及部署进度和状态。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Visual Studio Code 将逻辑应用部署到 Azure 后，将显示以下消息：

   ![显示部署到 Azure 的消息已成功完成的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   恭喜，现在逻辑应用在 Azure 中处于活动状态，并在默认情况下启用。

接下来，你可以了解如何执行这些任务：

* [向项目中添加一个空白工作流](#add-workflow-existing-project)。

* 使用[Azure 门户](#manage-deployed-apps-portal)[管理 Visual Studio Code 中的已部署逻辑应用](#manage-deployed-apps-vs-code)。

* [启用无状态工作流的运行历史记录](#enable-run-history-stateless)。

* [在已部署逻辑应用的 Azure 门户中启用监视视图](#enable-monitoring)。

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>向项目中添加空白工作流

逻辑应用项目中可以有多个工作流。 若要向项目中添加空白工作流，请执行以下步骤：

1. 在 "Visual Studio Code" 活动栏上，选择 "Azure" 图标。

1. 在 Azure 窗格中的 " **azure：逻辑应用 (预览")** 上，选择 "Azure 逻辑应用) 的 **创建工作流** (图标"。

1. 选择要添加、有 **状态** 或 **无状态** 的工作流类型。

1. 提供工作流的名称。

完成后，项目中会出现一个新的工作流文件夹，其中包含工作流定义的 **workflow.js** 文件。

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>在 Visual Studio Code 中管理已部署的逻辑应用

在 Visual Studio Code 中，可以查看 Azure 订阅中的所有已部署逻辑应用，无论它们是原始 **逻辑应用** 还是 **逻辑应用 (预览版)** 资源类型，然后选择可帮助你管理这些逻辑应用的任务。 但是，若要访问这两种资源类型，需要使用 **Azure 逻辑应用** 和 **azure 逻辑应用 (预览)** 扩展 Visual Studio Code。

1. 在左侧工具栏上，选择 "Azure" 图标。 在 " **Azure：逻辑应用 (预览")** 窗格中，展开订阅，其中显示了该订阅的所有已部署的逻辑应用。

1. 找到并选择要管理的逻辑应用。 打开逻辑应用的快捷菜单，并选择要执行的任务。

   例如，可以选择停止、启动、重新启动或删除已部署逻辑应用等任务。

   ![显示 Visual Studio Code，其中显示了已打开的 "Azure 逻辑应用 (预览版) " 扩展窗格和部署的工作流。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. 若要查看逻辑应用中的所有工作流，请展开逻辑应用，然后展开 " **工作流** " 节点。

1. 若要查看特定工作流，请打开工作流的快捷菜单，然后选择 " **在设计器中打开**" （在只读模式下打开工作流）。

   若要编辑工作流，可以使用以下选项：

   * 在 Visual Studio Code 中，在工作流设计器中打开项目的 **workflow.js** 文件，进行编辑，并将逻辑应用重新部署到 Azure。

   * 在 Azure 门户中，[找到并打开你的逻辑应用](#manage-deployed-apps-portal)。 查找、编辑和保存工作流。

1. 若要在 Azure 门户中打开已部署的逻辑应用，请打开逻辑应用的快捷菜单，然后选择 " **在门户中打开**"。

   Azure 门户在浏览器中打开，如果登录到 Visual Studio Code 并显示逻辑应用，则会自动登录到门户。

   ![显示 Visual Studio Code 中逻辑应用的 "Azure 门户" 页的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   你还可以单独登录到 Azure 门户，使用门户搜索框查找逻辑应用，然后从结果列表中选择逻辑应用。

   ![显示所选逻辑应用的搜索结果的 Azure 门户和搜索栏的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>在门户中管理已部署的逻辑应用

在 Azure 门户中，可以查看 Azure 订阅中的所有已部署逻辑应用，无论它们是原始 **逻辑应用** 资源类型还是 **逻辑应用 (预览版)** 资源类型。 目前，在 Azure 中，每种资源类型都作为单独的类别进行组织和管理。 若要查找逻辑 **应用 (预览)** 资源类型的逻辑应用，请执行以下步骤：

1. 在 Azure 门户搜索框中输入 `logic app preview` 。 出现 "结果" 列表时，请在 " **服务**" 下选择 " **逻辑应用 (预览")**。

   ![屏幕截图，显示带有 "逻辑应用预览" 搜索文本的 Azure 门户搜索框。](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. 在 " **逻辑应用 (预览")** 窗格中，查找并选择从 Visual Studio Code 部署的逻辑应用。

   ![显示在 Azure 中部署) 资源的 Azure 门户和逻辑应用 (预览的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure 门户打开所选逻辑应用的单个资源页。

   ![在 Azure 门户中显示逻辑应用工作流的资源页的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. 若要查看此逻辑应用的工作流，请在逻辑应用菜单上选择 " **工作流**"。

   " **工作流** " 窗格显示当前逻辑应用中的所有工作流。 此示例显示了你在 Visual Studio Code 中创建的工作流。

   ![屏幕截图，显示 "工作流" 窗格打开的 "逻辑应用 (预览) " 资源页和部署的工作流](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. 若要查看工作流，请在 " **工作** 流" 窗格中选择该工作流。

   "工作流" 窗格将打开，并显示可对该工作流执行的详细信息和任务。

   例如，若要查看工作流中的步骤，请选择 " **设计器**"。

   ![屏幕截图，显示所选工作流的 "概述" 窗格，而工作流菜单显示选定的 "设计器" 命令。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   工作流设计器将打开并显示你在 Visual Studio Code 中生成的工作流。 你现在可以在 Azure 门户中对此工作流进行更改。

   ![显示 Visual Studio Code 部署的工作流设计器和工作流的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>在门户中添加另一个工作流

通过 Azure 门户，您可以将空白工作流添加到 **逻辑应用中， (预览** 从 Visual Studio Code 部署) 资源并在 Azure 门户中生成这些工作流。

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择已部署的 **逻辑应用 (预览版)** 资源。

1. 在逻辑应用菜单上，选择 " **工作流**"。 在 " **工作流** " 窗格上，选择 " **添加**"。

   ![屏幕截图，显示所选逻辑应用的 "工作流" 窗格和工具栏，并选中 "添加" 命令。](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. 在 " **新建工作流** " 窗格中，提供工作流的名称。 选择有 **状态** 或 **无状态** **>** **创建**。

   在 Azure 部署了在 " **工作流** " 窗格中显示的新工作流后，请选择该工作流，以便可以管理和执行其他任务，例如打开设计器或代码视图。

   ![显示具有管理和查看选项的所选工作流的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   例如，打开新工作流的设计器将显示一个空白画布。 你现在可以在 Azure 门户中生成此工作流。

   ![显示工作流设计器和空白工作流的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>启用无状态工作流的运行历史记录

若要更轻松地调试无状态工作流，可以启用该工作流的运行历史记录，并在完成后禁用运行历史记录。 按照这些步骤 Visual Studio Code 操作，或者如果正在 Azure 门户中操作，请参阅在 [Azure 门户中创建有状态和无状态的工作流](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)。

1. 在 Visual Studio Code 项目中，展开 **designtime** 文件夹，然后打开文件 **上的local.settings.js** 。

1. 添加 `Workflows.{yourWorkflowName}.operationOptions` 属性，并将值设置为 `WithStatelessRunHistory` ，例如：

   **Windows 或 Linux**

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

   **macOS**

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

1. 若要在完成后禁用运行历史记录，请将 `Workflows.{yourWorkflowName}.OperationOptions` 属性设置为 `None` ，或者删除属性及其值。

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>在 Azure 门户中启用监视视图

部署 **逻辑应用后 (预览** 从 Visual Studio Code 到 Azure) 资源，你可以通过使用该工作流的 Azure 门户和 **监视** 体验来查看该资源中任何可用的运行历史记录和详细信息。 但是，必须先对该逻辑应用资源启用 " **监视** " 视图功能。

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择已部署的 **逻辑应用 (预览版)** 资源。

1. 在该资源的菜单中的 " **API**" 下，选择 " **CORS**"。

1. 在 " **CORS** " 窗格的 " **允许的来源**" 下，将通配符添加 ( * ) 。

1. 完成后，在 " **CORS** " 工具栏上，选择 " **保存**"。

   ![屏幕截图，显示已部署逻辑应用 (预览版) 资源的 Azure 门户。 在 "资源" 菜单中，选择 "CORS"，并将 "允许的来源" 的新条目设置为通配符 "*" 字符。](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>部署后启用或打开 Application Insights

在工作流执行过程中，逻辑应用会随其他事件一起发出遥测数据。 使用此遥测可以更好地了解工作流的运行情况以及逻辑应用运行时如何以各种方式工作。 你可以使用 Application Insights 来监视工作流，该[Application Insights](../azure-monitor/app/app-insights-overview.md)提供近实时遥测 (实时指标) 。 使用此数据诊断问题、设置警报和构建图表时，此功能可帮助您更轻松地调查故障和性能问题。

如果逻辑应用的创建和部署设置支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，则可以选择为逻辑应用启用诊断日志记录和跟踪。 在部署逻辑应用时，可以从 Visual Studio Code 或部署后执行此操作。 你需要有一个 Application Insights 实例，但你可以在部署逻辑应用时或部署后 [提前](../azure-monitor/app/create-workspace-resource.md)创建此资源。

若要在已部署的逻辑应用上启用 Application Insights 或在启用后查看 Application Insights 数据，请执行以下步骤：

1. 在 Azure 门户中，找到已部署的逻辑应用。

1. 在逻辑应用菜单上的 " **设置**" 下，选择 **Application Insights**。

1. 如果未启用 Application Insights，请在 " **Application Insights** " 窗格上，选择 " **启用 Application Insights**"。 在窗格更新后，单击底部的 " **应用**"。

   如果启用了 Application Insights，请在 " **Application Insights** " 窗格中选择 " **查看 Application Insights 数据**"。

Application Insights 打开后，可以查看逻辑应用的各种指标。 有关详细信息，请参阅在 [任意位置运行的 Azure 逻辑应用-显示器与 Application Insights-第1部分](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)。

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>部署到 Docker

可以使用[.NET CLI](/dotnet/core/tools/)将逻辑应用部署到[Docker 容器](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container)，作为宿主环境。 通过这些命令，可以生成并发布逻辑应用的项目。 然后，你可以生成并运行 Docker 容器作为逻辑应用的部署目标。

如果你不熟悉 Docker，请查看以下主题：

* [什么是 Docker？](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [容器和 Docker 简介](/dotnet/architecture/microservices/container-docker-introduction/)
* [.NET 和 Docker 简介](/dotnet/core/docker/introduction)
* [Docker 容器、映像和注册表](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [教程： Docker (Visual Studio Code) 入门 ](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>要求

* 逻辑应用用于部署的 Azure 存储帐户

* 构建 Docker 容器时使用的 .NET 工作流的 Docker 文件

   例如，此示例 Docker 文件使用有状态工作流部署逻辑应用。 此文件指定用于将逻辑应用发布到 Azure 门户的 Azure 存储帐户的连接字符串和访问密钥。

   ```text
   FROM mcr.microsoft.com/dotnet/core/sdk3.1 AS installer-env

   COPY . /src/dotnet-function-app
   RUN cd /src/dotnet-function-app && \
       mkdir -p /home/site/wwwroot && \
       dotnet publish *.csproj --output /home/site/wwwroot

   FROM mcr.microsoft.com/azure-functions/dotnet:3.0
   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
   ```

   有关详细信息，请参阅 [编写 Docker 文件的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

### <a name="build-and-publish-your-app"></a>生成并发布应用

1. 若要在本地生成逻辑应用的项目，请打开命令行提示符并运行以下命令：

   `dotnet build -c release`

   有关详细信息，请参阅 [dotnet 生成](/dotnet/core/tools/dotnet-build/) 引用页。

1. 通过运行以下命令将项目的生成发布到一个文件夹，以用于部署到宿主环境：

   `dotnet publish -c release`

   有关详细信息，请参阅 " [dotnet publish](/dotnet/core/tools/dotnet-publish/) 引用" 页。

### <a name="access-to-your-storage-account"></a>访问你的存储帐户

生成并运行 Docker 容器之前，需要获取连接字符串，其中包含存储帐户的访问密钥。

1. 在 Azure 门户的 "存储帐户" 菜单上的 " **设置**" 下，选择 " **访问密钥**"。 

   ![屏幕截图，显示具有存储帐户访问密钥和连接字符串的 Azure 门户。](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

1. 在 " **连接字符串**" 下，复制存储帐户的连接字符串。 连接字符串类似于以下示例：

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey={access-key};EndpointSuffix=core.windows.net`

   有关详细信息，请参阅 [管理存储帐户密钥](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)。

1. 将连接字符串保存在一个安全的位置。 在逻辑应用项目中，需要将此字符串同时添加到 **local.settings.js** 文件中。 还需要将此字符串添加到 Docker 文件。

### <a name="build-and-run-your-docker-container-image"></a>生成并运行 Docker 容器映像

1. 使用 Docker 文件构建 Docker 容器映像，并运行以下命令：

   `docker build --tag local/workflowcontainer .`

   有关详细信息，请参阅 [docker build](https://docs.docker.com/engine/reference/commandline/build/)。

1. 将字符串保存在一个安全的位置，以便稍后可以将该字符串添加到项目中的 **local.settings.js** 文件中，用于在 Visual Studio Code 中创建逻辑应用。

1. 使用以下命令在本地运行容器：

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   有关详细信息，请参阅 [docker run](https://docs.docker.com/engine/reference/commandline/run/)。

### <a name="get-callback-url-for-request-trigger"></a>获取请求触发器的回调 URL

若要获取请求触发器的回调 URL，请发送此请求：

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

在 Azure *master-key* 存储帐户（在中为文件中的 " `AzureWebJobsStorage` **azure web 作业-密码/{部署名称}/host.js**" 设置）中定义 <主密钥> 值，可在其中找到此部分中的值：

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

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>从设计器中删除项

若要从设计器中删除工作流中的项，请执行以下任一步骤：

* 选择项，打开该项的快捷菜单 (Shift + F10) ，然后选择 " **删除**"。 若要确认，请选择“确定”。

* 选择项，然后按 delete 键。 若要确认，请选择“确定”。

* 选择项，以便为该项打开详细信息窗格。 在窗格的右上角，打开 "省略号 (**...** ") 菜单，然后选择 " **删除**"。 若要确认，请选择“确定”。

  ![使用打开的详细信息窗格以及所选省略号按钮和 "删除" 命令在设计器上显示选定项的屏幕截图。](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > 如果省略号菜单不可见，请展开 Visual Studio Code 窗口宽度，以便详细信息窗格显示右上角的省略号 (**...) 。**

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>排查错误和问题

<a name="designer-fails-to-open"></a>

### <a name="opening-designer-fails-with-error-workflow-design-time-could-not-be-started"></a>打开设计器失败并出现错误： "无法启动工作流设计时间"

1. 在 Visual Studio Code 中，打开 "输出" 窗口。 从 " **视图** " 菜单中选择 " **输出**"。

1. 从 "输出" 窗口的标题栏的列表中，选择 " **Azure 逻辑应用 (预览")** 以便查看扩展的输出，例如：

   ![显示 "输出" 窗口的屏幕截图，其中已选择 "Azure 逻辑应用"。](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

1. 查看输出并检查是否显示此错误消息：

   ```text
   A host error has occurred during startup operation '{operationID}'.
   System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
   ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
   DurableTask.AzureStorage.dll' already exists.
   Value cannot be null. (Parameter 'provider')
   Application is shutting down...
   Initialization cancellation requested by runtime.
   Stopping host...
   Host shutdown completed.
   ```

   如果以前尝试打开设计器，然后停用或删除了项目，则会发生此错误。 若要解决此错误，请删除位于此位置的 **ExtensionBundles** 文件夹 **。 ..\Users \\ {用户名} \AppData\Local\Temp\Functions\ExtensionBundles**，然后重试在设计器中打开文件 **上的workflow.js** 。

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>对于以前创建的工作流，设计器选取器中缺少新的触发器和操作

Azure 逻辑应用预览支持对 Azure 函数操作、液体操作和 XML 操作（如 **Xml 验证** 和 **转换 xml**）进行内置操作。 但是，对于以前创建的逻辑应用，如果 Visual Studio Code 使用的是过期版本的扩展捆绑，则这些操作可能不会出现在设计器选取器中以供您选择 `Microsoft.Azure.Functions.ExtensionBundle.Workflows` 。

此外，除非你在创建逻辑应用时启用或选择了 "**使用 azure 中的连接器**"，否则 **azure 函数操作** 连接器和操作不会显示在设计器选取器中。 如果在应用创建时未启用 Azure 部署的连接器，则可以从 Visual Studio Code 中的项目启用它们。 在快捷菜单 **上打开workflow.js** ，并选择 " **使用 Azure 中的连接器**"。

若要修复过期的绑定，请按照以下步骤删除过期的捆绑包，这会使 Visual Studio Code 自动将扩展捆绑更新到最新版本。

> [!NOTE]
> 此解决方案仅适用于使用 Azure 逻辑应用 Visual Studio Code 创建和部署的逻辑应用， (预览版) 扩展，而不是使用 Azure 门户创建的逻辑应用。 请参阅 [Azure 门户中设计器缺少支持的触发器和操作](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions)。

1. 保存所有不希望丢失的工作，然后关闭 Visual Studio。

1. 在计算机上，浏览到以下文件夹，其中包含现有捆绑的版本控制文件夹：

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 删除早期包的版本文件夹，例如，如果有用于版本1.1.3 的文件夹，请删除该文件夹。

1. 现在，浏览到以下文件夹，其中包含所需 NuGet 包的版本控制文件夹：

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. 删除早期包的版本文件夹，例如，如果你有一个1.0.0.8 版本的文件夹，请删除该文件夹。

1. 在设计器中重新打开 Visual Studio Code、项目以及文件 **workflow.js** 。

缺少的触发器和操作现在将出现在设计器中。

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>触发器或操作中出现 "400 错误的请求"

当运行失败并且检查 "在监视视图中运行" 时，此错误可能出现在具有较长名称的触发器或操作上，这会导致基础统一资源标识符 (URI) 超过默认字符限制。

若要解决此问题并调整更长的 URI，请按照以下 `UrlSegmentMaxCount` `UrlSegmentMaxLength` 步骤编辑计算机上的和注册表项。 本主题中描述了这些密钥的默认值， [Http.sys 适用于 Windows 的注册表设置](/troubleshoot/iis/httpsys-registry-windows)。

> [!IMPORTANT]
> 在开始之前，请确保保存你的工作。 此解决方案要求你在完成后重启计算机，以便更改生效。

1. 在计算机上，打开 " **运行** " 窗口，并运行 `regedit` 命令，这将打开注册表编辑器。

1. 在 " **用户帐户控制** " 框中，选择 **"是"** 以允许对计算机进行更改。

1. 在左窗格中的 " **计算机**" 下，展开沿着路径 **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** 的节点，然后选择 " **参数**"。

1. 在右侧窗格中，找到 `UrlSegmentMaxCount` 和 `UrlSegmentMaxLength` 注册表项。

1. 增大这些键值，使 Uri 可以容纳您要使用的名称。 如果这些项不存在，请执行以下步骤，将它们添加到 **Parameters** 文件夹：

   1. 在 "**参数**" 快捷菜单中，选择 "**新建**  >  **DWORD (32 位) 值**"。

   1. 在出现的 "编辑" 框中，输入 `UrlSegmentMaxCount` 作为新的密钥名称。

   1. 打开新密钥的快捷菜单，然后选择 " **修改**"。

   1. 在出现的 " **编辑字符串** " 框中，以十六进制或十进制格式输入所需的 **值数据** 键值。 例如， `400` 在十六进制中等效于 `1024` decimal。

   1. 若要添加 `UrlSegmentMaxLength` 密钥值，请重复这些步骤。

   增加或添加这些键值后，注册表编辑器如以下示例所示：

   ![显示注册表编辑器的屏幕截图。](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. 准备就绪后，重新启动计算机，以使更改生效。

## <a name="next-steps"></a>后续步骤

我们想要收到有关 Azure 逻辑应用的体验 (预览版) 扩展！

* 对于 bug 或问题，请 [在 GitHub 中创建问题](https://github.com/Azure/logicapps/issues)。
* 对于问题、请求、评论和其他反馈，请 [使用此反馈表单](https://aka.ms/lafeedback)。