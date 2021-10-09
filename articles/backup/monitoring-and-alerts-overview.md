---
title: Azure 备份的监视和报告解决方案
description: 了解 Azure 备份提供的不同监视和报告解决方案。
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 9045290c51d012db0346b4b2b81fa8ac88f22a69
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698999"
---
# <a name="monitoring-and-reporting-solutions-for-azure-backup"></a>Azure 备份的监视和报告解决方案

本文概述了 Azure 备份提供的不同监视和报告解决方案。

Azure 备份提供与各种 Azure 服务（例如 Azure Resource Graph、Azure Monitor 警报、Azure Monitor 日志和 Azure 资源运行状况）的集成。 Azure 备份还提供各种 Azure 客户端（例如 Azure 门户、Azure PowerShell、Azure CLI 和 Azure REST API）的接口。 你可以根据自己的监视和报告要求使用这些集成的组合。

## <a name="monitoring-and-reporting-scenarios"></a>监视和报告方案

下表汇总了企业备份部署中常见的各种监视和报告方案、Azure 备份目前为其中每种方案提供的各种功能，以及相关文档参考。

| 方案 | 可用解决方案 |
| --- | --- |
| 监视备份作业和备份实例 | <ul><li>内置监视：可以通过[备份中心](/azure/backup/backup-center-overview)仪表板实时监视备份作业和备份实例。</li><li>自定义监视仪表板：Azure 备份允许使用非门户客户端（例如 [PowerShell](/azure/backup/backup-azure-vms-automation)、[CLI](/azure/backup/create-manage-azure-services-using-azure-command-line-interface) 和 [REST API](/azure/backup/backup-azure-arm-userestapi-managejobs)）来查询要在自定义仪表板中使用的备份监视数据。  <br><br>  此外，可以使用 [Azure Resource Graph (ARG)](/azure/backup/query-backups-using-azure-resource-graph) 大规模查询备份（跨保管库、订阅、区域和 Lighthouse 租户）。    <br><br>    [备份资源管理器](/azure/backup/monitor-azure-backup-with-backup-explorer)是一个示例监视工作簿，它使用 ARG 中的数据，你可以将其用作参考来创建自己的仪表板。 </li></ul> |
| 监视总体备份运行状况      |   资源运行状况：可以监视恢复服务保管库的运行状况，并对导致出现资源运行状况问题的事件进行故障排除。 [了解详细信息](/azure/service-health/resource-health-overview)。   <br><br>   可以查看运行状况历史记录，并识别影响了资源运行状况的事件。 还可以触发与资源运行状况事件相关的警报。  |
| 在发生关键备份事件时收到警报     |  <ul><li>使用 Azure Monitor 的内置警报（预览版）：针对删除备份数据、禁用软删除、备份失败和还原失败等情况，Azure 备份提供了[基于 Azure Monitor 的警报解决方案](/azure/backup/backup-azure-monitoring-built-in-monitor#azure-monitor-alerts-for-azure-backup-preview)。    <br><br>  可以通过备份中心查看这些警报并进行管理。 若要针对这些警报[配置通知](/azure/backup/backup-azure-monitoring-built-in-monitor#configuring-notifications-for-alerts)（例如电子邮件），可以使用 Azure Monitor 的[操作规则](/azure/azure-monitor/alerts/alerts-action-rules?tabs=portal)和[操作组](/azure/azure-monitor/alerts/action-groups)将警报路由到各种通知通道。  </li><li>经典警报：这是使用“恢复服务保管库”边栏选项卡中的[“备份警报”选项卡](/azure/backup/backup-azure-monitoring-built-in-monitor#backup-alerts-in-recovery-services-vault)访问的旧式警报解决方案。 无法在备份中心查看这些警报。 如果你使用经典警报，我们建议开始使用基于 Azure Monitor 的警报解决方案（如上所述），因为它是前瞻性的警报解决方案。 </li><li>自定义警报：如果在你的方案中需要根据自定义逻辑生成警报，你可以对此类方案使用[基于 Log Analytics 的警报](/azure/backup/backup-azure-monitoring-use-azuremonitor#create-alerts-by-using-log-analytics)，前提是已将保管库配置为向 Log Analytics (LA ) 工作区发送诊断数据。 由于 [LA 工作区中数据的当前更新频率](/azure/backup/backup-azure-monitoring-use-azuremonitor#diagnostic-data-update-frequency)方面的原因，此解决方案通常用于能够接受在发生实际事件之后、生成警报之前存在短时间延迟的方案。 </li></ul>     |
| 分析历史趋势        |     <ul><li>内置报告：可以使用[备份报告](/azure/backup/configure-reports)（基于 Azure Monitor 日志）来分析与作业成功状态和备份使用情况相关的历史趋势，并找到备份优化机会。 还可以[配置这些报告的定期电子邮件](/azure/backup/backup-reports-email)。 </li><li>自定义报告仪表板：还可以使用记录的[系统函数](/azure/backup/backup-reports-system-functions)查询 Azure Monitor 日志 (LA) 中的数据，以创建自己的仪表板来分析备份相关的历史信息。</li></ul>    |
| 审核用户对保管库触发的操作    |       活动日志：可以使用保管库的标准[活动日志](/azure/azure-monitor/essentials/activity-log)来查看有关用户触发的各种操作（例如修改备份策略、还原备份项等）的信息。 还可以针对活动日志配置警报，或将这些日志导出到 Log Analytics 工作区以便长期保留。

## <a name="next-steps"></a>后续步骤

- [详细了解](/azure/backup/backup-center-overview)备份中心。
- [详细了解](/azure/backup/backup-azure-monitoring-built-in-monitor#azure-monitor-alerts-for-azure-backup-preview) Azure Monitor 警报。
- [详细了解](/azure/service-health/resource-health-overview) Azure 资源运行状况。
