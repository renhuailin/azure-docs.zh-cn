---
title: Azure 安全基准 V2 - 日志记录和威胁检测
description: Azure 安全基准 V2 日志记录和威胁检测
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2a0ce52c094739ffbbce2f93a41b2533222066b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576807"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>安全控制 V2：日志记录和威胁检测

日志记录和威胁检测涵盖了相关控制措施用于检测 Azure 上的威胁以及启用、收集和存储 Azure 服务的审核日志。 这包括启用检测、调查和修正过程，使控件能够在 Azure 服务中生成具有本机威胁检测功能的高质量警报;它还包括 Azure Monitor 收集日志，通过 Azure Sentinel、时间同步和日志保留来集中进行安全分析。 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：为 Azure 资源启用威胁检测

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-1 | 6.7 | AU-3、AU-6、AU-12、SI-4 |

确保正在监视不同类型的 Azure 资产，以发现潜在的威胁和异常情况。 专注于获取高质量警报以减少误报，便于分析人员进行分类整理。 警报可能源自日志数据、代理或其他数据。

使用 Azure 安全中心内置的威胁检测功能，该功能基于监视 Azure 服务遥测和分析服务日志。 数据是使用 Log Analytics 代理收集的，该代理从系统中读取各种与安全相关的配置和事件日志，然后将数据复制到工作区进行分析。 

此外，还可以使用 Azure Sentinel 构建分析规则，这些规则会在环境中寻找符合特定条件的威胁。 规则匹配时，这些规则会生成事件，以便调查每个事件。 Azure Sentinel 还可以导入第三方威胁情报，以增强其威胁检测功能。 

- [Azure 安全中心的威胁防护](../../security-center/azure-defender.md)

- [Azure 安全中心安全警报参考指南](../../security-center/alerts-reference.md)

- [创建自定义分析规则以检测威胁](../../sentinel/tutorial-detect-threats-custom.md)

- [利用 Azure Sentinel 进行网络威胁智能](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：启用 Azure 标识和访问管理的威胁检测

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-2 | 6.8 | AU-3、AU-6、AU-12、SI-4 |

Azure AD 提供以下用户日志，可在 Azure AD 报表中进行查看，也可将这些日志与 Azure Monitor、Azure Sentinel 或其他 SIEM/监视工具集成，以用于更复杂的监视和分析用例： 
-   登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。

-   审核日志 - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 审核日志的示例包括对 Azure AD 中的任何资源（例如添加或删除用户、应用、组、角色和策略）所做的更改。

-   风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

-   已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

Azure 安全中心还可针对某些可疑活动发出警报，这些活动包括失败的身份验证尝试次数太多，以及帐户已在订阅中遭到弃用。 除了基本的安全卫生监视，Azure 安全中心的威胁防护模块还可从单个 Azure 计算资源（例如虚拟机、容器、应用服务）、数据资源（例如 SQL 数据库和存储）以及 Azure 服务层中收集信息更丰富的安全警报。 通过此功能可查看单个资源中的帐户异常情况。

- [Azure AD 中的审核活动报告](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [启用 Azure 标识保护](../../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 安全中心的威胁防护](../../security-center/azure-defender.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：为 Azure 网络活动启用日志记录

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-3 | 9.3、12.2、12.5、12.8 | AU-3、AU-6、AU-12、SI-4 |

启用并收集网络安全组 (NSG) 资源日志、NSG 流日志、Azure 防火墙日志和 Web 应用程序防火墙 (WAF) 日志进行安全分析，从而支持事件调查、威胁搜寻和安全警报生成。 可将流日志发送到 Azure Monitor Log Analytics 工作区，然后使用流量分析提供见解。 确保正在收集 DNS 查询日志，以帮助关联其他网络数据。

- [如何启用网络安全组流日志](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure 防火墙日志和指标](../../firewall/logs-and-metrics.md)

- [如何启用和使用流量分析](../../network-watcher/traffic-analytics.md)

- [使用网络观察程序进行监视](../../network-watcher/network-watcher-monitoring-overview.md)

- [Azure Monitor 中的 Azure 网络监视解决方案](../../azure-monitor/insights/azure-networking-analytics.md)

- [通过 DNS Analytics 解决方案收集有关 DNS 基础结构的见解](../../azure-monitor/insights/dns-analytics.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：为 Azure 资源启用日志记录

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-4 | 6.2、6.3、8.8 | AU-3、AU-12 |

为 Azure 资源启用日志记录，以满足合规性、威胁检测、搜寻和事件调查的要求。 

可使用 Azure 安全中心和 Azure Policy 在 Azure 资源上实现资源日志和日志数据收集，以访问审核、安全性和资源日志。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。 

- [了解 Azure 中的日志记录和不同的日志类型](../../azure-monitor/essentials/platform-logs-overview.md)

- [了解 Azure 安全中心数据收集](../../security-center/security-center-enable-data-collection.md)

**责任**：共享

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

基础结构和终结点安全性 

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中管理和分析安全日志

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-5 | 6.5、6.6 | AU-3、SI-4 |

集中记录存储和分析来实现关联。 对于每个日志源，请确保已分配数据所有者、访问指南、存储位置、用于处理和访问数据的工具以及数据保留要求。

确保正在将 Azure 活动日志集成到中央日志记录。 通过 Azure Monitor 引入日志，以聚合终结点设备、网络资源和其他安全系统生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

另外，请启用 Azure Sentinel 或第三方 SIEM 并将数据载入其中。

许多组织选择将 Azure Sentinel 用于频繁使用的“热”数据，并将 Azure 存储用于不太频繁使用的“冷”数据。 

- [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/essentials/diagnostic-settings.md)

- [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6：配置日志存储保留期

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-6 | 6.4 | AU-3、AU-11 |

根据合规性、法规和业务要求配置日志保留。 

在 Azure Monitor 中，可根据组织的合规性规则设置 Log Analytics 工作区保持期。 将 Azure 存储、Data Lake 或 Log Analytics 工作区帐户用于长期存储和存档存储。

- [更改 Log Analytics 中的数据保留期](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [如何为 Azure 存储帐户日志配置保留策略](../../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure 安全中心警报和建议导出](../../security-center/continuous-export.md)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [安全性和符合性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7：使用批准的时间同步源

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

Microsoft 会维护大多数 Azure PaaS 和 SaaS 服务的时间源。 对于虚拟机，除非有特定要求，否则请使用 Microsoft 默认 NTP 服务器进行时间同步。  如果需要建立自己的网络时间协议 (NTP) 服务器，请务必保护 UDP 服务端口 123 的安全。

Azure 中资源生成的所有日志都提供了时间戳，且默认指定时区。

- [如何为 Azure Windows 计算资源配置时间同步](../../virtual-machines/windows/time-sync.md)

- [如何为 Azure Linux 计算资源配置时间同步](../../virtual-machines/linux/time-sync.md)

- [如何对 Azure 服务禁用入站 UDP](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**责任**：共享

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [策略和标准](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)