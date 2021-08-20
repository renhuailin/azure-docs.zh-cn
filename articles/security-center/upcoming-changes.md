---
title: 即将推出的对 Azure 安全中心的重要更改
description: 即将推出的对 Azure 安全中心的更改，你可能需要了解这些更改并针对这些更改做好计划
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/25/2021
ms.author: memildin
ms.openlocfilehash: 2770c3532dd83051f9c2c7dcc770850e85aeae4b
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114689580"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

| 计划的更改                                                                                                                                                                                          | 预计更改日期 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)                                            | 2021 年 7 月                 |
| [弃用建议“应在计算机上解决 Log Analytics 代理运行状况问题”](#deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines) | 2021 年 7 月                 |
| [资源管理器警报的 Azure Defender 的逻辑重组](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                                           | 2021 年 8 月               |
| [CSV 导出限制为 20 MB](#csv-exports-to-be-limited-to-20-mb)                                                                                                                               | 2021 年 8 月               |
| [启用 Azure Defender 安全控件以在安全分数中增加得分](#enable-azure-defender-security-control-to-be-included-in-secure-score)                                                         | 2021 年第 3 季度                   |
| [针对用于对 SQL 数据库中的敏感数据进行分类的建议进行了增强](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)                                               | 2022 年第 1 季度                   ||                                                                                                                                                                                                         |                           |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>正在将 ISO 27001 的旧版实现替换为新的 ISO 27001:2013

预计更改日期：2021 年 7 月

将从安全中心的监管合规性仪表板中删除 ISO 27001 的旧版实现。 如果要跟踪 ISO 27001 与安全中心的合规性，请针对所有相关管理组或订阅加入新的 ISO 27001:2013 标准，很快将会从仪表板中删除当前的旧版 ISO 27001。

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="安全中心的监管合规性仪表板，其中显示了有关删除 ISO 27001 的旧版实现的消息。" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="deprecating-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>弃用建议“应在计算机上解决 Log Analytics 代理运行状况问题”

预计更改日期：2021 年 7 月

我们发现，建议“应在计算机上解决 Log Analytics 代理运行状况问题”会因与安全中心的云安全态势管理 (CSPM) 不一致而影响安全分数。 通常，CSPM 与识别安全错误配置有关。 代理运行状况问题不适用于此类问题。

此外，和与安全中心相关的其他代理相比时，该建议也是异常的：这是唯一一个具有与运行状况问题相关的建议的代理。

该建议将被弃用。

由于此弃用，我们还将对安装 Log Analytics 代理的建议做少许更改（Log Analytics 代理应安装在...）。

此更改可能会影响安全分数。 对于大多数订阅，此更改应会导致分数增加，但在某些情况下，对安装建议的更新可能会导致分数降低。

> [!TIP]
> [资产清单](asset-inventory.md)页也会受此更改影响，因为该页面也会显示有关计算机是否被监视、未被监视或被部分监视的信息（指存在运行状况问题的代理的状态）。 


### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>资源管理器警报的 Azure Defender 的逻辑重组

**预计更改日期：** 2021 年 8 月

目前作为[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 计划的一部分提供了下列警报。

在对某些 Azure Defender 计划进行逻辑重组的过程中，我们将一些警报从 **适用于资源管理器的 Azure Defender** 移动到了 **适用于服务器的 Azure Defender**。

警报将根据如下两个主要原则进行组织：

- 跨许多 Azure 资源类型提供控制平面保护的警报将属于适用于资源管理器的 Azure Defender
- 保护特定工作负载的警报将被移动到与该工作负载相关的对应 Azure Defender 计划

以下的警报目前属于适用于资源管理器的 Azure Defender，由于本次更改，这些警报将被移动到适用于服务器的 Azure Defender：

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

详细了解[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 和[适用于服务器的 Azure Defender](defender-for-servers-introduction.md)。


### <a name="csv-exports-to-be-limited-to-20-mb"></a>CSV 导出限制为 20 MB

**预计更改日期：** 2021 年 8 月

导出安全中心建议数据时，当前对可下载的数据量没有限制。

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="安全中心的“下载 CSV 报表”按钮，用于导出建议数据。":::

进行此更改后，我们将设置 20 MB 的限制。

如果需要导出大量数据，请在选择数据前使用可用的筛选器，或选择订阅的子集并批量下载数据。

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="在 Azure 门户中筛选“订阅”。":::

详细了解如何[执行安全建议的 CSV 导出](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations)。

### <a name="enable-azure-defender-security-control-to-be-included-in-secure-score"></a>启用 Azure Defender 安全控件以在安全分数中增加得分

**预计更改日期：** 2021 年第 3 季度

安全中心的强化建议已分组到安全控件中。 每个控件都是相关安全建议的逻辑组，反映易受攻击的攻击面。 建议页面以及[安全控件及其简易](secure-score-security-controls.md#security-controls-and-their-recommendations)中的控件列表中清楚地显示了每个安全控件对总体安全分数的贡献。

自从引入“启用 Azure Defender”控件之后，该控件的最大可能分数一直是 0 分。 **通过这次更改，该控件将在安全分数中占一席之地。**

启用 Azure Defender 时，会将安全中心的免费模式的功能扩展到私有云和其他公有云中运行的工作负载，并在混合云工作负载中提供了统一的安全管理和威胁防护。 Azure Defender 的一些主要功能包括：为服务器集成了 Microsoft Defender for Endpoint 许可证、对虚拟机和容器注册表进行漏洞扫描、基于高级行为分析和机器学习的安全警报以及容器安全功能。 有关完整列表，请参阅 [Azure 安全中心免费版与已启用 Azure Defender 的版本的比较](security-center-pricing.md)。

进行此更改后，将影响不受 Azure Defender 保护的任何订阅的安全得分。 建议在进行此更改之前启用 Azure Defender，以确保不会对得分产生任何影响。 

在[快速入门：启用 Azure Defender](enable-azure-defender.md) 中了解详细信息。

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>针对用于对 SQL 数据库中的敏感数据进行分类的建议进行了增强

**预计更改日期：** 2022 年第 1 季度

“应用数据分类”安全控制中的“应对 SQL 数据库中的敏感数据进行分类”建议将替换为更符合 Microsoft 的数据分类策略的新版本 。 因此，建议的 ID 也会更改（当前 ID 为 b0df6f56-862d-4730-8597-38c0fd4ebd59）。


## <a name="next-steps"></a>后续步骤

有关产品的所有最新更改，请参阅 [Azure 安全中心的新增功能](release-notes.md)。