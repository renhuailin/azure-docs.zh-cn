---
title: Azure Sentinel 新增功能存档
description: 介绍 Azure Sentinel 至少六个月前新增的功能和变更的功能。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 06/15/2021
ms.openlocfilehash: 979855c0ad6a19c03c0c2c23cde935bc03cba157
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179346"
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

## <a name="february-2021"></a>2021 年 2 月

- [网络安全成熟度模型认证 (CMMC) 工作簿](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [第三方数据连接器](#third-party-data-connectors)
- [实体页中的 UEBA 见解（公共预览版）](#ueba-insights-in-the-entity-page-public-preview)
- [改进的事件搜索（公共预览版）](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>网络安全成熟度模型认证 (CMMC) 工作簿

Azure Sentinel CMMC 工作簿提供了一种机制，用于查看与整个 Microsoft 产品组合（包括 Microsoft 安全产品/服务、Office 365、Teams、Intune、Windows 虚拟桌面等）中的 CMMC 控件一致的日志查询。

CMMC 工作簿使安全架构师、工程师、安全操作分析员、管理者和 IT 专业人员能够在态势感知的情况下了解到云工作负载安全状况。 此外，还提供了有关选择、设计、部署和配置 Microsoft 产品/服务的建议，以便与相应的 CMMC 要求和实践保持一致。

即使不需要遵守 CMMC，CMMC 工作簿也有助于构建安全运营中心、开发警报、直观呈现威胁和提供对工作负载的态势感知。

访问 Azure Sentinel 的“工作簿”区域中的 CMMC 工作簿。 选择“模板”，然后搜索“CMMC” 。

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="打开和关闭 C M M C 工作簿指南的 GIF 录制。" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


有关详细信息，请参阅：

- [Azure Sentinel 网络安全成熟度模型认证 (CMMC) 工作簿](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [可视化和监视数据](monitor-your-data.md)


### <a name="third-party-data-connectors"></a>第三方数据连接器

我们的第三方集成集合不断扩大，最近 2 个月添加了 30 款连接器。 列表如下：

- [Agari Phishing Defense 和 Agari Brand Protection](connect-agari-phishing-defense.md)
- [Akamai 安全事件](connect-akamai-security-events.md)
- [Alsid for Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace（之前称为 G Suite）](connect-google-workspace.md)
- [Imperva WAF 网关](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS 日志](connect-nxlog-dns.md)
- [NXLog Linux 审核](connect-nxlog-linuxaudit.md)
- [Onapsis 平台](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys 漏洞管理知识库](connect-data-sources.md)
- [Salesforce 服务云](connect-salesforce-service-cloud.md)
- [SonicWall 防火墙](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>实体页中的 UEBA 见解（公共预览版）

Azure Sentinel 实体详细信息页提供了一个[见解窗格](identify-threats-with-entity-behavior-analytics.md#entity-insights)，其中显示了有关实体的行为见解，有助于快速识别异常和安全威胁。

如果[已启用 UEBA](ueba-enrichments.md)，并且时间范围设为至少 4 天，那么此见解窗格现在还将包括有关 UEBA 见解的以下新部分：

|部分  |说明  |
|---------|---------|
|**UEBA 见解**     | 汇总异常用户活动： <br>- 跨地理位置、设备和环境<br>- 跨时间和频率（与用户自己的历史记录相比） <br>- 与对等方的行为进行比较 <br>- 与组织的行为进行比较     |
|**基于安全组成员身份的用户对等方**     |   列出基于 Azure AD 安全组成员身份的用户对等方，为安全操作团队提供共享类似权限的其他用户的列表。  |
|**Azure 订阅的用户访问权限**     |     显示用户对 Azure 订阅的访问权限 - 可直接访问或通过 Azure AD 组/服务主体访问。   |
|**与用户相关的威胁指示器**     |  列出与用户活动中表示的 IP 地址相关的已知威胁的集合。 威胁按威胁类型和系列列出，并由 Microsoft 威胁情报服务进行扩充。       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>改进的事件搜索（公共预览版）

我们改进了 Azure Sentinel 事件搜索体验，让你能够在调查特定威胁时通过事件更快地导航。

现在，在 Azure Sentinel 中搜索事件时，可按以下事件详细信息进行搜索：

- ID
- 标题
- 产品
- 所有者
- 标记

## <a name="january-2021"></a>2021 年 1 月

- [分析规则向导：改进的查询编辑体验（公共预览版）](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az.SecurityInsights PowerShell 模块（公共预览版）](#azsecurityinsights-powershell-module-public-preview)
- [SQL 数据库连接器](#sql-database-connector)
- [Dynamics 365 连接器（公共预览版）](#dynamics-365-connector-public-preview)
- [改进的事件注释](#improved-incident-comments)
- [专用 Log Analytics 群集](#dedicated-log-analytics-clusters)
- [逻辑应用托管标识](#logic-apps-managed-identities)
- [通过分析规则预览关系图改进的规则优化](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>分析规则向导：改进的查询编辑体验（公共预览版）

现在，Azure Sentinel 的计划分析规则向导对编写和编辑查询提供了以下增强功能：

-   可扩展的编辑窗口，提供更多屏幕空间来查看查询。
-   查询代码中突出显示关键字。
-   扩展了自动补全支持。
-   实时查询验证。 现在，查询中的错误在滚动条中显示为红色块，在“设置规则逻辑”选项卡名称中显示为一个红点。 此外，不能保存带有错误的查询。

有关详细信息，请参阅[创建自定义分析规则以检测威胁](detect-threats-custom.md)。
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az.SecurityInsights PowerShell 模块（公共预览版）

Azure Sentinel 现支持新的 PowerShell 模块 [Az SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/)。

Az.SecurityInsights 模块支持常见的 Azure Sentinel 用例，例如与事件交互来更改状态、严重性和所有者等内容，为事件添加注释和标签，以及创建书签。

虽然建议为 CI/CD 管道使用 [Azure 资源管理器 (ARM)](../azure-resource-manager/templates/index.yml) 模板，但 Az.SecurityInsights 模块对于部署后的任务非常有用，而且面向 SOC 自动化。  例如，SOC 自动化可能包含配置数据连接器、创建分析规则或向分析规则添加自动化操作的步骤。

有关详细信息（包括可用 cmdlet 、参数说明和示例的完整列表和描述），请参阅 [Az.SecurityInsights PowerShell 文档](/powershell/module/az.securityinsights/)。

### <a name="sql-database-connector"></a>SQL 数据库连接器

Azure Sentinel 现提供 Azure SQL 数据库连接器，可将数据库的审核和诊断日志流式传输到 Azure Sentinel，并持续监视所有实例中的活动。

Azure SQL 是一个完全托管的平台即服务 (PaaS) 数据库引擎，可在没有用户参与的情况下处理大部分的数据库管理功能，例如升级、修补、备份和监视。

有关详细信息，请参阅[连接 Azure SQL 数据库诊断和审核日志](connect-azure-sql-logs.md)。

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365 连接器（公共预览版）

Azure Sentinel 现提供 Microsoft Dynamics 365 连接器，可将 Dynamics 365 应用程序的用户、管理员和支持活动日志收集到 Azure Sentinel 中。 你可使用此数据来帮助审核正在执行的所有数据处理操作，并分析其是否存在安全漏洞。

有关详细信息，请参阅[将 Dynamics 365 活动日志连接到 Azure Sentinel](connect-dynamics-365.md)。

### <a name="improved-incident-comments"></a>改进的事件注释

分析员使用事件注释来协作处理事件，从而手动记录过程和步骤，或将其作为 playbook 的一部分进行记录。 

我们改进了事件注释体验，让你能够设置注释的格式，还可编辑或删除现有注释。

有关详细信息，请参阅[从 Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。
### <a name="dedicated-log-analytics-clusters"></a>专用 Log Analytics 群集

Azure Sentinel 现支持将专用 Log Analytics 群集作为部署选项。 建议在以下情况中考虑使用专用群集：

- 每天向 Azure Sentinel 工作区引入超过 1 TB 的数据
- Azure 注册中有多个 Azure Sentinel 工作区

当同一群集上有多个工作区时，可通过专用群集使用客户管理的密钥、密码箱、双重加密和更快跨工作区查询等功能。

有关详细信息，请参阅 [Azure Monitor 日志专用群集](../azure-monitor/logs/logs-dedicated-clusters.md)。

### <a name="logic-apps-managed-identities"></a>逻辑应用托管标识

Azure Sentinel 现支持对 Azure Sentinel 逻辑应用连接器使用托管标识，这样你就可直接向特定 playbook 授予权限以在 Azure Sentinel 上操作，而无需创建额外的标识。

- 如果没有托管标识，逻辑应用连接器需要具有 Azure Sentinel RBAC 角色的单独标识，才能在 Azure Sentinel 上运行。 单独的标识可以是 Azure AD 用户或服务主体，例如 Azure AD 注册的应用程序。

- 如果在逻辑应用中打开托管标识支持，会将逻辑应用注册到 Azure AD 并提供对象 ID。 请使用 Azure Sentinel 中的对象 ID 在 Azure Sentinel 工作区中为逻辑应用分配 Azure RBAC 角色。 

有关详细信息，请参阅：

- [在 Azure 逻辑应用中用托管标识进行身份验证](../logic-apps/create-managed-service-identity.md)
- [Azure Sentinel 逻辑应用连接器文档](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>通过分析规则预览关系图改进的规则优化（公共预览版）

Azure Sentinel 现可帮助你更好地优化分析规则，从而帮助提高其准确性并减少干扰。

在“设置规则逻辑”选项卡上编辑分析规则后，请在右侧找到“结果模拟”区域 。 

选择“使用当前数据进行测试”，让 Azure Sentinel 对分析规则的最后 50 次运行进行模拟。 这会生成一个图，显示该规则根据所评估的原始事件数据本会生成的警报的平均数量。 

有关详细信息，请参阅[定义规则查询逻辑和配置设置](detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)。

## <a name="december-2020"></a>2020 年 12 月

- [80 个新的内置搜寻查询](#80-new-built-in-hunting-queries)
- [Log Analytics 代理改进](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 个新的内置搜寻查询
 
Azure Sentinel 的内置搜寻查询可让 SOC 分析员缩小当前检测覆盖范围的差距，并激发新的搜寻线索。

Azure Sentinel 的本次更新包括新的搜寻查询，它们可覆盖整个 MITRE ATT&CK 框架矩阵：

- **集合**
- **命令和控制**
- **凭据访问**
- **发现**
- **执行**
- **外泄**
- **影响**
- **初始访问**
- **持久性**
- **特权提升**

添加的搜寻查询旨在帮助查找环境中的可疑活动。 尽管它们可能既会返回合法活动，又会返回潜在的恶意活动，但它们在指导搜寻方面很有用。 

如果在运行这些查询后，你对结果很有信心，那么你可能会想将这些结果转换为分析规则，或者将搜寻结果添加到现有或新的事件中。

所有添加的查询均可通过 Azure Sentinel 搜寻页面获得。 有关详细信息，请参阅[通过 Azure Sentinel 搜寻威胁](hunting.md)。

### <a name="log-analytics-agent-improvements"></a>Log Analytics 代理改进

Azure Sentinel 用户会从以下 Log Analytics 代理改进中受益：

- 支持更多操作系统，包括 CentOS 8、RedHat 8 和 SUSE Linux 15。
- 除了 Python 2 外，还支持 Python 3

Azure Sentinel 使用 Log Analytics 代理向工作区发送事件，包括 Windows 安全事件、Syslog 事件和 CEF 日志等。

> [!NOTE]
> Log Analytics 代理有时被称为 OMS 代理或 Microsoft Monitoring Agent (MMA)。 
> 

有关详细信息，请参阅 [Log Analytics 文档](../azure-monitor/agents/log-analytics-agent.md)和 [Log Analytics 代理发行说明](https://github.com/microsoft/OMS-Agent-for-Linux/releases)。
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
>[了解警报](get-visibility.md)