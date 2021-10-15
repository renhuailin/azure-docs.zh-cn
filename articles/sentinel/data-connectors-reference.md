---
title: 查找 Azure Sentinel 数据连接器 | Microsoft Docs
description: 了解 Azure Sentinel 数据连接器的特定配置步骤。
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: reference
ms.date: 08/12/2021
ms.author: bagol
ms.openlocfilehash: 3886d26c571246f42c54bb9aab45bae32e05b750
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362448"
---
# <a name="find-your-azure-sentinel-data-connector"></a>查找 Azure Sentinel 数据连接器

本文介绍如何在 Azure Sentinel 中部署数据连接器，其中列出所有受支持的内置数据连接器，还提供常规部署流量的链接和特定连接器所需的额外步骤。

> [!TIP]
> 某些数据连接器仅通过解决方案进行部署。 有关详细信息，请参阅 [Azure Sentinel 解决方案目录](sentinel-solutions-catalog.md)。 还可以在 [Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors)中找到其他社区构建的数据连接器。
>

## <a name="how-to-use-this-guide"></a>如何使用本指南

1. 首先，在右侧标题菜单中找到并选择适合你的产品、服务或设备的连接器。

    对于每个连接器，首先显示的信息是其数据引入方法。 这里显示的方法将是指向下列某一常规部署流程的链接，其中有你将数据源连接到 Azure Sentinel 所需的大部分信息：

    | 数据引入方法 | 带说明的链接文章 |
    | --- | --- |
    | **Azure 服务到服务集成** | [连接到 Azure、Windows、Microsoft 和 Amazon 服务](connect-azure-windows-microsoft-services.md) |
    | **使用 Syslog 的通用事件格式 (CEF)** | [将设备中 CEF 格式的日志提取到 Azure Sentinel](connect-common-event-format.md) |
    | **Azure Sentinel 数据收集器 API** | [将数据源连接到 Azure Sentinel 的数据收集器 API 以引入数据](connect-rest-api-template.md) |
    | **Azure Functions 和 REST API** | [使用 Azure Functions 将 Azure Sentinel 连接到数据源](connect-azure-functions-template.md) |
    | **Syslog** | [使用 Syslog 从基于 Linux 的源收集数据](connect-syslog.md) |
    | **自定义日志** | [使用 Log Analytics 代理将数据以自定义日志格式收集到 Azure Sentinel](connect-custom-logs.md) |
    |

    > [!NOTE]
    > “Azure 服务到服务集成”数据引入方法链接到其文章的三个不同部分，具体取决于连接器类型。 每个连接器的下列部分指定了该文章中它链接到的部分。

1. 部署到特定连接器时，请选择链接到其数据引入方法的相应文章，并使用下面相关部分中的信息和额外指南对该文章中的信息进行补充。

> [!TIP]
> - 许多数据连接器还可连同相关的分析规则、工作簿和 playbook，部署为 [Azure Sentinel 解决方案](sentinel-solutions.md)的一部分。 有关详细信息，请参阅 [Azure Sentinel 解决方案目录](sentinel-solutions-catalog.md)。
>
>
> - 更多数据连接器由 Azure Sentinel 社区提供，可在 Azure 市场中找到。 社区数据连接器的相关文档由创建连接器的组织负责。
>
>
> - 如果你有未列出或当前不受支持的数据源，则还可自行创建自定义连接器。 有关详细信息，请参阅[用于创建 Azure Sentinel 自定义连接器的资源](create-custom-connector.md)。
>

> [!IMPORTANT]
> 请注意，Azure Sentinel 数据连接器目前为预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense and Brand Protection（预览版）

