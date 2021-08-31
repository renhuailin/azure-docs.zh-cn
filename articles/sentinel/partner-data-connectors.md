---
title: 适用于 Azure Sentinel 的合作伙伴数据连接器 | Microsoft Docs
description: 了解适用于 Azure Sentinel 的所有合作伙伴数据连接器。
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: 25b09b496ff505bb37452b80355cae705af8936d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723521"
---
# <a name="azure-sentinel-partner-data-connectors"></a>Azure Sentinel 合作伙伴数据连接器

本文按字母顺序列出了 Azure Sentinel 支持的来自合作伙伴组织的非 Microsoft 数据连接器。 如果了解要查找的连接器，请在此页中滚动到其名称或搜索其名称，或者使用文章右侧或顶部的链接。

- 若要启用和配置所列连接器，请参阅[连接数据源](connect-data-sources.md)以及各自列表中的链接。
- 有关 Microsoft 编写的、服务到服务的数据连接器的信息，请参阅[服务到服务集成](connect-data-sources.md#service-to-service-integration)。
- 有关 Azure Sentinel 支持模型和“支持者”字段的信息，请参阅[数据连接器支持](connect-data-sources.md#data-connector-support)。

> [!IMPORTANT]
> 以下许多 Azure Sentinel 合作伙伴数据连接器目前以预览版提供。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense and Brand Protection（预览版）

Agari Phishing Defense and Brand Protection 连接器将品牌防护和仿冒防御解决方案日志连接到 Azure Sentinel。

有关详细信息，请参阅 [Agari 开发人员网站](https://developers.agari.com/agari-platform)和[将 Agari Phishing Defense and Brand Protection 连接到 Azure Sentinel](connect-agari-phishing-defense.md)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：[Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support)

## <a name="ai-analyst-aia-by-darktrace-preview"></a>Darktrace 的 AI Analyst (AIA)（预览版）

Darktrace 连接器将模型违规和 AIA 事件发送到 Azure Sentinel。 通过 Darktrace AIA 工作簿中的可视化效果以交互方式浏览数据，其中包括具有时间刷的概述图。 可以向下钻取查看特定违规和事件的详细信息，然后在 Darktrace UI 中查看这些事件，以进行进一步探索。

AIA 会将违规数据发送到 Azure Log Analytics 中的 Azure Sentinel CommonSecurityLog 表。 CommonSecurityLog 表支持简单的查询排序规则及不同合作伙伴连接器的聚合。

数据引入方法：通过 Syslog 的[通用事件格式 (CEF)](connect-common-event-format.md)。

支持者：[Darktrace](https://customerportal.darktrace.com/)

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect（预览版）

AI Vectra Detect 数据连接器将 AI Vectra Detect 数据引入 Azure Sentinel 中。 借助 AI Vectra 工作簿，可以直接从 Sentinel 启动网络攻击调查。 可以查看关键主机、帐户、营销活动和检测，并监视 Vectra 的系统运行状况和审核日志。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 AI Vectra Detect 连接到 Azure Sentinel](connect-ai-vectra-detect.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Vectra](https://www.vectra.ai/support)

## <a name="akamai-security-events-preview"></a>Akamai Security Events（预览版）

Akamai Security Events 数据连接器将 [Akamai Security Events](https://www.akamai.com/us/en/products/security/) 引入到 Azure Sentinel 中。 有关详细信息，请参阅[适用于 Splunk 和 CEF Syslog 的 AKAMAI SIEM 集成](https://developer.akamai.com/tools/integrations/siem)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Akamai Security Events 连接到 Azure Sentinel](connect-akamai-security-events.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：[Akamai](https://www.akamai.com/us/en/support/)

## <a name="alcide-kaudit-preview"></a>Alcide kAudit（预览版）

Alcide kAudit 连接器可将 Kubernetes 群集审核日志实时导出到 Azure Sentinel 中。 KAudit 连接器增强了 Kubernetes 审核日志的可见性和可观测性。 Alcide kAudit 提供强大的安全和监视功能，从而便于取证。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Alcide kAudit 连接到 Azure Sentinel](connect-alcide-kaudit.md)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：[Alcide](https://www.alcide.io/company/contact-us/)

## <a name="alsid-for-active-directory-preview"></a>Alsid for Active Directory（预览版）

Alsid for Active Directory 连接器可将 Alsid 漏洞、线索流和攻击指示器日志实时导出到 Azure Sentinel。 此连接器还提供数据分析器来帮助操作日志。 工作簿可提供 Active Directory 监视和数据可视化。 分析模板有助于自动响应事件、漏洞和攻击。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Alsid for Active Directory 连接到 Azure Sentinel](connect-alsid-active-directory.md)。

数据引入方法：[Log Analytics 代理自定义日志](connect-data-sources.md#custom-logs)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：[Alsid](https://www.alsid.com/contact-us/)

## <a name="amazon-web-services"></a>Amazon Web Services

AWS 数据连接器可将 AWS CloudTrail 管理事件导入到 Azure Sentinel 中。 若要启用此连接器，请参阅[将 Azure Sentinel 连接到 AWS CloudTrail](connect-aws.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：Microsoft

## <a name="apache-http-server-preview"></a>Apache HTTP Server（预览版）

Apache HTTP Server 数据连接器可将 Apache HTTP Server 事件引入 Azure Sentinel 中。 有关详细信息，请参阅 [Apache 日志文档](https://httpd.apache.org/docs/2.4/logs.html)。

数据引入方法：[Log Analytics 代理自定义日志](connect-data-sources.md#custom-logs)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="apache-tomcat-preview"></a>Apache Tomcat（预览版）

Apache Tomcat 数据连接器可将 [Apache Tomcat](http://tomcat.apache.org/) 事件引入 Azure Sentinel 中。 有关详细信息，请参阅 [Apache Tomcat 文档](http://tomcat.apache.org/tomcat-10.0-doc/logging.html)。

数据引入方法：[Log Analytics 代理自定义日志](connect-data-sources.md#custom-logs)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass（预览版）

Aruba ClearPass 连接器可将 Aruba ClearPass 审核、会话、系统和见解日志连接到 Azure Sentinel。 有关将 Aruba ClearPass 解决方案配置为转发 Syslog 的详细信息，请参阅[添加 Syslog 导出筛选器](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Aruba ClearPass 连接到 Azure Sentinel](connect-aruba-clearpass.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit（预览版）

[Atlassian Confluence](https://www.atlassian.com/software/confluence) Audit 数据连接器可引入 Confluence 审核记录。 该连接器可获取事件来检查潜在的安全风险、分析团队的协作情况、诊断配置问题等。 有关详细信息，请参阅[查看审核日志](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit（预览版）

Atlassian Jira Audit 数据连接器可将 Jira 审核记录事件引入 Azure Sentinel 中。 该连接器可获取事件来检查潜在的安全风险、分析团队的协作情况、诊断配置问题等。 有关详细信息，请参阅[审核记录](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="barracuda-cloudgen-firewall-cgfw"></a>Barracuda CloudGen Firewall (CGFW)

Barracuda CGFW 连接器可将 Barracuda CGFW 日志连接到 Azure Sentinel。 若要为 Barracuda CGFW 配置 Syslog 流式处理，请参阅[如何配置 Syslog 流式处理](https://aka.ms/sentinel-barracudacloudfirewall-connector)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Barracuda CloudGen Firewall 连接到 Azure Sentinel](connect-barracuda-cloudgen-firewall.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：[Barracuda](https://www.barracuda.com/support)

## <a name="barracuda-web-application-firewall-waf"></a>Barracuda Web 应用程序防火墙 (WAF)

Barracuda WAF 连接器可将 Barracuda WAF 日志连接到 Azure Sentinel。 有关详细信息，请参阅[配置 Barracuda Web 应用程序防火墙](https://aka.ms/asi-barracuda-connector)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Barracuda WAF 连接到 Azure Sentinel](connect-barracuda.md)。

数据引入方法：[Log Analytics 代理自定义日志](connect-data-sources.md#custom-logs)。

支持者：[Barracuda](https://www.barracuda.com/support)

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD)（预览版）

通过 BETTER MTD 连接器，企业可以将其 Better MTD 实例连接到 Azure Sentinel。 此连接器可帮助深入了解组织的移动设备和当前移动安全态势，从而提高 SecOps 整体功能。 有关详细信息，请参阅 BETTER Mobile [Azure Sentinel 安装文档](https://mtd-docs.bmobi.net/integrations/how-to-setup-azure-sentinel-integration#mtd-integration-configuration)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 BETTER Mobile Threat Defense 连接到 Azure Sentinel](connect-better-mtd.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：BETTER Mobile

## <a name="beyond-security-besecure-preview"></a>Beyond Security beSECURE（预览版）

Beyond Security beSECURE 连接器可将 Beyond Security beSECURE 扫描事件、扫描结果和审核线索连接到 Azure Sentinel。 有关详细信息，请参阅 beSECURE [概述](https://beyondsecurity.com/solutions/besecure.html)页。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Beyond Security beSECURE 连接到 Azure Sentinel](connect-besecure.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Beyond Security](https://beyondsecurity.freshdesk.com/support/home)

## <a name="blackberry-cylanceprotect-preview"></a>Blackberry CylancePROTECT（预览版）

Blackberry CylancePROTECT 连接器可将 CylancePROTECT 审核、威胁、应用程序控制、设备、内存保护和威胁分类日志连接到 Azure Sentinel。 若要将 CylancePROTECT 配置为转发 Syslog，请参阅 [Cylance Syslog 指南](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec Data Loss Prevention (DLP)（预览版）

Broadcom Symantec DLP 连接器可将 Symantec DLP 策略/响应规则触发器连接到 Azure Sentinel。 通过此连接器，可以创建自定义仪表板和警报以协助调查。 若要将 Symantec DLP 配置为转发 Syslog，请参阅[将日志配置为 Syslog 服务器操作](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Broadcom Symantec DLP 连接到 Azure Sentinel](connect-broadcom-symantec-dlp.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="check-point"></a>检查点

检查点防火墙连接器可将检查点日志连接到 Azure Sentinel。 若要将检查点产品配置为导出日志，请参阅[日志导出程序 - 检查点日志导出](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将检查点连接到 Azure Sentinel](connect-checkpoint.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[检查点](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.checkpoint.com%2Fsupport-services%2Fcontact-support%2F&data=04%7C01%7CNayef.Yassin%40microsoft.com%7C9965f53402ed44988a6c08d913fc51df%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637562796697084967%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=Av1vTkeYoEXzRKO%2FotZLtMMexIQI%2FIKjJGnPQsbhqmE%3D&reserved=0)

## <a name="cisco-asa"></a>Cisco ASA

Cisco ASA 防火墙连接器将 Cisco ASA 日志连接到 Azure Sentinel。 若要设置此连接，请按照 [CISCO ASA 系列 CLI 配置指南](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html)中的说明进行操作。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Cisco ASA 连接到 Azure Sentinel](connect-cisco.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：Microsoft

## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer（预览版）

Cisco Firepower eStreamer 连接器可将 eStreamer 日志连接到 Azure Sentinel。 Cisco Firepower eStreamer 是针对 Cisco Firepower NGFW 解决方案设计的客户端-服务器 API。 有关详细信息，请参阅[适用于 Sentinel 的 eStreamer eNcore 操作指南](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Cisco](https://www.cisco.com/c/en/us/support/index.html)

## <a name="cisco-meraki-preview"></a>Cisco Meraki（预览版）

Cisco Meraki 连接器可将 Cisco Meraki (MX/MR/MS) 事件日志、URL、流、IDS_Alerts、安全事件和 Airmarshal 事件连接到 Azure Sentinel。 若要将 Meraki 设备配置为转发 Syslog，请按照 [Meraki 设备报告 - Syslog、SNMP 和 API](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) 中的说明进行操作。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Cisco Meraki 连接到 Azure Sentinel](connect-cisco-meraki.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS)（预览版）

Cisco UCS 连接器可将 Cisco UCS 审核、事件和故障日志连接到 Azure Sentinel。 若要将 Cisco UCS 配置为转发 Syslog，请按照[将 Syslog 配置为远程 Syslog 服务器的步骤](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog)中的说明进行操作。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Cisco Unified Computing System (UCS) 连接到 Azure Sentinel](connect-cisco-ucs.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="cisco-umbrella-preview"></a>Cisco Umbrella（预览版）

Cisco Umbrella 数据连接器可将 Amazon S3（例如 DNS、代理和 IP 日志）中存储的 Cisco Umbrella 事件引入 Azure Sentinel 中。 Cisco Umbrella 数据连接器使用 Amazon S3 REST API。 若要设置日志记录和获取凭据，请参阅[记录到 Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Cisco Umbrella 连接到 Azure Sentinel](connect-cisco-umbrella.md)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="citrix-analytics-security"></a>Citrix Analytics (Security)

Citrix Analytics (Security) 数据连接器可将 Citrix Analytics (Security) 中的风险事件数据导出到 Azure Sentinel 中。 可以创建自定义仪表板，分析来自其他源的数据以及来自 Citrix Analytics (Security) 的数据，并创建自定义逻辑应用工作流来监视和缓解安全事件。 有关详细信息，请参阅 [Microsoft Azure Sentinel 集成](https://docs.citrix.com/en-us/security-analytics/getting-started-security/azure-sentinel-integration.html)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Citrix Analytics (Security) 连接到 Azure Sentinel](connect-citrix-analytics.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Citrix](https://www.citrix.com/support/)

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web 应用防火墙 (WAF)（预览版）

Citrix WAF 数据连接器可将 Citrix WAF 日志连接到 Azure Sentinel。 Citrix WAF 可缓解对面向公众的资产的威胁，包括网站、应用和 API。
- 若要配置 WAF，请参阅[支持 WIKI - 使用 NetScaler 进行 WAF 配置](https://support.citrix.com/article/CTX234174)。
- 若要配置 CEF 日志，请参阅[应用程序防火墙中的 CEF 日志记录支持](https://support.citrix.com/article/CTX136146)。
- 若要将日志转发到代理，请参阅[为审核日志记录配置 Citrix ADC 设备](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Citrix WAF 连接到 Azure Sentinel](connect-citrix-waf.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Citrix](https://www.citrix.com/support/)

## <a name="cognni-preview"></a>Cognni（预览版）

Cognni 数据连接器可简单快速地与 Azure Sentinel 集成。 可以使用 Cognni 自主映射以前未分类的重要信息并检测相关事件。 Cognni 可帮助识别重要信息的风险，了解事件的严重性，并调查需要修正的详细信息，以足够快的速度发挥作用。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Cognni](https://cognni.ai/contact-support/)

## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>CyberArk Enterprise Password Vault (EPV) Events（预览版）

CyberArk 数据连接器为对保管库执行的操作引入 CyberArk EPV XML Syslog 消息。 EPV 通过 Sentinel.xsl 转换器将 XML 消息转换为 CEF 标准格式，并将其发送到所选的 Syslog 暂存服务器（syslog-ng、rsyslog）。 Syslog 暂存服务器上的 Log Analytics 代理将消息导入到 Log Analytics 中。 有关详细信息，请参阅 CyberArk 文档中的[安全信息和事件管理 (SIEM) 应用程序](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 CyberArk Enterprise Password Vault 连接到 Azure Sentinel](connect-cyberark.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[CyberArk](https://www.cyberark.com/customer-support/)

## <a name="cyberpion-security-logs-preview"></a>Cyberpion Security Logs（预览版）

Cyberpion Security Logs 数据连接器可将 Cyberpion 系统中的日志直接引入 Azure Sentinel 中。 有关详细信息，请参阅 Cyberpion 文档中的 [Azure Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：Cyberpion

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector（预览版）

ESET Enterprise Inspector 数据连接器可使用 ESET Enterprise Inspector 1.4 版本和更高版本中提供的 REST API 引入 ESET Enterprise Inspector 中的检测。 有关详细信息，请参阅 ESET Enterprise Inspector 文档中的 [REST API](https://help.eset.com/eei/1.5/en-US/api.html)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：[ESET](https://support.eset.com/en)

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC)（预览版）

ESET SMC 数据连接器可将 ESET SMC 威胁事件、审核日志、防火墙事件和网站筛选器引入 Azure Sentinel 中。 有关详细信息，请参阅 ESET SMC 文档中的 [Syslog 服务器](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html)。

数据引入方法：[Log Analytics 代理自定义日志](connect-data-sources.md#custom-logs)。

支持者：[ESET](https://support.eset.com/en)

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics（预览版）

Exabeam Advanced Analytics 数据连接器可将 Exabeam Advanced Analytics 事件（例如系统运行状况、值得注意的会话/异常、高级分析和作业状态）引入 Azure Sentinel 中。 若要通过 Syslog 从 Exabeam Advanced Analytics 发送日志，请按照[配置 Advanced Analytics 系统活动通知](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08)中的说明进行操作。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

ExtraHop Reveal(x) 数据连接器可将 Reveal(x) 系统连接到 Azure Sentinel。 Azure Sentinel 集成需要 ExtraHop Detection SIEM 连接器。 若要在 Reveal(x) 系统上安装 SIEM 连接器，请按照 [ExtraHop Detection SIEM 连接器](https://aka.ms/asi-syslog-extrahop-forwarding)中的说明进行操作。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 ExtraHop Reveal(x) 连接到 Azure Sentinel](connect-extrahop.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[ExtraHop](https://www.extrahop.com/support/)

## <a name="f5-big-ip"></a>F5 BIG-IP 

F5 BIG-IP 连接器可将 F5 LTM、系统和 ASM 日志连接到 Azure Sentinel。 有关详细信息，请参阅[将 F5 BIGIP 与 Azure Sentinel 集成](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 F5 BIG-IP 连接到 Azure Sentinel](connect-f5-big-ip.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：Microsoft

## <a name="f5-networks"></a>F5 网络

F5 防火墙连接器可将 F5 应用程序安全事件连接到 Azure Sentinel。 若要设置远程日志记录，请参阅[配置应用程序安全事件日志记录](https://aka.ms/asi-syslog-f5-forwarding)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 F5 ASM 连接到 Azure Sentinel](connect-f5.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：Microsoft

## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint Cloud Access Security Broker (CASB)（预览版）

Forcepoint CASB 数据连接器可将 CASB 日志和事件自动实时导出到 Azure Sentinel。 有关详细信息，请参阅 [Forcepoint CASB 和 Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Forcepoint 产品连接到 Azure Sentinel](connect-forcepoint-casb-ngfw.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG)（预览版）

Forcepoint CSG 数据连接器可将 CSG 日志自动导出到 Azure Sentinel。 CSG 是一种聚合云安全服务，可为任何用户和数据提供可见性、控制和威胁防护。 有关详细信息，请参阅 [Forcepoint Cloud Security Gateway 和 Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Forcepoint 产品连接到 Azure Sentinel](connect-forcepoint-casb-ngfw.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP)（预览版）

Forcepoint DLP 数据连接器可将 DLP 事件数据从 Forcepoint DLP 自动实时导出到 Azure Sentinel 中。 有关详细信息，请参阅 [Forcepoint Data Loss Prevention 和 Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Forcepoint DLP 连接到 Azure Sentinel](connect-forcepoint-dlp.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW)（预览版）

Forcepoint NGFW 数据连接器可将用户定义的 Forcepoint NGFW 日志自动实时导出到 Azure Sentinel 中。 有关详细信息，请参阅 [Forcepoint Next-Gen Firewall 和 Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Forcepoint 产品连接到 Azure Sentinel](connect-forcepoint-casb-ngfw.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Forcepoint](https://support.forcepoint.com/)

## <a name="forgerock-common-audit-caud-for-cef-preview"></a>适用于 CEF 的 ForgeRock Common Audit (CAUD)（预览版）

ForgeRock 标识平台提供单一通用的审核框架，因此可以全面跟踪日志数据。 可以使用 CAUD 事件处理程序和唯一 ID 在整个平台中提取和聚合日志数据。 适用于 CEF 的 CAUD 连接器是开放且可扩展的连接器，可以使用其审核日志记录和报告功能实现与 Azure Sentinel 的集成。 有关详细信息，请参阅[适用于 Azure Sentinel 的 ForgeRock Common Audit (CAUD)](https://github.com/javaservlets/SentinelAuditEventHandler)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[ForgeRock](https://www.forgerock.com/support)

## <a name="fortinet"></a>Fortinet 

Fortinet 防火墙连接器可将 Fortinet 日志连接到 Azure Sentinel。 有关详细信息，请转到 [Fortinet 文档库](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)，选择你的版本，然后使用 *手册* 和 *日志消息参考* PDF。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Fortinet 连接到 Azure Sentinel](connect-fortinet.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Fortinet](https://support.fortinet.com/)

## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G Suite)（预览版）

Google Workspace 数据连接器可通过 REST API 将 Google Workspace 活动事件引入 Azure Sentinel 中。 事件引入功能可帮助你：
- 检查潜在的安全风险。
- 分析团队协作情况。
- 诊断配置问题。
- 跟踪登录者和登录时间。
- 分析管理员活动。
- 了解用户创建和共享内容的方式。
- 查看组织事件。

若要获取凭据，请按照[执行 Google Workspace 域范围颁发机构委派](https://developers.google.com/admin-sdk/reports/v1/guides/delegation)中的说明进行操作。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Google Workspace（以前称为 G Suite）连接到 Azure Sentinel](connect-google-workspace.md)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="illusive-attack-management-system-ams-preview"></a>Illusive Attack Management System (AMS)（预览版）

Illusive AMS 连接器可向 Azure Sentinel 共享 Illusive 攻击面分析数据和事件日志。 可以在专用仪表板中查看此信息。 ASM 工作簿可帮助深入了解组织的攻击面风险，ADS 工作簿可跟踪组织网络中未经授权的横向移动。 有关详细信息，请参阅 [Illusive Networks 管理员指南](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Illusive Networks AMS 连接到 Azure Sentinel](connect-illusive-attack-management-system.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Illusive Networks](https://www.illusivenetworks.com/technical-support/)

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF 网关（预览版）

Imperva 连接器可将 Imperva WAF 网关警报连接到 Azure Sentinel。 有关详细信息，请参阅[启用向 Azure Sentinel 发送 Imperva WAF 网关警报日志记录](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Imperva WAF 网关连接到 Azure Sentinel](connect-imperva-waf-gateway.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Imperva](https://www.imperva.com/support/technical-support/)

## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS)（预览版）

Infoblox NIOS 连接器可将 Infoblox NIOS 日志连接到 Azure Sentinel。 若要启用 Infoblox NIOS 日志的 Syslog 转发，请参阅 [NIOS SNMP 和 Syslog 部署指南](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Infoblox NIOS 连接到 Azure Sentinel](connect-infoblox.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="juniper-srx-preview"></a>Juniper SRX（预览版）

Juniper SRX 连接器可将 Juniper SRX 日志连接到 Azure Sentinel。 若要转发流量日志，请参阅[为 SRX Branch 设备配置流量日志记录（安全策略日志）](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)。 若要转发系统日志，请参阅[配置系统日志记录](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Juniper SRX 连接到 Azure Sentinel](connect-juniper-srx.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：[Juniper Networks](https://support.juniper.net/support/)

## <a name="morphisec-utpp-preview"></a>Morphisec UTPP（预览版）

适用于 Azure Sentinel 的 Morphisec 数据连接器可集成安全产品的重要见解。 可以使用搜索和关联、威胁情报和自定义警报来扩展分析功能。 Morphisec 数据连接器提供对高级威胁的可见性，例如复杂的无文件攻击、内存中攻击和零时差攻击。 使用单一的跨产品视图，可以实时做出基于数据的决策来保护最重要的资产。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Morphisec

## <a name="netskope-preview"></a>Netskope（预览版）

Netskope 云安全平台连接器可将 Netskope 日志和事件引入 Azure Sentinel 中。 有关详细信息，请参阅 [Netskope 云安全平台](https://www.netskope.com/platform)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server（预览版）

NGINX HTTP Server 数据连接器可将 NGINX HTTP Server 事件引入 Azure Sentinel 中。 有关详细信息，请参阅 [ngx_http_log_module 模块](https://nginx.org/en/docs/http/ngx_http_log_module.html)。

数据引入方法：[Log Analytics 代理自定义日志](connect-data-sources.md#custom-logs)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) macOS（预览版）

NXLog BSM macOS 数据连接器可使用 Sun BSM 审核 API 直接从 macOS 平台上的内核捕获审核事件。 此数据连接器可以有效将 macOS 审核事件实时导出到 Azure Sentinel。 有关详细信息，请参阅 [NXLog Azure Sentinel 用户指南](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-dns-logs-preview"></a>NXLog DNS 日志（预览版）

NXLog DNS 日志数据连接器可使用 Windows 事件跟踪 (ETW) 收集审核和分析 DNS 服务器事件。 为了最大程度提高效率，NXLog im_etw 模块直接读取事件跟踪数据，而无需将事件跟踪捕获到 .etl 文件中。 此 REST API 连接器可将 DNS 服务器事件实时转发到 Azure Sentinel。 有关详细信息，请参阅 [NXLog Azure Sentinel 用户指南](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 NXLog (Windows) DNS 日志连接到 Azure Sentinel](connect-nxlog-dns.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit（预览版）

NXLog LinuxAudit 数据连接器支持自定义审核规则，无需 AuditD 或其他用户软件即可收集日志。 此连接器可将 IP 地址和组/用户 ID 解析为各自的名称，使 Linux 审核日志更加明了。 此 REST API 连接器可将 Linux 安全事件实时导出到 Azure Sentinel。 有关详细信息，请参阅 [NXLog Azure Sentinel 用户指南](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 NXLog LinuxAudit 连接到 Azure Sentinel](connect-nxlog-linuxaudit.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[NXLog](https://nxlog.co/community-forum)

## <a name="okta-single-sign-on-preview"></a>Okta 单一登录（预览版）

Okta 单一登录 (SSO) 数据连接器可将审核和事件日志从 Okta API 引入 Azure Sentinel 中。 若要创建 API 令牌，请按照[创建令牌](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)中的说明进行操作。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Okta SSO 连接到 Azure Sentinel](connect-okta-single-sign-on.md)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="onapsis-platform-preview"></a>Onapsis 平台（预览版）

Onapsis 数据连接器可将 Onapsis 平台中触发的警报实时导出到 Azure Sentinel 中。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Onapsis](https://onapsis.force.com/s/login/)

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard（预览版）

One Identity Safeguard CEF Sentinel 数据连接器可通过特权会话保护措施特定的仪表板增强标准 CEF 连接器。 此连接器使用设备事件进行可视化、警报、调查等。 有关详细信息，请参阅[适用于特权会话的 One Identity Safeguard 管理指南](https://aka.ms/sentinel-cef-oneidentity-forwarding)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 One Identity Safeguard 连接到 Azure Sentinel](connect-one-identity.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[One Identity](https://support.oneidentity.com/)

## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server（预览版）

OracleWebLogicServer 数据连接器可将 OracleWebLogicServer 事件引入 Azure Sentinel 中。 有关详细信息，请参阅 [Oracle WebLogic Server 文档](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html)。

数据引入方法：[Log Analytics 代理自定义日志](connect-data-sources.md#custom-logs)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="orca-security-alert-preview"></a>Orca Security Alert（预览版）

Orca Security Alert 连接器可将警报日志自动导出到 Azure Sentinel。 有关详细信息，请参阅 [Azure Sentinel 集成](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Orca Security 连接到 Azure Sentinel](connect-orca-security-alerts.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Orca Security](http://support.orca.security/)

## <a name="ossec-preview"></a>OSSEC（预览版）

OSSEC 数据连接器可将 OSSEC 事件引入 Azure Sentinel 中。 有关详细信息，请参阅 [OSSEC 文档](https://www.ossec.net/docs/)。 若要将 OSSEC 配置为通过 Syslog 发送警报，请按照[通过 Syslog 发送警报](https://www.ossec.net/docs/docs/manual/output/syslog-output.html)中的说明进行操作。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：Microsoft

## <a name="palo-alto-networks"></a>Palo Alto Networks

Palo Alto Networks 防火墙数据连接器可将 Palo Alto Networks 日志连接到Azure Sentinel。 有关详细信息，请参阅[通用事件格式 (CEF) 配置指南](https://aka.ms/asi-syslog-paloalto-forwarding)和[配置 Syslog 监视](https://aka.ms/asi-syslog-paloalto-configure)。

Palo Alto Networks 数据连接器可通过 Syslog 的 [CEF](connect-common-event-format.md) 将日志引入 Azure Sentinel 中。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Palo Alto Networks 连接到 Azure Sentinel](connect-paloalto.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support)

## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 活动日志（预览版）

Perimeter 81 活动日志数据连接器可将 Perimeter 81 活动日志连接到Azure Sentinel。 有关详细信息，请参阅 Perimeter 81 [Azure Sentinel](https://support.perimeter81.com/docs/360012680780) 文档。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Perimeter 81 日志连接到 Azure Sentinel](connect-perimeter-81-logs.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Perimeter 81](https://support.perimeter81.com/)

## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) Email Security（预览版）

POD Email Security 数据连接器可获取 POD 电子邮件保护数据。 借助此连接器，可以检查消息可跟踪性，并监视攻击者和恶意内部人员的电子邮件活动、威胁和数据外泄情况。 可以加速查看组织事件，并以小时增量获取最近活动的事件日志。 有关如何启用和检查 POD 日志 API 的信息，请[登录 Proofpoint Community](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public) 并参阅 [Proofpoint-on-Demand-Pod-Log-API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Proofpoint On Demand (POD) Email Security 连接到 Azure Sentinel](connect-proofpoint-pod.md)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP)（预览版）

Proofpoint TAP 连接器可将 Proofpoint TAP 日志和事件引入 Azure Sentinel 中。 此连接器可在 Azure Sentinel 中提供对消息和单击事件的可见性。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Proofpoint TAP 连接到 Azure Sentinel](connect-proofpoint-tap.md)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure（预览版）

Pulse Connect Secure 连接器可将 Pulse Connect Secure 日志连接到 Azure Sentinel。 若要启用通过 Syslog 进行 Pulse Connect Secure 日志流式处理，请按照[配置 Syslog](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm) 中的说明进行操作。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Pulse Connect Secure 连接到 Azure Sentinel](connect-pulse-connect-secure.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="qualys-vulnerability-management-vm-knowledgebase-kb-preview"></a>Qualys 漏洞管理 (VM) 知识库 (KB)（预览版）

Qualys VM KB 数据连接器可将最新的漏洞数据从 Qualys KB 引入 Azure Sentinel 中。 可以使用此数据来关联和扩充 Qualys VM 数据连接器的漏洞检测。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="qualys-vm-preview"></a>Qualys VM（预览版）

Qualys VM 数据连接器可通过 Qualys API 将漏洞主机检测数据引入 Azure Sentinel 中。 此连接器为漏洞扫描中的主机检测数据提供可见性。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Qualys VM 连接到 Azure Sentinel](connect-qualys-vm.md)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud（预览版）

Salesforce Service Cloud 数据连接器可通过 REST API 将有关 Salesforce 操作事件的信息引入 Azure Sentinel 中。 借助此连接器，可以加速查看组织事件，并以小时增量获取最近活动的事件日志。 有关详细信息和凭据，请参阅 Salesforce [REST API 开发人员指南](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Salesforce Service Cloud 连接到 Azure Sentinel](connect-salesforce-service-cloud.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="sentinelone-preview"></a>SentinelOne（预览版）

SentinelOne 数据连接器可将 SentinelOne 事件引入 Azure Sentinel 中。 事件包括服务器对象，例如威胁、代理、应用程序、活动、策略、组等。 该连接器可获取事件来检查潜在的安全风险、分析团队的协作情况、诊断配置问题等。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="sonicwall-firewall-preview"></a>SonicWall 防火墙（预览版）

SonicWall 防火墙数据连接器可将防火墙日志连接到 Azure Sentinel。 有关详细信息，请参阅[日志 > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[SonicWall](https://www.sonicwall.com/support/)

## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix（预览版）

Sophos Cloud Optix 数据连接器可将 Sophos Cloud Optix 日志连接到 Azure Sentinel。 有关详细信息，请参阅 Cloud Optix 设置中的 Azure Sentinel [集成页](https://optix.sophos.com/#/integrations/sentinel)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Sophos Cloud Optix 连接到 Azure Sentinel](connect-sophos-cloud-optix.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Sophos](https://secure2.sophos.com/en-us/support.aspx)

## <a name="sophos-xg-firewall-preview"></a>Sophos XG 防火墙（预览版）

Sophos XG 防火墙可将 Sophos XG 防火墙日志连接到 Azure Sentinel。 有关详细信息，请参阅[添加 Syslog 服务器](https://docs.sophos.com/nsg/sophos-firewall/18.0/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Sophos XG 连接到 Azure Sentinel](connect-sophos-xg-firewall.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

Squadra Technologies secRMM 数据连接器可将 USB 可移动存储安全事件数据推送到 Azure Sentinel 中。 有关详细信息，请参阅 [secRMM Azure Sentinel 管理员指南](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Squadra Technologies secRMM 连接到 Azure Sentinel](connect-squadra-secrmm.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx)

## <a name="squid-proxy-preview"></a>Squid Proxy（预览版）

[Squid Proxy](http://www.squid-cache.org/) 数据连接器可将 Squid 代理日志连接到 Azure Sentinel。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Squid Proxy 连接到 Azure Sentinel](connect-squid-proxy.md)。

数据引入方法：[Log Analytics 代理自定义日志](connect-data-sources.md#custom-logs)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

Symantec ICDx 数据连接器可将 Symantec 安全解决方案日志连接到 Azure Sentinel。 有关详细信息，请参阅[连接 Symantec ICDx 设备](connect-symantec.md)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Symantec ICDx 连接到 Azure Sentinel](connect-symantec.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Broadcom Symantec](https://support.broadcom.com/security)

## <a name="symantec-proxysg-preview"></a>Symantec ProxySG（预览版）

Symantec ProxySG 数据连接器可将 Symantec ProxySG 日志连接到 Azure Sentinel。 有关详细信息，请参阅[将访问日志发送到 Syslog 服务器](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html)。

Symantec 的 ProxySG 数据连接器可通过 [Syslog](connect-syslog.md) 将日志引入 Azure Sentinel 中。 此连接器还使用基于 Kusto 函数的日志分析程序。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Symantec Proxy SG 连接到 Azure Sentinel](connect-symantec-proxy-sg.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="symantec-vip-preview"></a>Symantec VIP（预览版）

Symantec VIP 数据连接器可将 Symantec VIP 日志连接到 Azure Sentinel。 有关详细信息，请参阅[配置 Syslog](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US)。

Symantec 的 VIP 数据连接器可通过 [Syslog](connect-syslog.md) 将日志引入 Azure Sentinel 中。 此连接器还使用基于 Kusto 函数的日志分析程序。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Symantec VIP 连接到 Azure Sentinel](connect-symantec-vip.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server（预览版）

Thycotic Secret Server 数据连接器可将 Secret Server 日志连接到 Azure Sentinel。 有关详细信息，请参阅[保护 Syslog/CEF 日志记录](https://docs.thycotic.com/ss/10.9.0/events-and-alerts/secure-syslog-cef)。

Thycotic 数据连接器可通过 Syslog 的 [CEF](connect-common-event-format.md) 将日志引入 Azure Sentinel 中。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Thycotic Secret Server 连接到 Azure Sentinel](connect-thycotic-secret-server.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者:[Thycotic](https://thycotic.force.com/support/s/)

## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

Trend Micro Deep Security 数据连接器可将 Deep Security 日志连接到 Azure Sentinel。 有关详细信息，请参阅[将 Deep Security 事件转发到 Syslog 或 SIEM 服务器](https://help.deepsecurity.trendmicro.com/12_0/on-premise/siem-syslog-forwarding-secure.html?redirected=true&Highlight=Configure%20Syslog#Protection_modules_DSM)。

Trend Micro Deep Security 数据连接器可通过 Syslog 的 [CEF](connect-common-event-format.md) 将日志引入 Azure Sentinel 中。 此连接器还使用基于 Kusto 函数的日志分析程序。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Trend Micro Deep Security 连接到 Azure Sentinel](connect-trend-micro.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：[Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint（预览版）

Trend Micro TippingPoint 数据连接器可将 TippingPoint SMS IPS 事件连接到 Azure Sentinel。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Trend Micro TippingPoint 连接到 Azure Sentinel](connect-trend-micro-tippingpoint.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：[Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-xdr-preview"></a>Trend Micro XDR（预览版）

Trend Micro XDR 数据连接器可将 Trend Micro XDR API 中的工作台警报引入 Azure Sentinel 中。 若要创建帐户和 API 身份验证令牌，请按照[获取第三方访问 API 密钥](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys)中的说明进行操作。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：[Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard（预览版）

VMware Carbon Black Endpoint Standard 数据连接器可将 Carbon Black Endpoint Standard 数据引入 Azure Sentinel 中。 若要创建 API 密钥，请按照[创建 API 密钥](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key)中的说明进行操作。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 VMware Carbon Black Cloud Endpoint Standard 连接到 Azure Sentinel](connect-vmware-carbon-black.md)。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="vmware-esxi-preview"></a>VMware ESXi（预览版）

VMware ESXi 数据连接器可将 VMware ESXi 日志连接到 Azure Sentinel。 若要将 VMware ESXi 连接器配置为转发 Syslog，请参阅[在 ESXi 3.5 和 4.x 上启用 Syslog](https://kb.vmware.com/s/article/1016621)和[在 ESXi 主机上配置 Syslog](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 VMware ESXi 连接到 Azure Sentinel](connect-vmware-esxi.md)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox（预览版）

WatchGuard Firebox 数据连接器可将防火墙日志引入 Azure Sentinel 中。 有关详细信息，请参阅 [Microsoft Azure Sentinel 集成指南](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html)。

数据引入方法：[Syslog](connect-syslog.md)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：[WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview)

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform（预览版）

WireX Systems 数据连接器允许安全专业人员进行 Azure Sentinel 集成，以扩充取证调查。 此连接器不仅包含 WireX 提供的上下文内容，而且还可以分析来自其他源的数据。 可以创建自定义仪表板和工作流，从而对取证调查的过程有最全面的了解。 有关详细信息和配置支持，请与 [WireX 客户支持](https://wirexsystems.com/contact-us/)联系。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 WireX Network Forensics Platform 连接到 Azure Sentinel](connect-wirex-systems.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：WireX

## <a name="workplace-from-facebook-preview"></a>Facebook Workplace（预览版）

Workplace 数据连接器可通过 Webhook 将常见 Workplace 事件引入 Azure Sentinel 中。 Webhook 允许自定义集成应用订阅 Workplace 中的事件并实时接收更新。 发生更改时，Workplace 会将包含事件信息的 HTTPS POST 请求发送到回叫数据连接器 URL。 有关详细信息，请参阅 Webhook 文档。 该连接器可获取事件来检查潜在的安全风险、分析团队的协作情况、诊断配置问题等。

数据引入方法：[Azure Functions 和 REST API](connect-data-sources.md#rest-api-integration-using-azure-functions)。

支持者：Microsoft

## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense（预览版）

Zimperium Mobile Threat Defense 数据连接器可将 Zimperium 威胁日志连接到 Azure Sentinel，以查看仪表板、创建自定义警报并改进调查。 此连接器可帮助更深入了解组织的移动威胁态势，并增强安全操作能力。 有关更多说明，请参阅 [Zimperium 客户支持门户](https://support.zimperium.com/)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Zimperium 连接到 Azure Sentinel](connect-zimperium-mtd.md)。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：[Zimperium](https://www.zimperium.com/support)

## <a name="zoom-reports-preview"></a>Zoom Reports（预览版）

Zoom Reports 数据连接器可将 Zoom Reports 事件引入 Azure Sentinel 中。 该连接器可获取事件来检查潜在的安全风险、分析团队的协作情况、诊断配置问题等。 若要获取凭据，请按照 [JWT With Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt) 中的说明进行操作。

数据引入方法：[REST API](connect-data-sources.md#rest-api-integration-on-the-provider-side)。

支持者：Microsoft

## <a name="zscaler"></a>Zscaler 

Zscaler 数据连接器可将 Zscaler Internet Access (ZIA) 日志连接到 Azure Sentinel。 使用 Azure Sentinel 上的 Zscaler 可以深入了解组织的 Internet 使用情况，并增强其安全操作功能。 有关详细信息，请参阅 [Zscaler 和 Microsoft Azure Sentinel 部署指南](https://aka.ms/ZscalerCEFInstructions)。

有关连接到 Azure Sentinel 的详细信息，请参阅[将 Zscaler 连接到 Azure Sentinel](connect-zscaler.md)。

数据引入方法：通过 Syslog 的 [CEF](connect-common-event-format.md)。

支持者：[Zscaler](https://help.zscaler.com/submit-ticket-links)

## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA)（预览版）

ZPA 数据连接器可将 Zscaler Private Access 事件引入 Azure Sentinel 中。 有关详细信息，请参阅 [Zscaler Private Access 文档](https://help.zscaler.com/zpa)。

数据引入方法：[Log Analytics 代理自定义日志](connect-data-sources.md#custom-logs)。 此连接器还使用基于 Kusto 函数的日志分析程序。

支持者：Microsoft
