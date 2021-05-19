---
title: 查看针对逻辑应用的 Azure Monitor 日志并创建查询
description: 查看针对 Azure 逻辑应用的 Azure Monitor 日志并在其中创建查询
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5de795811923c965a1473f8d91e3fd654aeef266
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574541"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>查看针对 Azure 逻辑应用的 Azure Monitor 日志并创建用于在其中进行监视和跟踪的查询

可查看从 [Azure Monitor 日志](../azure-monitor/logs/log-query-overview.md)生成结果的基础查询，并创建基于特定条件筛选结果的查询。 例如，你可以根据特定的交换控制编号查找消息。 查询使用 [Kusto 查询语言](/azure/data-explorer/kusto/query/)，如果希望查看不同的结果，可对其进行编辑。 有关详细信息，请参阅 [Azure Monitor 日志查询](/azure/data-explorer/kusto/query/)。

## <a name="prerequisites"></a>先决条件

* Log Analytics 工作区。 如果没有 Log Analytics 工作区，请了解[如何创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。

* 设置了 Azure Monitor 日志记录并将该信息发送至 Log Analytics 工作区的逻辑应用。 了解[如何为逻辑应用设置 Azure Monitor 日志](../logic-apps/monitor-logic-apps.md)。

* 如果使用的是集成帐户，请确保已为该帐户设置 Azure Monitor 日志记录，以便将该信息发送至 Log Analytics 工作区。 了解如何[为集成帐户设置 Azure Monitor 日志记录](../logic-apps/monitor-b2b-messages-log-analytics.md)。

## <a name="view-queries-behind-results"></a>查看生成结果的查询

若要查看或编辑在工作区摘要中生成结果的查询，请执行以下步骤：

1. 在任何结果页的底部，选择“查看全部”。

   ![查看所有结果](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   “日志”页随即打开，并显示生成之前的结果的查询页。

   ![日志页 - 查询视图](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. 在“日志”页上，可选择以下选项：

   * 若要以表的形式查看查询结果，请在查询编辑器下选择“表”。

   * 若要更改查询，请更新查询字符串，并选择“运行”以查看表中的结果。

## <a name="create-your-own-query"></a>创建自己的查询

若要基于特定属性或值查找或筛选结果，可以通过从空查询开始创建自己的查询，也可以使用现有查询。 有关详细信息，请参阅 [Azure Monitor 日志查询入门](../azure-monitor/logs/get-started-queries.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，找到并选择 Log Analytics 工作区。

1. 在工作区菜单中的“常规”下，选择“日志” 。

1. 从空查询或任何可用的现有查询开始。

   * 若要查看任何现有查询是否可用，在查询工具栏上，可以选择“示例查询” > “历史记录”以显示之前的查询运行中的查询，也可以选择“查询资源管理器”以显示预生成的查询  。

     例如，逻辑应用 B2B 解决方案提供以下预生成的查询：

     ![从“逻辑应用 B2B”解决方案预生成的查询开始](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * 若要从空查询开始，请在查询编辑器中，首先为查询键入 [Kusto 查询语言](/azure/data-explorer/kusto/query/)。

     ![从空查询开始](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>后续步骤

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)