---
title: Azure 安全日志记录和审核 | Microsoft Docs
description: 了解 Azure 中提供的日志以及可获取的安全见解。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: f7481e22b9d03d7f49f4ee383bb52c4772631c16
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178917"
---
# <a name="azure-security-logging-and-auditing"></a>Azure 安全日志记录和审核

Azure 提供多种不同的可配置安全审核和日志记录选项，帮助你识别安全策略和机制方面的差距。 本文介绍如何生成、收集和分析 Azure 上托管的服务的安全日志。

> [!Note]
> 本文中的某些建议可能会导致数据、网络或计算资源使用量增加，还可能导致许可或订阅成本增加。

## <a name="types-of-logs-in-azure"></a>Azure 中的日志类型

云应用程序很复杂，包含很多移动部件。 日志记录数据可以提供有关应用程序的见解，并帮助你：

- 排查过去的问题，或避免潜在的问题
- 提高应用程序性能或可维护性
- 自动执行本来需要手动干预的操作

Azure 日志划分为以下类型：
* **控制/管理日志** 提供有关 Azure 资源管理器 CREATE、UPDATE 和 DELETE 操作的信息。 有关详细信息，请参阅 [Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)。

* **数据平面日志** 提供作为 Azure 资源使用情况的一部分引发的事件的相关信息。 此类日志的示例是虚拟机 (VM) 中的 Windows 事件系统、安全性、应用程序日志以及通过 Azure Monitor 配置的[诊断日志](../../azure-monitor/essentials/platform-logs-overview.md)。

* **已处理的事件** 提供已以用户名义处理的分析事件/警报的相关信息。 此类日志的示例是 [Azure 安全中心警报](../../security-center/security-center-managing-and-responding-alerts.md)，[Azure 安全中心](../../security-center/security-center-introduction.md)已处理和分析了订阅，并提供简明的安全警报。

下表列出了 Azure 中最重要的日志类型：

| 日志类别 | 日志类型 | 使用情况 | 集成 |
| ------------ | -------- | ------ | ----------- |
|[活动日志](../../azure-monitor/essentials/platform-logs-overview.md)|Azure 资源管理器资源上的控制平面事件|    提供见解，方便用户了解对订阅中的资源执行的操作。|    Rest API、[Azure Monitor](../../azure-monitor/essentials/platform-logs-overview.md)|
|[Azure 资源日志](../../azure-monitor/essentials/platform-logs-overview.md)|关于订阅中 Azure 资源管理器资源操作频繁生成的数据| 提供见解，以便深入了解资源本身执行的操作。| Azure Monitor|
|[Azure Active Directory 报告](../../active-directory/reports-monitoring/overview-reports.md)|日志和报告 | 报告有关用户和组管理的用户登录活动和系统活动信息。|[图形 API](../../active-directory/develop/microsoft-graph-intro.md)|
|[虚拟机和云服务](../../azure-monitor/vm/monitor-virtual-machine.md)|Windows 事件日志服务和 Linux Syslog|   在虚拟机上捕获系统数据和日志记录数据，并将这些数据传输到所选的存储帐户中。|   Azure Monitor 中的 Windows（使用 Windows Azure 诊断 [[WAD](../../azure-monitor/agents/diagnostics-extension-overview.md)] 存储）和 Linux|
|[Azure 存储分析](/rest/api/storageservices/fileservices/storage-analytics)|存储执行日志记录并为存储帐户提供指标数据|提供相关信息，以便深入了解如何跟踪请求、分析使用情况趋势以及诊断存储帐户的问题。| REST API 或[客户端库](/dotnet/api/overview/azure/storage)|
|[网络安全组 (NSG) 流日志](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|采用 JSON 格式，并根据规则显示出站和入站流|显示有关通过网络安全组的入口和出口 IP 流量的信息。|[Azure 网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application Insights](../../azure-monitor/app/app-insights-overview.md)|日志、异常和自定义诊断|  提供多个平台上面向 Web 开发人员的应用程序性能监视 (APM) 服务。| REST API，[Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[处理数据/安全警报](../../security-center/security-center-introduction.md)|   Azure 安全中心警报、Azure Monitor 日志警报|    提供安全信息和警报。|  REST API，JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>与本地 SIEM 系统进行日志集成
[集成安全中心警报](../../security-center/security-center-partner-integration.md)介绍如何将安全中心警报以及 Azure 诊断日志和 Azure 审核日志收集的虚拟机安全事件与 Azure Monitor 日志或 SIEM 解决方案同步。

## <a name="next-steps"></a>后续步骤

- [审核和日志记录](management-monitoring-overview.md)：通过维护可视性和快速响应及时安全警报来保护数据。

- [Azure 中的安全日志记录和审核日志收集](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)：实施这些设置可确保 Azure 实例收集正确的安全和审核日志。

- [配置网站集的审核设置](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)：如果你是网站集管理员，请检索单个用户的操作历史记录，以及在特定日期范围内执行的操作的历史记录。

- [在 Microsoft 365 安全中心内搜索审核日志](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)：使用 Microsoft 365 安全中心搜索统一的审核日志，并查看组织中的用户和管理员活动。