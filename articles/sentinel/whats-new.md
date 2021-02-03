---
title: Azure Sentinel 中的新增功能
description: 本文介绍了过去几个月内 Azure Sentinel 中的新增功能。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 0eb3b8feda09d931654d1781ea37737d68a1b377
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526630"
---
# <a name="whats-new-in-azure-sentinel"></a>Azure Sentinel 中的新增功能

本文列出了 Azure Sentinel 中添加的最新功能，以及在 Azure Sentinel 中提供增强的用户体验的相关服务中的新增功能。

有关更早交付的功能的信息，请参阅 [技术社区博客](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)。

记录的功能目前以预览版提供。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。


> [!TIP]
> 跨 Microsoft 的威胁搜寻团队将查询、行动手册、工作簿和笔记本加入 [Azure Sentinel 社区](https://github.com/Azure/Azure-Sentinel)，其中包括你的团队可以改编和使用的特定 [搜寻查询](https://github.com/Azure/Azure-Sentinel) 。 
>
> 你还可以参与！ 加入我们的 [Azure Sentinel 威胁销售 GitHub 社区](https://github.com/Azure/Azure-Sentinel/wiki)。
> 

## <a name="january-2021"></a>2021 年 1 月

- [SecurityInsights PowerShell 模块 (公开预览版) ](#azsecurityinsights-powershell-module-public-preview)
- [SQL 数据库连接器](#sql-database-connector)
- [改进的事件评论](#improved-incident-comments)
- [专用 Log Analytics 群集](#dedicated-log-analytics-clusters)
- [逻辑应用托管标识](#logic-apps-managed-identities)
- [通过分析规则预览关系图改进了规则优化](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="azsecurityinsights-powershell-module-public-preview"></a>SecurityInsights PowerShell 模块 (公开预览版) 

Azure Sentinel 现在支持新的 [Az SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell 模块。

**SecurityInsights** 模块支持常见的 Azure Sentinel 用例，例如与事件交互以更改 statues、严重性、所有者等、向事件添加注释和标签以及创建书签。

尽管我们建议使用 Azure 资源管理器 CI/CD 管道 [ (ARM)](/azure/azure-resource-manager/templates/) 模板，但 **SecurityInsights** 模块对于后期部署任务非常有用，并且针对 SOC 自动化。  例如，SOC automation 可能包含配置数据连接器、创建分析规则或向分析规则添加自动化操作的步骤。

有关详细信息，包括可用 cmdlet 的完整列表和说明、参数说明和示例，请参阅 [SecurityInsights PowerShell 文档](/powershell/module/az.securityinsights/)。

### <a name="sql-database-connector"></a>SQL 数据库连接器

Azure Sentinel 现在提供 Azure SQL 数据库连接器，可以将数据库审核和诊断日志流式传输到 Azure Sentinel，并在所有实例中持续监视活动。

Azure SQL 是一种完全托管的平台即服务 (PaaS) 数据库引擎，可处理大多数数据库管理功能，例如升级、修补、备份和监视，无需用户参与。

有关详细信息，请参阅 [连接 AZURE SQL 数据库诊断和审核日志](connect-azure-sql-logs.md)。

### <a name="improved-incident-comments"></a>改进的事件评论

分析师使用事件评论来对事件进行协作，手动记录过程和步骤，或将其作为操作手册的一部分进行记录。 

我们改进的事件注释体验使你可以设置注释的格式，以及编辑或删除现有注释。

有关详细信息，请参阅 [从 Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。
### <a name="dedicated-log-analytics-clusters"></a>专用 Log Analytics 群集

Azure Sentinel 现在支持将专用 Log Analytics 群集作为部署选项。 建议在以下情况考虑使用专用群集：

- **每天从 1 Tb** 引入 Azure Sentinel 工作区
- Azure 注册中 **有多个 Azure Sentinel 工作区**

当同一群集上有多个工作区时，专用群集可让你使用客户管理的密钥、密码箱、双加密和更快跨工作区查询等功能。

有关详细信息，请参阅 [Azure Monitor 记录专用群集](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters)。

### <a name="logic-apps-managed-identities"></a>逻辑应用托管标识

Azure Sentinel 现在支持 Azure Sentinel 逻辑应用连接器的托管标识，这使你可以直接向特定操作手册授予权限，以便在 Azure Sentinel 上操作，而不是创建额外的标识。

- 如果 **没有托管标识**，则逻辑应用连接器需要具有 AZURE sentinel RBAC 角色的单独标识，才能在 azure sentinel 上运行。 单独的标识可以是 Azure AD 用户或服务主体，如 Azure AD 注册的应用程序。

- 在 **逻辑应用中打开托管标识支持** 会将逻辑应用注册到 Azure AD，并提供对象 ID。 使用 Azure Sentinel 中的对象 ID 在 Azure Sentinel 工作区中使用 Azure RBAC 角色分配逻辑应用。 

有关详细信息，请参阅：

- [在 Azure 逻辑应用中用托管标识进行身份验证](/azure/logic-apps/create-managed-service-identity)
- [Azure Sentinel 逻辑应用连接器文档](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>通过分析规则预览关系图 (公开预览版来改进规则优化) 

Azure Sentinel 现在可帮助你更好地优化分析规则，帮助你提高准确性并减少干扰。

在 " **设置规则逻辑** " 选项卡上编辑分析规则后，请在右侧找到 " **结果模拟** " 区域。 

选择 " **使用当前数据测试** "，让 Azure Sentinel 运行分析规则最后50运行的模拟。 将生成一个关系图，以显示根据计算出的原始事件数据，该规则生成的警报的平均数量。 

有关详细信息，请参阅 [定义规则查询逻辑和配置设置](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)。

## <a name="december-2020"></a>2020 年 12 月

- [80新的内置搜寻查询](#80-new-built-in-hunting-queries)
- [Log Analytics 代理改进](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80新的内置搜寻查询
 
Azure Sentinel 的内置搜寻查询可让 SOC 分析人员更容易地减少当前检测范围中的缺口，并 ignite 新的搜寻线索。

Azure Sentinel 的此更新包括新的搜寻查询，可跨 MITRE ATT&CK framework 矩阵提供覆盖：

- **集合**
- **命令和控制**
- **凭据访问**
- **发现**
- **执行**
- **外泄**
- **影响**
- **初始访问**
- **持久性**
- **权限升级**

添加的搜索查询旨在帮助您在您的环境中查找可疑活动。 尽管它们可能会返回合法活动和潜在的恶意活动，但它们在指导你的搜寻时非常有用。 

如果在运行这些查询后，你可以确信结果，你可能想要将其转换为分析规则或将搜索结果添加到现有或新的事件中。

所有添加的查询均可通过 Azure Sentinel 搜寻页面获得。 有关详细信息，请参阅 [通过 Azure Sentinel 寻找威胁](hunting.md)。

### <a name="log-analytics-agent-improvements"></a>Log Analytics 代理改进

Azure Sentinel 用户从以下 Log Analytics 代理改进中受益：

- **支持更多操作系统**，包括 CentOS 8、RedHat 8 和 SUSE Linux 15。
- 除了 Python 2 外，还 **支持 python 3**

Azure Sentinel 使用 Log Analytics 代理向工作区发送事件，包括 Windows 安全事件、Syslog 事件、CEF 日志等。

> [!NOTE]
> Log Analytics 代理有时称为 OMS 代理或 Microsoft Monitoring Agent (MMA) 。 
> 

有关详细信息，请参阅 [Log Analytics 文档](/azure/azure-monitor/platform/log-analytics-agent) 和 [Log Analytics 代理发行说明](https://github.com/microsoft/OMS-Agent-for-Linux/releases)。
## <a name="november-2020"></a>2020 年 11 月

- [监视 Azure Sentinel 中的逻辑应用行动手册](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Microsoft 365 Defender 连接器 (公共预览版) ](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>监视 Azure Sentinel 中的逻辑应用行动手册

Azure Sentinel 现在集成了 [Azure 日志应用](/azure/logic-apps/)（一种云服务，可帮助你计划、自动执行和协调任务、业务流程和工作流）。

使用 Azure 中的 Azure 逻辑应用作为操作手册，可以在创建事件时或会审和使用事件时自动调用操作手册。 

为了深入了解行动手册的运行状况、性能和使用情况（包括使用 Azure 逻辑应用添加的任何应用程序），我们添加了名为 "执行 **手册运行状况监视**" 的 [Azure 工作簿](/azure/azure-monitor/platform/workbooks-overview)。 

使用 " **行动手册" 健康状况监视** 工作簿监视行动手册的运行状况，或查找成功或失败的运行量中的异常。 

" **行动手册" 健康状况监视** 工作簿现已在 Azure Sentinel 模板库中提供：

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="示例行动手册运行状况监视工作簿":::

有关详细信息，请参阅：

- [逻辑应用文档](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Azure Monitor 文档](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender 连接器 (公共预览版) 
 
使用适用于 Azure Sentinel 的 Microsoft 365 Defender 连接器，你可以将高级搜寻日志流式传输 (一种类型的原始事件数据) 从 Microsoft 365 Defender 转换到 Azure Sentinel。 

通过将 [Microsoft defender For endpoint (MDATP) ](/windows/security/threat-protection/) 集成到 [Microsoft 365 defender](/microsoft-365/security/mtp/microsoft-threat-protection) 安全机制，你现在可以使用 Microsoft 365 Defender 连接器收集 microsoft defender for endpoint 高级搜寻事件，并将它们直接流式传输到 Azure Sentinel 工作区中的新用途构建的表中。 

Azure Sentinel 表建立在 Microsoft 365 Defender 门户中使用的同一架构上，并提供对完整的高级搜寻日志集的完全访问权限。 

有关详细信息，请参阅 [将数据从 Microsoft 365 Defender 连接到 Azure Sentinel](connect-microsoft-365-defender.md)。

> [!NOTE]
> Microsoft 365 Defender 以前称为 Microsoft 威胁防护或 MTP。 Microsoft Defender for Endpoint 以前称为 "Microsoft Defender 高级威胁防护" 或 MDATP。
> 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[机载 Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[了解警报](quickstart-get-visibility.md)
