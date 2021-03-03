---
title: Azure Sentinel 数据源架构引用
description: 本文列出了 Azure Sentinel 支持的 Azure 和第三方数据源架构，并提供指向其参考文档的链接。
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 0cc1a083e1400b5a3167376e374af6c7bf7ab7c7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698342"
---
# <a name="data-source-schema-reference"></a>数据源架构引用

本文列出了支持的 Azure 和第三方数据源架构，并提供指向其参考文档的链接。

## <a name="azure-data-sources"></a>Azure 数据源

| 类型                             | 数据源             | Log Analytics tablename | 架构参考 |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Azure AD 活动报表登录属性](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure Monitor AuditLogs 引用](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Azure Monitor AzureActivity 引用](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Office 365 管理活动 API 架构： <br>- [常见架构 ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange 管理架构 ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange 邮箱架构](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [SharePoint 基本架构](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [SharePoint 文件操作](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Azure Monitor AzureDiagnostics 引用](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **主机**                             | Linux                  | Syslog                 | [Azure Monitor Syslog 引用](/azure/azure-monitor/reference/tables/syslog) |
| **Network**                          | IIS 日志               | W3CIISLog              | [Azure Monitor W3CIISLog 引用](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Network**                          | VMinsights             | VMConnection           | [Azure Monitor VMConnection 引用](/azure/azure-monitor/reference/tables/vmconnection) |
| **Network**                          | 线路数据解决方案     | WireData               | [Azure Monitor WireData 引用](/azure/azure-monitor/reference/tables/wiredata) |
| **Network**                          | NSG 流日志          | AzureNetworkAnalytics  | [流量分析中的架构和数据聚合](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> 有关详细信息，请参阅整个 [Azure Monitor 数据参考](/azure/azure-monitor/reference/)。
>
## <a name="3rd-party-vendor-data-sources"></a>第三方供应商数据源

下表列出了支持的第三方供应商及其 Syslog 或常见事件格式 (CEF 的各种受支持的日志类型的文档) ，其中包含每个类别类型的 CEF 字段映射和示例日志。

| 类型 |    Vendor |    Products | Log Analytics tablename | CEF 字段映射引用  |
| ----- | ----- | ----- | ----- |----- |
| **Network** | 帕洛阿尔托   | 平移 OS    | CommonSecurityLog |   [平移操作系统9.0 常见事件格式集成指南](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (搜索 *CEF 样式日志格式*)  |
| **Network** | 检查点  |ALL   | CommonSecurityLog | [日志字段说明](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Network** | Fortigate   | ALL   | CommonSecurityLog | [日志架构结构](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Network** | Barracuda | Web 应用程序防火墙 |  CommonSecurityLog   | [如何配置 Syslog 和其他日志](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Network** | Cisco | ASA | CommonSecurityLog | [Cisco ASA 系列 Syslog 消息](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Network** | Cisco | Firepower   | CommonSecurityLog | [Cisco Firepower 威胁防御 Syslog 消息](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Network** | Cisco   | 防护  | 自定义日志表  | [日志格式和版本控制](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Network**   | Cisco | Meraki    | CommonSecurityLog |   [Syslog 事件类型和日志示例](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Network**   | Zscaler | Nano 流式处理服务 (NSS) |   CommonSecurityLog | [格式化 NSS 源](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (Web、防火墙、DNS 和隧道日志仅)  |
| **Network**   |F5 | BigIP LTM|    CommonSecurityLog|  [事件消息和攻击类型](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Network** | F5  | BigIP ASM|    CommonSecurityLog|  [记录应用程序安全事件](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Network** | Citrix  |Web 应用防火墙   | CommonSecurityLog|    [常见事件格式 (CEF) 日志记录支持应用程序防火墙](https://support.citrix.com/article/CTX136146) <br>  [NetScaler 12.0 Syslog 消息引用](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**主机** |Symantec | Symantec Endpoint Protection 管理员 (SEPM)  | CommonSecurityLog|[Endpoint Protection 管理员的外部日志记录设置和日志事件的严重级别](https://support.symantec.com/us/en/article.tech171741.html)|
|**主机** |Trend Micro |全部 |CommonSecurityLog | [Syslog 内容映射-CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

## <a name="next-steps"></a>后续步骤

了解更多受支持的 Azure Sentinel 连接器，例如 CEF、Syslog、直接、代理和自定义连接器：

- [连接数据源](connect-data-sources.md)

- [Azure Sentinel Syslog、CEF 和其他第三方连接器](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)