---
title: 在 Azure 门户中使用单租户 Azure 逻辑应用（标准版）创建工作流
description: 在 Azure 门户中使用单租户 Azure 逻辑应用（标准版）创建集成应用、数据、服务和系统的自动化工作流。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/25/2021
ms.openlocfilehash: 33a40f118daf90ccc97ea6c14b87c5aa7c79b6c9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708802"
---
# <a name="create-an-integration-workflow-with-single-tenant-azure-logic-apps-standard-in-the-azure-portal"></a>在 Azure 门户中使用单租户 Azure 逻辑应用（标准版）创建集成工作流

本文介绍如何使用逻辑应用（标准版）资源类型和 Azure 门户创建在单租户 Azure 逻辑应用环境中运行的自动化集成工作流示例。 该资源类型可以托管多个[有状态和无状态工作流](single-tenant-overview-compare.md#stateful-stateless)。 同样，同一逻辑应用和租户中的工作流的运行过程与重新设计的 Azure 逻辑应用运行时相同，因此它们共享相同的资源并提供更好的性能。 有关单租户 Azure 逻辑应用产品/服务的详细信息，请查看[单租户与多租户以及集成服务环境](single-tenant-overview-compare.md)。

虽然此示例工作流是基于云的工作流，并且只有两个步骤，但可以从数百个操作创建工作流，这些操作可以跨云、本地和混合环境连接各种应用、数据、服务和系统。 示例工作流从内置的“请求”触发器开始，然后执行 Office 365 Outlook 操作。 触发器为工作流创建可调用终结点，并等待来自任何调用方的入站 HTTPS 请求。 当触发器收到请求并触发时，会向指定电子邮件地址发送电子邮件以及触发器中所选的输出来运行下一个操作。

> [!TIP]
> 如果没有 Office 365 帐户，可以使用任何其他可从电子邮件帐户发送消息的可用操作，例如使用 Outlook.com。
>
> 若要改为在 Visual Studio Code 中创建此工作流示例，请按照[使用单租户 Azure 逻辑应用和 Visual Studio Code 创建集成工作流](create-single-tenant-workflows-visual-studio-code.md)中的步骤进行操作。 
> 可使用这两个选项在相同类型的环境中开发、运行和部署逻辑应用工作流。 
> 但是借助 Visual Studio Code，你可以在开发环境中本地开发、测试和运行工作流。

![屏幕截图显示了 Azure 门户，其中包含“逻辑应用(标准版)”资源的工作流设计器。](./media/create-single-tenant-workflows-azure-portal/azure-portal-logic-apps-overview.png)

你将逐渐完成以下高级任务：

* 创建逻辑应用资源并添加一个空白的[有状态](single-tenant-overview-compare.md#stateful-stateless)工作流。
* 添加触发器和操作。
* 触发一个工作流运行。
* 查看该工作流的运行和触发器历史记录。
* 在部署后启用或打开 Application Insights。
* 启用无状态工作流的运行历史记录。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* [Azure 存储帐户](../storage/common/storage-account-overview.md)。 如果没有，可以提前创建一个存储帐户或在逻辑应用创建期间创建一个存储帐户。

  > [!NOTE]
  > 逻辑应用（标准版）资源类型由 Azure Functions 提供支持，其[存储要求与函数应用类似](../azure-functions/storage-considerations.md)。 
  > [有状态工作流](single-tenant-overview-compare.md#stateful-stateless)执行存储事务，例如，使用队列在表和 blob 中计划和存储工作流状态。 这些事务会产生[存储费用](https://azure.microsoft.com/pricing/details/storage/)。 若要详细了解有状态工作流如何将数据存储在外部存储中，请查看[有状态工作流和无状态工作流](single-tenant-overview-compare.md#stateful-stateless)。

* 若要创建与本文中相同的示例工作流，你需要一个使用 Microsoft 工作或学校帐户进行登录的 Office 365 Outlook 电子邮件帐户。

  如果你选择[不同的电子邮件连接器](/connectors/connector-reference/connector-reference-logicapps-connectors)，比如Outlook.com，你仍然可以遵循这个示例，整体常规步骤是一样的。 但是，你的选项可能在某些方面有所不同。 例如，如果你使用 Outlook.com 连接器，请改用你的个人 Microsoft 帐户进行登录。

* 若要测试本文中的示例工作流，则需要一个可以向“请求”触发器创建的终结点发送调用的工具。 如果没有此类工具，你可以下载、安装并使用 [Postman](https://www.postman.com/downloads/)。

* 如果你通过支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 的设置创建逻辑应用资源，则可以选择为你的逻辑应用启用诊断日志记录和跟踪。 你可以在创建逻辑应用时或在部署后执行此操作。 你需要有一个 Application Insights 实例，但你可以在创建逻辑应用时[提前](../azure-monitor/app/create-workspace-resource.md)创建此资源，或在部署后创建此资源。

<a name="create-logic-app-resource"></a>

## <a name="create-the-logic-app-resource"></a>创建逻辑应用资源

1. 在 [Azure 门户](https://portal.azure.com)中，使用 Azure 帐户凭据登录。

1. 在 Azure 门户搜索框中，输入 `logic apps`，然后选择“逻辑应用”。

   ![屏幕截图显示了 Azure 门户的搜索框，其中包含“逻辑应用”搜索词，并且“逻辑应用(标准版)”资源处于选中状态。](./media/create-single-tenant-workflows-azure-portal/find-logic-app-resource-template.png)

1. 在“逻辑应用”页上，选择“添加” 。

1. 在“创建逻辑应用”页面中的“基本信息”选项卡上，提供有关你的逻辑应用资源的以下信息： 

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **订阅** | 是 | <*Azure-subscription-name*> | 要用于你的逻辑应用的 Azure 订阅。 |
   | **资源组** | 是 | <*Azure-resource-group-name*> | 你在其中创建逻辑应用和相关资源的 Azure 资源组。 此资源名称在各个区域中必须唯一，并且只能包含字母、数字、连字符 ( **-** )、下划线 ( **_** )、括号 ( **()** ) 和句点 ( **.** )。 <p><p>此示例创建一个名为 `Fabrikam-Workflows-RG` 的资源组。 |
   | 类型 | 是 | **标准** | 此逻辑应用资源类型在单租户 Azure 逻辑应用环境中运行，并使用[标准使用、计费和定价模型](logic-apps-pricing.md#standard-pricing)。 |
   | **逻辑应用名称** | 是 | <*logic-app-name*> | 用于逻辑应用的名称。 此资源名称在各个区域中必须唯一，并且只能包含字母、数字、连字符 ( **-** )、下划线 ( **_** )、括号 ( **()** ) 和句点 ( **.** )。 <p><p>此示例创建一个名为 `Fabrikam-Workflows` 的逻辑应用。 <p><p>注意：你的逻辑应用名称会自动添加后缀 `.azurewebsites.net`，因为“逻辑应用(标准版)”资源由 Azure Functions 提供支持，后者使用相同的应用命名约定 。 |
   | **发布** | 是 | <*deployment-environment*> | 逻辑应用的部署目标。 默认选择“工作流”，目的是部署到单租户 Azure 逻辑应用。 Azure 会创建一个空的逻辑应用资源，你必须在其中添加第一个工作流。 <p><p>注意：目前，“Docker 容器”选项要求已启用 Azure Arc 的 Kubernetes 群集上有[自定义位置](../azure-arc/kubernetes/conceptual-custom-locations.md)，你可将该群集与[已启用 Azure Arc 的逻辑应用（预览版）](azure-arc-enabled-logic-apps-overview.md)配合使用 。 逻辑应用资源位置、自定义位置和群集必须相同。 |
   | **区域** | 是 | <*Azure-region*> | 用于创建资源组和资源的位置。 如果选择了“Docker 容器”，请选择自定义位置。 <p><p>此示例将示例逻辑应用部署到 Azure 并使用“美国西部”。 |
   |||||

   以下示例显示了“创建逻辑应用(标准)”页：

   ![显示了 Azure 门户和“创建逻辑应用”页面的屏幕截图。](./media/create-single-tenant-workflows-azure-portal/create-logic-app-resource-portal.png)

1. 在“托管”选项卡上，提供有关要用于逻辑应用的存储解决方案和托管计划的以下信息。

   | 属性 | 必须 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **存储帐户** | 是 | <*Azure-storage-account-name*> | 要用于存储事务的 [Azure 存储帐户](../storage/common/storage-account-overview.md)。 <p><p>此资源名称在各个区域中必须唯一，长度为 3-24 个字符，并且仅包含数字和小写字母。 选择一个现有的帐户，或者创建一个新帐户。 <p><p>此示例创建一个名为 `fabrikamstorageacct` 的存储帐户。 |
   | **计划类型** | 是 | <托管计划> | 要用于部署逻辑应用的托管计划。 <p><p>有关详细信息，请查看[托管计划和定价层](logic-apps-pricing.md#standard-pricing)。 |
   | **Windows 计划** | 是 | <*plan-name*> | 要使用的计划名称。 选择一个现有计划名称或为新计划提供一个名称。 <p><p>此示例使用名称 `Fabrikam-Service-Plan`。 |
   | **SKU 和大小** | 是 | <*pricing-tier*> | 用于逻辑应用的[定价层](../app-service/overview-hosting-plans.md)。 你的选择会影响逻辑应用和工作流使用的定价、计算、内存和存储。 <p><p>若要更改默认定价层，请选择“更改大小”。 然后，你可以根据所需的工作负载选择其他定价层。 <p><p>有关详细信息，请查看[托管计划和定价层](logic-apps-pricing.md#standard-pricing)。 |
   |||||

1. 接下来，如果你的创建和部署设置支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，则可以选择为你的逻辑应用启用诊断日志记录和跟踪。

   1. 在“监视”选项卡上的“Application Insights”下，将“启用 Application Insights”设置为“是”（如果尚未选择）。

   1. 对于“Application Insights”设置，请选择一个现有 Application Insights 实例，如果要创建新的实例，请选择“新建”并提供要使用的名称。

1. 在 Azure 验证你的逻辑应用设置后，在“查看 + 创建”选项卡上选择“创建”，例如：

   ![屏幕截图显示了 Azure 门户和新的逻辑应用资源设置。](./media/create-single-tenant-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > 如果在此步骤后收到验证错误，请打开并查看错误详细信息。 例如，如果所选区域达到了你要尝试创建的资源的配额，则你可能必须尝试另一个区域。

   Azure 完成部署后，你的逻辑应用将自动处于活动状态并运行，但不会执行任何操作，因为资源为空，并且尚未添加任何工作流。

1. 在部署完成页上，选择“转到资源”，以便可以添加空白工作流。

   ![屏幕截图显示了 Azure 门户和完成的部署。](./media/create-single-tenant-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>添加空白工作流

创建空逻辑应用资源后，必须添加第一个工作流。

1. 在 Azure 打开资源后，在逻辑应用的菜单中选择“工作流”。 在“工作流”工具栏上，选择“添加”。

   ![屏幕截图显示了逻辑应用资源菜单，其中“工作流”处于选中状态，并且工具栏上的“添加”也处于选中状态。](./media/create-single-tenant-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. 在“新建工作流”窗格打开后，为工作流提供一个名称，并选择[“有状态”或“无状态”](single-tenant-overview-compare.md#stateful-stateless)工作流类型  。 完成操作后，选择“创建”。

   此示例将添加一个名为 `Fabrikam-Stateful-Workflow` 的空白有状态工作流。 默认情况下，工作流处于启用状态，但在你添加触发器和操作之前，它不会执行任何操作。

   ![屏幕截图显示了新添加的空白有状态工作流“Fabrikam-Stateful-Workflow”。](./media/create-single-tenant-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. 接下来，在设计器中打开该空白工作流，以便可以添加触发器和操作。

   1. 从工作流列表中，选择该空白工作流。

   1. 在工作流菜单上的“开发人员”下，选择“设计器” 。

      在设计器图面上，已显示并默认选中了“选择操作”提示，因此，“添加触发器”窗格也已打开。

      ![屏幕截图显示了打开的工作流设计器，其中设计器图面上选中了“选择操作”。](./media/create-single-tenant-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>添加触发器和操作

此示例将构建一个包含以下步骤的工作流：

* 内置的 [请求触发器](../connectors/connectors-native-reqres.md)：**当收到 HTTP 请求时**，该触发器将收到入站调用或请求，并创建其他服务或逻辑应用可以调用的终结点。

* [Office 365 Outlook 操作](../connectors/connectors-create-api-office365-outlook.md)：**发送电子邮件**。

### <a name="add-the-request-trigger"></a>添加“请求”触发器

在将触发器添加到空白工作流之前，请确保工作流设计器处于打开状态并且在设计器图面上选择了“选择操作”提示。

1. 在设计器图面旁边的“添加触发器”窗格中的“选择操作”搜索框下，检查是否已选中“内置”选项卡。 此选项卡显示在 Azure 逻辑应用中以原生方式运行的触发器。

1. 在“选择操作”搜索框中输入 `when a http request`，并选择名为“当收到 HTTP 请求时”的内置请求触发器。

   ![屏幕截图显示了设计器和“添加触发器”窗格，该窗格中的“当收到 HTTP 请求时”触发器处于选中状态。](./media/create-single-tenant-workflows-azure-portal/find-request-trigger.png)

   当触发器出现在设计器上时，触发器的详细信息窗格将打开，以显示触发器的属性、设置和其他操作。

   ![屏幕截图显示了设计器，其中“当收到 HTTP 请求时”触发器处于选中状态，并且触发器详细信息窗格已打开。](./media/create-single-tenant-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > 如果未显示详细信息窗格，请确保在设计器上选择了触发器。

1. 如果需要从设计器中删除某个项，请[遵循从设计器中删除项的这些步骤](#delete-from-designer)。

1. 若要保存你的工作，请在设计器工具栏上选择“保存”。

   首次保存工作流时，如果工作流是通过请求触发器启动的，则逻辑应用服务会自动为请求触发器创建的终结点生成 URL。 稍后，在你测试工作流时，将向此 URL 发送请求，这会激发触发器并启动工作流运行。

### <a name="add-the-office-365-outlook-action"></a>添加 Office 365 Outlook 操作

1. 在设计器上已添加的触发器下，选择加号“新建步骤”+ “添加操作” 。

   设计器上将显示“选择操作”提示，并且“添加操作”窗格将重新打开，以便你可以选择下一个操作。

   > [!NOTE]
   > 如果“添加操作”窗格显示了错误消息“无法读取未定义的属性‘筛选器’”，请保存你的工作流，重新加载页面，重新打开工作流，然后重试。

1. 在“添加操作”窗格中的“选择操作”搜索框下，选择“Azure”。 此选项卡显示在 Azure 中可用并已托管的托管连接器。

   > [!NOTE]
   > 如果“添加操作”窗格显示了错误消息“`The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'`”，请保存你的工作流，重新加载页，重新打开工作流，然后重新尝试添加操作。

   此示例使用名为“发送电子邮件(V2)”的 Office 365 Outlook 操作。

   ![屏幕截图显示了设计器和“添加操作”窗格，该窗格中的 Office 365 Outlook 操作“发送电子邮件”处于选中状态。](./media/create-single-tenant-workflows-azure-portal/find-send-email-action.png)

1. 在操作的详细信息窗格中的“创建连接”选项卡上选择“登录”，以便可以创建与电子邮件帐户的连接。

   ![屏幕截图显示了设计器和“发送电子邮件(V2)”详细信息窗格，该窗格中的“登录”处于选中状态。](./media/create-single-tenant-workflows-azure-portal/send-email-action-sign-in.png)

1. 当系统提示你访问电子邮件帐户时，请使用你的帐户凭据登录。

   > [!NOTE]
   > 如果你收到错误消息“`Failed with error: 'The browser is closed.'. Please sign in again`”，请检查你的浏览器是否阻止了第三方 cookie。 如果这些 cookie 被阻止，请尝试将 `https://portal.azure.com` 添加到可以使用 cookie 的站点列表。 如果使用的是 incognito 模式，请确保在该模式下工作时不会阻止第三方 cookie。
   > 
   > 如有必要，请重新加载页面，打开你的工作流，再次添加电子邮件操作，然后尝试创建连接。

   在 Azure 创建连接后，“发送电子邮件”操作将显示在设计器上，并且默认情况下处于选中状态。 如果未选择该操作，请选择该操作，以使其详细信息窗格也打开。

1. 在操作的详细信息窗格中的“参数”选项卡上，提供操作所需的信息，例如：

   ![屏幕截图显示了设计器和“发送电子邮件”详细信息窗格，该窗格中的“参数”选项卡处于选中状态。](./media/create-single-tenant-workflows-azure-portal/send-email-action-details.png)

   | 属性 | 必须 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **To** | 是 | <*your-email-address*> | 电子邮件收件人，这可以是你自己的电子邮件地址（用于测试）。 此示例使用虚构的电子邮件 `sophiaowen@fabrikam.com`。 |
   | **主题** | 是 | `An email from your example workflow` | 电子邮件主题 |
   | **正文** | 是 | `Hello from your example workflow!` | 电子邮件正文内容 |
   ||||

   > [!NOTE]
   > 在详细信息窗格中的“设置”、“静态结果”或“随后运行”选项卡上进行任何更改时，请确保在切换选项卡或将焦点更改为设计器之前选择“完成”以提交这些更改。 否则，设计器不会保留所做的更改。

1. 保存所有内容。 在设计器工具栏上选择“保存”。

1. 如果你的环境具有限制流量的严格网络要求或防火墙，则必须为工作流中存在的任何触发器或操作连接设置权限。 若要查找完全限定的域名，请查看[查找用于防火墙访问的域名](#firewall-setup)。

   否则，若要测试工作流，请[手动触发运行](#trigger-workflow)。

<a name="firewall-setup"></a>

## <a name="find-domain-names-for-firewall-access"></a>查找用于防火墙访问的域名

在 Azure 门户中部署逻辑应用并运行工作流之前，如果你的环境具有限制流量的严格网络要求或防火墙，则必须为逻辑应用中存在的工作流中的任何触发器或操作连接设置网络或防火墙权限。

若要查找逻辑应用和工作流所使用的入站和出站 IP 地址，请执行以下步骤：

1. 在逻辑应用菜单上的“设置”下，选择“网络(预览版)” 。

1. 在“网络”页上，查找并查看“入站流量”和“出站流量”部分 。

若要查找用于连接的完全限定的域名 (FQDN)，请执行以下步骤：

1. 在逻辑应用菜单上的“工作流”下，选择“连接”。 在“API 连接”选项卡上，选择连接的资源名称，例如：

   ![屏幕截图显示了 Azure 门户和逻辑应用菜单，其中的“连接”和“offic365”连接资源名称处于选中状态。](./media/create-single-tenant-workflows-azure-portal/logic-app-connections.png)

1. 充分扩展浏览器的宽度，以便在浏览器的右上角出现“JSON 视图”时，选择“JSON 视图”。

   ![屏幕截图显示了 Azure 门户和“API 连接”窗格，其中的“JSON 视图”处于选中状态。](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-view-json.png)

1. 复制 `connectionRuntimeUrl` 属性值并将其保存到一个安全的位置，以便可以使用此信息设置防火墙。

   ![屏幕截图显示了处于选定状态的“connectionRuntimeUrl”属性值。](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. 对于每个连接，重复相关步骤。

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>触发工作流

在此示例中，工作流在请求触发器收到入站请求时运行，该请求将发送到触发器所创建的终结点的 URL。 首次保存工作流时，逻辑应用服务自动生成了此 URL。 因此，你需要找到此 URL，然后才能发送此请求来触发工作流。

1. 在工作流设计器上，选择名为“当收到 HTTP 请求时”的请求触发器。

1. 在详细信息窗格打开后，在“参数”选项卡上找到“HTTP POST URL”属性。 若要复制生成的 URL，请选择“复制 URL”（“复制文件”图标），并暂时将 URL 保存到其他位置。 此 URL 遵循以下格式：

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![屏幕截图显示了包含请求触发器的设计器，以及“HTTP POST URL”属性中的终结点 URL。](./media/create-single-tenant-workflows-azure-portal/find-request-trigger-url.png)

   对于此示例，URL 如下所示：

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > 还可以在逻辑应用的“概览”窗格中的“工作流 URL”属性中找到终结点 URL。
   >
   > 1. 在资源菜单中，选择“概述”。
   > 1. 在“概述”窗格中，找到“工作流 URL”属性。
   > 1. 若要复制终结点 URL，请将指针移到“终结点 URL”文本的末尾，然后选择“复制到剪贴板”（“复制文件”图标）。

1. 若要发送请求来测试 URL，请打开 [Postman](https://www.postman.com/downloads/) 或你习惯用来创建和发送请求的工具。

   此示例使用 Postman 继续。 有关详细信息，请参阅 [Postman 入门](https://learning.postman.com/docs/getting-started/introduction/)。

   1. 在 Postman 工具栏上，选择“新建”。

      ![屏幕截图显示了 Postman，其中“新建”按钮处于选中状态](./media/create-single-tenant-workflows-azure-portal/postman-create-request.png)

   1. 在“新建”窗格上的“构建基块”下，选择“请求”。

   1. 在“保存请求”窗口中的“请求名称”下，为请求提供一个名称，例如 `Test workflow trigger`。

   1. 在“选择要保存到的集合或文件夹”下，选择“创建集合”。

   1. 在“所有集合”下，为要创建的用于组织你的请求的集合提供一个名称，按 Enter，然后选择“保存到 <*collection-name*>”。 此示例使用 `Logic Apps requests` 作为集合名称。

      在 Postman 应用中，会打开请求窗格，以便将请求发送到请求触发器的终结点 URL。

      ![屏幕截图显示了 Postman，其中包含打开的请求窗格](./media/create-single-tenant-workflows-azure-portal/postman-request-pane.png)

   1. 在请求窗格上的方法列表（当前显示了 **GET** 作为默认请求方法）旁边的地址栏中，粘贴以前复制的 URL，然后选择“发送”。

      ![屏幕截图显示了 Postman 和地址框中的终结点 URL，并且“发送”按钮处于选中状态](./media/create-single-tenant-workflows-azure-portal/postman-test-endpoint-url.png)

      当触发了触发器时，示例工作流将运行，并发送类似于以下示例的电子邮件：

      ![屏幕截图显示了示例中所述的 Outlook 电子邮件](./media/create-single-tenant-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>查看运行历史记录

对于有状态工作流，在每个工作流运行后，你可以查看运行历史记录，包括整个运行的状态、触发器的状态，以及每个操作的状态及其输入和输出。 在 Azure 门户中，运行历史记录和触发器历史记录显示在工作流级别，而非逻辑应用级别。 若要在运行历史记录上下文外部查看触发器历史记录，请参阅[查看触发器历史记录](#view-trigger-histories)。

1. 在 Azure 门户中的工作流菜单上，选择“概述”。

1. 在“概述”窗格上，选择“运行历史记录”，其中显示了该工作流的运行历史记录 。

   ![屏幕截图显示了工作流的“概述”页，其中“运行历史记录”处于选中状态。](./media/create-single-tenant-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > 如果未显示最新的运行状态，请在“概述”窗格工具栏上选择“刷新” 。 如果由于不符合条件或找不到数据而跳过了触发器，则不会发生运行。

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

1. 若要查看运行中每个步骤的状态，请选择要查看的运行。

   运行详细信息视图随即打开，并显示运行中每个步骤的状态。

   ![屏幕截图显示了运行详细信息视图，其中包含工作流中每个步骤的状态。](./media/create-single-tenant-workflows-azure-portal/review-run-details.png)

   下面是工作流中的每个步骤可以具有的可能状态：

   | 操作状态 | 说明 |
   |---------------|-------------|
   | **Aborted** | 由于外部问题（例如，系统中断或 Azure 订阅过期），操作已停止或未完成。 |
   | 已取消 | 操作正在运行，但收到取消请求。 |
   | **已失败** | 操作已失败。 |
   | **正在运行** | 操作当前正在运行。 |
   | 已跳过 | 此操作已被跳过，因为不满足其 `runAfter` 条件（例如，先前的操作失败）。 每个操作都有一个 `runAfter` 对象，你可以在其中设置必须满足的条件，然后才能执行当前操作。 |
   | 成功 | 操作已成功。 |
   | **重试成功** | 操作已成功，但是在一次或多次重试后才成功。 若要查看重试历史记录，请在运行历史记录详细信息视图中选择该操作，以便可以查看输入和输出。 |
   | **已超时** | 操作由于超出了操作设置指定的超时限制而停止。 |
   | **正在等待** | 适用于正在等待来自调用方的入站请求的 Webhook 操作。 |
   |||

   [aborted-icon]: ./media/create-single-tenant-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-single-tenant-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-single-tenant-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-single-tenant-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-single-tenant-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-single-tenant-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-single-tenant-workflows-azure-portal/waiting.png

1. 若要查看某个特定步骤的输入和输出，请选择该步骤。

   ![屏幕截图显示了所选“发送电子邮件”操作中的输入和输出。](./media/create-single-tenant-workflows-azure-portal/review-step-inputs-outputs.png)

1. 若要进一步查看该步骤的原始输入和输出，请选择“显示原始输入”或“显示原始输出”。

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>查看触发器历史记录

对于有状态工作流，你可以独立于[运行历史记录上下文](#view-run-history)查看每个运行的触发器历史记录，包括触发器状态以及输入和输出。 在 Azure 门户中，触发器历史记录和运行历史记录显示在工作流级别，而非逻辑应用级别。 若要查找此历史数据，请执行以下步骤：

1. 在 Azure 门户中的工作流菜单上，选择“概述”。

1. 在“概述”页面上，选择“触发历史记录” 。

   “触发器历史记录”窗格将显示你的工作流运行的触发器历史记录。

1. 若要查看特定的触发器历史记录，请选择该运行的 ID。

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>在部署后启用或打开 Application Insights

在工作流运行过程中，你的逻辑应用会发出遥测数据以及其他事件。 使用此遥测数据可以更好地了解工作流的运行情况以及逻辑应用运行时如何以各种方式工作。 你可以使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 来监视工作流，该工具可提供近乎实时的遥测数据（实时指标）。 使用该数据来诊断问题、设置警报和构建图表时，此功能可帮助你更轻松地调查失败和性能问题。

如果逻辑应用的创建和部署设置支持使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，则可以选择为逻辑应用启用诊断日志记录和跟踪。 你可以在从 Azure 门户中创建逻辑应用时或在部署后执行此操作。 你需要有一个 Application Insights 实例，但你可以在创建逻辑应用时[提前](../azure-monitor/app/create-workspace-resource.md)创建此资源，或在部署后创建此资源。

若要在已部署的逻辑应用上启用 Application Insights 或要打开 Application Insights 仪表板（如果已启用），请执行以下步骤：

1. 在 Azure 门户中，找到你的已部署逻辑应用。

1. 在逻辑应用菜单上，在“设置”下，选择“Application Insights” 。

1. 如果未启用 Application Insights，请在“Application Insights”窗格上选择“启用 Application Insights”。 在窗格更新后，选择底部的“应用” > “是” 。

   如果启用了 Application Insights，请在“Application Insights”窗格上选择“查看 Application Insights 数据”。

在 Application Insights 打开后，你可以查看逻辑应用的各种指标。 有关详细信息，请查看以下主题：

* [Azure Logic Apps Running Anywhere - Monitor with Application Insights - part 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)（随处运行的 Azure 逻辑应用 - 使用 Application Insights 进行监视 - 第 1 部分）
* [Azure Logic Apps Running Anywhere - Monitor with Application Insights - part 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)（随处运行的 Azure 逻辑应用 - 使用 Application Insights 进行监视 - 第 2 部分）

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>为无状态工作流启用运行历史记录

若要更轻松地调试某个无状态工作流，可以为该工作流启用运行历史记录，并在完成后禁用运行历史记录。 对于 Azure 门户，请按照下面的步骤操作；如果使用的是 Visual Studio Code，请参阅[在 Visual Studio Code 中创建有状态和无状态工作流](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless)。

1. 在 [Azure 门户](https://portal.azure.com)中，打开你的“逻辑应用(标准版)”资源。

1. 在逻辑应用菜单上的“设置”下，选择“配置” 。

1. 在“应用程序设置”选项卡上，选择“新建应用程序设置” 。

1. 在“添加/编辑应用程序设置”窗格上的“名称”框中，输入此操作选项名称： 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. 在“值”框中，输入以下值：`WithStatelessRunHistory`

   例如：

   ![屏幕截图显示了 Azure 门户和“逻辑应用(标准版)”资源，其中“配置”>“新建应用程序设置”>“添加/编辑应用程序设置”窗格已打开，并且“Workflows.{yourWorkflowName}.OperationOptions”选项设置为“WithStatelessRunHistory”。](./media/create-single-tenant-workflows-azure-portal/stateless-operation-options-run-history.png)

1. 若要完成此任务，请选择“确定”。 在“配置”窗格工具栏上，选择“保存”。

1. 若要在完成时禁用运行历史记录，请将 `Workflows.{yourWorkflowName}.OperationOptions` 属性设置为 `None`，或者删除此属性及其值。

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>从设计器中删除项

若要从设计器中删除工作流中的某个项，请执行以下任一步骤：

* 选择该项，打开该项的快捷菜单 (Shift + F10)，然后选择“删除”。 若要确认，请选择“确定”。

* 选择该项，然后按 Delete 键。 若要确认，请选择“确定”。

* 选择该项，以便为该项打开详细信息窗格。 在窗格的右上角，打开省略号 ( **...** ) 菜单，然后选择“删除”。 若要确认，请选择“确定”。

  ![屏幕截图显示了设计器上的一个所选项，以及打开的详细信息窗格和选中的省略号按钮与“删除”命令。](./media/create-single-tenant-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > 如果省略号菜单不可见，请充分扩展浏览器窗口，以使详细信息窗格在右上角显示省略号 ( **...** ) 按钮。

<a name="restart-stop-start"></a>

## <a name="restart-stop-or-start-logic-apps"></a>重启、停止或启动逻辑应用

你可以启动或停止[单个逻辑应用](#restart-stop-start-single-logic-app)，或者一次性启动或停止[多个逻辑应用](#stop-start-multiple-logic-apps)。 还可以在不先停止的情况下重启单个逻辑应用。 基于单租户的逻辑应用可以包含多个工作流，因此你可以停止整个逻辑应用或[仅禁用工作流](#disable-enable-workflows)。

> [!NOTE]
> 停止逻辑应用和禁用工作流操作具有不同的效果。 有关详细信息，请参阅[停止逻辑应用的注意事项](#considerations-stop-logic-apps)和[禁用工作流的注意事项](#disable-enable-workflows)。

<a name="considerations-stop-logic-apps"></a>

### <a name="considerations-for-stopping-logic-apps"></a>停止逻辑应用的注意事项

停止逻辑应用会通过以下方式影响工作流实例：

* Azure 逻辑应用会立即取消所有正在进行和挂起的运行。

* Azure 逻辑应用不会创建或运行新的工作流实例。

* 下次满足触发器的条件时，触发器不会触发。 但是，触发器状态会记住逻辑应用的停止点。 因此，如果重启逻辑应用，触发器就会触发自上次运行以来的所有未处理项。

  若要阻止每个工作流触发自上次运行以来的未处理项，请在重启逻辑应用之前按照以下步骤清除触发状态：

  1. 在 Azure 门户中打开逻辑应用。
  1. 在逻辑应用菜单的“工作流”下，选择“工作流”。
  1. 打开工作流，然后编辑该工作流触发器的任何部分。
  1. 保存所做更改。 此步骤会重置触发器的当前状态。
  1. 对每个工作流重复此操作。
  1. 完成后，[重启逻辑应用](#restart-stop-start-single-logic-app)。

<a name="restart-stop-start-single-logic-app"></a>

### <a name="restart-stop-or-start-a-single-logic-app"></a>重启、停止或启动单个逻辑应用

1. 在 Azure 门户中打开逻辑应用。

1. 在逻辑应用菜单中，选择“概述”  。

   * 若要在不停止的情况下重启逻辑应用，请在“概述”窗格工具栏上选择“重启”。
   * 若要停止正在运行的逻辑应用，请在“概述”窗格工具栏上选择“停止”。 确认选择。
   * 若要启动已停止的逻辑应用，请在“概述”窗格工具栏上选择“启动”。

   > [!NOTE]
   > 如果逻辑应用已停止，则只会看到“启动”选项。 如果逻辑应用已经在运行，则只会看到“停止”选项。
   > 你可以随时重启逻辑应用。

1. 若要确认操作是成功还是失败，请在 Azure 主工具栏上打开“通知”列表（钟形图标）。

<a name="stop-start-multiple-logic-apps"></a>

### <a name="stop-or-start-multiple-logic-apps"></a>停止或启动多个逻辑应用

可以同时停止或启动多个逻辑应用，但不能在不先停止多个逻辑应用的情况下重启这些逻辑应用。

1. 在 Azure 门户的主搜索框中，输入 `logic apps`，然后选择“逻辑应用”。

1. 在“逻辑应用”页上，查看逻辑应用的“状态”列 。

1. 在复选框列中，选择要停止或启动的逻辑应用。

   * 若要停止所选的正在运行的逻辑应用，请在“概述”窗格工具栏上选择“禁用/停止”。 确认选择。
   * 若要启动所选的已停止的逻辑应用，请在“概述”窗格工具栏上选择“启用/启动”。

1. 若要确认操作是成功还是失败，请在 Azure 主工具栏上打开“通知”列表（钟形图标）。

<a name="disable-enable-workflows"></a>

## <a name="disable-or-enable-workflows"></a>禁用或启用工作流

若要在下一次满足触发条件时阻止触发器触发，请禁用工作流。 你可以禁用或启用单个工作流，但不能同时禁用或启用多个工作流。 禁用工作流会通过以下方式影响工作流实例：

* Azure 逻辑应用会继续所有正在进行和挂起的运行，直到完成为止。 根据卷或积压工作 (backlog)，此过程可能需要一些时间才能完成。

* Azure 逻辑应用不会创建或运行新的工作流实例。

* 下一次满足触发器的条件时，触发器不会触发。 但是，触发器状态会记住工作流的禁用点。 因此，如果重新启用工作流，此触发器将会触发自上次运行以来的所有未处理项。

  若要阻止触发器触发自上次运行以来未处理的项，请在重新激活工作流之前清除触发器的状态：

  1. 在工作流中，编辑工作流触发器的任何部分。
  1. 保存所做更改。 此步骤会重置触发器的当前状态。
  1. [重新激活工作流](#disable-enable-workflows)。

> [!NOTE]
> 禁用工作流和停止逻辑应用操作具有不同的效果。 有关详细信息，请参阅[停止逻辑应用的注意事项](#considerations-stop-logic-apps)。

<a name="disable-workflow"></a>

### <a name="disable-workflow"></a>禁用工作流

1. 在逻辑应用菜单的“工作流”下，选择“工作流”。 在复选框列中，选择要禁用的工作流。

1. 在“工作流”窗格工具栏上，选择“禁用” 。

1. 若要确认操作是成功还是失败，请在 Azure 主工具栏上打开“通知”列表（钟形图标）。

<a name="enable-workflow"></a>

### <a name="enable-workflow"></a>启用工作流

1. 在逻辑应用菜单的“工作流”下，选择“工作流”。 在复选框列中，选择要启用的工作流。

1. 在“工作流”窗格工具栏上，选择“启用” 。

1. 若要确认操作是成功还是失败，请在 Azure 主工具栏上打开“通知”列表（钟形图标）。

<a name="delete"></a>

## <a name="delete-logic-apps-or-workflows"></a>删除逻辑应用或工作流

你可以[删除单个逻辑应用或同时删除多个逻辑应用](#delete-logic-apps)。 基于单租户的逻辑应用可以包含多个工作流，因此你可以删除整个逻辑应用或[仅删除工作流](#delete-workflows)。

<a name="delete-logic-apps"></a>

### <a name="delete-logic-apps"></a>删除逻辑应用

删除逻辑应用会立即取消正在进行和挂起的运行，但不会在应用使用的存储上运行清理任务。

1. 在 Azure 门户的主搜索框中，输入 `logic apps`，然后选择“逻辑应用”。

1. 从“逻辑应用”列表的复选框列中，选择要删除的单个或多个逻辑应用。 在工具栏中选择“删除”。

1. 确认框出现时，输入 `yes` 并选择“删除”。

1. 若要确认操作是成功还是失败，请在 Azure 主工具栏上打开“通知”列表（钟形图标）。

<a name="delete-workflows"></a>

### <a name="delete-workflows"></a>删除工作流

删除工作流会通过以下方式影响工作流实例：

* Azure 逻辑应用会立即取消正在进行和挂起的运行，但会在工作流使用的存储上运行清理任务。

* Azure 逻辑应用不会创建或运行新的工作流实例。

* 如果删除工作流，然后重新创建相同的工作流，则重新创建的工作流不会具有与删除的工作流相同的元数据。 若要刷新元数据，必须重新保存调用已删除工作流的所有工作流。 这样，调用方就可获取重新创建的工作流的正确信息。 否则，对重新创建的工作流的调用将失败并显示 `Unauthorized` 错误。 此行为也适用于在集成帐户中使用项目的工作流和调用 Azure 函数的工作流。

1. 在 Azure 门户中打开逻辑应用。

1. 在逻辑应用菜单的“工作流”下，选择“工作流”。 在复选框列中，选择要删除的一个或多个工作流。

1. 在工具栏中选择“删除”。

1. 若要确认操作是成功还是失败，请在 Azure 主工具栏上打开“通知”列表（钟形图标）。

<a name="recover-deleted"></a>

## <a name="recover-deleted-logic-apps"></a>恢复已删除的逻辑应用

如果使用源代码管理，可以顺利将已删除的逻辑应用（标准版）资源重新部署到单租户Azure 逻辑应用。 但是，如果不使用源代码管理，请尝试以下步骤来恢复已删除的逻辑应用。

> [!NOTE]
> 在尝试恢复已删除的逻辑应用之前，请查看以下注意事项：
>
> * 只能恢复使用“工作流标准”托管计划的已删除的逻辑应用（标准版）资源 。 
> 无法恢复已删除的逻辑应用（消耗版）资源。
>
> * 如果工作流以“请求”触发器开始，则已恢复的逻辑应用的回叫 URL 与已删除的逻辑应用的 URL 不同。
>
> * 在在已恢复的逻辑应用中，已删除的逻辑应用的运行历史记录不可用。

1. 确认逻辑应用的存储帐户仍然存在。 如果存储帐户已删除，则首先必须[首先恢复已删除的存储帐户](../storage/common/storage-account-recover.md)。

1. 在存储帐户菜单上的“安全性 + 网络”下，选择“访问密钥” 。

1. 在“访问密钥”页上，复制帐户的主连接字符串，并保存供以后使用，例如：

   `DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accesskey>;EndpointSuffix=core.windows.net`

1. 在存储帐户菜单的“数据存储”下，选择“文件共享”，复制与逻辑应用关联的文件共享的名称，并保存供以后使用 。

1. 使用相同的托管计划和定价层创建一个新的逻辑应用（标准版）。 可以使用新名称，也可以重复使用已删除的逻辑应用中的名称。

1. 在继续操作之前，请停止逻辑应用。 在逻辑应用菜单中，选择“概述”。 在“概述”窗格工具栏上，选择“停止” 。

1. 在逻辑应用菜单的“设置”下，选择“配置” 。

1. 在“配置”页上，更新以下应用程序设置值，并记得在完成后保存更改。

   | 应用设置 | 替换值 |
   |-------------|-------------------|
   | `AzureWebJobsStorage` | 将现有值替换为以前从存储帐户中复制的连接字符串。 |
   | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | 将现有值替换为以前从存储帐户中复制的字符串。 |
   | `WEBSITE_CONTENTSHARE` | 将现有值替换为先前复制的文件共享名。 |
   |||

1. 在逻辑应用菜单上的“工作流”下，选择“连接”。

1. 打开每个连接，然后在“设置”下选择“访问策略” 。

1. 删除已删除逻辑应用的访问策略，然后为替代逻辑应用添加新的访问策略。

1. 返回到逻辑应用的“配置”页，并添加已删除逻辑应用上存在的所有自定义设置。

1. 完成后，重启逻辑应用。

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>解决问题和错误

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>对于以前创建的工作流，设计器选取器中缺少新的触发器和操作

单租户 Azure 逻辑应用支持将内置操作用于 Azure 函数操作、Liquid 操作和 XML 操作（例如 XML 验证和转换 XML） 。 但是，对于以前创建的逻辑应用，如果逻辑应用使用的是过时版本的扩展捆绑包 `Microsoft.Azure.Functions.ExtensionBundle.Workflows`，则这些操作可能不会出现在设计器中供你选择。

若要解决此问题，请按照以下步骤删除过时的版本，以便扩展捆绑包可以自动更新到最新版本。

> [!NOTE]
> 此特定解决方案仅适用于使用 Azure 门户创建的逻辑应用（标准版）资源，不适用于使用 Visual Studio Code 和 Azure 逻辑应用（标准版）扩展创建和部署的逻辑应用。 请参阅 [Visual Studio Code 中的设计器缺少受支持的触发器和操作](create-single-tenant-workflows-visual-studio-code.md#missing-triggers-actions)。

1. 在 Azure 门户中，停止你的逻辑应用。

   1. 在逻辑应用菜单中，选择“概述”。

   1. 在“概述”窗格的工具栏上，选择“停止”。

1. 在逻辑应用菜单上的“开发工具”下，选择“高级工具”。

1. 在“高级工具”窗格上，选择“转到”，这将为你的逻辑应用打开 Kudu 环境。

1. 在 Kudu 工具栏上，打开“调试控制台”菜单，并选择“CMD”。

   此时将打开一个控制台窗口，以便你可以使用命令提示符浏览到捆绑包文件夹。 另外，你可以浏览控制台窗口上方显示的目录结构。

1. 浏览到以下文件夹，其中包含现有捆绑包的带版本号的文件夹：

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 删除现有捆绑包的版本文件夹。 在控制台窗口中，你可以运行以下命令（请将其中的 `{bundle-version}` 替换为现有版本）：

   `rm -rf {bundle-version}`

   例如：`rm -rf 1.1.3`

   > [!TIP]
   > 如果你收到“权限被拒绝”或“文件正在使用中”之类的错误，请在浏览器中刷新页面，并再次尝试前面的步骤，直到该文件夹被删除。

1. 在 Azure 门户中，返回到你的逻辑应用的“概述”页，然后选择“重启”。

   门户将自动获取并使用最新的捆绑包。

## <a name="next-steps"></a>后续步骤

我们想要倾听你对此方案的体验反馈！

* 如果发现 bug 或问题，请[在 GitHub 中创建问题](https://github.com/Azure/logicapps/issues)。
* 如有问题、请求、意见和其他反馈，请[使用此反馈表单](https://aka.ms/lafeedback)。
