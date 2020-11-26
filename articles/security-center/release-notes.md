---
title: Azure 安全中心的发行说明
description: 介绍 Azure 安全中心的新增功能和已更改的功能。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2020
ms.author: memildin
ms.openlocfilehash: 9b715ea890c7c85161a9e360bc16f9a2a608d64b
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95320987"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 安全中心的新增功能

安全中心正在积极开发中，并不断得到改进。 为及时了解最新开发成果，此页提供了有关新功能、bug 修复和已弃用功能的信息。

本页面会频繁更新，请经常回来查看。 

若要了解即将在安全中心推出的计划性更改，请参阅[即将推出的对 Azure 安全中心的重要更改](upcoming-changes.md)。 

> [!TIP]
> 如果要查找 6 个月之前的项目，可查看 [Azure 安全中心的新增功能存档](release-notes-archive.md)。


## <a name="november-2020"></a>2020 年 11 月

11 月的更新包括：

- [添加了 29 条预览建议，以扩大 Azure 安全基准的覆盖范围](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [向安全中心的法规合规性仪表板添加了 NIST SP 800 171 R2](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [建议列表现包含筛选器](#recommendations-list-now-includes-filters)
- [自动预配体验得到改进和扩展](#auto-provisioning-experience-improved-and-expanded)
- [现可在连续导出中使用安全功能分数（预览）](#secure-score-is-now-available-in-continuous-export-preview)
- [“应在计算机上安装系统更新”建议现包含子建议](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>添加了 29 条预览建议，以扩大 Azure 安全基准的覆盖范围

Azure 安全基准是由 Microsoft 创作的特定于 Azure 的一组准则，适用于基于常见合规框架的安全与合规最佳做法。 [详细了解 Azure 安全基准](../security/benchmarks/introduction.md)。

已在安全中心添加下列 29 条预览建议，以扩大此基准的覆盖范围。

预览版建议不会显示资源运行不正常，并且在计算安全功能分数时不会包含这些建议。 请尽量修正这些建议，以便在预览期结束之后，借助这些建议提高安全功能分数。 如需详细了解如何响应这些建议，请参阅[修正 Azure 安全中心的建议](security-center-remediate-recommendations.md)。

| 安全控制                     | 新建议                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 加密传输中的数据              | - 应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”<br>- 应为 MySQL 数据库服务器启用“强制 SSL 连接”<br>- 应将 TLS 更新为 API 应用的最新版本<br>- 应将 TLS 更新为函数应用的最新版本<br>- 应将 TLS 更新为 Web 应用的最新版本<br>- 应在 API 应用中要求使用 FTPS<br>- 应在函数应用中要求使用 FTPS<br>- 应在 Web 应用中要求使用 FTPS                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 管理访问和权限        | - Web 应用应请求一个用于所有传入请求的 SSL 证书<br>- 应在 API 应用中使用托管标识<br>- 应在函数应用中使用托管标识<br>- 应在 Web 应用中使用托管标识                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 限制未经授权的网络访问 | - 应为 PostgreSQL 服务器启用专用终结点<br>- 应为 MariaDB 服务器启用专用终结点<br>- 应为 MySQL 服务器启用专用终结点                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 启用审核和日志记录          | - 应启用应用服务中的诊断日志                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 实现安全最佳实践    | - 应为虚拟机启用 Azure 备份<br>- 应为 Azure Database for MariaDB 启用异地冗余备份<br>- 应为 Azure Database for MySQL 启用异地冗余备份<br>- 应为 Azure Database for PostgreSQL 启用异地冗余备份<br>- 应将 PHP 更新为 API 应用的最新版本<br>- 应将 PHP 更新为 Web 应用的最新版本<br>- 应将 Java 更新为 API 应用的最新版本<br>- 应将 Java 更新为函数应用的最新版本<br>- 应将 Java 更新为 Web 应用的最新版本<br>- 应将 Python 更新为 API 应用的最新版本<br>- 应将 Python 更新为函数应用的最新版本<br>- 应将 Python 更新为 Web 应用的最新版本<br>- 应将 SQL Server 的审核保留设置为至少 90 天 |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

相关链接：

- [详细了解 Azure 安全基准](../security/benchmarks/introduction.md)
- [详细了解 Azure API 应用](../app-service/app-service-web-tutorial-rest-api.md)
- [详细了解 Azure 函数应用](../azure-functions/functions-overview.md)
- [详细了解 Azure Web 应用](../app-service/overview.md)
- [详细了解 Azure Database for MariaDB](../mariadb/overview.md)
- [详细了解 Azure Database for MySQL](../mysql/overview.md)
- [详细了解 Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>向安全中心的法规合规性仪表板添加了 NIST SP 800 171 R2

NIST SP 800-171 R2 标准现可以内置计划的形式提供，用于安全中心的法规合规性仪表板。 有关控制措施的映射，可参阅 [NIST SP 800-171 R2 法规合规性内置计划的详细信息](../governance/policy/samples/nist-sp-800-171-r2.md)。 

若要将该标准用于订阅并持续监视合规性状态，请按照[自定义法规合规性仪表板中的标准集](update-regulatory-compliance-packages.md)中的说明操作。

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="安全中心法规合规性仪表板中的 NIST SP 800 171 R2 标准":::

有关此符合性标准的详细信息，请参阅 [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)。


### <a name="recommendations-list-now-includes-filters"></a>建议列表现包含筛选器

现在，你可以根据一系列条件筛选安全建议列表。 在以下示例中，已筛选建议列表，用于显示满足以下条件的建议：

- 正式发布（即非预览）
- 适用于存储帐户
- 支持快速修复修正

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="建议列表的筛选器":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>自动预配体验得到改进和扩展

通过在新的和现有的 Azure VM 上安装所需的扩展，使 VM 能够受益于安全中心的保护，自动预配功能有助于降低管理开销。 

随着 Azure 安全中心的发展，更多的扩展得到了开发，安全中心可以监视更大的资源类型列表。 自动预配工具现已扩展，可通过利用 Azure Policy 的功能来支持其他扩展和资源类型。

你现在可配置以下项的自动预配：

- Log Analytics 代理
- （新）适用于 Kubernetes 的 Azure Policy 加载项
- （新）Microsoft Dependency Agent

有关详细信息，请参阅[从 Azure 安全中心自动预配代理和扩展](security-center-enable-data-collection.md)。


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>现可在连续导出中使用安全功能分数（预览）

借助安全功能分数的连续导出，你可将对分数的更改实时地流式传输到 Azure 事件中心或 Log Analytics 工作区。 此功能可用于：

- 通过动态报表跟踪一段时间内的安全功能分数
- 将安全功能分数数据导出到 Azure Sentinel（或任何其他 SIEM）
- 将此数据与你可能已在使用的任何进程集成来监视你组织中的安全功能分数

详细了解如何[连续导出安全中心数据](continuous-export.md)。


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>“应在计算机上安装系统更新”建议现包含子建议

“应在计算机上安装系统更新”建议已得到增强。 新版本包括针对每个缺失的更新的子建议，其中还引入了以下改进：

- 重新设计了 Azure 门户的 Azure 安全中心页面的体验。 “应在计算机上安装系统更新”的建议详细信息页包括发现结果列表，如下所示。 选择单个发现结果时，结果窗格将打开，并提供指向修正信息和受影响资源列表的链接。

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="在门户体验中打开其中一个子建议，了解更新的建议":::

- 丰富了 Azure Resource Graph (ARG) 的建议数据。 ARG 是一项 Azure 服务，专用于提供高效的资源探索。 可以使用 ARG 在一组给定的订阅中进行大规模查询，以便有效地控制环境。 

    对于 Azure 安全中心，你可以使用 ARG 和 [Kusto 查询语言 (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) 来查询各种安全状态数据。

    以前，如果你在 ARG 中查询此建议，唯一提供的信息就是“需要在计算机上修正建议”。 以下查询的增强版本将返回按计算机分组的每个缺失的系统更新。

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```



## <a name="october-2020"></a>2020 年 10 月

10月更新包括：
- [本地和多云计算机的漏洞评估（预览版）](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [添加了 Azure 防火墙建议（预览版）](#azure-firewall-recommendation-added-preview)
- [“应在 Kubernetes 服务上定义已授权的 IP 范围”建议更新了快速修复](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [法规合规性仪表板现在包含用于删除标准的选项](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [从 Azure Resource Graph (ARG) 中删除了 Microsoft.Security/securityStatuses 表](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>本地和多云计算机的漏洞评估（预览版）

[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 的集成式漏洞评估扫描器（由 Qualys 提供支持）现可扫描启用了 Azure Arc 的服务器。

当你在非 Azure 计算机上启用了 Azure Arc 后，安全中心将提供两种向计算机部署集成式漏洞扫描器的选项（手动和大规模）。

此次更新后，你便可以发掘 Azure Defender 的强大功能，合并所有 Azure 和非 Azure 资产的漏洞管理计划。

主要功能：

- 监视 Azure Arc 计算机上的 VA（漏洞评估）扫描器预配状态
- 将集成式 VA 代理预配到未受保护的 Windows 和 Linux Azure Arc 计算机（手动或大规模）
- 从部署的代理接收和分析检测到的漏洞（手动和大规模）
- 统一的 Azure VM 和 Azure Arc 计算机体验

[详细了解如何将集成式漏洞扫描器部署到混合计算机](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[详细了解启用了 Azure Arc 的服务器](https://docs.microsoft.com/azure/azure-arc/servers/)。


### <a name="azure-firewall-recommendation-added-preview"></a>添加了 Azure 防火墙建议（预览版）

添加了新的建议，即使用 Azure 防火墙保护所有虚拟网络。

“虚拟网络应受 Azure 防火墙保护”建议使用 Azure 防火墙限制虚拟网络的访问权限和防止潜在威胁。

了解有关 [Azure 防火墙](https://azure.microsoft.com/services/azure-firewall/)的详细信息。


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>“应在 Kubernetes 服务上定义已授权的 IP 范围”建议更新了快速修复

“应在 Kubernetes 服务上定义已授权的 IP 范围”建议现提供一个快速修复选项。

要详细了解此建议和所有其他安全中心建议，请参阅[安全建议 - 参考指南](recommendations-reference.md)。

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="具有快速修复选项的“应在 Kubernetes 服务上定义已授权的 IP 范围”建议":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>法规合规性仪表板现在包含用于删除标准的选项

安全中心的法规合规性仪表板基于你满足特定合规控制和要求的情况来提供合规态势的见解。

该仪表板包含一组默认的法规标准。 如果提供的任何标准都与你的组织不相关，现在就可以简单地从订阅的 UI 中将其删除。 只能在“订阅”级别删除标准，而不能从管理组范围删除。

有关详细信息，请参阅[从仪表板中删除标准](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard)。


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>从 Azure Resource Graph (ARG) 中删除了 Microsoft.Security/securityStatuses 表

Azure Resource Graph 是 Azure 中的一项服务，旨在提供高效的资源浏览功能，它能够在一组给定的订阅中进行大规模查询，使你能够有效地管理环境。 

对于 Azure 安全中心，你可以使用 ARG 和 [Kusto 查询语言 (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) 来查询各种安全状态数据。 例如：

- 资产清单利用 (ARG)
- 我们提供了一个示例 ARG 查询，说明如何[在未启用多重身份验证 (MFA) 的情况下标识帐户](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

ARG 中提供了可以在查询中使用的数据表。

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph 资源管理器和可用的表":::

> [!TIP]
> ARG 文档列出了 [Azure Resource Graph 表和资源类型参考](../governance/resource-graph/reference/supported-tables-resources.md)中所有可用的表。

在此次更新中，删除了 Microsoft.Security/securityStatuses。 securityStatuses API 仍可用。

Microsoft.Security/Assessments 表可以使用数据替换。

Microsoft.Security/securityStatuses 和 Microsoft.Security/Assessments 的主要区别在于，前者显示评估聚合，而后者会为每项评估保留一条记录。

例如，Microsoft.Security/securityStatuses 将返回包含两个 policyAssessments 数组的结果：

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
而 Microsoft.Security/Assessments 将为这样的策略评估各保留一条记录，如下所示：

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**将使用 securityStatuses 的现有 ARG 查询转换为现在使用 Assessments 表的示例：**

引用 SecurityStatuses 的查询：

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Assessments 表的替换查询：

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

若要了解详细信息，请参阅下列链接：
- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)
- [Kusto 查询语言 (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>2020 年 9 月

9 月的更新包括：
- [安全中心获得新的外观！](#security-center-gets-a-new-look)
- [Azure Defender 已发布](#azure-defender-released)
- [适用于 Key Vault 的 Azure Defender 已正式发布](#azure-defender-for-key-vault-is-generally-available)
- [适用于存储的 Azure Defender 针对文件存储和 ADLS Gen2 的保护已正式发布](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [资产清单工具现已正式发布](#asset-inventory-tools-are-now-generally-available)
- [对容器注册表和虚拟机的扫描禁用特定漏洞发现结果](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [从建议中免除资源](#exempt-a-resource-from-a-recommendation)
- [安全中心的 AWS 和 GCP 连接器引入了多云体验](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes 工作负载保护建议捆绑](#kubernetes-workload-protection-recommendation-bundle)
- [漏洞评估发现结果现已可以连续导出](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [在创建新资源时通过强制执行建议来防止安全性配置错误](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [改进了网络安全组建议](#network-security-group-recommendations-improved)
- [弃用了预览 AKS 建议“应在 Kubernetes 服务上定义 Pod 安全策略”](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [优化了 Azure 安全中心内的电子邮件通知](#email-notifications-from-azure-security-center-improved)
- [安全功能分数不包括预览建议](#secure-score-doesnt-include-preview-recommendations)
- [建议现在包含严重性指标和新鲜度间隔](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>安全中心获得新的外观！

我们已发布安全中心门户页面的更新 UI。 新页面包括新的概述页面以及用于安全分数、资产清单和 Azure Defender 的面板。

重新设计的概述页面现在包含一个磁贴，用于访问安全分数、资产清单和 Azure Defender 的面板。 它还包含一个可以链接到合规性面板的磁贴。

详细了解[概述页面](overview-page.md)。


### <a name="azure-defender-released"></a>Azure Defender 已发布

Azure Defender 是集成到安全中心内部的云工作负载保护平台 (CWPP)，用于为 Azure 和混合工作负载提供高级智能的保护。 它取代了安全中心的标准定价层选项。 

从 Azure 安全中心的“定价和设置”区域启用 Azure Defender 时，将同时启用以下 Defender 计划，并为环境的计算、数据和服务层提供全面防护：

- [适用于服务器的 Azure Defender](defender-for-servers-introduction.md)
- [适用于应用服务的 Azure Defender](defender-for-app-service-introduction.md)
- [适用于存储的 Azure Defender](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [适用于 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)
- [适用于 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md)

安全中心的文档对其中每个计划单独进行了介绍。

借助其专用面板，Azure Defender 为虚拟机、SQL 数据库、容器、Web 应用程序、网络等提供安全警报和高级威胁防护。

[详细了解 Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>适用于 Key Vault 的 Azure Defender 已正式发布

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 

适用于 Key Vault 的 Azure Defender 提供针对 Azure Key Vault 的 Azure 原生高级威胁防护，从而提供额外的安全情报层。 因此，适用于 Key Vault 的 Azure Defender 可保护依赖于 Key Vault 帐户的多个资源。

可选计划现已正式发布。 此功能在预览版中为“Azure Key Vault 的高级威胁防护”。

此外，Azure 门户中的 Key Vault 页面现在添加了一个专门的“安全性”页面，用于提供安全中心的建议和警报 。

有关详细信息，请参阅[适用于 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)。


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>适用于存储的 Azure Defender 针对文件存储和 ADLS Gen2 的保护已正式发布 

适用于存储的 Azure Defender 会检测 Azure 存储帐户上可能存在的有害活动。 无论数据是存储为 blob 容器、文件共享还是数据湖，都可以为其提供保护。

[Azure 文件存储](../storage/files/storage-files-introduction.md)和[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 的支持现已正式发布。

从 2020 年 10 月 1 日起，我们将开始有偿保护这些服务上的资源。

有关详细信息，请参阅[适用于存储的 Azure Defender](defender-for-storage-introduction.md)。


### <a name="asset-inventory-tools-are-now-generally-available"></a>资产清单工具现已正式发布

Azure 安全中心的资产清单页提供了一个页面，用于查看已连接到安全中心的资源的安全状况。

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后会提供有关如何消除这些安全漏洞的建议。

当任何资源具有未完成的建议时，它们将显示在清单中。

有关详细信息，请参阅[利用资产清单和管理工具浏览和管理资源](asset-inventory.md)。



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>对容器注册表和虚拟机的扫描禁用特定漏洞发现结果

Azure Defender 包含漏洞扫描程序，用于扫描 Azure 容器注册表和虚拟机中的映像。

如果组织需要忽略发现结果，而不是修正漏洞，则可以选择禁用发现结果。 禁用发现结果不会影响安全分数，也不会产生有害的噪音。

当发现结果与在禁用规则中定义的条件相匹配时，它不会显示在发现结果列表中。

此选项在建议详细信息页中提供，用于：

- **应修正 Azure 容器注册表映像中的漏洞**
- **应修正虚拟机中的漏洞**

有关详细信息，请参阅[禁用容器映像的特定发现结果](defender-for-container-registries-usage.md#disable-specific-findings-preview)和[禁用虚拟机的特定发现结果](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)。


### <a name="exempt-a-resource-from-a-recommendation"></a>从建议中免除资源

有时，某个资源就某个特定建议而言会被列为不正常（因而会降低安全分数），尽管你认为不应是这样。 它可能已被安全中心未跟踪的进程修正。 或者，你的组织可能已决定接受该特定资源的风险。 

在这种情况下，可以创建免除规则，确保将来不会将该资源列为不正常资源。 这些规则可以包括下文所述的书面理由。

有关详细信息，请参阅[从建议和安全分数中免除资源](exempt-resource.md)。


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>安全中心的 AWS 和 GCP 连接器引入了多云体验

由于云工作负载通常跨多个云平台分布，因此云安全服务也需要如此。

Azure 安全中心现在可保护 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作负载。

将 AWS 和 GCP 帐户加入安全中心，并将 AWS 安全中心、GCP 安全命令和 Azure 安全中心相集成。 

有关详细信息，请参阅[将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md)和[将 GCP 帐户连接到 Azure 安全中心](quickstart-onboard-gcp.md)。


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes 工作负载保护建议捆绑

为了确保 Kubernetes 工作负载在默认情况下是安全的，安全中心将添加 Kubernetes 级别强化建议，其中包括具有 Kubernetes 准入控制的执行选项。

在 AKS 群集上安装了适用于 Kubernetes 的 Azure Policy 加载项后，将按照预先定义的一组最佳做法监视对 Kubernetes API 服务器的每个请求，然后再将其保存到群集。 然后，可以配置为强制实施最佳做法，并规定将其用于未来的工作负载。

例如，可以规定不应创建特权容器，并且阻止以后的任何请求。

有关详细信息，请参阅[使用 Kubernetes 准入控制实现工作负载保护最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)。


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>漏洞评估发现结果现已可以连续导出

使用连续导出将警报和建议实时流式传输到 Azure 事件中心、Log Analytics 工作区或 Azure Monitor。 在此处可以将此数据与 SIEM（如 Azure Sentinel、Power BI、Azure 数据资源管理器等）集成。

安全中心的集成漏洞评估工具在“父”建议中将有关资源的发现结果作为可操作性建议返回，例如“应修正虚拟机中的漏洞”。 

现在选择建议并启用“包括安全性结果”选项时，可以通过连续导出来导出安全性结果。

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="在连续导出配置中包括安全结果开关" :::

相关页面：

- [Azure 虚拟机安全中心的集成漏洞评估解决方案](deploy-vulnerability-assessment-vm.md)
- [用于 Azure 容器注册表映像的安全中心集成漏洞评估解决方案](defender-for-container-registries-usage.md)
- [连续导出](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>在创建新资源时通过强制执行建议来防止安全性配置错误

安全性配置错误是造成安全事件的主要原因。 安全中心现在能够帮助阻止新资源在特定建议方面的错误配置。 

此功能可帮助保护工作负载的安全和稳定安全分数。

根据特定建议，有两种方式可以强制实施安全配置：

- 利用 Azure Policy 的“拒绝”效果，可以阻止创建不正常的资源

- 通过“强制执行”选项，可以利用 Azure Policy 的“不存在时部署”效果，在创建时自动修正不合规的资源 
 
这适用于所选的安全建议，位于资源详细信息页的顶部。

有关详细信息，请参阅[使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)。

###  <a name="network-security-group-recommendations-improved"></a>改进了网络安全组建议

以下与网络安全组相关的安全建议已得到优化，可减少误报。

- 应在与 VM 关联的 NSG 上限制所有网络端口
- 应关闭虚拟机上的管理端口
- 面向 Internet 的虚拟机应使用网络安全组进行保护
- 子网应与网络安全组关联


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>弃用了预览 AKS 建议“应在 Kubernetes 服务上定义 Pod 安全策略”

如 [Azure Kubernetes 服务](../aks/use-pod-security-policies.md)文档中所述，弃用了预览建议“应在 Kubernetes Services 上定义 Pod 安全策略”。

Pod 安全策略（预览）功能已设置为弃用，在 2020 年 10 月 15 日之后将不再提供，以支持适用于 AKS 的 Azure Policy。

弃用 Pod 安全策略（预览版）之后，必须在使用已弃用功能的任何现有群集上禁用该功能，以执行将来的群集升级并保留在 Azure 支持范围内。


### <a name="email-notifications-from-azure-security-center-improved"></a>优化了 Azure 安全中心内的电子邮件通知

电子邮件中与安全警报相关的以下部分已得到优化： 

- 添加了发送针对所有严重性级别的电子邮件警报通知的功能
- 添加了在订阅上通知具有不同 Azure 角色的用户的功能
- 默认情况下，我们会主动向订阅所有者通知高严重性警报（这些警报很可能表示真正的漏洞）
- 我们已从电子邮件通知配置页面中删除了电话号码字段

有关详细信息，请参阅[设置安全警报的电子邮件通知](security-center-provide-security-contact-details.md)。


### <a name="secure-score-doesnt-include-preview-recommendations"></a>安全功能分数不包括预览建议 

安全中心会持续评估资源、订阅和组织的安全问题。 然后，它将所有调查结果汇总成一个分数，让你可以一目了然地了解当前的安全状况：分数越高，识别出的风险级别就越低。

发现新的威胁后，安全中心会通过提出新的建议来提供新的安全建议。 为避免安全功能分数出现意外变化，以及为了提供一个宽限期（可以在新建议影响分数之前在此宽限期内了解新建议），安全功能分数的计算中将不再包括标记为“预览”的建议。 但仍应尽可能按这些建议进行修正，这样在预览期结束时，它们会有助于提升分数。

此外，“预览”建议不会使资源“运行不正常”。

预览建议示例如下：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="带有预览标志的建议":::

[详细了解安全功能分数](secure-score-security-controls.md)。


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>建议现包含严重性指示器和刷新时间间隔

现在，建议的详细信息页面包括一个刷新时间间隔指示器（如相关），并且清楚显示了建议的严重性。

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="显示刷新频率和严重性的建议页面":::



## <a name="august-2020"></a>2020 年 8 月

8 月的更新包括：

- [资产清单 - 功能强大的资产安全状况新视图](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [添加了对 Azure Active Directory 安全默认值的支持（用于多重身份验证）](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [添加了服务主体建议](#service-principals-recommendation-added)
- [VM 上的漏洞评估 - 已合并建议和策略](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [新的 AKS 安全策略已添加到 ASC_default 计划 - 仅供个人预览版客户使用](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>资产清单 - 功能强大的资产安全状况新视图

安全中心的资产清单页（当前为预览版）提供了一种方法，用于查看已连接到安全中心的资源的安全状况。

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后会提供有关如何消除这些安全漏洞的建议。 当任何资源具有未完成的建议时，它们将显示在清单中。

你可以使用该视图及其筛选器来浏览安全状况数据，并根据发现结果采取更多操作。

详细了解[资产清单](asset-inventory.md)。


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>添加了对 Azure Active Directory 安全默认值的支持（用于多重身份验证）

安全中心已添加对[安全默认值](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)（Microsoft 的免费标识安全保护）的全部支持。

安全默认值提供了预配置的标识安全设置，以保护组织免受与标识相关的常见攻击。 安全默认值总计已保护了逾 500 万名租户；50,000 名租户也受安全中心的保护。

现在，安全中心在识别出未启用安全默认值的 Azure 订阅时将提供安全建议。 到目前为止，安全中心建议使用条件访问启用多重身份验证，这是 Azure Active Directory (AD) 高级许可证的一部分。 对于使用 Azure AD Free 的客户，我们现在建议启用安全默认值。 

我们旨在鼓励更多客户使用 MFA 保护其云环境，并缓解对[安全功能分数](secure-score-security-controls.md)影响最大的最高风险。

详细了解[安全默认值](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)。


### <a name="service-principals-recommendation-added"></a>添加了服务主体建议

添加了一条新建议，该建议推荐使用管理证书来管理订阅的安全中心客户改用服务主体。

“应使用服务主体而不是管理证书来保护订阅”这一建议推荐使用服务主体或 Azure 资源管理器，以更安全地管理订阅。 

详细了解 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>VM 漏洞评估 - 合并了建议和策略

安全中心检查 VM，以检测其是否正在运行漏洞评估解决方案。 如果未找到漏洞评估解决方案，安全中心将建议简化部署。

如果发现漏洞，安全中心将建议总结结果，以便必要时进行调查和修正。

无论用户使用哪些类型的扫描仪，为确保所有用户享受一致的体验，我们已将四条建议统一为以下两条：

|统一的建议|更改描述|
|----|:----|
|**应在虚拟机上启用漏洞评估解决方案**|替换以下两条建议：<br> • 在虚拟机上启用内置漏洞评估解决方案（由 Qualys 提供技术支持）（现已弃用）（仅标准层显示此建议）<br> • 漏洞评估解决方案应安装在虚拟机上（现已弃用）（标准和免费层显示此建议）|
|**应修正虚拟机中的漏洞**|替换以下两条建议：<br>• 修正虚拟机上发现的漏洞（由 Qualys 提供支持）（现已弃用）<br>• 应通过漏洞评估解决方案修正漏洞（现已弃用）|
|||

现在可根据同一建议从 Qualys 或 Rapid7 等合作伙伴部署安全中心的漏洞评估扩展或专用许可解决方案（“BYOL”）。

此外，发现漏洞并报告到安全中心时，无论哪个漏洞评估解决方案标识了结果，都会有一条建议提醒你注意这些结果。

#### <a name="updating-dependencies"></a>更新依赖项

如果脚本、查询或自动化引用了先前的建议或策略密钥/名称，请使用下表更新引用：

##### <a name="before-august-2020"></a>2020 年 8 月之前

|建议|范围|
|----|:----|
|**在虚拟机上启用内置漏洞评估解决方案（由 Qualys 提供支持）**<br>注册表项：550e890b-e652-4d22-8274-60b3bdb24c63|内置|
|修正虚拟机上发现的漏洞（由 Qualys 提供支持）<br>注册表项：1195afff-c881-495e-9bc5-1486211ae03f|内置|
|应在虚拟机上安装漏洞评估解决方案<br>注册表项：01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**应通过漏洞评估解决方案修复漏洞**<br>注册表项：71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|策略|范围|
|----|:----|
|**应对虚拟机启用漏洞评估**<br>策略 ID：501541f7-f7e7-4cd6-868c-4190fdad3ac9|内置|
|**应通过漏洞评估解决方案修正漏洞**<br>策略 ID：760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>2020 年 8 月之后

|建议|范围|
|----|:----|
|**应在虚拟机上启用漏洞评估解决方案**<br>密钥：ffff0522-1e88-47fc-8382-2a80ba848f5d|内置 + BYOL|
|**应修正虚拟机中的漏洞**<br>注册表项：1195afff-c881-495e-9bc5-1486211ae03f|内置 + BYOL|
||||

|策略|范围|
|----|:----|
|[应对虚拟机启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>策略 ID：501541f7-f7e7-4cd6-868c-4190fdad3ac9 |内置 + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>新的 AKS 安全策略已添加到 ASC_default 计划 - 仅供个人预览版客户使用

为了确保 Kubernetes 工作负载在默认情况下是安全的，安全中心将添加 Kubernetes 级别策略和强化建议，其中包括具有 Kubernetes 准入控制的执行选项。

早期阶段的此项目包括个人预览版，并增加了 ASC_default 计划的新策略（默认情况下禁用）。

可以放心地忽略这些策略，这些策略不会对环境造成影响。 如果想要启用这些策略，请在 https://aka.ms/SecurityPrP 注册预览版，并从以下选项中进行选择：

1. **单个预览版** – 仅加入此个人预览版。 明确提及将“ASC 连续扫描”作为要加入的预览版。
1. **正在进行的程序** – 将添加到当前个人预览版和将来的个人预览版。 你需要完成个人资料和隐私协议。


## <a name="july-2020"></a>2020 年 7 月

7 月的更新包括：
- [虚拟机的漏洞评估现在适用于非市场映像](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure 存储的威胁防护已扩展为包括 Azure 文件存储和 Azure Data Lake Storage Gen2（预览版）](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [启用威胁防护功能的八条新建议](#eight-new-recommendations-to-enable-threat-protection-features)
- [容器安全性优化 - 注册表扫描和刷新文档速度更快](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [更新了自适应应用程序控制，添加了新建议以及对路径规则中的通配符的支持](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [已弃用六个 SQL 高级数据安全策略](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>虚拟机的漏洞评估现在适用于非市场映像

部署漏洞评估解决方案时，安全中心以前会在部署之前执行验证检查。 检查的目的是确认目标虚拟机的市场 SKU。 

从本次更新起，已删除该项检查，你现在可以将漏洞评估工具部署到“自定义”Windows 和 Linux 计算机。 自定义映像是你根据市场默认值修改的映像。

虽然现在可以在更多台计算机上部署集成的漏洞评估扩展（由 Qualys 提供支持），但只有在使用[将集成漏洞扫描程序部署到标准层 VM](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)列出的操作系统时，才可以使用支持

详细了解[虚拟机的集成漏洞扫描程序（需要 Azure Defender）](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。

要详细了解如何使用 Qualys 或 Rapid7 中自己私下许可的漏洞评估解决方案，请参阅[部署合作伙伴漏洞扫描解决方案](deploy-vulnerability-assessment-vm.md)。


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure 存储的威胁防护已扩展为包括 Azure 文件存储和 Azure Data Lake Storage Gen2（预览版）

Azure 存储的威胁防护可检测 Azure 存储帐户上的潜在有害活动。 安全中心在检测到对存储帐户的访问或攻击尝试时会显示警报。 

无论数据是存储为 blob 容器、文件共享还是数据湖，都可以为其提供保护。




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>启用威胁防护功能的八条新建议

添加了八条新建议，简化了为以下资源类型启用 Azure 安全中心的威胁防护功能的过程：虚拟机、应用服务计划、Azure SQL 数据库服务器、计算机上的 SQL 服务器、Azure 存储帐户、Azure Kubernetes 服务群集、Azure 容器注册表注册表和 Azure Key Vault 保管库。

新建议如下所示：

- **应在 Azure SQL 数据库服务器上启用高级数据安全**
- **应在计算机的 SQL 服务器上启用高级数据安全**
- **应在 Azure 应用服务计划上启用高级威胁防护**
- **应对 Azure 容器注册表的注册表启用高级威胁防护**
- **应对 Azure Key Vault 的保管库启用高级威胁防护**
- **应对 Azure Kubernetes 服务的群集启用高级威胁防护**
- **应对 Azure 存储帐户启用高级威胁防护**
- 应对虚拟机启用高级威胁防护

这些新建议属于“启用 Azure Defender”安全控制。

建议还包括快速修复功能。 

> [!IMPORTANT]
> 修正任一建议都将产生相关资源的保护费用。 如果当前订阅中有相关资源，则立即开始计费。 或者以后在你添加资源时，开始计费。
> 
> 例如，如果订阅中没有任何 Azure Kubernetes 服务群集，并且启用了威胁防护，则不会产生任何费用。 如果以后在同一订阅中添加了群集，它将自动受到保护，并从该时间点开始计费。

有关上述各项的详细信息，请参阅[安全建议参考页面](recommendations-reference.md)。

详细了解 [Azure 安全中心的威胁防护](azure-defender.md)。




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>容器安全性优化 - 注册表扫描和刷新文档速度更快

作为对容器安全领域的持续投资的一部分，我们很高兴分享安全中心对 Azure 容器注册表中存储的容器映像的动态扫描方面的显著性能优化。 目前，扫描通常会在大约两分钟内完成。 在某些情况下，可能最多需要 15 分钟。

为更好地说明和指导 Azure 安全中心的容器安全功能，我们还更新了容器安全文档页面。 

若要详细了解安全中心的容器安全，请参阅以下文章：

- [安全中心的容器安全功能概述](container-security.md)
- [与 Azure 容器注册表集成的详细信息](defender-for-container-registries-introduction.md)
- [与 Azure Kubernetes 服务集成的详细信息](defender-for-kubernetes-introduction.md)
- [扫描注册表并强化 Docker 主机的操作说明](container-security.md)
- [威胁防护功能中适用于 Azure Kubernetes 服务群集的安全警报](alerts-reference.md#alerts-akscluster)
- [威胁防护功能中适用于 Azure Kubernetes 服务主机的安全警报](alerts-reference.md#alerts-containerhost)
- [容器的安全建议](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>更新了自适应应用程序控制，添加了新建议以及对路径规则中的通配符的支持

自适应应用程序控制功能已收到两个重要更新：

* 一项新的建议指出以前不允许的可能合法的行为。 “应更新自适应应用程序控制策略中的允许列表规则”这一新建议提示向现有策略添加新规则，以减少自适应应用程序控制违规警报的误报数。

* 路径规则现支持通配符。 在此更新中，可以使用通配符配置允许的路径规则。 支持以下两种方案：

    * 在路径末尾使用通配符允许该文件夹和子文件夹中的所有可执行文件

    * 在路径中间使用通配符来启用具有更改的文件夹名称的已知可执行文件名称（例如，包含已知可执行文件的个人用户文件夹，自动生成的文件夹名称等）。


[详细了解自适应应用程序控制](security-center-adaptive-application.md)。



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>已弃用六个 SQL 高级数据安全性策略

即将弃用与 SQL 计算机的高级数据安全性相关的六个策略：

- 应在 SQL 托管实例的“高级数据安全”设置中将“高级威胁保护类型”设置为“全部”
- 应在 SQL Server 的高级数据安全设置中将“高级威胁防护类型”设置为“全部”
- SQL 托管实例的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址
- SQL 服务器的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址
- 应在 SQL 托管实例高级数据安全设置中启用“向管理员和订阅所有者发送电子邮件通知”
- 应在 SQL 服务器高级数据安全设置中为管理员和订阅所有者启用电子邮件通知

了解有关[内置策略](./policy-reference.md)的详细信息。





## <a name="june-2020"></a>2020 年 6 月

6 月的更新包括以下内容：
- [安全分数 API（预览）](#secure-score-api-preview)
- [SQL 计算机（Azure、其他云和本地）的高级数据安全（预览）](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [将 Log Analytics 代理部署到 Azure Arc 计算机的两条新建议（预览）](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [大规模创建连续导出和工作流自动化配置的新策略](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [使用 NSG 保护非面向 Internet 的虚拟机的新建议](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [启用威胁防护和高级数据安全性的新策略](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>安全功能分数 API（预览）

可以通过[安全功能分数 API](/rest/api/securitycenter/securescores/)（当前处于预览阶段）立即访问分数。 通过 API 方法，可灵活地查询数据，久而久之构建自己的安全功能分数报告机制。 例如，可以使用安全功能分数 API 来获取特定订阅的分数。 此外，还可以使用安全功能分数控件 API 列出安全控件和订阅的当前分数。

有关使用安全功能分数 API 实现的外部工具的示例，请参阅 [GitHub 社区的安全功能分数区域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

详细了解 [Azure 安全中心的安全分数和安全控件](secure-score-security-controls.md)。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>SQL 计算机（Azure、其他云和本地）的高级数据安全（预览）

Azure 安全中心的 SQL 计算机高级数据安全现在保护托管在 Azure 中、其他云环境，甚至本地计算机上的 SQL Server。 这会扩展 Azure 原生 SQL Server 的保护，以完全支持混合环境。

高级数据安全可为任何位置的 SQL 计算机提供漏洞评估和高级威胁防护。

设置包含两个步骤：

1. 将 Log Analytics 代理部署到 SQL Server 的主机计算机，以提供与 Azure 帐户的连接。

1. 在安全中心的“定价和设置”页中启用可选捆绑。

详细了解 [SQL 计算机的高级数据安全](defender-for-sql-usage.md)。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>将 Log Analytics 代理部署到 Azure Arc 计算机的两条新建议（预览）

添加了两条新建议，以帮助将 [Log Analytics 代理](../azure-monitor/platform/log-analytics-agent.md)部署到 Azure Arc 计算机，并确保其受 Azure 安全中心的保护：

- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上(预览)
- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上(预览)

这些新建议将出现在“应在计算机上安装监视代理”这一现有（相关）建议所在的四个安全控制中：修正安全配置、应用自适应应用程序控制、应用系统更新，以及启用 Endpoint Protection。

建议还包括快速修复功能，以帮助加快部署过程。 

有关这两项新建议的详细信息，请参阅[计算和应用建议](recommendations-reference.md#recs-computeapp)。

若要详细了解 Azure 安全中心如何使用代理，请参阅[什么是 Log Analytics 代理？](faq-data-collection-agents.md#what-is-the-log-analytics-agent)

详细了解 [Azure Arc 计算机的扩展](../azure-arc/servers/manage-vm-extensions.md)。


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>大规模创建连续导出和工作流自动化配置的新策略

自动执行组织的监视和事件响应流程可以显著缩短调查和缓解安全事件所需的时间。

若要在整个组织中部署自动化配置，请使用以下内置的“DeployIfdNotExist”Azure 策略来创建和配置[连续导出](continuous-export.md)和[工作流自动化](workflow-automation.md)过程：

可在 Azure 策略中找到这些策略：


|目标  |策略  |策略 ID  |
|---------|---------|---------|
|将内容连续导出到事件中心|[为 Azure 安全中心警报和建议部署“导出到事件中心”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|将内容连续导出到 Log Analytics 工作区|[为 Azure 安全中心警报和建议配置“导出到 Log Analytics 工作区”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|安全警报的工作流自动化|[为 Azure 安全中心警报部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|安全建议的工作流自动化|[为 Azure 安全中心建议部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

开始使用[工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

请参阅[使用提供的策略大规模地配置工作流自动化](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies)以及[设置连续导出](continuous-export.md#set-up-a-continuous-export)，了解关于如何使用两种导出策略的详细信息。


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>使用 NSG 保护非面向 Internet 的虚拟机的新建议

“实现安全最佳做法”安全控制现包括以下新建议：

- **应使用网络安全组来保护非面向 Internet 的虚拟机**

“应使用网络安全组保护面向 Internet 的虚拟机”这一现有建议不区分面向 Internet 的虚拟机和面向非 Internet 的虚拟机。 对于这两种情况，如果未将 VM 分配给网络安全组，则会生成高严重性建议。 这一新建议将区分面向非 Internet 的计算机，以减少误报并避免出现不必要的高严重性警报。

有关详细详细，请参阅[网络建议](recommendations-reference.md#recs-network)表。




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>启用威胁防护和高级数据安全性的新策略

以下新策略已添加到 ASC Default 计划，旨在帮助为相关资源类型启用威胁防护或高级数据安全性。

可在 Azure 策略中找到这些策略：


| 策略                                                                                                                                                                                                                                                                | 策略 ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [应在 Azure SQL 数据库服务器上启用高级数据安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [应在计算机的 SQL 服务器上启用高级数据安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [应对 Azure 存储帐户启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [应对 Azure Key Vault 的保管库启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [应在 Azure 应用服务计划上启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [应对 Azure 容器注册表的注册表启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [应对 Azure Kubernetes 服务的群集启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [应在虚拟机上启用高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

详细了解 [Azure 安全中心的威胁防护](azure-defender.md)。
