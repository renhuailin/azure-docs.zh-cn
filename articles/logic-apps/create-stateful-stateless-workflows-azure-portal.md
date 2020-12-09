---
title: 在 Azure 门户中创建逻辑应用预览工作流
description: 在 Azure 门户中通过 Azure 逻辑应用预览，生成并运行自动化和集成方案的工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: d10689937a037469399863395e0190e399334bd3
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96924198"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>通过 Azure 逻辑应用预览在 Azure 门户中创建有状态和无状态工作流

> [!IMPORTANT]
> 此功能现为公共预览版，在提供时不附带服务级别协议，建议不要用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

通过 [Azure 逻辑应用预览](logic-apps-overview-preview.md)，你可以通过在 Azure 门户中创建和运行逻辑应用，其中包含有 [*状态* 和 *无状态* 的工作流](logic-apps-overview-preview.md#stateful-stateless) ，从而在应用、数据、云服务和系统中生成自动化和集成解决方案，) 资源类型开始， **(预览版** 。 利用这一新的逻辑应用类型，你可以构建由重新设计的 Azure 逻辑应用预览运行时提供支持的多个工作流，该运行时提供可移植性、更好的性能和灵活性，可用于在各种宿主环境中进行部署和运行，而不仅限于 Azure，还支持 Docker 容器。 若要了解有关新的逻辑应用类型的详细信息，请参阅 [Azure 逻辑应用的概述预览](logic-apps-overview-preview.md)。

![显示 Azure 门户，其中显示了 "逻辑应用 (预览) " 资源的工作流设计器。](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

在 Azure 门户中，可以通过 **(预览版)** 资源创建新的逻辑应用开始。 你还可以从 [使用 Azure 逻辑应用 Visual Studio Code 创建项目开始， (预览版) 扩展](create-stateful-stateless-workflows-visual-studio-code.md)，这两种方法都可让你在同一种类的宿主环境中部署和运行逻辑应用。

同时，你仍可创建原始逻辑应用类型。 尽管门户中的开发体验在原始和新的逻辑应用类型之间有所不同，但 Azure 订阅可以同时包含这两种类型。 可以在 Azure 订阅中查看和访问所有已部署的逻辑应用，但这些应用会组织到它们自己的类别和分区中。

本文介绍如何使用 **逻辑应用 (预览)** 资源类型和执行以下高级任务，在 Azure 门户中构建逻辑应用和工作流：

* 创建新的逻辑应用资源并添加一个空白工作流。

* 添加触发器和操作。

* 触发工作流运行。

* 查看工作流的运行历史记录。

* 部署后启用或打开 Application Insights。

* 启用无状态工作流的运行历史记录。

> [!NOTE]
> 有关当前已知问题的信息，请查看 [GitHub 中的逻辑应用公共预览版已知问题页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个 [Azure 存储帐户](../storage/common/storage-account-overview.md) ，因为 **逻辑应用 (预览)** 资源由 Azure Functions 提供支持并且具有 [与函数应用类似的存储要求](../azure-functions/storage-considerations.md)。 可以使用现有存储帐户，也可以在创建逻辑应用过程中提前或创建存储帐户。

  > [!NOTE]
  > 有[状态逻辑应用](logic-apps-overview-preview.md#stateful-stateless)执行存储事务，如使用队列在表和 blob 中计划和存储工作流状态。 这些事务会产生 [Azure 存储费用](https://azure.microsoft.com/pricing/details/storage/)。 有关有状态逻辑应用如何将数据存储在外部存储中的详细信息，请参阅有 [状态和无状态](logic-apps-overview-preview.md#stateful-stateless)。

* 若要在本文中生成相同的示例逻辑应用，需要使用 Microsoft 工作或学校帐户登录的 Office 365 Outlook 电子邮件帐户。

  如果你选择使用 [Azure 逻辑应用支持的其他电子邮件连接器](/connectors/)（如 Outlook.com 或 [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)），仍可以按照示例操作，一般的整体步骤相同，但你的用户界面和选项在某些方面可能会有所不同。 例如，如果你使用 Outlook.com 连接器，则使用你的个人 Microsoft 帐户改为登录。

* 若要测试在本文中创建的示例逻辑应用，需要一个可将调用发送到请求触发器的工具，这是示例逻辑应用的第一个步骤。 如果没有此类工具，可以下载、安装和使用 [Postman](https://www.postman.com/downloads/)。

* 如果使用支持 [Application Insights](../azure-monitor/app/app-insights-overview.md)的设置创建逻辑应用，则可以选择为逻辑应用启用诊断日志记录和跟踪。 你可以在创建逻辑应用时或部署后执行此操作。 你需要有一个 Application Insights 实例，但你可以在创建逻辑应用时或部署后 [提前](../azure-monitor/app/create-workspace-resource.md)创建此资源。

## <a name="create-the-logic-app-resource"></a>创建逻辑应用资源

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 "Azure 门户搜索框中输入 `logic app preview` ，然后选择" **逻辑应用 " (预览")**"。

   ![屏幕截图，其中显示了 "逻辑应用预览" 搜索术语和 "逻辑应用 (预览) " 资源的 Azure 门户搜索框。](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. 在 **逻辑应用 (预览 ")** " 页上，选择 " **添加**"。

1. 在 " **创建逻辑应用 (预览")** "页上的" **基本** 信息 "选项卡上，提供有关逻辑应用的此信息。

   | 属性 | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **订阅** | 是 | <*Azure-subscription-name*> | 要用于逻辑应用的 Azure 订阅。 |
   | **资源组** | 是 | <*Azure-resource-group-name*> | 在其中创建逻辑应用和相关资源的 Azure 资源组。 此资源名称在区域中必须唯一，并且只能包含字母、数字、连字符 (**-**) 、下划线 (**_**) 、括号 (**( # B6**) 和 **()** 。 <p><p>此示例将创建一个名为的资源组 `Fabrikam-Workflows-RG` 。 |
   | **逻辑应用名称** | 是 | <*logic-app-name*> | 用于逻辑应用的名称。 此资源名称在区域中必须唯一，并且只能包含字母、数字、连字符 (**-**) 、下划线 (**_**) 、括号 (**( # B6**) 和 **()** 。 <p><p>此示例创建一个名为的逻辑应用 `Fabrikam-Workflows` 。 <p><p>**注意**：逻辑应用的名称会自动获得后缀， `.azurewebsites.net` 因为 **逻辑应用 (预览)** 资源由 Azure Functions 提供支持（使用相同的应用命名约定）。 |
   | **发布** | 是 | <*部署-环境*> | 逻辑应用的部署目标。 可以通过选择 " **工作流** " 或 Docker 容器部署到 Azure。 <p><p>此示例使用 **工作流，该工作流** 是在 Azure 中 **(预览) 资源的逻辑应用** 。 <p><p>如果选择 " **Docker 容器**"，请 [指定要在逻辑应用的设置中使用的容器](#set-docker-container)。 |
   | **区域** | 是 | <*Azure-region*> | 创建资源组和资源时要使用的 Azure 区域。 <p><p>此示例使用“美国西部”。 |
   |||||

   下面是一个示例：

   ![显示 "Azure 门户" 和 "创建逻辑应用 (预览) " 页的屏幕截图。](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. 接下来，在 " **托管** " 选项卡上，提供有关要用于逻辑应用的存储解决方案和托管计划的信息。

   | 属性 | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **存储帐户** | 是 | <*Azure-storage-account-name*> | 用于存储事务的 [Azure 存储帐户](../storage/common/storage-account-overview.md) 。 此资源名称在不同区域中必须唯一，并且包含3-24 个字符且仅包含数字和小写字母。 选择现有帐户或创建新帐户。 <p><p>此示例将创建一个名为的存储帐户 `fabrikamstorageacct` 。 |
   | **计划类型** | 是 | <*Azure 托管-计划*> | 用于部署逻辑应用的 [托管计划](../app-service/overview-hosting-plans.md) ，该计划为 [**高级**](../azure-functions/functions-scale.md#premium-plan) 或 [**应用服务计划**](../azure-functions/functions-scale.md#app-service-plan)。 你的选择会影响你稍后可以选择的定价层。 <p><p>此示例使用 **应用服务计划**。 <p><p>**注意**：与 Azure Functions 类似， **逻辑应用 (预览版)** 资源类型需要托管计划和定价层。 此资源类型不支持和使用托管计划。 有关详细信息，请查看以下主题： <p><p>- [Azure Functions 缩放和托管](../azure-functions/functions-scale.md) <br>- [应用服务定价详细信息](https://azure.microsoft.com/pricing/details/app-service/) <p><p> |
   | **Windows 计划** | 是 | <*计划名称*> | 要使用的计划名称。 选择现有计划或提供新计划的名称。 <p><p>此示例使用名称 `Fabrikam-Service-Plan`。 |
   | **SKU 和大小** | 是 | <*定价层*> | 用于托管逻辑应用的 [定价层](../app-service/overview-hosting-plans.md) 。 你的选择会受到你之前选择的计划类型的影响。 若要更改默认层，请选择 " **更改大小**"。 然后，你可以根据所需的工作负荷选择其他定价层。 <p><p>此示例使用 **适用于开发/测试** 工作负荷的免费 **F1 定价层**。 有关详细信息，请参阅 [应用服务定价详细信息](https://azure.microsoft.com/pricing/details/app-service/)。 |
   |||||

1. 接下来，如果你的创建和部署设置支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，则可以选择为逻辑应用启用诊断日志记录和跟踪。

   1. 在 " **监视** " 选项卡的 " **Application Insights** 下，将" **启用 Application Insights** "设置为 **" 是 "** （如果尚未选择）。

   1. 对于 " **Application Insights** " 设置，请选择现有 Application Insights 实例，如果要创建新的实例，请选择 **"新建" 并提供** 要使用的名称。

1. Azure 验证逻辑应用设置后，请在 " **查看 + 创建** " 选项卡上选择 " **创建**"。

   例如：

   ![显示 Azure 门户和新的逻辑应用资源设置的屏幕截图。](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   完成部署后，逻辑应用将自动处于活动状态，但不会执行任何操作，因为不存在工作流。

1. 在 "部署完成" 页上，选择 " **中转到资源** "，以便可以开始生成工作流。

   ![显示 Azure 门户和完成的部署的屏幕截图。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>指定 Docker 容器以进行部署

如果在创建逻辑应用时选择了 **Docker 容器** ，请确保在 Azure 门户创建 **逻辑应用 (预览版)** 资源后，提供要用于部署的容器的相关信息。

1. 在 Azure 门户中，请参阅逻辑应用资源。

1. 在逻辑应用菜单上的 " **设置**" 下，选择 " **容器设置**"。 提供 Docker 容器映像的详细信息和位置。

   ![显示逻辑应用菜单的屏幕截图，其中选择了 "容器设置"。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-deploy-container-settings.png)

1. 完成后，保存设置。

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>添加空白工作流

1. Azure 打开资源后，请在逻辑应用的菜单中选择 " **工作流**"。 在 **工作流** 工具栏上，选择 " **添加**"。

   ![显示逻辑应用资源菜单的屏幕截图，其中选择了 "工作流"，然后在工具栏上 h) "添加"。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. **新工作流** 窗格打开后，提供工作流的名称，并选择有 [**状态** 或 **无状态**](logic-apps-overview-preview.md#stateful-stateless)的工作流类型。 完成操作后，选择“创建”  。

   此示例将添加一个名为的空白有状态工作流 `Fabrikam-Stateful-Workflow` 。 默认情况下，工作流处于启用状态，但在添加触发器和操作之前不会执行任何操作。

   ![屏幕截图，显示新添加的空白状态工作流 "Fabrikam-有状态工作流"。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. 接下来，在设计器中打开空白工作流，以便可以添加触发器和操作。

   1. 从 "工作流" 列表中，选择空白工作流。

   1. 在 "工作流" 菜单的 " **开发人员**" 下，选择 " **设计器**"。

      在设计器图面上， **选择操作** 提示已经显示并且默认情况下处于选中状态，以便 " **添加触发器** " 窗格也显示为打开状态。

      ![显示已打开的工作流设计器的屏幕截图，其中选择了设计器图面上的 "选择一个操作"。](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>添加触发器和操作

此示例将生成一个包含以下步骤的工作流：

* 内置 [请求触发器](../connectors/connectors-native-reqres.md)： **收到 HTTP 请求时**，接收入站调用或请求，并创建其他服务或逻辑应用可调用的终结点。

* [Office 365 Outlook 操作](../connectors/connectors-create-api-office365-outlook.md)**发送电子邮件**。

* 内置 [响应操作](../connectors/connectors-native-reqres.md)，用于发送答复并将数据返回给调用方。

### <a name="add-the-request-trigger"></a>添加“请求”触发器

在将触发器添加到空白工作流之前，请确保工作流设计器处于打开状态，并且在设计器图面上选择了 " **选择操作** " 提示。

1. 在设计器图面旁边的 " **添加触发器** " 窗格的 " **选择操作** " 搜索框下，检查是否已选中 **内置** 选项卡。 此选项卡显示在 Azure 逻辑应用中以本机方式运行的触发器。

1. 在 " **选择操作** " 搜索框中输入 `when a http request` ，并选择在 **收到 HTTP 请求时** 命名的内置请求触发器。

   ![屏幕截图，显示设计器，并将 "在收到 HTTP 请求时添加触发器 * *" 窗格添加为选中状态。](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   当设计器上出现触发器时，触发器的详细信息窗格将打开，显示触发器的属性、设置和其他操作。

   ![显示设计器的屏幕截图，其中显示了 "收到 HTTP 请求时" 触发器，并打开了触发器详细信息窗格。](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > 如果未显示详细信息窗格，请确保在设计器上选择了触发器。

1. 如果需要从设计器中删除项，请 [按照以下步骤从设计器中删除项](#delete-from-designer)。

1. 若要保存您的工作，请在设计器工具栏上选择 " **保存**"。

   首次保存工作流时，如果工作流是通过请求触发器启动的，则逻辑应用服务会自动为请求触发器创建的终结点生成 URL。 稍后，在测试工作流时，将向此 URL 发送请求，这会激发触发器并启动工作流运行。

### <a name="add-the-office-365-outlook-action"></a>添加 Office 365 Outlook 操作

1. 在设计器的已添加的触发器下，选择 " **新建步骤**"。

   设计器上将显示 " **选择操作** " 提示，并重新打开 " **添加操作** " 窗格，以便您可以选择下一个操作。

   > [!NOTE]
   > 如果 " **添加操作** " 窗格显示错误消息 "无法读取未定义的属性 ' 筛选器 '"，则保存工作流，重新加载页面，重新打开工作流，然后重试。

1. 在 " **添加操作** " 窗格中的 " **选择操作** " 搜索框下，选择 " **Azure**"。 此选项卡显示 Azure 中可用和部署的托管连接器。

   > [!NOTE]
   > 如果 " **添加操作** " 窗格显示错误消息，则 `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` 保存工作流，重新加载页，重新打开工作流，然后再次尝试添加操作。

   此示例使用名为的 Office 365 Outlook 操作 **(V2) 发送电子邮件**。

   ![显示设计器和 "添加操作" 窗格的屏幕截图，其中选择了 Office 365 Outlook "发送电子邮件" 操作。](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. 在操作的详细信息窗格中，在 " **创建连接** " 选项卡上选择 " **登录** "，以便可以创建与电子邮件帐户的连接。

   ![屏幕截图，显示设计器和 "发送电子邮件 (V2) " 详细信息窗格，并选择 "登录"。](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. 当系统提示你同意访问你的电子邮件帐户时，请使用你的帐户凭据登录。

   > [!NOTE]
   > 如果收到错误消息， `Failed with error: 'The browser is closed.'. Please sign in again` 请检查浏览器是否会阻止第三方 cookie。 如果这些 cookie 被阻止，请尝试将其添加 `https://portal.azure.com` 到可以使用 cookie 的站点列表。 如果使用的是 incognito 模式，请确保第三方 cookie 在该模式下工作时不会被阻止。
   > 
   > 如有必要，请重新加载页面，打开工作流，再次添加电子邮件操作，然后尝试创建连接。

   Azure 创建连接后，" **发送电子邮件** " 操作将显示在设计器上，并在默认情况下处于选中状态。 如果未选择该操作，请选择该操作，以使其详细信息窗格也处于打开状态。

1. 在操作的详细信息窗格中的 " **参数** " 选项卡上，提供操作所需的信息，例如：

   ![显示设计器和 "发送电子邮件" 详细信息窗格并选择 "参数" 选项卡的屏幕截图。](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | 属性 | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **To** | 是 | <*your-email-address*> | 电子邮件收件人，可以是你的电子邮件地址，用于测试目的。 此示例使用虚构电子邮件 `sophiaowen@fabrikam.com` 。 |
   | **主题** | 是 | `An email from your example workflow` | 电子邮件主题 |
   | **正文** | 是 | `Hello from your example workflow!` | 电子邮件正文内容 |
   ||||

   > [!NOTE]
   > 在 " **设置**"、" **静态结果**" 或 "选项卡 **后运行** " 的详细信息窗格中进行任何更改时，请确保选择 " **完成** " 以在切换选项卡或将焦点更改为设计器之前提交这些更改。 否则，设计器不会保留您的更改。

1. 保存所有内容。 在设计器工具栏上选择“保存”。

接下来，若要测试工作流，请手动触发运行。

## <a name="trigger-the-workflow"></a>触发工作流

在此示例中，工作流在请求触发器收到入站请求时运行，该请求将发送到触发器所创建终结点的 URL。 首次保存工作流时，逻辑应用服务会自动生成此 URL。 因此，在你可以发送此请求以触发工作流之前，你需要找到此 URL。

1. 在工作流设计器中，选择在 **收到 HTTP 请求时** 命名的请求触发器。

1. "详细信息" 窗格打开后，请在 " **参数** " 选项卡上找到 " **HTTP POST URL** " 属性。 若要复制生成的 URL，请选择 " **复制 url** " ("复制文件" 图标) ，并将 url 保存到其他位置。 URL 采用以下格式：

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![屏幕截图，显示具有 "HTTP POST URL" 属性中的 "请求触发器" 和 "终结点 URL" 的设计器。](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   在此示例中，URL 如下所示：

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > 还可以在逻辑应用的 " **概述** " 窗格中的 " **工作流 url** " 属性中找到终结点 URL。
   >
   > 1. 在 "资源" 菜单中，选择 " **概述**"。
   > 1. 在 " **概述** " 窗格中，找到 " **工作流 URL** " 属性。
   > 1. 若要复制终结点 URL，请将指针移到 "终结点 URL" 文本的末尾，然后选择 " **复制到剪贴板** " ("复制文件" 图标) 。

1. 若要通过发送请求来测试 URL，请打开 [Postman](https://www.postman.com/downloads/) 或用于创建和发送请求的首选工具。

   此示例通过使用 Postman 继续进行。 有关详细信息，请参阅 [Postman 入门](https://learning.postman.com/docs/getting-started/introduction/)。

   1. 在 Postman 工具栏上，选择 " **新建**"。

      ![显示 Postman 并选择 "新建" 按钮的屏幕截图](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. 在 " **新建** " 窗格的 " **构建基块**" 下，选择 " **请求**"。

   1. 在 " **保存请求** " 窗口中的 " **请求名称**" 下，提供请求的名称，例如 `Test workflow trigger` 。

   1. 在 " **选择要保存到的集合或文件夹**" 下，选择 " **创建集合**"。

   1. 在 "**所有集合**" 下，提供要创建的集合的名称以组织你的请求，按 enter，然后选择 "**保存到 <*集合名称* >**"。 此示例使用 `Logic Apps requests` 作为集合名称。

      此时将打开 Postman 的请求窗格，以便可以将请求发送到请求触发器的终结点 URL。

      ![用打开的请求窗格显示 Postman 的屏幕截图](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. 在 "请求" 窗格中，在 "方法列表" 旁边的 "地址" 框中，该列表 **当前显示为** 默认请求方法，粘贴之前复制的 URL，然后选择 " **发送**"。

      ![屏幕截图，显示已选中 "发送" 按钮的 "地址" 框中的 Postman 和 endpoint URL](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      当触发器触发时，示例工作流将运行并发送与以下示例类似的电子邮件：

      ![显示 Outlook 电子邮件的屏幕截图，如示例中所述](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

## <a name="review-run-history"></a>查看运行历史记录

对于有状态的工作流，在每个工作流运行后，您可以查看运行历史记录（包括整个运行的状态）、触发器以及每个操作及其输入和输出。

1. 在 Azure 门户的工作流菜单上，选择 " **监视器**"。

   " **监视器** " 窗格显示该工作流的运行历史记录。

   ![显示工作流的 "监视器" 窗格和运行历史记录的屏幕截图。](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > 如果未出现最新的运行状态，请在 " **监视器** " 窗格工具栏上选择 " **刷新**"。 由于不符合条件或找不到任何数据而跳过的触发器不会运行。

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

1. 若要查看运行中每个步骤的状态，请选择要查看的运行。

   "运行详细信息" 视图随即打开，并显示运行中每个步骤的状态。

   ![显示 "运行详细信息" 视图的屏幕截图，其中包含工作流中每个步骤的状态。](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

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

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. 若要查看特定步骤的输入和输出，请选择该步骤。

   ![屏幕截图，显示所选 "发送电子邮件" 操作中的输入和输出。](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. 若要进一步查看该步骤的原始输入和输出，请选择 " **显示原始输入** " 或 " **显示原始输出**"。

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>部署后启用或打开 Application Insights

在工作流执行过程中，逻辑应用会随其他事件一起发出遥测数据。 使用此遥测可以更好地了解工作流的运行情况以及逻辑应用运行时如何以各种方式工作。 你可以使用 Application Insights 来监视工作流，该[Application Insights](../azure-monitor/app/app-insights-overview.md)提供近实时遥测 (实时指标) 。 使用此数据诊断问题、设置警报和构建图表时，此功能可帮助您更轻松地调查故障和性能问题。

如果逻辑应用的创建和部署设置支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，则可以选择为逻辑应用启用诊断日志记录和跟踪。 在 Azure 门户或部署后创建逻辑应用时，可以执行此操作。 你需要有一个 Application Insights 实例，但你可以在创建逻辑应用时或部署后 [提前](../azure-monitor/app/create-workspace-resource.md)创建此资源。

若要在已部署的逻辑应用上启用 Application Insights 或打开 Application Insights 仪表板（如果已启用），请执行以下步骤：

1. 在 Azure 门户中，找到已部署的逻辑应用。

1. 在逻辑应用菜单上的 " **设置**" 下，选择 **Application Insights**。

1. 如果未启用 Application Insights，请在 " **Application Insights** " 窗格上，选择 " **启用 Application Insights**"。 在窗格更新后，单击底部的 " **应用**"。

   如果启用了 Application Insights，请在 " **Application Insights** " 窗格中选择 " **查看 Application Insights 数据**"。

Application Insights 打开后，可以查看逻辑应用的各种指标。

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>启用无状态工作流的运行历史记录

若要更轻松地调试无状态工作流，可以启用该工作流的运行历史记录，并在完成后禁用运行历史记录。 按照这些步骤操作 Azure 门户; 如果正在 Visual Studio Code，请参阅 [在 Visual Studio Code 中创建有状态和无状态的工作流](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)。

1. 在 [Azure 门户](https://portal.azure.com)中，查找并打开 **逻辑应用 (预览)** 资源。

1. 在逻辑应用的菜单的 " **设置**" 下，选择 " **配置**"。

1. 在 " **应用程序设置** " 选项卡上，选择 " **新建应用程序设置**"。

1. 在 " **添加/编辑应用程序设置** " 窗格上的 " **名称** " 框中，输入此操作选项名称： 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. 在 " **值** " 框中，输入以下值： `WithStatelessRunHistory`

   例如：

   ![屏幕截图，显示 Azure 门户和逻辑应用 (预览版) 资源，其中 "配置" > "新应用程序设置" < "添加/编辑应用程序设置" 窗格打开和 "工作流"。{yourWorkflowName}.OperationOptions "选项设置为" WithStatelessRunHistory "。](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. 若要完成此任务，请选择 **"确定"**。 在 **配置** 窗格工具栏上，选择 " **保存**"。

1. 若要在完成后禁用运行历史记录，请将 `Workflows.{yourWorkflowName}.OperationOptions` 属性设置为 `None` ，或者删除属性及其值。

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>从设计器中删除项

若要从设计器中删除工作流中的项，请执行以下任一步骤：

* 选择项，打开该项的快捷菜单 (Shift + F10) ，然后选择 " **删除**"。 若要确认，请选择“确定”。

* 选择项，然后按 delete 键。 若要确认，请选择“确定”。

* 选择项，以便为该项打开详细信息窗格。 在窗格的右上角，打开 "省略号 (**...** ") 菜单，然后选择 " **删除**"。 若要确认，请选择“确定”。

  ![使用打开的详细信息窗格以及所选省略号按钮和 "删除" 命令在设计器上显示选定项的屏幕截图。](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > 如果省略号菜单不可见，请将浏览器窗口的宽度放大到足够大，以便详细信息窗格显示右上角的省略号 (**...**) "按钮。

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>解决问题和错误

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>对于以前创建的工作流，设计器选取器中缺少新的触发器和操作

Azure 逻辑应用预览支持对 Azure 函数操作、液体操作和 XML 操作（如 **Xml 验证** 和 **转换 xml**）进行内置操作。 但是，对于以前创建的逻辑应用，如果逻辑应用使用的是过期版本的扩展捆绑，则这些操作可能不会出现在设计器中以供选择 `Microsoft.Azure.Functions.ExtensionBundle.Workflows` 。

若要解决此问题，请按照以下步骤删除过期的版本，以便扩展绑定可以自动更新到最新版本。

> [!NOTE]
> 此特定解决方案仅适用于使用 Azure 门户创建的 **逻辑应用 (预览)** 资源，而不是使用 Visual Studio Code 和 Azure 逻辑应用 (预览版) 扩展创建和部署的逻辑应用。 [Visual Studio Code 中的设计器中，请参阅支持的触发器和操作](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions)。

1. 在 Azure 门户中，停止逻辑应用。

   1. 在逻辑应用菜单中，选择“概述”。

   1. 在 " **概述** " 窗格的工具栏上，选择 " **停止**"。

1. 在逻辑应用菜单的 " **开发工具**" 下，选择 " **高级工具**"。

1. 在 " **高级工具** " 窗格上，选择 " **开始**"，这将打开逻辑应用的 Kudu 环境。

1. 在 Kudu 工具栏上，打开 " **调试控制台** " 菜单，并选择 " **CMD**"。 

   此时将打开一个控制台窗口，以便你可以使用命令提示符浏览到捆绑包文件夹。 或者，你可以浏览显示控制台窗口的目录结构。

1. 浏览到以下文件夹，其中包含现有捆绑的版本控制文件夹：

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 删除现有绑定的版本文件夹。 在控制台窗口中，可以运行以下命令，将替换 `{bundle-version}` 为现有版本：

   `rm -rf {bundle-version}`

   例如：`rm -rf 1.1.3`

   > [!TIP]
   > 如果收到 "权限被拒绝" 或 "正在使用的文件" 之类的错误，请刷新浏览器中的页面，并再次尝试前面的步骤，直到删除该文件夹。

1. 在 Azure 门户中，返回到逻辑应用的 " **概述** " 页，然后选择 " **重新启动**"。

   门户会自动获取并使用最新的捆绑包。

## <a name="next-steps"></a>后续步骤

我们想要倾听你对此公共预览版的体验！

* 对于 bug 或问题，请 [在 GitHub 中创建问题](https://github.com/Azure/logicapps/issues)。
* 对于问题、请求、评论和其他反馈，请 [使用此反馈表单](https://aka.ms/lafeedback)。
