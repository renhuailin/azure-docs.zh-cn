---
title: 创建自动化任务以管理和监视 Azure 资源
description: 通过创建在 Azure 逻辑应用上运行的工作流来设置自动化任务，以帮助你管理 Azure 资源和监视成本。
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 06/09/2021
ms.openlocfilehash: bd8ac7857d5be31aafd9a1e91cbd276d79823ed2
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747142"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>通过创建自动化任务（预览版）来管理 Azure 资源和监视成本

> [!IMPORTANT]
> 此功能为预览版，不建议用于生产工作负载，并将排除在服务水平协议之外。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

为了帮助你更轻松地管理 [Azure 资源](../azure-resource-manager/management/overview.md#terminology)，你可以使用自动化任务模板为特定资源或资源组创建自动化管理任务，这些模板的可用性因资源类型而异。 例如，对于 [Azure 存储帐户](../storage/common/storage-account-overview.md)，你可以设置一个自动化任务，向你发送该存储帐户的每月费用。 对于 [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)，你可以创建一个自动化任务，按预定义计划打开或关闭该虚拟机。

下面是此预览版中当前可用的任务模板：

| 资源类型 | 自动化任务模板 |
|---------------|---------------------------|
| Azure 资源组 | **删除资源时** |
| 所有 Azure 资源 | **发送资源的每月费用** |
| Azure 虚拟机 | 此外： <p>- **关闭虚拟机** <br>- **启动虚拟机** |
| Azure 存储帐户 | 此外： <p>- **删除旧 blob** |
| Azure Cosmos DB | 另外， <p>- **通过电子邮件发送查询结果** |
|||

本文演示如何完成以下任务：

* 为特定的 Azure 资源[创建自动化任务](#create-automation-task)。

* [查看任务的历史记录](#review-task-history)，其中包括运行状态、输入、输出和其他历史记录信息。

* [编辑任务](#edit-task)，以便你可以在逻辑应用设计器中更新任务或自定义任务的基础工作流。

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>自动化任务与 Azure 自动化有何不同？

与 [Azure 自动化](../automation/automation-intro.md)相比，自动化任务更加基础和精简。 目前，只能在 Azure 资源级别创建自动化任务。 在后台，自动化任务实际上是运行工作流的逻辑应用资源，由[多租户 Azure 逻辑应用服务](../logic-apps/logic-apps-overview.md)提供支持。 创建自动化任务后，可以通过在工作流设计器中打开任务来查看和编辑基础工作流。 某项任务完成至少一次运行后，你可以查看任务状态、工作流运行历史记录、每次运行的输入和输出。

相比之下，Azure 自动化是一种基于云的自动化和配置服务，用于支持 Azure 环境和非 Azure 环境之间的一致管理。 该服务包括[用于通过使用 [runbook](../automation/automation-runbook-execution.md) 来编排流程的流程自动化](../automation/automation-intro.md#process-automation)、具有[更改跟踪和清单](../automation/change-tracking/overview.md)的配置管理、更新管理、共享功能以及异类功能。 在部署、操作和解除工作负荷与资源期间，自动化可以提供全面的控制。

<a name="pricing"></a>

## <a name="pricing"></a>定价

仅创建自动化任务不会自动产生费用。 在下方，自动化任务是一个基于多租户的逻辑应用，因此[消耗定价](logic-apps-pricing.md)模型也适用于自动化任务。 计量和计费基于基础逻辑应用工作流中的触发器和操作执行。

无论工作流是否成功运行或者甚至是否已实例化，都会对此类执行进行计量和计费。 例如，假设你的自动化任务使用轮询触发器，该触发器定期对终结点进行传出调用。 无论是否激发或跳过触发器，此出站请求都会作为执行进行计量和计费，这会影响是否创建工作流实例。

触发器和操作遵循[消耗计划费率](https://azure.microsoft.com/pricing/details/logic-apps/)，具体费率根据这些操作是[“内置”](../connectors/built-in.md)还是[“托管”](../connectors/managed.md)（标准或企业）而异。 触发器和操作还进行存储交易，这使用[消耗计划数据费率](https://azure.microsoft.com/pricing/details/logic-apps/)。

> [!TIP]
> 作为每月的额外奖励，消耗计划包含数千次免费的内置执行。 有关特定信息，请查看[消耗计划费率](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 你要管理的 Azure 资源。 本文以 Azure 存储帐户为例。

* 一个 Office 365 帐户（如果要遵循本示例），该帐户使用 Office 365 Outlook 向你发送电子邮件。

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>创建自动化任务

1. 在 [Azure 门户](https://portal.azure.com)中，找到要管理的资源。

1. 在资源菜单上，滚动到“自动化”部分，然后选择“任务”

   ![该屏幕截图显示了 Azure 门户和存储帐户资源菜单，其中“自动化”部分选择了“任务”菜单项。](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. 在“任务”窗格上，选择“添加”，以便你可以选择任务模板。

   ![该屏幕截图显示了存储帐户“任务”窗格，其中工具栏选择了“添加”](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. 在“添加任务”窗格上的“选择模板”下，选择要创建的任务的模板。 如果没有出现下一页，请选择“下一步: 身份验证”。

   本示例通过选择“发送资源的每月费用”任务模板继续操作。

   ![该屏幕截图显示了选择“发送资源的每月费用”和“下一步: 身份验证”](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. 在“身份验证”下的“连接”部分中，为任务中出现的每个连接选择“创建”，以便为所有连接提供身份验证凭据。 每个任务中的连接类型因任务而异。

   本示例仅显示此任务所需的连接之一。

   ![该屏幕截图显示了 Azure 资源管理器连接选定的“创建”选项](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. 出现提示时，使用 Azure 帐户凭据登录。

   ![该屏幕截图显示了选择“登录”](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   每个成功通过身份验证的连接看起来都类似于以下示例：

   ![该屏幕截图显示了成功创建的连接](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. 对所有连接进行身份验证之后，如果没有出现下一页，请选择“下一步: 配置”。

1. 在“配置”下，提供任务的名称以及任务所需的任何其他信息。 完成操作后，选择“创建”。

   > [!NOTE]
   > 创建后无法更改任务名称，因此请考虑使用一个在[编辑基础工作流](#edit-task-workflow)时仍适用的名称。 你对基础工作流所做的更改仅应用于已创建的任务，而不会应用于任务模板。
   >
   > 例如，如果将任务命名为 `Send monthly cost`，但随后又将基础工作流编辑为每周运行，则无法将任务名称更改为 `Send weekly cost`。

   发送电子邮件通知的任务需要一个电子邮件地址。

   ![该屏幕截图显示了所选任务的必填信息](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   你创建的任务（将自动启动并运行）现在出现在“自动化任务”列表中。

   ![该屏幕截图显示了自动化任务列表](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > 如果任务未立即显示，请尝试刷新任务列表或稍等一会再刷新。 在工具栏上，选择“刷新”。

   所选任务运行后，你会收到一封电子邮件，如以下示例所示：

   ![该屏幕截图显示了任务发送的电子邮件通知](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>查看任务历史记录

若要查看任务的运行历史记录以及它们的状态、输入、输出和其他信息，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，找到具有你要查看的任务历史记录的资源。

1. 在资源菜单上的“设置”下，选择“自动化任务”。

1. 在任务列表中，找到要查看的任务。 在该任务的“运行”列中，选择“查看”。

   ![该屏幕截图显示了任务和所选的“查看”选项](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   “运行历史记录”窗格显示了该任务的所有运行以及它们的状态、开始时间、标识符和运行持续时间。

   ![该屏幕截图显示了某个任务的运行、其状态以及其他信息](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   可能的运行状态如下：

   | 状态 | 说明 |
   |--------|-------------|
   | 已取消 | 任务在运行时被取消。 |
   | 失败 | 任务至少有一个失败的操作，但是没有后续操作来处理该失败。 |
   | **正在运行** | 任务目前正在运行。 |
   | 成功 | 所有操作成功。 如果某个操作失败，但是有后续操作来处理该失败，那么，任务仍然可以成功完成。 |
   | **正在等待** | 运行尚未开始，并且已暂停，因为任务的早期实例仍在运行。 |
   |||

   有关详细信息，请参阅[查看运行历史记录](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. 若要查看运行中每个步骤的状态和其他信息，请选择该运行。

   “逻辑应用运行”窗格随即打开，并显示已运行的基础工作流。

   * 工作流始终从[触发器](../connectors/apis-list.md#triggers)开始。 对于此任务，工作流从[定期触发器](../connectors/connectors-native-recurrence.md)开始。

   * 每个步骤都会显示其状态和运行持续时间。 持续时间为 0 秒的步骤的运行时间不到 1 秒。

   ![该屏幕截图显示了运行中的每个步骤、状态和运行持续时间](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. 若要查看每个步骤的输入和输出，请选择该步骤，该步骤随即展开。

   本示例显示了定期触发器的输入，该触发器没有输出，因为它仅指定工作流的运行时间，不提供任何输出供后续操作处理。

   ![该屏幕截图显示了展开的触发器和输入](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   相反，“发送电子邮件”操作具有工作流中先前操作的输入和输出。

   ![该屏幕截图显示了展开的操作、输入和输出](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

若要了解如何生成自己的自动化工作流，以便独立于 Azure 资源的自动化任务上下文来集成应用、数据、服务和系统，请参阅[快速入门：使用 Azure 逻辑应用创建第一个集成工作流 - Azure 门户](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="edit-task"></a>

## <a name="edit-the-task"></a>编辑任务

若要更改任务，你可以使用以下选项：

* [“内联”编辑任务](#edit-task-inline)，以便更改任务的属性，比如连接信息或配置信息（例如电子邮件地址）。

* 在逻辑应用设计器中[编辑任务的基础工作流](#edit-task-workflow)。

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>内联编辑任务

1. 在 [Azure 门户](https://portal.azure.com)中，找到包含要更新的任务的资源。

1. 在资源菜单上的“自动化”下，选择“任务”。

1. 在任务列表中，找到要更新的任务。 打开任务的省略号 (...) 菜单，然后选择“内联编辑”。

   ![该屏幕截图显示了打开的省略号菜单和所选选项“内联编辑”](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   默认情况下，“身份验证”选项卡将出现，并显示现有连接。

1. 若要为连接添加新的身份验证凭据或选择其他现有的身份验证凭据，请打开连接的省略号 (...) 菜单，然后选择“添加新的连接”或其他身份验证凭据（如果可用）。

   ![该屏幕截图显示了“身份验证”选项卡、现有连接和所选的省略号菜单](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. 若要更新其他任务属性，请选择“下一步: 配置”。

   对于本示例中的任务，唯一可供编辑的属性是电子邮件地址。

   ![该屏幕截图显示了“配置”选项卡](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. 完成后，选择“保存”。

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>编辑任务的基础工作流

更改自动化任务的基础工作流时，所做的更改仅影响你创建的任务实例，而不影响创建该任务的模板。 进行更改并保存后，为原始任务提供的名称可能无法再准确描述该任务，因此可能需要使用其他名称重新创建任务。

> [!TIP]
> 最佳做法是克隆基础工作流，以便可以编辑复制的版本。 这样，你就可以在原始自动化任务继续运行的同时，在副本上进行更改并测试更改，而不会有中断或破坏现有功能的风险。 完成更改并确保新版本成功运行后，你可以禁用或删除原始自动化任务，并将克隆的版本用于自动化任务。 以下步骤包括有关如何克隆工作流的信息。

1. 在 [Azure 门户](https://portal.azure.com)中，找到包含要更新的任务的资源。

1. 在资源菜单上的“自动化”下，选择“任务”。

1. 在任务列表中，找到要更新的任务。 打开任务的省略号 (...) 菜单，然后选择“在逻辑应用中打开”。

   ![该屏幕截图显示了打开的省略号菜单和所选选项“在逻辑应用中打开”](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   该任务的基础工作流在 Azure 逻辑应用服务中打开，并显示“概述”窗格，你可以在其中查看可用于该任务的相同运行历史记录。

   ![该屏幕截图显示了选中了“概述”窗格的 Azure 逻辑应用视图中的任务](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. 若要在逻辑应用设计器中打开基础工作流，请在逻辑应用的菜单上选择“逻辑应用设计器”。

   ![该屏幕截图显示了所选的“逻辑应用设计器”菜单选项，以及包含基础工作流的设计器图面](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   现在，你可以编辑工作流触发器和操作的属性，也可以编辑定义工作流本身的触发器和操作。 但是，作为最佳做法，请按照以下步骤克隆工作流，以便在原始工作流继续运行的同时对副本进行更改。

1. 若要改为克隆工作流并编辑复制的版本，请执行以下步骤：

   1. 在逻辑应用工作流菜单上，选择“概述”。

   1. 在概述窗格的工具栏上，选择“克隆”。

   1. 在逻辑应用创建窗格的“名称”下，为复制的逻辑应用工作流输入新名称。

      除“逻辑应用状态”外，其他属性均不可编辑。 
      
   1. 在“逻辑应用状态”下选择“已禁用”，这样，克隆的工作流就不会在你进行更改时运行。 准备好测试更改时，可以启用工作流。

   1. Azure 预配完克隆的工作流后，在逻辑应用设计器中找到并打开该工作流。

1. 若要查看触发器或操作的属性，请展开该触发器或操作。

   例如，你可以将定期触发器更改为每周运行一次，而不是每月运行一次。

   ![该屏幕截图显示了展开的定期触发器，其中“频率”列表打开并显示了可用的频率选项](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   有关定期触发器的详细信息，请参阅[使用定期触发器创建、计划和运行周期性任务与工作流](../connectors/connectors-native-recurrence.md)。 有关可使用的其他触发器和操作的详细信息，请参阅[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)。

1. 若要保存更改，请在设计器工具栏上选择“保存”。

   ![该屏幕截图显示了设计器工具栏和所选的“保存”命令](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. 若要测试并运行更新的工作流，请在设计器工具栏上，选择“运行”。

   运行完成后，设计器会显示工作流的运行详细信息。

   ![该屏幕截图显示了设计器上工作流的运行详细信息](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. 若要禁用工作流，以使该任务不再继续运行，请参阅[在 Azure 门户中管理逻辑应用](../logic-apps/manage-logic-apps-with-azure-portal.md)。

## <a name="provide-feedback"></a>提供反馈

我们非常乐意听到你的反馈！ 若要报告 bug、提供反馈或询问有关此预览版功能的问题，[请联系 Azure 逻辑应用团队](mailto:logicappspm@microsoft.com)。

## <a name="next-steps"></a>后续步骤

* [在 Azure 门户中管理逻辑应用](../logic-apps/manage-logic-apps-with-azure-portal.md)
