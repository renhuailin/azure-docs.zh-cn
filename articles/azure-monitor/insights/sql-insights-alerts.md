---
title: 使用 SQL 见解创建警报（预览版）
description: 使用 Azure Monitor 的 SQL 见解创建警报
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726855"
---
# <a name="create-alerts-with-sql-insights-preview"></a>使用 SQL 见解创建警报（预览版）
SQL 见解包括一组警报规则模板，可用于在 Azure Monitor 中针对常见 SQL 问题[创建警报规则](../alert/../alerts/alerts-overview.md)。 SQL 见解中的警报规则是以 Azure Monitor 日志的 InsightsMetrics 表中存储的性能数据为基础的日志警报规则。  

> [!NOTE]
> 若要使用资源管理器模板创建 SQL 见解的警报，请参阅 [SQL 见解的资源管理器模板示例](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights)。


> [!NOTE]
> 如果要请求更多 SQL 见解警报规则模板，请使用此页面底部的链接或使用 Azure 门户中的 SQL 见解反馈链接发送反馈。

## <a name="enable-alert-rules"></a>启用警报规则 
在 Azure 门户上使用以下步骤在 Azure Monitor 中启用警报。创建的警报规则将作用于在所选监视配置文件下监视的所有 SQL 资源。  触发警报规则时，它将在特定 SQL 实例或数据库上触发。

> [!NOTE]
> 还可以创建自定义[日志警报规则](../alerts/alerts-log.md)，方法是对 InsightsMetrics 表中的数据集运行查询，然后将这些查询另存为警报规则。 

从 Azure 门户的“Azure Monitor”菜单的“见解”部分，选择“SQL（预览版）” 。 单击“警报”

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="“警报”按钮":::

“警报”窗格将在页面右侧打开。 默认情况下，它会根据已创建的警报规则，显示所选监视配置文件中的 SQL 资源的触发警报。 选择“警报模板”，将显示可用于创建警报规则的可用模板的列表。

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="警报模板":::

在“创建警报规则”页上，检查规则的默认设置，并根据需要对其进行编辑。 还可以选择[操作组](../alerts/action-groups.md)，用于在触发警报规则时创建通知和操作。 在验证其所有属性后，单击“启用警报规则”以创建警报规则。


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="警报规则页面":::

若要立即部署警报规则，请单击“部署警报规则”。 如果要在部署规则模板之前对其进行查看，请单击“查看模板”。

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="部署警报规则":::

如果选择查看模板，请从模板页中选择“部署”以创建警报规则。

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="从视图模板部署":::


## <a name="next-steps"></a>后续步骤

详细了解 [Azure Monitor 中的警报](../alerts/alerts-overview.md)。