| 连接器属性 | 说明|
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) <br><br>**部署前**：[启用安全图形 API（可选）](#enable-the-security-graph-api-optional)。 <br>**部署后**：[向函数应用分配必要的权限](#assign-necessary-permissions-to-your-function-app)|
| **Log Analytics 表** | agari_bpalerts_log_CL<br>agari_apdtc_log_CL<br>agari_apdpolicy_log_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-agari-functionapp |
| **API 凭据** | <li>客户端 ID<li>客户端机密<li>（可选：图形租户 ID、图形客户端 ID、图形客户端密码） |
| **供应商文档/<br>安装说明** | <li>[快速启动](https://developers.agari.com/agari-platform/docs/quick-start)<li>[Agari 开发人员网站](https://developers.agari.com/agari-platform) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPS) |
| **应用程序设置** | <li>clientID<li>clientSecret<li>workspaceID<li>workspaceKey<li>enableBrandProtectionAPI (true/false)<li>enablePhishingResponseAPI (true/false)<li>enablePhishingDefenseAPI (true/false)<li>resGroup（输入资源组）<li>functionName<li>subId（输入订阅 ID）<li>enableSecurityGraphSharing（true/false，详见下方）<br>如果将 enableSecurityGraphSharing 设置为 true，则必需（详见下方）：<li>GraphTenantId<li>GraphClientId<li>GraphClientSecret<li>logAnalyticsUri（可选） |
| **支持的服务** | [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support) |
| | |

### <a name="enable-the-security-graph-api-optional"></a>启用安全图形 API（可选）

> [!IMPORTANT]
> 如果执行此步骤，请在部署数据连接器之前执行。
>
Agari 函数应用允许通过安全图形 API 与 Azure Sentinel 共享威胁情报。 若要使用此功能，你需要启用 [Sentinel 威胁情报平台连接器](./connect-threat-intelligence-tip.md)，同时在 Azure Active Directory 中[注册应用程序](/graph/auth-register-app-v2)。

此过程将提供下面三条信息，供你在[部署函数应用](connect-azure-functions-template.md)时使用：图形租户 ID、图形客户端 ID 和图形客户端密码（请查看上表中的“应用程序设置”）  。

### <a name="assign-necessary-permissions-to-your-function-app"></a>向函数应用分配必要的权限

Agari 连接器使用环境变量来存储日志访问时间戳。 为了使应用程序能够写入此变量，必须将权限分配给系统分配的标识。

1. 在 Azure 门户中，导航到“函数应用”。
1. 在“函数应用”边栏选项卡中，从列表中选择“函数应用”，然后在“函数应用”导航菜单中的“设置”下选择“标识”。
1. 在“系统分配”选项卡中，将“状态”设置为“启用”  。
1. 选择“保存”，将出现“Azure 角色分配”按钮。 选择该文件夹。
1. 在“Azure 角色分配”屏幕中，选择“添加角色分配”。 将“作用域”设置为“订阅”，从“订阅”下拉菜单中选择订阅，并将“角色”设置为“应用配置数据所有者”。
1. 选择“保存”。


## <a name="ai-analyst-aia-by-darktrace-preview"></a>Darktrace 的 AI Analyst (AIA)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** <br><br>[为 AI Analyst 分配 CEF 日志转发功能](#configure-cef-log-forwarding-for-ai-analyst) |
| **Log Analytics 表** | CommonSecurityLog |
| **支持的服务** | [Darktrace](https://customerportal.darktrace.com/) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-analyst"></a>为 AI Analyst 分配 CEF 日志转发功能

配置 Darktrace，以通过 Log Analytics 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区。

1. 在 Darktrace Threat Visualizer 中，导航到“管理员”下主菜单中的“系统配置”页面 。
1. 从右侧菜单中，选择“模块”，再从可用的工作流集成中选择“Azure Sentinel”  。
1. 这将打开配置窗口。 找到“Azure Sentinel Syslog CEF”，然后选择“新建”来调出配置设置，除非已公开 。
1. 在“服务器配置”字段中，输入日志转发器的位置，并选择性地修改通信端口。 确保所选端口设置为 514，且任何中间防火墙都不阻止它。
1. 根据需要配置任何警报阈值、时间偏移量或其他设置。
1. 查看可能需要启用来修改 Syslog 语法的任何其他配置选项。
1. 启用“发送警报”并保存所作更改。

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** <br><br>[为 AI Vectra Detect 分配 CEF 日志转发功能](#configure-cef-log-forwarding-for-ai-vectra-detect)|
| **Log Analytics 表** | CommonSecurityLog |
| **支持的服务** | [Vectra AI](https://www.vectra.ai/support) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-vectra-detect"></a>为 AI Vectra Detect 分配 CEF 日志转发功能

配置 Vectra（X 系列）代理，以通过 Log Analytics 代理将 CEF 格式的 Syslog 消息转发到 Azure Sentinel 工作区。

在 Vectra 接口中，导航到“设置”>“通知”，然后选择“编辑 Syslog 配置”。 请按照以下说明建立连接：

- 添加新目标（日志转发器的主机名）
- 将端口设置为“514”
- 将协议设置为“UDP”
- 将格式设置为“CEF”
- 设置日志类型（选择所有可用的日志类型）
- 选择“保存”

可选择“测试”按钮，强制将一些测试事件发送到日志转发器。

有关详细信息，请查看“Cognito Detect Syslog 指南”（可从 Detect UI 中的资源页下载）。

## <a name="akamai-security-events-preview"></a>Akamai Security Events（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | 使用 Syslog 的[通用事件格式 (CEF)](connect-common-event-format.md)，采用 Kusto 函数分析程序 |
| **Log Analytics 表** | CommonSecurityLog |
| **Kusto 函数别名：** | AkamaiSIEMEvent |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-akamaisecurityevents-parser |
| **供应商文档/<br>安装说明** | [配置 SIEM 集成](https://developer.akamai.com/tools/integrations/siem)<br>[设置 CEF 连接器](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector)。 |
| **支持的服务** | [Akamai](https://www.akamai.com/us/en/support/) |
| | |

## <a name="alcide-kaudit"></a>Alcide kAudit

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | alcide_kaudit_activity_1_CL - Alcide kAudit 活动日志<br>alcide_kaudit_detections_1_CL - Alcide kAudit 检测<br>alcide_kaudit_selections_count_1_CL - Alcide kAudit 活动数<br>alcide_kaudit_selections_details_1_CL - Alcide kAudit 活动详细信息 |
| **供应商文档/<br>安装说明** | [Alcide kAudit 安装指南](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit) |
| **支持的服务** | [Alcide](https://www.alcide.io/company/contact-us/) |
| | |

## <a name="alsid-for-active-directory"></a>Alsid for Active Directory

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Log Analytics 代理 - 自定义日志**](connect-custom-logs.md) <br><br>[Alsid 的额外配置](#extra-configuration-for-alsid)|
| **Log Analytics 表** | AlsidForADLog_CL |
| **Kusto 函数别名：** | afad_parser |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-alsidforad-parser |
| **支持的服务** | [Alsid](https://www.alsid.com/contact-us/) |
| | |

### <a name="extra-configuration-for-alsid"></a>Alsid 的额外配置

1. **配置 Syslog 服务器**

    首先需要一个 Linux Syslog 服务器，Alsid for AD 会将日志发送到该服务器。 通常，可在 Ubuntu 上运行 rsyslog 。 

    然后，可按照需要配置此服务器，但建议能够在单独的文件中输出 AFAD 日志。 或者，可使用[快速启动模板](https://azure.microsoft.com/resources/templates/alsid-syslog-proxy/)来部署 Syslog 服务器和 Microsoft 代理。 如果使用该模板，可跳过[代理安装说明](connect-custom-logs.md#install-the-log-analytics-agent)。

1. **将 Alsid 配置为将日志发送到 Syslog 服务器**

    在“Alsid for AD”门户上，依次单击“系统”、“配置”和“Syslog”。 从这里，可以针对 Syslog 服务器创建新的 Syslog 警报。

    完成后，检查是否在服务器上将日志正确收集在单独的文件中（为此，可使用 AFAD 的 Syslog 警报配置中的“测试配置”按钮）。 如果使用了快速启动模板，则 Syslog 服务器将默认侦听 UDP 中的端口 514 和 TCP 中的端口 1514，而不通过 TLS。

## <a name="amazon-web-services---cloudtrail"></a>Amazon Web Services - CloudTrail

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[将 AWS CloudTrail 连接到 Azure Sentinel](connect-aws.md)** （主要连接器文章） |
| **Log Analytics 表** | AWSCloudTrail |
| **支持的服务** | Microsoft |
| | |

## <a name="apache-http-server"></a>Apache HTTP Server

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Log Analytics 代理 - 自定义日志**](connect-custom-logs.md) |
| **Log Analytics 表** | ApacheHTTPServer_CL |
| **Kusto 函数别名：** | ApacheHTTPServer |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-apachehttpserver-parser |
| **自定义日志示例文件：** | access.log 或 error.log |
| | |

## <a name="apache-tomcat"></a>Apache Tomcat

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Log Analytics 代理 - 自定义日志**](connect-custom-logs.md) |
| **Log Analytics 表** | Tomcat_CL |
| **Kusto 函数别名：** | TomcatEvent |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-ApacheTomcat-parser |
| **自定义日志示例文件：** | access.log 或 error.log |
| | |

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | 使用 Syslog 的[通用事件格式 (CEF)](connect-common-event-format.md)，采用 Kusto 函数分析程序 |
| **Log Analytics 表** | CommonSecurityLog |
| **Kusto 函数别名：** | ArubaClearPass |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-arubaclearpass-parser |
| **供应商文档/<br>安装说明** | 按照 Aruba 的说明[配置 ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm)。 |
| **支持的服务** | Microsoft |
| | |

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | Confluence_Audit_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-confluenceauditapi-functionapp |
| **API 凭据** | <li>ConfluenceAccessToken<li>ConfluenceUsername<li>ConfluenceHomeSiteName |
| **供应商文档/<br>安装说明** | <li>[API 文档](https://developer.atlassian.com/cloud/confluence/rest/api-group-audit/)<li>[有关获取凭据的要求和说明](https://developer.atlassian.com/cloud/confluence/rest/intro/#auth)<li>[查看审核日志](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 函数别名** | ConfluenceAudit |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-confluenceauditapi-parser |
| **应用程序设置** | <li>ConfluenceUsername<li>ConfluenceAccessToken<li>ConfluenceHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | Jira_Audit_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-jiraauditapi-functionapp |
| **API 凭据** | <li>JiraAccessToken<li>JiraUsername<li>JiraHomeSiteName |
| **供应商文档/<br>安装说明** | <li>[API 文档 - 审核记录](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)<li>[有关获取凭据的要求和说明](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/#authentication) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 函数别名** | JiraAudit |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-jiraauditapi-parser |
| **应用程序设置** | <li>JiraUsername<li>JiraAccessToken<li>JiraHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-active-directory"></a>Azure Active Directory

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[将 Azure Active Directory 数据连接到 Azure Sentinel](connect-azure-active-directory.md)** （主要连接器文章） |
| **许可先决条件/<br>成本信息** | <li>针对登录日志的 Azure Active Directory P1 或 P2 许可证<li>针对其他日志类型的 Any Azure AD 许可证（免费/O365/P1/P2）<br>可能会收取其他费用 |
| **Log Analytics 表** | SigninLogs<br>AuditLogs<br>AADNonInteractiveUserSignInLogs<br>AADServicePrincipalSignInLogs<br>AADManagedIdentitySignInLogs<br>AADProvisioningLogs<br>ADFSSignInLogs |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory 标识保护

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 API 的连接](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **许可先决条件/<br>成本信息** | [Azure AD Premium P2 订阅](https://azure.microsoft.com/pricing/details/active-directory/)<br>可能会收取其他费用 |
| **Log Analytics 表** | SecurityAlert |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-activity"></a>Azure 活动

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于诊断设置的连接，由 Azure Policy 进行管理](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)**<br><br>[升级到新的 Azure 活动连接器](#upgrade-to-the-new-azure-activity-connector) |
| **Log Analytics 表** | AzureActivity |
| **支持的服务** | Microsoft |
| | |

### <a name="upgrade-to-the-new-azure-activity-connector"></a>升级到新的 Azure 活动连接器

#### <a name="data-structure-changes"></a>数据结构更改

此连接器近期更改了其用于收集活动日志事件的后端机制。 它现在使用“诊断设置”管道。 如果仍在使用此连接器的旧方法，强烈建议升级到新版本，这将提供更好的功能，并增强与资源日志的一致性。 请参阅以下说明。

诊断设置方法发送的数据与旧方法从活动日志服务发送的数据相同，尽管对 AzureActivity 表的[结构进行了一些更改](../azure-monitor/essentials/activity-log.md#data-structure-changes) 。

下面是迁移到诊断设置管道后的一些关键改进：
- 改进了引入延迟（在事件发生后 2-3 分钟内而不是 15-20 分钟内引入事件）。
- 提高了可靠性。
- 改进的性能。
- 支持活动日志服务记录的所有类别的事件（旧机制仅支持部分类别，例如不支持服务运行状况事件）。
- 使用 Azure Policy 进行大规模管理。

请参阅 [Azure Monitor 文档](../azure-monitor/logs/data-platform-logs.md)，深入了解 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)和[诊断设置管道](../azure-monitor/essentials/diagnostic-settings.md)。

#### <a name="disconnect-from-old-pipeline"></a>断开与旧管道的连接

设置新的 Azure 活动日志连接器之前，必须将现有订阅与旧方法断开连接。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。 在连接器列表中，选择“Azure 活动”，然后单击右下角的“打开连接器页面”按钮 。

1. 在“说明”选项卡下“配置”部分的步骤 1 中，查看连接到旧方法的现有订阅列表（以便知道要将哪些订阅添加到新方法），然后单击下面的“全部断开”按钮，一次性断开所有连接  。

1. 按照[上表中链接的说明](connect-azure-windows-microsoft-services.md#diagnostic-settings-based-connections)继续设置新的连接器。

## <a name="azure-ddos-protection"></a>Azure DDoS 防护

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于诊断设置的连接](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **许可先决条件/<br>成本信息** | <li>你必须具有已配置的 [Azure DDoS 标准防护计划](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan)。<li>你必须配置了[已启用 Azure DDoS 标准的虚拟网络](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network)<br>可能会收取其他费用 |
| **Log Analytics 表** | AzureDiagnostics |
| **建议的诊断** | DDoSProtectionNotifications<br>DDoSMitigationFlowLogs<br>DDoSMitigationReports |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-defender"></a>Azure Defender

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[连接 Azure 安全中心的 Azure Defender 警报](connect-azure-security-center.md)** （主要连接器文章） |
| **Log Analytics 表** | SecurityAlert |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-defender-for-iot"></a>适用于 IoT 的 Azure Defender

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 API 的连接](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Log Analytics 表** | SecurityAlert |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-firewall"></a>Azure 防火墙

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于诊断设置的连接](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Log Analytics 表** | AzureDiagnostics |
| **建议的诊断** | AzureFirewallApplicationRule<br>AzureFirewallNetworkRule<br>AzureFirewallDnsProxy |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-information-protection"></a>Azure 信息保护

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure 服务到服务集成**](connect-azure-windows-microsoft-services.md) |
| **Log Analytics 表** | InformationProtectionLogs_CL |
| **支持的服务** | Microsoft |
| | |

有关详细信息，请参阅 [Azure 信息保护文档](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)。

## <a name="azure-key-vault"></a>Azure Key Vault

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于诊断设置的连接，由 Azure Policy 进行管理](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Log Analytics 表** | KeyVaultData |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS)

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于诊断设置的连接，由 Azure Policy 进行管理](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Log Analytics 表** | kube-apiserver<br>kube-audit<br>kube-audit-admin<br>kube-controller-manager<br>kube-scheduler<br>cluster-autoscaler<br>防护 |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-sql-databases"></a>Azure SQL 数据库

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于诊断设置的连接，由 Azure Policy 进行管理](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Log Analytics 表** | SQLSecurityAuditEvents<br>SQLInsights<br>AutomaticTuning<br>QueryStoreWaitStatistics<br>错误<br>DatabaseWaitStatistics<br>超时<br>块<br>死锁数<br>基本<br>InstanceAndAppAdvanced<br>WorkloadManagement<br>DevOpsOperationsAudit |
| **支持的服务** | Microsoft |
| | |

## <a name="azure-storage-account"></a>Azure 存储帐户

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于诊断设置的连接](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)**<br><br>[有关存储帐户诊断设置配置的说明](#notes-about-storage-account-diagnostic-settings-configuration) |
| **Log Analytics 表** | StorageBlobLogs<br>StorageQueueLogs<br>StorageTableLogs<br>StorageFileLogs |
| **建议的诊断** | **帐户资源**<li>事务<br>**Blob/队列/表/文件资源**<br><li>StorageRead<li>StorageWrite<li>StorageDelete<li>事务 |
| **支持的服务** | Microsoft |
| | |

### <a name="notes-about-storage-account-diagnostic-settings-configuration"></a>有关存储帐户诊断设置配置的说明

存储帐户（父）资源具有每种存储类型的其他（子）资源：文件、表、队列和 Blob。

为存储帐户配置诊断时，必须依次选择和配置以下内容：
- 父帐户资源，用于导出“事务”指标。
- 每种子存储类型的资源，用于导出所有日志和指标（请参见上表）。

你将只能看到实际为其定义了资源的存储类型。

## <a name="azure-web-application-firewall-waf"></a>Azure Web 应用程序防火墙 (WAF)

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于诊断设置的连接](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Log Analytics 表** | AzureDiagnostics |
| **建议的诊断** | **应用程序网关**<br><li>ApplicationGatewayAccessLog<li>ApplicationGatewayFirewallLog<br>**Front Door**<li>FrontdoorAccessLog<li>FrontdoorWebApplicationFirewallLog<br>**CDN WAF 策略**<li>WebApplicationFirewallLogs |
| **支持的服务** | Microsoft |
| | |


## <a name="barracuda-cloudgen-firewall"></a>Barracuda CloudGen 防火墙

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | CGFWFirewallActivity |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-barracudacloudfirewall-function |
| **供应商文档/<br>安装说明** | https://aka.ms/sentinel-barracudacloudfirewall-connector |
| **支持的服务** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="barracuda-waf"></a>Barracuda WAF

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Log Analytics 代理 - 自定义日志**](connect-custom-logs.md) |
| **Log Analytics 表** | Syslog |
| **供应商文档/<br>安装说明** | https://aka.ms/asi-barracuda-connector |
| **支持的服务** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | BetterMTDDeviceLog_CL<br>BetterMTDIncidentLog_CL<br>BetterMTDAppLog_CL<br>BetterMTDNetflowLog_CL |
| **供应商文档/<br>安装说明** | [BETTER MTD 文档](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration)<br><br>威胁策略设置，用于定义报告给 Azure Sentinel 的事件：<br><ol><li>在“Better MTD 控制台”中，选择侧边栏上的“策略” 。<li>选择你正在使用的策略的“编辑”按钮。<li>对于想要记录的每种事件类型，请转到“发送到集成”字段，然后选择“Sentinel” 。 |
| **支持的服务** | [Better Mobile](mailto:support@better.mobi) |
| | |


## <a name="beyond-security-besecure"></a>Beyond Security beSECURE

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | beSECURE_ScanResults_CL<br>beSECURE_ScanEvents_CL<br>beSECURE_Audit_CL |
| **供应商文档/<br>安装说明** | 访问“集成”菜单：<br><ol><li>选择“更多”菜单选项。<li>选择“服务器”<li>选择“集成”<li>启用 Azure Sentinel <li>在 beSECURE 配置中粘贴工作区 ID 和主密钥 。<li>选择“修改”。 |
| **支持的服务** | [Beyond Security](https://beyondsecurity.freshdesk.com/support/home) |
| | |


## <a name="blackberry-cylanceprotect-preview"></a>Blackberry CylancePROTECT（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | CylancePROTECT |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-cylanceprotect-parser |
| **供应商文档/<br>安装说明** | [Cylance Syslog 指南](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf) |
| **支持的服务** | Microsoft |
| | |

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec Data Loss Prevention (DLP)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | 使用 Syslog 的[通用事件格式 (CEF)](connect-common-event-format.md)，采用 Kusto 函数分析程序 |
| **Log Analytics 表** | CommonSecurityLog |
| **Kusto 函数别名：** | SymantecDLP |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-symantecdlp-parser |
| **供应商文档/<br>安装说明** | [配置“记录到 Syslog 服务器”操作](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US) |
| **支持的服务** | Microsoft |
| | |

## <a name="check-point"></a>检查点

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [日志导出程序 - Check Point 日志导出](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323) |
| **支持的服务** | [Check Point](https://www.checkpoint.com/support-services/contact-support/) |
| | |

## <a name="cisco-asa"></a>Cisco ASA

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [Cisco ASA 系列 CLI 配置指南](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html) |
| **支持的服务** | Microsoft |
| | |

## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** <br><br>[Cisco Firepower eStreamer 的额外配置](#extra-configuration-for-cisco-firepower-estreamer)|
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [eStreamer eNcore for Sentinel 操作指南](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html) |
| **支持的服务** | [Cisco](https://www.cisco.com/c/en/us/support/index.html)
| | |

### <a name="extra-configuration-for-cisco-firepower-estreamer"></a>Cisco Firepower eStreamer 的额外配置

1. **安装 Firepower eNcore 客户端**  
安装和配置 Firepower eNcore eStreamer 客户端。 有关详细信息，请参阅[完整的 Cisco 安装说明](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html)。

1. **从 GitHub 下载 Firepower 连接器**  
从 [Cisco GitHub 存储库](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector)下载适用于 Azure Sentinel 的最新版 Firepower eNcore 连接器。 如果计划使用 python3，请使用 [python3 eStreamer 连接器](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector/tree/python3)。

1. **使用 Azure/VM IP 地址创建 pkcs12 文件**  
在 Firepower 的“系统”>“集成”>“eStreamer”下，使用 VM 实例的公共 IP 创建 pkcs12 证书。 有关详细信息，请参阅[安装指南](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049443)。

1. **测试 Azure/VM 客户端与 FMC 之间的连接**  
将 pkcs12 文件从 FMC 复制到 Azure/VM 实例，并运行测试实用程序（./encore.sh 测试）来确保可建立连接。 有关详细信息，请参阅[设置指南](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049430)。

1. **配置 eNcore 来将数据流式传输到代理**  
配置 eNcore，以通过 TCP 将数据流式传输到 Log Analytics 代理。 默认情况下，会启用此项。 不过，可根据网络安全状况配置其他端口和流式传输协议。 还可将数据保存到文件系统。 有关详细信息，请参阅[配置 eNcore](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049433)。

## <a name="cisco-meraki-preview"></a>Cisco Meraki（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | CiscoMeraki |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-ciscomeraki-parser |
| **供应商文档/<br>安装说明** | [Meraki Device Reporting 文档](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) |
| **支持的服务** | Microsoft |
| | |

## <a name="cisco-umbrella-preview"></a>Cisco Umbrella（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | Cisco_Umbrella_dns_CL<br>Cisco_Umbrella_proxy_CL<br>Cisco_Umbrella_ip_CL<br>Cisco_Umbrella_cloudfirewall_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-CiscoUmbrellaConn-functionapp |
| **API 凭据** | <li>AWS 访问密钥 ID<li>AWS 机密访问密钥<li>AWS S3 存储桶名称 |
| **供应商文档/<br>安装说明** | <li>[记录到 Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 函数别名** | Cisco_Umbrella |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-ciscoumbrella-function |
| **应用程序设置** | <li>WorkspaceID<li>WorkspaceKey<li>S3Bucket<li>AWSAccessKeyId<li>AWSSecretAccessKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | CiscoUCS |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-ciscoucs-function |
| **供应商文档/<br>安装说明** | [设置 Syslog for Cisco UCS - Cisco](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) |
| **支持的服务** | Microsoft |
| | |

## <a name="citrix-analytics-security"></a>Citrix Analytics

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | CitrixAnalytics_SAlerts_CL |
| **供应商文档/<br>安装说明** | [将 Citrix 连接到 Azure Sentinel](https://aka.ms/Sentinel-Citrix-Connector) |
| **支持的服务** | [Citrix Systems](https://www.citrix.com/support/) |
| | |

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web 应用防火墙 (WAF)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | 若要配置 WAF，请参阅[支持 WIKI - 使用 NetScaler 进行 WAF 配置](https://support.citrix.com/article/CTX234174)。<br><br>若要配置 CEF 日志，请参阅[应用程序防火墙中的 CEF 日志记录支持](https://support.citrix.com/article/CTX136146)。<br><br>若要将日志转发到代理，请参阅[为审核日志记录配置 Citrix ADC 设备](https://docs.citrix.com/en-us/citrix-adc/current-release/system/audit-logging/configuring-audit-logging.html)。 |
| **支持的服务** | [Citrix Systems](https://www.citrix.com/support/) |
| | |

## <a name="cognni-preview"></a>Cognni（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | CognniIncidents_CL |
| **供应商文档/<br>安装说明** | **连接到 Cognni**<br><ol><li>转到 [Cognni 集成页面](https://intelligence.cognni.ai/integrations)。<li>在 Azure Sentinel 框上选择“连接”。<li>将 workspaceId 和 sharedKey（主要密钥）粘贴到 Cognni 集成屏幕上的字段中 。<li>选择“连接”按钮来完成配置。 |
| **支持的服务** | [Cognni](https://cognni.ai/contact-support/)
| | |

## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>CyberArk Enterprise Password Vault (EPV) Events（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [安全信息和事件管理 (SIEM) 应用程序](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) |
| **支持的服务** | [CyberArk](https://www.cyberark.com/customer-support/) |
| | |


## <a name="cyberpion-security-logs-preview"></a>Cyberpion Security Logs（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | CyberpionActionItems_CL |
| **供应商文档/<br>安装说明** | [获取 Cyberpion 订阅](https://azuremarketplace.microsoft.com/en/marketplace/apps/cyberpion1597832716616.cyberpion)<br>[将 Cyberpion 安全警报集成到 Azure Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) |
| **支持的服务** | [Cyberpion](https://www.cyberpion.com/) |
| | |

## <a name="domain-name-server"></a>域名服务器

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 Log Analytics 代理的连接](connect-azure-windows-microsoft-services.md#log-analytics-agent-based-connections)** |
| **Log Analytics 表** | DnsEvents<br>DnsInventory |
| **支持的服务** | Microsoft |
| | |

## <a name="dynamics-365"></a>Dynamics 365

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 API 的连接](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **许可先决条件/<br>成本信息** | <li>[Microsoft Dynamics 365 生产许可证](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)。 不适用于沙盒环境。<li>需要 Microsoft 365 企业版 [E3 或 E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) 订阅才能进行活动日志记录。<br>可能会收取其他费用 |
| **Log Analytics 表** | Dynamics365Activity |
| **支持的服务** | Microsoft |
| | |

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md)<br><br>[创建 API 用户](#create-an-api-user) |
| **Log Analytics 表** | ESETEnterpriseInspector_CL |
| **API 凭据** | <li>EEI 用户名<li>EEI 密码<li>基 URL |
| **供应商文档/<br>安装说明** | <li>[ESET Enterprise Inspector REST API 文档](https://help.eset.com/eei/1.5/en-US/api.html) |
| **连接器部署说明** | 通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM) |
| **支持的服务** | [ESET](https://support.eset.com/en) |
| | |
### <a name="create-an-api-user"></a>创建 API 用户

1. 使用管理员帐户登录 ESET Security Management Center/ESET PROTECT 控制台，依次选择“更多”选项卡和“用户”子选项卡 。
1. 选择“新增”按钮，然后添加本机用户 。
1. 为 API 帐户创建新用户。 可选：选择“家庭组”而不是“全部”来限制引入的检测  。
1. 在“权限集”选项卡下，分配“Enterprise Inspector 审阅者”权限集 。
1. 注销管理员帐户，再使用新的 API 凭据登录控制台进行验证，然后注销 API 帐户。

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md)<br><br>[配置要收集的 ESET SMC 日志](#configure-the-eset-smc-logs-to-be-collected) <br>[配置 OMS 代理，以 API 格式传递 ESET SMC 数据](#configure-oms-agent-to-pass-eset-smc-data-in-api-format)<br>[更改 OMS 代理配置以捕获标记 oms.api.eset 并分析结构化数据](#change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data)<br>[禁用自动配置并重启代理](#disable-automatic-configuration-and-restart-agent)|
| **Log Analytics 表** | eset_CL |
| **供应商文档/<br>安装说明** | [ESET Syslog 服务器文档](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html) |
| **支持的服务** | [ESET](https://support.eset.com/en) |
| | |

### <a name="configure-the-eset-smc-logs-to-be-collected"></a>配置要收集的 ESET SMC 日志

配置 rsyslog 以接受来自 Eset SMC IP 地址的日志。

```bash
    sudo -i
    # Set ESET SMC source IP address
    export ESETIP={Enter your IP address}

    # Create rsyslog configuration file
    cat > /etc/rsyslog.d/80-remote.conf << EOF
    \$ModLoad imudp
    \$UDPServerRun 514
    \$ModLoad imtcp
    \$InputTCPServerRun 514
    \$AllowedSender TCP, 127.0.0.1, $ESETIP
    \$AllowedSender UDP, 127.0.0.1, $ESETIP user.=alert;user.=crit;user.=debug;user.=emerg;user.=err;user.=info;user.=notice;user.=warning  @127.0.0.1:25224
    EOF

    # Restart rsyslog
    systemctl restart rsyslog
```

### <a name="configure-oms-agent-to-pass-eset-smc-data-in-api-format"></a>配置 OMS 代理，以 API 格式传递 ESET SMC 数据

为了轻松识别 Eset 数据，请将其推送到单独的表中并在代理中进行分析，以便简化和加快 Azure Sentinel 查询。 

在 /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsagent.conf 文件中，通过将类型更改为 `out_oms_api` 来修改 `match oms.**` 部分，以 API 对象的形式发送数据。
    
下面是完整 `match oms.**` 部分的示例：

```bash
    <match oms.** docker.**>
      type out_oms_api
      log_level info
      num_threads 5
      run_in_background false

      omsadmin_conf_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsadmin.conf
      cert_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.crt
      key_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.key

      buffer_chunk_limit 15m
      buffer_type file
      buffer_path /var/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/state/out_oms_common*.buffer

      buffer_queue_limit 10
      buffer_queue_full_action drop_oldest_chunk
      flush_interval 20s
      retry_limit 10
      retry_wait 30s
      max_retry_wait 9m
    </match>
```

### <a name="change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data"></a>更改 OMS 代理配置以捕获标记 oms.api.eset 并分析结构化数据

修改 /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsagent.d/syslog.conf 文件。 

例如：

```bash
    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.api.eset
    </source>

    <filter oms.api.**>
      @type parser
      key_name message
      format /(?<message>.*?{.*})/
    </filter>

    <filter oms.api.**>
      @type parser
      key_name message
      format json
    </filter>
```
### <a name="disable-automatic-configuration-and-restart-agent"></a>禁用自动配置并重启代理

例如： 

```bash
    # Disable changes to configuration files from Portal
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'

    # Restart agent
    sudo /opt/microsoft/omsagent/bin/service_control restart

    # Check agent logs
    tail -f /var/opt/microsoft/omsagent/log/omsagent.log
```

### <a name="configure-eset-smc-to-send-logs-to-connector"></a>配置 ESET SMC 以将日志发送到连接器

使用 BSD 样式和 JSON 格式配置 Eset 日志。

- 转到 Syslog 服务器配置来配置主机（连接器）、格式 BSD 和传输 TCP
- 转到“日志记录”部分并启用 JSON

有关详细信息，请参阅 Eset 文档。

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | ExabeamEvent |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-Exabeam-parser |
| **供应商文档/<br>安装说明** | [配置 Advanced Analytics 系统活动通知](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08) |
| **支持的服务** | Microsoft |
| | |

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [ExtraHop Detection SIEM Connector](https://aka.ms/asi-syslog-extrahop-forwarding) |
| **支持的服务** | [ExtraHop](https://www.extrahop.com/support/) |
| | |

## <a name="f5-big-ip"></a>F5 BIG-IP

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | F5Telemetry_LTM_CL<br>F5Telemetry_system_CL<br>F5Telemetry_ASM_CL |
| **供应商文档/<br>安装说明** | [将 F5 BIG-IP 与 Azure Sentinel 集成](https://aka.ms/F5BigIp-Integrate) |
| **支持的服务** | [F5 网络](https://support.f5.com/csp/home) |
| | |
## <a name="f5-networks-asm"></a>F5 Networks (ASM)

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [配置应用程序安全性事件日志记录](https://aka.ms/asi-syslog-f5-forwarding) |
| **支持的服务** | [F5 网络](https://support.f5.com/csp/home) |
| | |

## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint Cloud Access Security Broker (CASB)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [Forcepoint CASB 和 Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/) |
| **支持的服务** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [Forcepoint Cloud Security Gateway 和 Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/) |
| **支持的服务** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | ForcepointDLPEvents_CL |
| **供应商文档/<br>安装说明** | [Forcepoint Data Loss Prevention 和 Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/) |
| **支持的服务** | [Forcepoint](https://support.forcepoint.com/) |
| | |
## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [Forcepoint Next-Gen Firewall 和 Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/) |
| **支持的服务** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forgerock-common-audit-caud-for-cef-preview"></a>适用于 CEF 的 ForgeRock Common Audit (CAUD)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [请先安装 ForgeRock Common Audit (CAUD) for Azure Sentinel](https://github.com/javaservlets/SentinelAuditEventHandler) |
| **支持的服务** | [ForgeRock](https://www.forgerock.com/support) |
| | |

## <a name="fortinet"></a>Fortinet

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** <br><br>[将 Fortinet 日志发送到日志转发器](#send-fortinet-logs-to-the-log-forwarder) |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [Fortinet 文档库](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)<br>选择你的版本，然后使用“手册”和“日志消息参考”PDF 。 |
| **支持的服务** | [Fortinet](https://support.fortinet.com/) |
| | |
### <a name="send-fortinet-logs-to-the-log-forwarder"></a>将 Fortinet 日志发送到日志转发器

在 Fortinet 设备上打开 CLI，并运行以下命令：

```Console
config log syslogd setting
set status enable
set format cef
set port 514
set server <ip_address_of_Forwarder>
end
```

- 将服务器 IP 地址替换为日志转发器的 IP 地址。
- 将“Syslog 端口”设置为 514 或在转发器上的 Syslog 守护程序上设置的端口 。
- 若要在早期 FortiOS 版本中启用 CEF 格式，你可能需要运行命令集“csv 禁用”。


## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G Suite)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md)<br><br>[Google Reports API 的额外配置](#extra-configuration-for-the-google-reports-api) |
| **Log Analytics 表** | GWorkspace_ReportsAPI_admin_CL<br>GWorkspace_ReportsAPI_calendar_CL<br>GWorkspace_ReportsAPI_drive_CL<br>GWorkspace_ReportsAPI_login_CL<br>GWorkspace_ReportsAPI_mobile_CL<br>GWorkspace_ReportsAPI_token_CL<br>GWorkspace_ReportsAPI_user_accounts_CL<br> |
| **Azure 函数应用代码** | https://aka.ms/sentinel-GWorkspaceReportsAPI-functionapp |
| **API 凭据** | <li>GooglePickleString |
| **供应商文档/<br>安装说明** | <li>[API 文档](https://developers.google.com/admin-sdk/reports/v1/reference/activities)<li>在[执行 Google Workspace 域范围颁发机构委派](https://developers.google.com/admin-sdk/reports/v1/guides/delegation)处获取凭据<li>[将 token.pickle 文件转换为 pickle 字符串](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 函数别名** | GWorkspaceActivityReports |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-GWorkspaceReportsAPI-parser |
| **应用程序设置** | <li>GooglePickleString<li>WorkspaceID<li>workspaceKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

### <a name="extra-configuration-for-the-google-reports-api"></a>Google Reports API 的额外配置

在授权的重定向 URI 下添加 http://localhost:8081/ ，同时创建 [Web 应用程序凭据](https://developers.google.com/workspace/guides/create-credentials#web)。

1. [按照说明](https://developers.google.com/admin-sdk/reports/v1/quickstart/python)获取 credentials.json。
1. 若要获取 Google pickle 字符串，请运行[此 python 脚本](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode)（在包含 credentials.json 的路径中）。
1. 复制单引号中的 pickle 字符串输出并进行保存。 部署函数应用时将需要用到它。

## <a name="illusive-attack-management-system-ams-preview"></a>Illusive Attack Management System (AMS)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [Illusive Networks 管理员指南](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version) |
| **支持的服务** | [Illusive Networks](https://www.illusivenetworks.com/technical-support/) |
| | |

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF 网关（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [有关启用向 Azure Sentinel 发送 Imperva WAF 网关警报日志记录的步骤](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert) |
| **支持的服务** | [Imperva](https://www.imperva.com/support/technical-support/) |
| | |


## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | InfobloxNIOS |
| **Kusto 函数 URL：** | https://aka.ms/sentinelgithubparsersinfoblox |
| **供应商文档/<br>安装说明** | [NIOS SNMP 和 Syslog 部署指南](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf) |
| **支持的服务** | Microsoft |
| | |

## <a name="juniper-srx-preview"></a>Juniper SRX（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | JuniperSRX |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-junipersrx-parser |
| **供应商文档/<br>安装说明** | [为 SRX Branch 设备配置流量日志记录（安全策略日志）](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)<br>[配置系统日志记录](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502) |
| **支持的服务** | [Juniper Networks](https://support.juniper.net/support/) |
| | |


## <a name="microsoft-365-defender"></a>Microsoft 365 Defender

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[将 Microsoft 365 Defender 中的数据连接到 Azure Sentinel](connect-microsoft-365-defender.md)** （主要连接器文章） |
| **许可先决条件/<br>成本信息** | [Microsoft 365 Defender 的有效许可证](/microsoft-365/security/mtp/prerequisites)
| **Log Analytics 表** | SecurityAlert<br>SecurityIncident<br>DeviceEve<br>DeviceFileEvents<br>DeviceImageLoadEvents<br>DeviceInfo<br>DeviceLogonEvents<br>DeviceNetworkEvents<br>DeviceNetworkInfo<br>DeviceProcessEvents<br>DeviceRegistryEvents<br>DeviceFileCertificateInfo |
| **支持的服务** | Microsoft |
| | |

## <a name="microsoft-cloud-app-security-mcas"></a>Microsoft Cloud App Security (MCAS)

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 API 的连接](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>对于 Cloud Discovery 日志，请[在 Microsoft Cloud App Security 中启用 Azure Sentinel 作为 SIEM](/cloud-app-security/siem-sentinel) |
| **Log Analytics 表** | SecurityAlert - 针对警报<br>McasShadowItReporting - 针对 Cloud Discovery 日志 |
| **支持的服务** | Microsoft |
| | |

## <a name="microsoft-defender-for-endpoint"></a>用于终结点的 Microsoft Defender

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 API 的连接](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **许可先决条件/<br>成本信息** | [Microsoft Defender for Endpoint 部署的有效许可证](/microsoft-365/security/defender-endpoint/production-deployment)
| **Log Analytics 表** | SecurityAlert |
| **支持的服务** | Microsoft |
| | |

## <a name="microsoft-defender-for-identity"></a>Microsoft Defender for Identity

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 API 的连接](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Log Analytics 表** | SecurityAlert |
| **支持的服务** | Microsoft |
| | |

## <a name="microsoft-defender-for-office-365"></a>Microsoft Defender for Office 365

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 API 的连接](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **许可先决条件/<br>成本信息** | 你必须具有 [Office 365 ATP 计划 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) 的有效许可证
| **Log Analytics 表** | SecurityAlert |
| **支持的服务** | Microsoft |
| | |

## <a name="microsoft-office-365"></a>Microsoft Office 365

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 API 的连接](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **许可先决条件/<br>成本信息** | Office 365 部署必须与 Azure Sentinel 工作区在同一租户中。<br>可能会收取其他费用 |
| **Log Analytics 表** | OfficeActivity |
| **支持的服务** | Microsoft |
| | |

## <a name="morphisec-utpp-preview"></a>Morphisec UTPP（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | 使用 Syslog 的[通用事件格式 (CEF)](connect-common-event-format.md)，采用 Kusto 函数分析程序 |
| **Log Analytics 表** | CommonSecurityLog |
| **Kusto 函数别名：** | Morphisec |
| **Kusto 函数 URL** | https://aka.ms/sentinel-Morphiescutpp-parser |
| **支持的服务** | [Morphisec](https://support.morphisec.com/support/home) |
| | |


## <a name="netskope-preview"></a>Netskope（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | Netskope_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-netskope-functioncode |
| **API 凭据** | <li>Netskope API 令牌 |
| **供应商文档/<br>安装说明** | <li>[Netskope Cloud Security Platform](https://www.netskope.com/platform)<li>[Netskope API 文档](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v1-overview.html)<li>[获取 API 令牌](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v2-overview.html) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto 函数别名** | Netskope |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-netskope-parser |
| **应用程序设置** | <li>apikey<li>workspaceID<li>workspaceKey<li>uri（由区域而定，遵循架构：`https://<Tenant Name>.goskope.com`） <li>timeInterval（设置为 5）<li>logTypes<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Log Analytics 代理 - 自定义日志**](connect-custom-logs.md) |
| **Log Analytics 表** | NGINX_CL |
| **Kusto 函数别名：** | NGINXHTTPServer |
| **Kusto 函数 URL** | https://aka.ms/sentinel-NGINXHTTP-parser |
| **供应商文档/<br>安装说明** | [模块 ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html) |
| **自定义日志示例文件：** | access.log 或 error.log |
| **支持的服务** | Microsoft |
| | |

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) macOS（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | BSMmacOS_CL |
| **供应商文档/<br>安装说明** | [NXLog Azure Sentinel 用户指南](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **支持的服务** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-dns-logs-preview"></a>NXLog DNS 日志（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | DNS_Logs_CL |
| **供应商文档/<br>安装说明** | [NXLog Azure Sentinel 用户指南](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **支持的服务** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | LinuxAudit_CL |
| **供应商文档/<br>安装说明** |  [NXLog Azure Sentinel 用户指南](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **支持的服务** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="okta-single-sign-on-preview"></a>Okta 单一登录（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | Okta_CL |
| **Azure 函数应用代码** | https://aka.ms/sentineloktaazurefunctioncodev2 |
| **API 凭据** | <li>API 令牌 |
| **供应商文档/<br>安装说明** | <li>[Okta System Log API 文档](https://developer.okta.com/docs/reference/api/system-log/)<li>[创建 API 令牌](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)<li>[将 Okta SSO 连接到 Azure Sentinel](#okta-single-sign-on-preview) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPS) |
| **应用程序设置** | <li>apiToken<li>workspaceID<li>workspaceKey<li>uri（遵循 `https://<OktaDomain>/api/v1/logs?since=`。 [标识域命名空间](https://developer.okta.com/docs/reference/api-overview/#url-namespace)。） <li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |


## <a name="onapsis-platform-preview"></a>Onapsis 平台（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | 使用 Syslog 的[通用事件格式 (CEF)](connect-common-event-format.md)，采用 Kusto 查找和扩充函数<br><br>[配置 Onapsis 以将 CEF 日志发送到日志转发器](#configure-onapsis-to-send-cef-logs-to-the-log-forwarder) |
| **Log Analytics 表** | CommonSecurityLog |
| **Kusto 函数别名：** | incident_lookup |
| **Kusto 函数 URL** | https://aka.ms/sentinel-Onapsis-parser |
| **支持的服务** | [Onapsis](https://onapsis.force.com/s/login/) |
| | |

### <a name="configure-onapsis-to-send-cef-logs-to-the-log-forwarder"></a>配置 Onapsis 以将 CEF 日志发送到日志转发器

请查看 Onapsis 产品内帮助来设置到 Log Analytics 代理的日志转发。

1. 转到“设置”>“第三方集成”>“防护警报”，按照 Azure Sentinel 的说明操作。
1. 确保 Onapsis 控制台可连接到安装了代理的日志转发器计算机。 应使用 TCP 将日志发送到端口 514。

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [One Identity Safeguard for Privileged Sessions 管理指南](https://aka.ms/sentinel-cef-oneidentity-forwarding) |
| **支持的服务** | [统一标识](https://support.oneidentity.com/) |
| | |


## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Log Analytics 代理 - 自定义日志**](connect-custom-logs.md) |
| **Log Analytics 表** | OracleWebLogicServer_CL |
| **Kusto 函数别名：** | OracleWebLogicServerEvent |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-OracleWebLogicServer-parser |
| **供应商文档/<br>安装说明** | [Oracle WebLogic Server 文档](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html) |
| **自定义日志示例文件：** | server.log |
| **支持的服务** | Microsoft |
| | |

## <a name="orca-security-preview"></a>Orca Security（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | OrcaAlerts_CL |
| **供应商文档/<br>安装说明** | [Azure Sentinel 集成](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration) |
| **支持的服务** | [Orca Security](http://support.orca.security/) |
| | |


## <a name="ossec-preview"></a>OSSEC（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | 使用 Syslog 的[通用事件格式 (CEF)](connect-common-event-format.md)，采用 Kusto 函数分析程序 |
| **Log Analytics 表** | CommonSecurityLog |
| **Kusto 函数别名：** | OSSECEvent |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-OSSEC-parser |
| **供应商文档/<br>安装说明** | [OSSEC 文档](https://www.ossec.net/docs/)<br>[通过 syslog 发送警报](https://www.ossec.net/docs/docs/manual/output/syslog-output.html) |
| **支持的服务** | Microsoft |
| | |


## <a name="palo-alto-networks"></a>Palo Alto Networks

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [通用事件格式 (CEF) 配置指南](https://aka.ms/asi-syslog-paloalto-forwarding)<br>[配置 Syslog 监视](https://aka.ms/asi-syslog-paloalto-configure) |
| **支持的服务** | [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support) |
| | |


## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 活动日志（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | Perimeter81_CL |
| **供应商文档/<br>安装说明** | [Perimeter 81 文档](https://support.perimeter81.com/docs/360012680780) |
| **支持的服务** | [Perimeter 81](https://support.perimeter81.com/) |
| | |


## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) Email Security（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | ProofpointPOD_message_CL<br>ProofpointPOD_maillog_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-proofpointpod-functionapp |
| **API 凭据** | <li>ProofpointClusterID<li>ProofpointToken |
| **供应商文档/<br>安装说明** | <li>[登录 Proofpoint Community](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public)<li>[Proofpoint API 文档和说明](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 函数别名** | ProofpointPOD |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-proofpointpod-parser |
| **应用程序设置** | <li>ProofpointClusterID<li>ProofpointToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | ProofPointTAPClicksPermitted_CL<br>ProofPointTAPClicksBlocked_CL<br>ProofPointTAPMessagesDelivered_CL<br>ProofPointTAPMessagesBlocked_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinelproofpointtapazurefunctioncode |
| **API 凭据** | <li>API 用户名<li>API 密码 |
| **供应商文档/<br>安装说明** | <li>[Proofpoint SIEM API 文档](https://help.proofpoint.com/Threat_Insight_Dashboard/API_Documentation/SIEM_API) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPS) |
| **应用程序设置** | <li>apiUsername<li>apiUsername<li>uri（设置为 `https://tap-api-v2.proofpoint.com/v2/siem/all?format=json&sinceSeconds=300`）<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | PulseConnectSecure |
| **Kusto 函数 URL：** | https://aka.ms/sentinelgithubparserspulsesecurevpn |
| **供应商文档/<br>安装说明** | [配置 Syslog](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm) |
| **支持的服务** | Microsoft |
| | |


## <a name="qualys-vm-knowledgebase-kb-preview"></a>Qualys VM KnowledgeBase (KB)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md)<br><br>[Qualys VM KB 的额外配置](#extra-configuration-for-the-qualys-vm-kb) |
| **Log Analytics 表** | QualysKB_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-qualyskb-functioncode |
| **API 凭据** | <li>API 用户名<li>API 密码 |
| **供应商文档/<br>安装说明** | <li>[QualysVM API 用户指南](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto 函数别名** | QualysKB |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-qualyskb-parser |
| **应用程序设置** | <li>apiUsername<li>apiUsername<li>uri（按区域，详见 [API 服务器列表](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348)。 遵循架构 `https://<API Server>/api/2.0`。<li>WorkspaceID<li>WorkspaceKey<li>filterParameters（添加到 URI 的末尾，由 `&` 分隔。 无空格。）<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm-kb"></a>Qualys VM KB 的额外配置

1. 使用管理员帐户登录 Qualys Vulnerability Management 控制台，依次选择“用户”选项卡和“用户”子选项卡 。
1. 选择“新建”下拉菜单，然后选择“用户” 。
1. 为 API 帐户创建用户名和密码。
1. 在“用户角色”选项卡中，确保帐户角色设置为“管理员”，并且允许访问 GUI 和 API   
1. 注销管理员帐户，再使用新的 API 凭据登录控制台进行验证，然后注销 API 帐户。
1. 使用管理员帐户重新登录到控制台，然后修改 API 帐户的“用角色”，移除对 GUI 的访问权限。
1. 保存所有更改。

## <a name="qualys-vulnerability-management-vm-preview"></a>Qualys Vulnerability Management (VM)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md)<br><br>[Qualys VM 的额外配置](#extra-configuration-for-the-qualys-vm) <br>[手动部署 - 配置函数应用后](#manual-deployment---after-configuring-the-function-app)|
| **Log Analytics 表** | QualysHostDetection_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinelqualysvmazurefunctioncode |
| **API 凭据** | <li>API 用户名<li>API 密码 |
| **供应商文档/<br>安装说明** | <li>[QualysVM API 用户指南](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPS) |
| **应用程序设置** | <li>apiUsername<li>apiUsername<li>uri（按区域，详见 [API 服务器列表](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348)。 遵循架构 `https://<API Server>/api/2.0/fo/asset/host/vm/detection/?action=list&vm_processed_after=`。<li>WorkspaceID<li>WorkspaceKey<li>filterParameters（添加到 URI 的末尾，由 `&` 分隔。 无空格。）<li>timeInterval（设置为 5。 如果修改，请相应地更改函数应用计时器触发器。）<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm"></a>Qualys VM 的额外配置

1. 使用管理员帐户登录 Qualys Vulnerability Management 控制台，依次选择“用户”选项卡和“用户”子选项卡 。
1. 选择“新建”下拉菜单，然后选择“用户” 。
1. 为 API 帐户创建用户名和密码。
1. 在“用户角色”选项卡中，确保帐户角色设置为“管理员”，并且允许访问 GUI 和 API   
1. 注销管理员帐户，再使用新的 API 凭据登录控制台进行验证，然后注销 API 帐户。
1. 使用管理员帐户重新登录到控制台，然后修改 API 帐户的“用角色”，移除对 GUI 的访问权限。
1. 保存所有更改。

### <a name="manual-deployment---after-configuring-the-function-app"></a>手动部署 - 配置函数应用后

**配置 host.json 文件**

由于可能会引入大量的 Qualys 主机检测数据，这可能导致执行时间超过 5 分钟这一默认的函数应用超时。 在消耗计划下将默认超时持续时间调高到最长 10 分钟，让函数应用有更多的时间来执行。

1. 在函数应用中，选择函数应用名称，然后选择“应用服务编辑器”边栏选项卡。
1. 选择“转到”来打开编辑器，然后选择 wwwroot 目录下的 host.json 文件  。
1. 在 `managedDependancy` 行的上面添加 `"functionTimeout": "00:10:00",` 行。
1. 确保编辑器右上角显示“已保存”，然后退出编辑器。

如果需要更长的超时持续时间，请考虑升级到[应用服务计划](../azure-functions/functions-scale.md)。


## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | SalesforceServiceCloud_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-SalesforceServiceCloud-functionapp |
| **API 凭据** | <li>Salesforce API 用户名<li>Salesforce API 密码<li>Salesforce 安全令牌<li>Salesforce 使用者密钥<li>Salesforce 使用者机密 |
| **供应商文档/<br>安装说明** | [Salesforce REST API 开发人员指南](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)<br>在“设置授权”下，使用“会话 ID”方法而不是 OAuth 。 |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 函数别名** | SalesforceServiceCloud |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-SalesforceServiceCloud-parser |
| **应用程序设置** | <li>SalesforceUser<li>SalesforcePass<li>SalesforceSecurityToken<li>SalesforceConsumerKey<li>SalesforceConsumerSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |


## <a name="security-events-windows"></a>安全事件 (Windows)

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[连接到 Windows 服务器以收集安全事件](connect-windows-security-events.md)** （主要连接器文章） |
| **Log Analytics 表** | SecurityEvents |
| **支持的服务** | Microsoft |
| | |

有关详细信息，请参阅[不安全的协议工作簿设置](./get-visibility.md#use-built-in-workbooks)。

## <a name="sentinelone-preview"></a>SentinelOne（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) <br><br>[SentinelOne 的额外配置](#extra-configuration-for-sentinelone)|
| **Log Analytics 表** | SentinelOne_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-SentinelOneAPI-functionapp |
| **API 凭据** | <li>SentinelOneAPIToken<li>SentinelOneUrl (`https://<SOneInstanceDomain>.sentinelone.net`) |
| **供应商文档/<br>安装说明** | <li>https://`<SOneInstanceDomain>`.sentinelone.net/api-doc/overview<li>请参阅下面的说明 |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 函数别名** | SentinelOne |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-SentinelOneAPI-parser |
| **应用程序设置** | <li>SentinelOneAPIToken<li>SentinelOneUrl<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

### <a name="extra-configuration-for-sentinelone"></a>SentinelOne 的额外配置

按照说明获取凭据。

1. 使用管理员用户凭据登录 SentinelOne 管理控制台。
1. 在管理控制台上选择“设置”。
1. 在“设置”视图中选择“用户” 
1. 选择“新建用户”。
1. 输入新的控制台用户的信息。
1. 在“角色”中选择“管理员”。
1. 选择“保存”
1. 保存新用户的凭据，以便在数据连接器中使用。

## <a name="sonicwall-firewall-preview"></a>SonicWall 防火墙（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [日志 > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm)<br>选择 facility local4 和 ArcSight 用作 Syslog 格式。  |
| **支持的服务** | [SonicWall](https://www.sonicwall.com/support/) |
| | | 


## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | SophosCloudOptix_CL |
| **供应商文档/<br>安装说明** | [与 Azure Sentinel 集成](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html)，跳过第一个步骤。<br>[Sophos 查询示例](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html) |
| **支持的服务** | [Sophos](https://secure2.sophos.com/en-us/support.aspx) |
| | |


## <a name="sophos-xg-firewall-preview"></a>Sophos XG 防火墙（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | SophosXGFirewall |
| **Kusto 函数 URL：** | https://aka.ms/sentinelgithubparserssophosfirewallxg |
| **供应商文档/<br>安装说明** | [添加 syslog 服务器](https://docs.sophos.com/nsg/sophos-firewall/18.5/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html) |
| **支持的服务** | Microsoft |
| | |

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | secRMM_CL |
| **供应商文档/<br>安装说明** | [secRMM Azure Sentinel 管理员指南](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) |
| **支持的服务** | [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx) |
| | |


## <a name="squid-proxy-preview"></a>Squid Proxy（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Log Analytics 代理 - 自定义日志**](connect-custom-logs.md) |
| **Log Analytics 表** | SquidProxy_CL |
| **Kusto 函数别名：** | SquidProxy |
| **Kusto 函数 URL** | https://aka.ms/sentinel-squidproxy-parser |
| **自定义日志示例文件：** | access.log 或 cache.log |
| **支持的服务** | Microsoft |
| | |

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md) |
| **Log Analytics 表** | SymantecICDx_CL |
| **供应商文档/<br>安装说明** | [配置 Microsoft Azure Sentinel (Log Analytics) 转发器](https://techdocs.broadcom.com/us/en/symantec-security-software/integrated-cyber-defense/integrated-cyber-defense-exchange/1-4-3/Forwarders/configuring-forwarders-v131944722-d2707e17438.html) |
| **支持的服务** | [Broadcom Symantec](https://support.broadcom.com/security) |
| | |


## <a name="symantec-proxysg-preview"></a>Symantec ProxySG（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | SymantecProxySG |
| **Kusto 函数 URL：** | https://aka.ms/sentinelgithubparserssymantecproxysg |
| **供应商文档/<br>安装说明** | [将访问日志发送到 Syslog 服务器](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html) |
| **支持的服务** | Microsoft |
| | |


## <a name="symantec-vip-preview"></a>Symantec VIP（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | SymantecVIP |
| **Kusto 函数 URL：** | https://aka.ms/sentinelgithubparserssymantecvip |
| **供应商文档/<br>安装说明** | [配置 syslog](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US) |
| **支持的服务** | Microsoft |
| | |


## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [安全 Syslog/CEF 日志记录](https://thy.center/ss/link/syslog) |
| **支持的服务** | [Thycotic](https://thycotic.force.com/support/s/) |
| | |

## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | 使用 Syslog 的[通用事件格式 (CEF)](connect-common-event-format.md)，采用 Kusto 函数分析程序 |
| **Log Analytics 表** | CommonSecurityLog |
| **Kusto 函数别名：** | TrendMicroDeepSecurity |
| **Kusto 函数 URL** | https://aka.ms/TrendMicroDeepSecurityFunction |
| **供应商文档/<br>安装说明** | [将 Deep Security 事件转发到 Syslog 或 SIEM 服务器](https://aka.ms/Sentinel-trendMicro-connectorInstructions) |
| **支持的服务** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | 使用 Syslog 的[通用事件格式 (CEF)](connect-common-event-format.md)，采用 Kusto 函数分析程序 |
| **Log Analytics 表** | CommonSecurityLog |
| **Kusto 函数别名：** | TrendMicroTippingPoint |
| **Kusto 函数 URL** | https://aka.ms/sentinel-trendmicrotippingpoint-function |
| **供应商文档/<br>安装说明** | 以 ArcSight CEF Format v4.2 格式发送 Syslog 消息。 |
| **支持的服务** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-vision-one-xdr-preview"></a>Trend Micro Vision One (XDR)（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | TrendMicro_XDR_CL |
| **API 凭据** | <li>API 令牌 |
| **供应商文档/<br>安装说明** | <li>[Trend Micro Vision One API](https://automation.trendmicro.com/xdr/home)<li>[获取第三方访问的 API 密钥](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys) |
| **连接器部署说明** | 通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM) |
| **支持的服务** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | CarbonBlackEvents_CL<br>CarbonBlackAuditLogs_CL<br>CarbonBlackNotifications_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinelcarbonblackazurefunctioncode |
| **API 凭据** | **API 访问级别**（针对“审核”和“事件”日志） ：<li>API ID<li>API 密钥<br><br>**SIEM 访问级别**（针对“通知”事件）：<li>SIEM API ID<li>SIEM API 密钥 |
| **供应商文档/<br>安装说明** | <li>[Carbon Black API 文档](https://developer.carbonblack.com/reference/carbon-black-cloud/cb-defense/latest/rest-api/)<li>[创建 API 密钥](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPS) |
| **应用程序设置** | <li>apiId<li>apiKey<li>WorkspaceID<li>WorkspaceKey<li>uri（按区域；[查看选项列表](https://community.carbonblack.com/t5/Knowledge-Base/PSC-What-URLs-are-used-to-access-the-APIs/ta-p/67346)。 遵循架构 `https://<API URL>.conferdeploy.net`。）<li>timeInterval（设置为 5）<li>SIEMapiId（如果引入“通知”事件）<li>SIEMapiKey（如果引入“通知”事件）<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

## <a name="vmware-esxi-preview"></a>VMware ESXi（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | VMwareESXi |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-vmwareesxi-parser |
| **供应商文档/<br>安装说明** | [在 ESXi 3.5 和 4.x 上启用 syslog](https://kb.vmware.com/s/article/1016621)<br>[在 ESXi 主机上配置 Syslog](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html) |
| **支持的服务** | Microsoft |
| | |

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Syslog**](connect-syslog.md) |
| **Log Analytics 表** | Syslog |
| **Kusto 函数别名：** | WatchGuardFirebox |
| **Kusto 函数 URL：** | https://aka.ms/sentinel-watchguardfirebox-parser |
| **供应商文档/<br>安装说明** | [Microsoft Azure Sentinel 集成指南](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html) |
| **支持的服务** | [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview) |
| | |

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | 请联系 [WireX 支持人员](https://wirexsystems.com/contact-us/)，配置 NFP 解决方案来发送 CEF 格式的 Syslog 消息。 |
| **支持的服务** | [WireX Systems](mailto:support@wirexsystems.com) |
| | |


## <a name="windows-firewall"></a>Windows 防火墙

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[基于 Log Analytics 代理的连接](connect-azure-windows-microsoft-services.md#log-analytics-agent-based-connections)** |
| **Log Analytics 表** | WindowsFirewall |
| **支持的服务** | Microsoft |
| | |

## <a name="windows-security-events"></a>Windows 安全事件

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **Azure 服务到服务集成：<br>[连接到 Windows 服务器以收集安全事件](connect-windows-security-events.md)** （主要连接器文章） |
| **Log Analytics 表** | SecurityEvents |
| **支持的服务** | Microsoft |
| | |

## <a name="workplace-from-facebook-preview"></a>Facebook Workplace（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md)<br><br>[配置 Webhook](#configure-webhooks) <br>[将回叫 URL 添加到 Webhook 配置](#add-callback-url-to-webhook-configuration)|
| **Log Analytics 表** | Workplace_Facebook_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-WorkplaceFacebook-functionapp |
| **API 凭据** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken |
| **供应商文档/<br>安装说明** | <li>[配置 Webhook](https://developers.facebook.com/docs/workplace/reference/webhooks)<li>[配置权限](https://developers.facebook.com/docs/workplace/reference/permissions) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 函数别名** | Workplace_Facebook |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-WorkplaceFacebook-parser |
| **应用程序设置** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

### <a name="configure-webhooks"></a>配置 Webhook

1. 使用管理员用户凭据登录到 Workplace。
1. 在管理员面板中，选择“集成”。
1. 在“所有集成”视图中，选择“创建自定义集成” 。
1. 输入名称和说明，然后选择“创建”。
1. 在“集成详细信息”面板中，显示应用机密并复制它 。
1. 在“集成权限”面板中，设置所有读取权限。 有关详细信息，请参阅[权限页](https://developers.facebook.com/docs/workplace/reference/permissions)。

### <a name="add-callback-url-to-webhook-configuration"></a>将回叫 URL 添加到 Webhook 配置

1. 打开函数应用的页面，转到“函数”列表，然后选择“获取函数 URL”并复制它 。
1. 返回到 Facebook Workplace。 在“配置 Webhook”面板中，在每个选项卡上将“回叫 URL”设置为你在上一步中复制的函数 URL，将“验证令牌”设置为你在自动部署期间接收的或在手动部署期间输入的值  。
1. 选择“保存”  。

## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense（预览版）

Zimperium Mobile Threat Defense 数据连接器可将 Zimperium 威胁日志连接到 Azure Sentinel，以查看仪表板、创建自定义警报并改进调查。 此连接器可帮助更深入了解组织的移动威胁态势，并增强安全操作能力。 有关更多说明，请参阅相关文章。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Zimperium 连接到 Azure Sentinel](#zimperium-mobile-thread-defense-preview)。

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Sentinel 数据收集器 API**](connect-rest-api-template.md)<br><br>[配置和连接 Zimperium MTD](#configure-and-connect-zimperium-mtd) |
| **Log Analytics 表** | ZimperiumThreatLog_CL<br>ZimperiumMitigationLog_CL |
| **供应商文档/<br>安装说明** | [Zimperium 客户支持门户](https://support.zimperium.com/)（需要登录） |
| **支持的服务** | [Zimperium](https://www.zimperium.com/support) |
| | |

### <a name="configure-and-connect-zimperium-mtd"></a>配置和连接 Zimperium MTD

1. 在 zConsole 中，选择导航栏上的“管理”。
1. 选择“集成”选项卡。
1. 依次选择“威胁报告”按钮和“添加集成”按钮 。
1. 创建集成：
    1. 从可用集成中，选择“Microsoft Azure Sentinel”。
    1. 输入工作区 ID 和主密钥，然后选择“下一步” 。
    1. 填入 Azure Sentinel 集成的名称。
    1. 选择想要将威胁数据推送到 Azure Sentinel 的筛选器级别。
    1. 选择“完成”。

## <a name="zoom-reports-preview"></a>Zoom Reports（预览版）

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Azure Functions 和 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 表** | Zoom_CL |
| **Azure 函数应用代码** | https://aka.ms/sentinel-ZoomAPI-functionapp |
| **API 凭据** | <li>ZoomApiKey<li>ZoomApiSecret |
| **供应商文档/<br>安装说明** | <li>[通过 Zoom 使用 JWT 获取凭据](https://marketplace.zoom.us/docs/guides/auth/jwt) |
| **连接器部署说明** | <li>通过 Azure 资源管理器 (ARM) 模板进行[单击部署](connect-azure-functions-template.md?tabs=ARM)<li>[手动部署](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 函数别名** | 缩放 |
| **Kusto 函数 URL/<br>分析程序配置说明** | https://aka.ms/sentinel-ZoomAPI-parser |
| **应用程序设置** | <li>ZoomApiKey<li>ZoomApiSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri（可选） |
| **支持的服务** | Microsoft |
| | |

## <a name="zscaler"></a>Zscaler

| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | **使用 Syslog 的 [通用事件格式 (CEF)](connect-common-event-format.md)** |
| **Log Analytics 表** | CommonSecurityLog |
| **供应商文档/<br>安装说明** | [Zscaler 和 Microsoft Azure Sentinel 部署指南](https://aka.ms/ZscalerCEFInstructions) |
| **支持的服务** | [Zscaler](https://help.zscaler.com/submit-ticket-links) |
| | |


## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA)（预览版）


| 连接器属性 | 说明 |
| --- | --- |
| **数据引入方法** | [**Log Analytics 代理 - 自定义日志**](connect-custom-logs.md)<br><br>[Zscaler Private Access 的额外配置](#extra-configuration-for-zscaler-private-access) |
| **Log Analytics 表** | ZPA_CL |
| **Kusto 函数别名：** | ZPAEvent |
| **Kusto 函数 URL** | https://aka.ms/sentinel-zscalerprivateaccess-parser |
| **供应商文档/<br>安装说明** | [Zscaler Private Access 文档](https://help.zscaler.com/zpa)<br>另请参阅下述内容 |
| **支持的服务** | Microsoft |
| | |

### <a name="extra-configuration-for-zscaler-private-access"></a>Zscaler Private Access 的额外配置

按照下面的配置步骤，将 Zscaler Private Access 日志提取到 Azure Sentinel 中。 有关这些步骤的更多详细信息，请参阅 [Azure Monitor 文档](../azure-monitor/agents/data-sources-json.md)。 Zscaler Private Access 日志通过日志流式处理服务 (LSS) 进行传输。 有关详细信息，请参阅 [LSS 文档](https://help.zscaler.com/zpa/about-log-streaming-service)。

1. 配置[日志接收器](https://help.zscaler.com/zpa/configuring-log-receiver)。 配置日志接收器时，选择 JSON 作为日志模板 。
1. 下载配置文件 [zpa.conf](https://aka.ms/sentinel-zscalerprivateaccess-conf)。

    ```bash
    wget -v https://aka.ms/sentinel-zscalerprivateaccess-conf -O zpa.conf
    ```

1. 登录到安装了 Azure Log Analytics 代理的服务器。
1. 将 zpa.conf 复制到 /etc/opt/microsoft/omsagent/`workspace_id`/conf/omsagent.d/ 文件夹。
1. 编辑 zpa.conf，如下所示：
    1. 指定已设置 Zscaler 日志接收器来将日志转发到的端口（行 4）
    1. 将 `workspace_id` 替换为工作区 ID 的实际值（行 14、15、16、19）
1. 保存更改，并使用以下命令重启 Linux 服务的 Azure Log Analytics 代理：

    ```bash
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```
可在 ZScaler Private Access 连接器页面上，或在 Log Analytics 工作区的代理管理页上找到工作区 ID 的值。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Sentinel 解决方案目录](sentinel-solutions-catalog.md)
- [Azure Sentinel 中的威胁情报集成](threat-intelligence-integration.md)
