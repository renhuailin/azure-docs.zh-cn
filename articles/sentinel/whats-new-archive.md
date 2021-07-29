---
title: Azure Sentinel 新增功能存档
description: 介绍 Azure Sentinel 至少六个月前新增的功能和变更的功能。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: 1e629b52b7904d2f671c9d472916ba0a2185a986
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103689"
---
# <a name="archive-for-whats-new-in-azure-sentinel"></a>Azure Sentinel 新增功能存档

[Azure Sentinel 新增功能](whats-new.md)发行说明主页包含过去六个月的更新，而本页包含更早的项目。

若要了解所提供的更早的功能，请参阅[技术社区博客](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)。

请注意，这些功能目前为预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。


> [!TIP]
> Microsoft 中的威胁搜寻团队会向 [Azure Sentinel 社区](https://github.com/Azure/Azure-Sentinel)贡献查询、playbook、工作簿和笔记本，包括你的团队可调整和使用的特定[搜寻查询](https://github.com/Azure/Azure-Sentinel)。
>
> 你也可贡献内容！ 加入我们的 [Azure Sentinel 威胁猎人 GitHub 社区](https://github.com/Azure/Azure-Sentinel/wiki)吧。
>

## <a name="november-2020"></a>2020 年 11 月

- [在 Azure Sentinel 中监视 playbook 的运行状况](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender 连接器（公共预览版）](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>在 Azure Sentinel 中监视 playbook 的运行状况

Azure Sentinel playbook 基于 [Azure 日志应用](../logic-apps/index.yml)中内置的工作流，这是一项云服务，可帮助计划、自动执行和协调任务、业务流程及工作流。 创建事件或会审和使用事件时，可自动调用 playbook。 

为便于你深入了解 playbook 的运行状况、性能和使用情况，我们添加了一个名为“playbook 运行状况监视”的[工作簿](../azure-monitor/visualize/workbooks-overview.md)。 

可使用“playbook 运行状况监视”工作簿监视 playbook 的运行状况，或查找成功或失败的运行数量中的异常。 

“playbook 运行状况监视”工作簿现已在 Azure Sentinel 模板库中提供：

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="“playbook 运行状况监视”工作簿示例":::

有关详细信息，请参阅：

- [逻辑应用文档](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure Monitor 文档](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender 连接器（公共预览版）
 
通过适用于 Azure Sentinel 的 Microsoft 365 Defender 连接器，可将高级搜寻日志（一种原始事件数据类型）从 Microsoft 365 Defender 流式传输到 Azure Sentinel。 

通过将 [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) 集成到 [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) 安全产品系列中，现可使用 Microsoft 365 Defender 连接器收集 Microsoft Defender for Endpoint 高级搜寻事件，并将它们直接流式传输到 Azure Sentinel 工作区中新的专用表中。 

Azure Sentinel 表基于 Microsoft 365 Defender 门户中使用的同一架构进行构建，提供了对整个高级搜寻日志集的完整访问权限。 

有关详细信息，请参阅[将数据从 Microsoft 365 Defender 连接到 Azure Sentinel](connect-microsoft-365-defender.md)。

> [!NOTE]
> Microsoft 365 Defender 以前被称为 Microsoft 威胁防护 (MTP)。 Microsoft Defender for Endpoint 以前被称为 Microsoft Defender 高级威胁防护 (MDATP)。
> 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[加入 Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[了解警报](quickstart-get-visibility.md)
