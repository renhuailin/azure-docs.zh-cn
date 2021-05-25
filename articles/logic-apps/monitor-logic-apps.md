---
title: 监视状态、查看历史记录并设置警报
description: 通过在 Azure 逻辑应用中检查运行状态、查看触发历史记录和启用警报来排查逻辑应用的问题
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 3c3d1930234c178a56227830ef0702450ddf4a8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "100580661"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>监视 Azure 逻辑应用的运行状态、查看其触发历史记录并为其设置警报

在[创建并运行逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)后，可以检查该逻辑应用的运行状态、[运行历史记录](#review-runs-history)、[触发历史记录](#review-trigger-history)和性能。 要获取关于故障或其他可能问题的通知，请设置[警报](#add-azure-alerts)。 例如，可以创建一个警报，用于检测“一小时内运行失败超过五次的情况”。

若要进行实时事件监视和更丰富的调试，可以使用 [Azure Monitor 日志](../azure-monitor/overview.md)为逻辑应用设置诊断日志记录。 此 Azure 服务可帮助你监视云和本地环境，使你能够更轻松地维持其可用性和性能。 然后，可以查找和查看事件，例如触发事件、运行事件和操作事件。 将此信息存储在 [Azure Monitor 日志](../azure-monitor/logs/data-platform-logs.md)中，可以创建[日志查询](../azure-monitor/logs/log-query-overview.md)来帮助查找和分析此信息。 还可以将此诊断数据与其他 Azure 服务一起使用，例如 Azure 存储和 Azure 事件中心。 有关详细信息，请参阅[使用 Azure Monitor 监视逻辑应用](../logic-apps/monitor-logic-apps-log-analytics.md)。

> [!NOTE]
> 如果逻辑应用程序在为使用[内部访问终结点](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)而创建的[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中运行，则只能从虚拟网络内部查看和访问逻辑应用的运行历史记录中的输入和输出。 请确保专用终结点与要从中访问运行历史记录的计算机之间存在网络连接。 例如，客户端计算机可能位于 ISE 的虚拟网络中，也可能存在于通过对等互连或虚拟专用网络等连接到 ISE 的虚拟网络的某个虚拟网络中。 有关详细信息，请参阅 [ISE 终结点访问](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>查看运行历史记录

每次针对某个项或事件激发触发器时，逻辑应用引擎将针对每个项或事件创建并运行一个独立的工作流实例。 默认情况下，每个工作流实例并行运行，因此在启动运行之前无需等待任何工作流。 可查看该次运行过程中发生的情况，包括工作流中的每个步骤的状态，以及每个步骤的输入和输出。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中查找逻辑应用并将其打开。

   若要查找逻辑应用，请在 Azure 的主搜索框中输入 `logic apps`，然后选择“逻辑应用”。

   ![查找并选择“逻辑应用”服务](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 门户将显示与 Azure 订阅关联的所有逻辑应用。 可以根据名称、订阅、资源组、位置等筛选此列表。

   ![查看与订阅关联的逻辑应用](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 选择你的逻辑应用，然后选择“概述”。

   在概述窗格中的“运行历史记录”下，显示了逻辑应用的所有以往、当前和任何等待中的运行。 如果列表显示多次运行，且你找不到所需条目，请尝试筛选列表。

   > [!TIP]
   > 如果未显示运行状态，请尝试通过选择“刷新”来刷新概述页。 如果由于不符合条件或找不到数据而跳过了触发器，则不会发生运行。

   ![概述、运行历史记录和其他逻辑应用信息](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   可能的运行状态如下：

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

1. 若要查看特定运行的步骤和其他信息，请在“运行历史记录”下选择该运行。

   ![选择要查看的特定运行](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   “逻辑应用运行”窗格将显示所选运行中的每个步骤、每个步骤的运行状态，以及运行每个步骤花费的时间，例如：

   ![特定运行中的每个操作](./media/monitor-logic-apps/logic-app-run-pane.png)

   若要以列表格式查看此信息，请在“逻辑应用运行”工具栏上选择“运行详细信息”。 

   ![在工具栏上选择“运行详细信息”](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   “运行详细信息”视图显示每个步骤、其状态和其他信息。

   ![查看有关运行中每个步骤的详细信息](./media/monitor-logic-apps/review-logic-app-run-details.png)

   例如，可以获取运行的“关联 ID”属性，在使用[适用于逻辑应用的 REST API](/rest/api/logic) 时，可能需要此信息。

1. 若要获取有关特定步骤的详细信息，请选择以下任一选项：

   * 在“逻辑应用运行”窗格中选择该步骤，使形状展开。 现在可以查看输入、输出以及该步骤中发生的任何错误等信息，例如：

     ![在“逻辑应用运行”窗格中查看失败的步骤](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * 在“逻辑应用运行详细信息”窗格中选择所需的步骤。

     ![在“运行详细信息”窗格中查看失败的步骤](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     现在可以查看该步骤的信息（例如输入和输出），例如：

   > [!NOTE]
   > 所有运行时详细信息和事件都在逻辑应用服务中进行加密。 只有当用户请求查看该数据时，才会进行解密。 可以[在运行历史记录中隐藏输入和输出](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate)，或使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 来控制用户对此信息的访问。

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>查看触发器历史记录

每个逻辑应用运行都以触发器开头。 触发历史记录列出逻辑应用做出的所有触发尝试，以及有关每次触发尝试的输入和输出的信息。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中查找逻辑应用并将其打开。

   若要查找逻辑应用，请在 Azure 的主搜索框中输入 `logic apps`，然后选择“逻辑应用”。

   ![查找并选择“逻辑应用”服务](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 门户将显示与 Azure 订阅关联的所有逻辑应用。 可以根据名称、订阅、资源组、位置等筛选此列表。

   ![查看与订阅关联的逻辑应用](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 选择你的逻辑应用，然后选择“概述”。

1. 在逻辑应用的菜单中，选择“概述”。 在“摘要”部分中的“评估”下，选择“查看触发历史记录”。  

   ![查看逻辑应用的触发历史记录](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   触发历史记录窗格显示逻辑应用做出的所有触发尝试。 每次针对某个项或事件激发触发器时，逻辑应用引擎就会创建一个独立的逻辑应用实例来运行工作流。 默认情况下，每个实例并行运行，因此在启动运行前无需等待任何工作流。 因此，如果逻辑应用同时针对多个项触发，则会对每个项显示具有相同日期和时间的触发器项。

   ![针对不同项的多次触发尝试](./media/monitor-logic-apps/logic-app-trigger-history.png)

   可能的触发器尝试状态如下：

   | 触发器状态 | 说明 |
   |----------------|-------------|
   | 失败 | 出现了错误。 若要查看失败触发器生成的任何错误消息，请选择该触发器尝试并选择“输出”。 例如，你可能发现输入无效。 |
   | 已跳过 | 触发器检查了终结点，但找不到满足指定条件的数据。 |
   | 成功 | 触发器已检查终结点并找到可用数据。 通常，此状态还会伴随出现“已触发”状态。 如果没有，触发器定义可能未满足某一条件或 `SplitOn` 命令。 <p><p>此状态可应用于手动触发器、定期触发器或轮询触发器。 如果操作生成未处理的错误，尽管可以成功运行触发器，但运行本身可能仍会失败。 |
   |||

   > [!TIP]
   > 可以重新检查触发器，无需等待下一个周期。 在概述工具栏上，选择“运行触发器”并选择强制执行检查的触发器。 或者，在“逻辑应用设计器”工具栏中选择“运行”。

1. 若要查看有关特定触发尝试的信息，请在触发器窗格中选择该触发事件。 如果列表显示许多触发尝试，但你找不到所需的条目，请尝试筛选列表。 如果找不到预期的数据，请尝试在工具栏中选择“刷新”。

   ![查看特定的触发尝试](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   现在可以查看有关所选触发事件的信息，例如：

   ![查看特定触发器的信息](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>设置监视警报

若要在出现特定的指标或超出逻辑应用的阈值时收到警报，请[在 Azure Monitor 中设置警报](../azure-monitor/alerts/alerts-overview.md)。 了解 [Azure 中的指标](../azure-monitor/data-platform.md)。 若要在不使用 [Azure Monitor](../azure-monitor/logs/log-query-overview.md) 的情况下设置警报，请执行以下步骤。

1. 在逻辑应用菜单中的“监视”下，选择“警报” > “新建警报规则”。  

   ![为逻辑应用添加警报](./media/monitor-logic-apps/add-new-alert-rule.png)

1. 在“创建规则”窗格中的“资源”下，如果未选择你的逻辑应用，请选择它。  在“条件”下选择“添加”，以便可以定义触发警报的条件。 

   ![为规则添加条件](./media/monitor-logic-apps/add-condition-for-rule.png)

1. 在“配置信号逻辑”窗格中，找到并选择要接收其警报的信号。 可以使用搜索框；若要按字母顺序对信号排序，请选择“信号名称”列标题。

   例如，若要在触发器失败时发送警报，请执行以下步骤：

   1. 在“信号名称”列中，找到并选择“触发器失败”信号。 

      ![选择要为其创建警报的信号](./media/monitor-logic-apps/find-and-select-signal.png)

   1. 在针对所选信号打开的信息窗格中的“警报逻辑”下设置条件，例如：

   1. 对于“运算符”，请选择“大于或等于”。 

   1. 对于“聚合类型”，请选择“计数”。 

   1. 对于“阈值”，请输入 `1`。

   1. 在“条件预览”下，确认显示的条件正确。

   1. 在“评估依据”下，设置运行警报规则的间隔和频率。 对于“聚合粒度(周期)”，请选择数据的分组周期。 对于“评估频率”，请选择检查条件的频率。

   1. 准备就绪后，选择“完成”。

   下面是已完成的条件：

   ![为警报设置条件](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   现在，“创建规则”页会显示创建的条件以及运行该警报所需的开销。

   ![“创建规则”页上的“新建警报”](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. 指定警报的名称、可选说明和严重性级别。 保持启用“创建时启用规则”设置，或者将此设置禁用到准备好启用规则为止。

1. 完成后，选择“创建警报规则”。

> [!TIP]
> 要从警报运行逻辑应用，可以在工作流中包括[请求触发器](../connectors/connectors-native-reqres.md)，从而可以执行类似以下示例的任务：
> 
> * [发布到 Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [发送短信](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [向队列添加消息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 监视逻辑应用](../logic-apps/monitor-logic-apps-log-analytics.md)
