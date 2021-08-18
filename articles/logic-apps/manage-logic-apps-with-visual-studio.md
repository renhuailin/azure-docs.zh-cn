---
title: 通过将 Visual Studio 与 Cloud Explorer 配合使用来编辑和管理逻辑应用
description: 通过将 Visual Studio 与 Cloud Explorer 配合使用来编辑、更新、管理和部署逻辑应用以及将其添加到源代码管理
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.custom: mvc
ms.date: 04/23/2021
ms.openlocfilehash: 14b3487a158062e3186b485a8bcbd4584a0f09d2
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113665342"
---
# <a name="manage-logic-apps-with-visual-studio"></a>使用 Visual Studio 管理逻辑应用

尽管可以在 [Azure 门户](https://portal.azure.com)中创建、编辑、管理和部署逻辑应用，但在需要将逻辑应用添加到源代码管理、发布不同的版本，以及为各种部署环境创建 [Azure 资源管理器](../azure-resource-manager/management/overview.md)模板时，也可以使用 Visual Studio。 使用 Visual Studio Cloud Explorer 可以查找和管理逻辑应用以及其他 Azure 资源。 例如，可以打开、下载、编辑、运行、禁用和启用已在 Azure 门户中部署的逻辑应用，以及查看其运行历史记录。 如果你还不太熟悉如何在 Visual Studio 中处理 Azure 逻辑应用，请参阅[如何使用 Visual Studio 创建逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

还可以[在 Azure 门户中管理逻辑应用](manage-logic-apps-with-azure-portal.md)。

> [!IMPORTANT]
> 从 Visual Studio 部署或发布逻辑应用会覆盖该应用在 Azure 门户中的版本。 因此，若要在 Azure 门户进行更改并保存这些更改，请务必通过 Azure 门户[刷新 Visual Studio 中的逻辑应用](#refresh)，然后，便可以通过 Visual Studio 部署或发布该应用。

<a name="requirements"></a>

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 下载并安装以下工具（如果没有）：

  * [Visual Studio 2019、2017 或 2015 - Community Edition 或更高版本](https://aka.ms/download-visual-studio)。 本快速入门使用免费的 Visual Studio Community 2017。

    > [!IMPORTANT]
    > 安装 Visual Studio 2019 或 2017 时，请务必选择“Azure 开发”工作负荷。
    > 有关详细信息，请参阅[在 Visual Studio Cloud Explorer 中管理与 Azure 帐户关联的资源](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer)。

    若要安装 Cloud Explorer for Visual Studio 2015，请[从 Visual Studio Marketplace 下载 Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)。 有关详细信息，请参阅[在 Visual Studio Cloud Explorer (2015) 中管理与 Azure 帐户关联的资源](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015&preserve-view=true)。

  * [Azure SDK（2.9.1 或更高版本）](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * 适用于所需版本的 Visual Studio 扩展的最新 Azure 逻辑应用工具：

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    可以直接从 Visual Studio Marketplace 下载并安装 Azure 逻辑应用工具，或了解[如何从 Visual Studio 内部安装此扩展](/visualstudio/ide/finding-and-using-visual-studio-extensions)。 完成安装后，请务必重启 Visual Studio。

  * 若要将 Azure 政府订阅与 Visual Studio 结合使用，请参阅以下主题以进行其他设置：

    * Visual Studio 2019：[快速入门：使用 Visual Studio 连接到 Azure 政府](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017：[介绍 Azure 环境选择器 Visual Studio 扩展](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/)，你可以从 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector) 下载并安装该扩展。

* 使用嵌入式逻辑应用设计器时访问 Web

  设计器需要通过 Internet 连接在 Azure 中创建资源，以及从逻辑应用中的连接器读取属性和数据。

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>查找逻辑应用

在 Visual Studio 中，可以使用 Cloud Explorer 来查找与 Azure 订阅关联的，以及在 Azure 门户中部署的所有逻辑应用。

1. 打开 Visual Studio。 在“视图”菜单中选择“Cloud Explorer”。

1. 在 Cloud Explorer 中选择“帐户管理”图标。 选择与逻辑应用关联的 Azure 订阅，然后选择“应用”。 例如：

   ![选择“帐户管理”](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. 选择“帐户管理”图标旁的“资源类型”。  在 Azure 订阅下，展开“逻辑应用”，以便查看与你的订阅关联的所有已部署逻辑应用。

接下来，在逻辑应用编辑器中打开逻辑应用。

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>在 Visual Studio 中打开逻辑应用

在 Visual Studio 中，可以打开以前直接通过 Azure 门户或者使用 Visual Studio 作为 Azure 资源组项目创建和部署的逻辑应用。

1. [打开 Cloud Explorer 并找到自己的逻辑应用](#find-logic-apps-vs)。

1. 在逻辑应用的快捷菜单中，选择“使用逻辑应用编辑器打开”。

   > [!TIP]
   > 如果你的 Visual Studio 2019 中没有此命令，请检查是否安装了 Visual Studio 的最新更新。

   ![从 Azure 门户打开部署的逻辑应用](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   逻辑应用在逻辑应用设计器中打开后，可以在设计器的底部选择“代码视图”，以便查看逻辑应用的基础定义结构。 若要为逻辑应用创建部署模板，请了解如何为该逻辑应用[下载 Azure 资源管理器模板](#download-logic-app)。 详细了解[资源管理器模板](../azure-resource-manager/templates/overview.md)。

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>从 Azure 下载

可以从 [Azure 门户](https://portal.azure.com)[下载](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource)逻辑应用，并将其保存为 [Azure 资源管理器](../azure-resource-manager/management/overview.md)模板。 然后，可以在本地使用 Visual Studio 编辑模板，并根据不同的部署环境自定义逻辑应用。  下载逻辑应用会在 [资源管理器模板](../azure-resource-manager/templates/overview.md)中将这些逻辑应用的定义（也使用 JavaScript 对象表示法 (JSON)）*参数化*。

1. 在 Visual Studio 中，使用 Cloud Explorer，[打开要从 Azure 下载的逻辑应用](#open-designer)。

1. 在逻辑应用的快捷菜单中，选择“使用逻辑应用编辑器打开”。

   > [!TIP]
   > 如果你的 Visual Studio 2019 中没有此命令，请检查是否安装了 Visual Studio 的最新更新。

   逻辑应用在逻辑应用设计器中打开。

1. 在设计器工具栏上选择“下载”。

   ![从 Azure 门户下载逻辑应用](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. 当系统提示输入位置时，请浏览到该位置，并在资源管理器模板中以 JSON (.json) 文件格式保存逻辑应用的定义。

   逻辑应用定义显示在资源管理器模板中的 `resources` 子节内。 现在，可以使用 Visual Studio 来编辑逻辑应用定义和资源管理器模板。 还可以将模板作为 [Azure 资源组项目](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)添加到 Visual Studio 解决方案中。 了解 [Visual Studio 中逻辑应用的 Azure 资源组项目](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>关联到集成帐户

若要针对企业到企业 (B2B) 企业集成方案构建逻辑应用，可以将逻辑应用关联到以前创建的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，该帐户与逻辑应用存在于同一区域。 集成帐户包含 B2B 项目（例如贸易合作伙伴、协议、架构和映射），允许逻辑应用使用 B2B 连接器进行 XML 验证和平面文件编码或解码。 虽然可以[使用 Azure 门户创建此关联](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)，但也可以在满足[先决条件](#requirements)后使用 Visual Studio，而逻辑应用则以 JSON (.json) 文件形式存在于 [Azure 资源组项目](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)中。 了解 [Visual Studio 中逻辑应用的 Azure 资源组项目](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)。

1. 在 Visual Studio 中，打开逻辑应用所在的 Azure 资源组项目。

1. 在解决方案资源管理器中打开 **<logic-app-name>.json** 文件的快捷菜单，然后选择“使用逻辑应用设计器打开”。 （键盘：Ctrl + L）

   ![使用逻辑应用设计器打开逻辑应用的 .json 文件](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > 如果 Visual Studio 2019 中没有此命令，请检查是否安装了 Visual Studio 和 Azure 逻辑应用工具扩展的最新更新。

1. 确保逻辑应用设计器有焦点，方法是：选择设计器的选项卡或图面，使“属性”窗口显示逻辑应用的“集成帐户”属性。

   ![“属性”窗口 -“集成帐户”属性](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > 如果尚未打开“属性”窗口，请选择“视图”菜单中的“属性窗口”。 （键盘：按 F4）

1. 打开“集成帐户”属性列表，选择要关联到逻辑应用的集成帐户，例如：

   ![打开“集成帐户”属性列表](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. 完成后，请记得保存 Visual Studio 解决方案。

在 Visual Studio 中设置“集成帐户”属性并将逻辑应用另存为 Azure 资源管理器模板时，该模板也包含一个针对所选集成帐户的参数声明。 有关模板参数和逻辑应用的详细信息，请参阅[概述：自动完成逻辑应用部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)。

<a name="change-location"></a>

## <a name="change-deployment-location"></a>更改部署位置

在 Visual Studio 中，如果逻辑应用在用于自动执行部署的 [Azure 资源组项目](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)中作为 JSON (.json) 文件存在，则会为该逻辑应用设置一个位置类型和一个具体位置。 此位置可以是 Azure 区域，也可以是现有的[集成服务环境 (ISE)](connect-virtual-network-vnet-isolated-environment.md)。

若要更改逻辑应用的位置类型或位置，必须使用逻辑应用设计器从解决方案资源管理器中打开逻辑应用的工作流定义 (.json) 文件。 不能使用 Cloud Explorer 更改这些属性。

> [!IMPORTANT]
> 将位置类型从“区域”更改为[集成服务环境](connect-virtual-network-vnet-isolated-environment-overview.md)会影响逻辑应用的[定价模型](logic-apps-pricing.md#ise-pricing)，此模型用于计费、[限制](logic-apps-limits-and-config.md#integration-account-limits)、[集成帐户支持](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)等。 在选择其他位置类型之前，请确保理解对逻辑应用产生的影响。

1. 在 Visual Studio 中，打开逻辑应用所在的 Azure 资源组项目。

1. 在解决方案资源管理器中打开 `<logic-app-name>.json` 文件的快捷菜单，然后选择“使用逻辑应用设计器打开”。 （键盘：Ctrl + L）

   ![使用逻辑应用设计器打开逻辑应用的 .json 文件](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > 如果 Visual Studio 2019 中没有此命令，请检查是否安装了 Visual Studio 和 Azure 逻辑应用工具扩展的最新更新。

1. 确保逻辑应用设计器有焦点，方法是：选择设计器的选项卡或图面，使“属性”窗口显示逻辑应用的“选择位置类型”和“位置”属性。  项目的位置类型设置为“区域”或“集成服务环境”。

   ![属性窗口 -“选择位置类型”和“位置”属性](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > 如果尚未打开“属性”窗口，请选择“视图”菜单中的“属性窗口”。 （键盘：按 F4）

1. 若要更改位置类型，请打开“选择位置类型”属性列表，然后选择所需的位置类型。

   例如，如果位置类型为“集成服务环境”，则可以选择“区域”。

   ![“选择位置类型”属性 - 更改位置类型](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. 若要更改具体位置，请打开“位置”属性列表。 根据位置类型选择所需的位置，例如：

   * 选择其他 Azure 区域：

     ![打开“位置”属性列表，选择另一个 Azure 区域](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * 选择其他 ISE：

     ![打开“位置”属性列表，选择另一个 ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. 完成后，请记得保存 Visual Studio 解决方案。

在 Visual Studio 中更改位置类型或位置并将逻辑应用另存为 Azure 资源管理器模板时，该模板还会包含针对该位置类型和位置的参数声明。 有关模板参数和逻辑应用的详细信息，请参阅[概述：自动完成逻辑应用部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)。

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>从 Azure 刷新

如果在 Azure 门户中编辑了逻辑应用并想要保存这些更改，请务必在 Visual Studio 中使用这些更改刷新该应用的版本。

* 在 Visual Studio 中的逻辑应用设计器工具栏上，选择“刷新”。

  -或-

* 在 Visual Studio Cloud Explorer 中打开逻辑应用的快捷菜单，选择“刷新”。

![使用更新内容刷新逻辑应用](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>发布逻辑应用更新

准备好将逻辑应用更新从 Visual Studio 部署到 Azure 时，请在逻辑应用设计器工具栏上选择“发布”。

![将更新的逻辑应用发布到 Azure 门户](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>手动运行逻辑应用

可以通过 Visual Studio 手动触发 Azure 中部署的逻辑应用。 在逻辑应用设计器工具栏上，选择“运行触发器”。

![为逻辑应用手动运行触发器](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>查看运行历史记录

若要检查逻辑应用运行的状态和诊断问题，可以在 Visual Studio 中查看这些运行的详细信息，例如输入和输出。

1. 在 Cloud Explorer 中打开逻辑应用的快捷菜单，选择“打开运行历史记录”。

   ![打开逻辑应用的运行历史记录](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. 若要查看特定运行的详细信息，请双击该运行。 例如：

   ![查看有关特定运行的信息](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > 若要按属性将表排序，请选择该属性的列标题。

1. 展开要查看其输入和输出的步骤，例如：

   ![查看每个步骤的输入和输出](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

<a name="disable-enable-logic-apps"></a>

## <a name="disable-or-enable-logic-apps"></a>禁用或启用逻辑应用

若要在下一次满足触发条件时阻止触发器激发，请禁用逻辑应用。 禁用逻辑应用会以下列方式影响工作流实例：

* 逻辑应用服务将继续所有正在进行和挂起的运行，直到它们完成。 根据卷或积压工作 (backlog)，此过程可能需要一些时间才能完成。

* 逻辑应用服务不会创建或运行新的工作流实例。

* 下一次满足触发器的条件时，触发器不会触发。

* 触发器状态会记住逻辑应用的停止位置。 因此，如果重新激活逻辑应用，触发器将会针对自上次运行以来未处理的所有项触发。

  若要阻止触发器针对自上次运行以来未处理的项触发，请在重新激活逻辑应用之前清除触发器的状态：

  1. 在逻辑应用中，编辑工作流触发器的任何部分。
  1. 保存所做更改。 此步骤会重置触发器的当前状态。
  1. [重新激活逻辑应用](#enable-logic-apps)。

<a name="disable-logic-apps"></a>

### <a name="disable-logic-apps"></a>禁用逻辑应用

在 Cloud Explorer 中打开逻辑应用的快捷菜单，选择“禁用”。

![在 Cloud Explorer 中禁用逻辑应用](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

<a name="enable-logic-apps"></a>

### <a name="enable-logic-apps"></a>启用逻辑应用

在 Cloud Explorer 中打开逻辑应用的快捷菜单，选择“启用”。

![在 Cloud Explorer 中启用逻辑应用](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

<a name="delete-logic-apps"></a>

## <a name="delete-logic-apps"></a>删除逻辑应用

删除逻辑应用会在以下几方面影响工作流实例：

* 逻辑应用服务会可能取消任何正在进行和挂起的运行。

  即使使用较大的卷或积压工作 (backlog)，大多数运行在完成或开始之前都将被取消。 但是，取消过程可能需要一些时间才能完成。 同时，在运行时执行取消过程中，可能会选取某些运行来执行。

* 逻辑应用服务不会创建或运行新的工作流实例。

* 如果删除工作流，然后重新创建相同的工作流，则重新创建的工作流不会具有与删除的工作流相同的元数据。 必须重新保存任何调用删除工作流的工作流。 这样，调用方就可获取重新创建的工作流的正确信息。 否则，对重新创建的工作流的调用将失败并显示 `Unauthorized` 错误。 此行为也适用于在集成帐户中使用项目的工作流和调用 Azure 函数的工作流。

若要从 Azure 门户删除逻辑应用，请在 Cloud Explorer 中打开逻辑应用的快捷菜单，并选择“删除”。

![从 Azure 门户删除逻辑应用](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

## <a name="troubleshooting"></a>故障排除

在逻辑应用设计器中打开逻辑应用项目时，可能无法获取用于选择 Azure 订阅的选项。 而在逻辑应用打开时，Azure 订阅并非所要使用的订阅。 之所以发生此行为，原因在于当你打开逻辑应用的 .json 文件后，Visual Studio 将缓存首次选择的订阅，以供将来使用。 若要解决该问题，请尝试执行以下步骤之一：

* 重命名逻辑应用的 .json 文件。 订阅缓存依赖于该文件名。

* 若要删除解决方案中以前针对所有逻辑应用选择的订阅，请删除该解决方案目录中隐藏的 Visual Studio 设置文件夹 (.vs)。 此位置用于存储订阅信息。

## <a name="next-steps"></a>后续步骤

本文已介绍如何使用 Visual Studio 管理部署的逻辑应用。 接下来，请了解如何自定义部署的逻辑应用定义：

> [!div class="nextstepaction"]
> [以 JSON 编写逻辑应用定义](../logic-apps/logic-apps-author-definitions.md)
