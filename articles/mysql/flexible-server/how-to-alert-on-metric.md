---
title: 配置指标警报 - Azure 门户 - Azure Database for MySQL 灵活服务器
description: 本文介绍如何通过 Azure 门户配置和访问针对 Azure Database for MySQL 灵活服务器的指标警报。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: c257941079c3a43639337dd1b010002ddac4672e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652855"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>使用 Azure 门户设置针对 Azure Database for MySQL 灵活服务器指标的警报 

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍了如何使用 Azure 门户设置 Azure Database for MySQL 警报。 可根据监视指标接收 Azure 服务的警报。

当指定的指标值越过了分配的阈值时，就会触发此警报。 首次满足条件时，以及之后不再满足条件时，都会触发此警报。 指标警报是有状态的，即，它们只会在状态有更改时才发出通知。

可配置警报，使警报触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 将电子邮件发送到指定的其他电子邮件地址。
* 调用 Webhook

可使用以下项配置并获取预警规则相关信息：

* [Azure 门户](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure 监视器 REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>通过 Azure 门户针对指标创建警报规则

1. 在 [Azure 门户](https://portal.azure.com/)中，选择要监视的 Azure Database for MySQL 灵活服务器。
2. 在边栏的“监视”部分，选择“警报” 。
3. 选择“+ 新建警报规则”。
4. 将打开“创建规则”页。 填写所需信息：
5. 在“条件”部分中，选择“选择条件” 。
6. 此时会显示支持的信号列表，请选择要为其创建警报的指标。 例如，选择“存储百分比”。
7. 随后会该指标在显示过去 6 小时的图表。 可以使用“图表期间”  下拉框进行选择以查看更长时间的指标历史记录。
8. 选择“阈值”类型（例如 “静态”或“动态”）、“运算符”（例如 “大于”）和“聚合类型”（例如 平均值）。 这将确定指标警报规则将评估的逻辑。
    - 如果使用“静态”阈值，请继续定义“阈值” （例如 85%）。 指标图表可以帮助你确定可能合理的阈值。
    - 如果使用“动态”  阈值，请继续定义“阈值敏感度”  。 指标图表将显示基于最新数据计算得出的阈值。 [详细了解动态阈值条件类型和敏感度选项](../../azure-monitor/alerts/alerts-dynamic-thresholds.md)。
9. 通过对使用聚合类型函数进行分组的数据点调整“聚合粒度(时间范围)”间隔（例如 “30 分钟”）和“频率”（例如“每 15 分钟”）来优化条件。
10. 单击“完成”。
11. 添加操作组。 操作组是由 Azure 订阅的所有者定义的通知首选项的集合。 在“操作组”部分，选择“选择操作组”以选择要附加到警报规则的现有操作组 。
12. 你还可创建一个新的操作组来接收有关警报的通知。 有关详细信息，请参阅[创建和管理操作组](../../azure-monitor/alerts/action-groups.md)。
13. 若要创建新的操作组，请选择“+ 创建操作组”。 填写“创建操作组”窗体，其中包含“订阅”、“资源组”、操作组名称”和“显示名称”   。
14. 为操作组配置“通知”。
    
    在“通知类型”中选择“向 Azure 资源管理器角色发送电子邮件”以选择订阅所有者、参与者和读者来接收通知。 选择 Azure 资源管理器角色来发送电子邮件。
    还可选择“电子邮件/短信/推送/语音”将通知发送给特定收件人。

    提供通知类型的“名称”，并在完成后选择“查看 + 创建” 。

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. 填写“警报规则详细信息”，例如“警报规则名称”、“说明”、“将预警规则保存到资源组”和“严重性”    。

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. 选择“创建警报规则”可以创建警报  。
    几分钟后，警报将处于活动状态，并按前面所述进行触发。
## <a name="manage-your-alerts"></a>管理警报
创建警报后，可选择它并执行以下操作：

* 查看图，了解与此警报相关的指标阈值和前一天实际值。
* 编辑或删除预警规则   。
* 如果要暂时停止或恢复接收通知，可禁用或启用警报   。


## <a name="next-steps"></a>后续步骤
- 详细了解如何[设置指标警报](../../azure-monitor/alerts/alerts-metric.md)。
- 详细了解 [Azure Database for MySQL 灵活服务器中的可用指标](./concepts-monitoring.md)。
- [了解指标警报在 Azure Monitor 中的工作原理](../../azure-monitor/alerts/alerts-metric-overview.md)