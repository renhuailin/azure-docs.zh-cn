---
title: Azure 安全中心的发行说明
description: 介绍 Azure 安全中心的新增功能和已更改的功能
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: memildin
ms.openlocfilehash: 5dd58dd5f43481184b17ca4bdd694a1df76697db
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916451"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 安全中心的新增功能

安全中心正在积极开发中，并不断得到改进。 为及时了解最新开发成果，此页提供了有关新功能、bug 修复和已弃用功能的信息。

本页面会频繁更新，请经常回来查看。 

若要了解即将在安全中心推出的计划性更改，请参阅[即将推出的对 Azure 安全中心的重要更改](upcoming-changes.md)。 

> [!TIP]
> 如果要查找 6 个月之前的项目，可查看 [Azure 安全中心的新增功能存档](release-notes-archive.md)。


## <a name="january-2021"></a>2021 年 1 月

一月的更新包括：

- [Azure 安全基准现在是 Azure 安全中心的默认策略计划](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [本地和多云计算机的漏洞评估已发布正式发布版 (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [预览版中现可提供管理组的安全分数](#secure-score-for-management-groups-is-now-available-in-preview)
- [安全分数 API 已发布正式发布版 (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [用于应用服务的 Azure Defender 添加了无关联的 DNS 保护](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [多云连接器发布了正式发布版 (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [从订阅和管理组的安全分数中免除所有建议](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [用户现在可以向其全局管理员请求租户范围内的可见性](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [添加了 35 条预览建议，以扩大 Azure 安全基准的覆盖范围](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [将经筛选的建议列表导出为 CSV](#csv-export-of-filtered-list-of-recommendations)
- [在 Azure Policy 评估中，“不适用”资源现报告为“合规”](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [通过连续导出（预览版）导出安全分数和法规合规性数据的每周快照](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure 安全基准现在是 Azure 安全中心的默认策略计划

Azure 安全基准是由 Microsoft 创作的特定于 Azure 的一组准则，适用于基于常见合规框架的安全与合规最佳做法。 这一公认的基准建立在 [Internet 安全中心 (CIS)](https://www.cisecurity.org/benchmark/azure/) 和[国家标准与技术研究院 (NIST)](https://www.nist.gov/) 的控制基础上，重点关注以云为中心的安全性。

最近几个月，安全中心的内置安全建议列表已显著增加，从而扩大了此基准的覆盖范围。

从此版本开始，该基准是安全中心建议的基础，并且已完全集成为默认策略计划。 

所有 Azure 服务文档中都有一个安全基线页面。 例如，[这是安全中心的基线](security-baseline.md)。 这些基线均基于 Azure 安全基准。

如果使用的是安全中心的法规符合性仪表板，则你在过渡期间会看到两个基准实例：

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure 安全中心的法规符合性仪表板显示 Azure 安全基准":::

现有建议不受影响，并且随着基准的增加，安全中心内将自动反映出这些更改。 

若要了解详细信息，请参阅以下页面：

- [详细了解 Azure 安全基准](../security/benchmarks/introduction.md)
- [在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>本地和多云计算机的漏洞评估已发布正式发布版 (GA)

10 月，我们随[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 的集成式漏洞评估扫描器（由 Qualys 提供支持）提供了扫描已启用 Azure Arc 的服务器的预览。

现已发布正式发布版 (GA)。

当你在非 Azure 计算机上启用了 Azure Arc 后，安全中心将提供两种向计算机部署集成式漏洞扫描器的选项（手动和大规模）。

此次更新后，你便可以发掘 Azure Defender 的强大功能，合并所有 Azure 和非 Azure 资产的漏洞管理计划。

主要功能：

- 监视 Azure Arc 计算机上的 VA（漏洞评估）扫描器预配状态
- 将集成式 VA 代理预配到未受保护的 Windows 和 Linux Azure Arc 计算机（手动或大规模）
- 从部署的代理接收和分析检测到的漏洞（手动和大规模）
- 统一的 Azure VM 和 Azure Arc 计算机体验

[详细了解如何将集成式漏洞扫描器部署到混合计算机](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)。

[详细了解启用了 Azure Arc 的服务器](../azure-arc/servers/index.yml)。


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>预览版中现可提供管理组的安全分数

除了订阅级别外，“安全分数”页面现在还会显示管理组的汇总安全分数。 因此，现在可以查看组织中管理组的列表以及每个管理组的分数。

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="正在查看管理组的安全分数。":::

详细了解 [Azure 安全中心的安全分数和安全控件](secure-score-security-controls.md)。

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>安全分数 API 已发布正式发布版 (GA)

现在可以通过[安全分数 API](/rest/api/securitycenter/securescores/) 访问分数。 通过 API 方法，可灵活地查询数据，久而久之构建自己的安全功能分数报告机制。 例如，可以使用安全功能分数 API 来获取特定订阅的分数。 此外，还可以使用安全功能分数控件 API 列出安全控件和订阅的当前分数。

有关使用安全功能分数 API 实现的外部工具的示例，请参阅 [GitHub 社区的安全功能分数区域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

详细了解 [Azure 安全中心的安全分数和安全控件](secure-score-security-controls.md)。


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>用于应用服务的 Azure Defender 添加了无关联的 DNS 保护

子域接管是组织常见的严重威胁。 当拥有指向已撤销的网站的 DNS 记录时，可能会发生子域接管。 这类 DNS 记录也称为“无关联的 DNS”项。 CNAME 记录特别容易受到此威胁的攻击。 

通过子域接管，威胁参与者可以将要流向组织的域的流量重定向到执行恶意活动的站点。

现在，当对应用服务网站解除授权时，用于应用服务的 Azure Defender 会检测到无关联的 DNS 条目。 此时，DNS 条目指向不存在的资源，并且网站容易受到子域接管攻击。 无论你的域是由 Azure DNS 托管还是由外部域注册器托管，都可使用这些保护；这些保护既适用于 Windows 上的应用服务，也适用于 Linux 上的应用服务。

了解详细信息：

- [应用服务警报引用表](alerts-reference.md#alerts-azureappserv) - 包括两个新的 Azure Defender 警报，它们在检测到无关联的 DNS 条目时触发
- [防止无关联的 DNS 条目并避免子域接管](../security/fundamentals/subdomain-takeover.md) - 了解子域接管威胁和无关联的 DNS 方面
- [用于应用服务的 Azure Defender 简介](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>多云连接器发布了正式发布版 (GA)

由于云工作负载通常跨多个云平台分布，因此云安全服务也需要如此。

Azure 安全中心可保护 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作负载。

连接 AWS 或 GCP 帐户会将其本机安全工具（如 AWS 安全中心和 GCP 安全命令中心）集成到 Azure 安全中心。

此功能意味着安全中心可以在所有主要的云环境中提供可见性和保护。 此集成的一些优势如下：

- 自动代理预配 - 安全中心使用 Azure Arc 将 Log Analytics 代理部署到 AWS 实例
- 策略管理
- 漏洞管理
- 嵌入的终结点检测和响应 (EDR)
- 检测安全配置错误
- 显示所有云提供商提供的安全建议的单一视图
- 将所有资源整合到安全中心的安全分数计算中
- AWS 和 GCP 资源的法规符合性评估

在安全中心的菜单中，选择“多云连接器”，随即将显示用于新建连接器的选项：

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="安全中心“多云连接器”页面上的“添加 AWS 帐户”按钮":::

了解详细信息，请参阅：
- [将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md)
- [将 GCP 帐户连接到 Azure 安全中心](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>从订阅和管理组的安全分数中免除所有建议

我们正在扩展免除功能，以免除所有建议。 提供其他选项来微调安全中心针对订阅、管理组或资源提出的安全建议。

有时，当你知道问题已被第三方工具解决，而安全中心未检测到时，资源将被列为运行不正常。 或者，建议会在你认为它不属于的范围内显示。 该建议可能不适用于特定的订阅。 或者，组织可能只决定接受与特定资源或建议相关的风险。

使用此预览功能，现在可以针对建议创建免除，以执行以下操作：

- 免除某资源，以确保其将来不会被列入运行不正常的资源，并且不会影响安全分数。 该资源将被列为不适用，原因将显示“已免除”以及你选择的特定理由。

- 免除某订阅或管理组，以确保建议不会影响安全分数，并且将来不会针对该订阅或管理组显示。 这与现有资源以及将来创建的任何资源相关。 建议将标记有你针对所选范围选择的特定理由。

了解详细信息，请参阅[从安全评分中免除资源和建议](exempt-resource.md)。



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>用户现在可以向其全局管理员请求租户范围内的可见性

如果用户不具有查看安全中心数据的权限，他们现在将会看到一个链接，用于向其组织的全局管理员请求权限。 该请求包括他们想要的角色以及需要这样做的理由。

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="横幅通知用户他们可以请求租户范围内的权限。":::

了解详细信息，请参阅[当你的权限不足时请求租户范围内的权限](security-center-management-groups.md#request-tenant-wide-permissions-when-yours-are-insufficient)。


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>添加了 35 条预览建议，以扩大 Azure 安全基准的覆盖范围

Azure 安全基准是 Azure 安全中心的默认策略计划。 

为扩大此基准的覆盖范围，安全中心已添加下列 35 条预览建议。

> [!TIP]
> 预览版建议不会显示资源运行不正常，并且在计算安全功能分数时不会包含这些建议。 请尽量修正这些建议，以便在预览期结束之后，借助这些建议提高安全功能分数。 如需详细了解如何响应这些建议，请参阅[修正 Azure 安全中心的建议](security-center-remediate-recommendations.md)。

| 安全控制                     | 新建议                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 启用静态加密            | - Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据<br>- Azure 机器学习工作区应使用客户管理的密钥 (CMK) 进行加密<br>- 应为 MySQL 服务器启用“创建自己的密钥”数据保护<br>- 应为 PostgreSQL 服务器启用“创建自己的密钥”数据保护<br>- 认知服务帐户应启用使用客户管理的密钥 (CMK) 进行数据加密<br>- 容器注册表应使用客户管理的密钥 (CMK) 进行加密<br>- SQL 托管实例应使用客户管理的密钥进行静态数据加密<br>- SQL Server 应使用客户管理的密钥进行静态数据加密<br>- 存储帐户应使用客户管理的密钥 (CMK) 进行加密                                                                                                                                                              |
| 实现安全最佳实践    | - 订阅应有一个联系人电子邮件地址，用于接收安全问题通知<br> - 你的订阅应启用 Log Analytics 代理自动预配<br> - 应启用高严重性警报的电子邮件通知<br> - 应启用向订阅所有者发送高严重性警报的电子邮件通知<br> - 密钥保管库应启用清除保护<br> - 密钥保管库应启用软删除 |
| 管理访问和权限        | - 确保函数应用已启用“客户端证书(传入客户端证书)” |
| 保护应用程序免受 DDoS 攻击 | - 应为应用程序网关启用 Web 应用程序防火墙 (WAF)<br> - 应为 Web 应用程序防火墙 (WAF) 启用 Azure Front Door 服务 |
| 限制未经授权的网络访问 | - 应在 Key Vault 上启用防火墙<br> - 应为 Key Vault 配置专用终结点<br> - 应用程序配置应使用专用链接<br> - Azure Cache for Redis 应驻留在虚拟网络中<br> - Azure 事件网格域应使用专用链接<br> - Azure 事件网格主题应使用专用链接<br> - Azure 机器学习工作区应使用专用链接<br> - Azure SignalR 服务应使用专用链接<br> - Azure Spring Cloud 应使用网络注入<br> - 容器注册表不得允许无限制的网络访问<br> - 容器注册表应使用专用链接<br> - 应为 MariaDB 服务器禁用公用网络访问<br> - 应为 MySQL 服务器禁用公用网络访问<br> - 应为 PostgreSQL 服务器禁用公用网络访问<br> - 存储帐户应使用专用链接连接<br> - 存储帐户应使用虚拟网络规则来限制网络访问<br> - VM 映像生成器模板应使用专用链接|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

相关链接：

- [详细了解 Azure 安全基准](../security/benchmarks/introduction.md)
- [详细了解 Azure Database for MariaDB](../mariadb/overview.md)
- [详细了解 Azure Database for MySQL](../mysql/overview.md)
- [详细了解 Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>将经筛选的建议列表导出为 CSV 

2020 年 11 月，我们在“建议”页面中添加了筛选器（[建议列表现包含筛选器](#recommendations-list-now-includes-filters)）。 12 月，我们扩展了这些筛选器（[建议页面包含用于环境、严重性和可用响应的新筛选器](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)）。 

随着此次公告的发布，我们将更改为“下载到 CSV”按钮的行为，使 CSV 导出仅包含经筛选的列表中当前显示的建议。 

例如，在下图中，可以看到列表已筛选为显示两个建议。 生成的 CSV 文件包括受这两个建议影响的每项资源的状态详细信息。   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="将经筛选的建议导出到 CSV 文件":::

可在 [Azure 安全中心内的安全建议](security-center-recommendations.md)中了解详细信息。


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>在 Azure Policy 评估中，“不适用”资源现报告为“合规”

以前，为建议评估且发现不适用的资源在 Azure Policy 中显示为“不合规”。 任何用户操作都不能将资源状态更改为“合规”。 进行此更改后，为了显得更加清楚，将资源报告为“合规”。

唯一的影响是 Azure Policy 中合规资源的数量将增加。 Azure 安全中心的安全评分不受影响。


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>通过连续导出（预览版）导出安全分数和法规合规性数据的每周快照

我们已在[连续导出](continuous-export.md)工具中添加了新的预览功能，用于导出安全分数和法规合规性数据的每周快照。

定义连续导出时，请设置导出频率：

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="选择连续导出的频率":::

- **流式处理** - 更新资源的运行状况时，将实时发送评估（如果没有更新，则不发送任何数据）。
- **快照** - 每周将发送所有法规合规性评估的当前状态的快照（这是面向安全分数和法规合规性数据每周快照的一项预览功能）。

请在[持续导出安全中心数据](continuous-export.md)中详细了解此功能的所有性能

## <a name="december-2020"></a>2020 年 12 月

12 月的更新包括：

- [适用于计算机上的 SQL 服务器的 Azure Defender 现已正式发布](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [针对 Azure Synapse Analytics 专用 SQL 池的 Azure Defender for SQL 支持现已正式发布](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [全局管理员现在可以授予自己的租户级别权限](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [两项新的 Azure Defender 计划：适用于 DNS 的 Azure Defender 和适用于资源管理器的 Azure Defender（预览版）](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Azure 门户中的新安全警报页（预览版）](#new-security-alerts-page-in-the-azure-portal-preview)
- [在 Azure SQL 数据库和 SQL 托管实例中更新了安全中心体验](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [更新了资产清单工具和筛选器](#asset-inventory-tools-and-filters-updated)
- [有关请求 SSL 证书的 Web 应用的建议不再属于安全功能分数](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [建议页面包含用于环境、严重性和可用响应的新筛选器](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [连续导出获得新的数据类型和改进的 deployifnotexist 策略](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>适用于计算机上的 SQL 服务器的 Azure Defender 现已正式发布

Azure 安全中心为 SQL 服务器提供两个 Azure Defender 计划：

- 适用于 Azure SQL 数据库服务器的 Azure Defender - 保护 Azure 原生 SQL 服务器 
- 适用于计算机上的 SQL 服务器的 Azure Defender - 将相同的保护扩展到混合、多云和本地环境中的 SQL 服务器

根据此公告，适用于 SQL 的 Azure Defender 现在可以保护位于任何位置的数据库及其数据。

适用于 SQL 的 Azure Defender 包括漏洞评估功能。 漏洞评估工具包括以下高级功能：

- 基线配置（新功能！），可以智能地将漏洞扫描的结果细化为可能表示实际安全问题的结果。 建立基线安全状态后，漏洞评估工具仅报告与该基线状态的偏差。 与基线匹配的结果被视为通过后续扫描。 这样，你和你的分析师就可以将注意力集中在重要的方面。
- 详细的基准信息有助于了解已发现的结果，以及这些结果为何与资源相关。
- 修正脚本有助于减轻已确定的风险。

详细了解 [Azure Defender for SQL](defender-for-sql-introduction.md)。


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>针对 Azure Synapse Analytics 专用 SQL 池的 Azure Defender for SQL 支持现已正式发布

Azure Synapse Analytics（以前称为 SQL DW）是一种分析服务，它将企业数据仓库和大数据分析合并在一起。 专用 SQL 池是 Azure Synapse 的企业数据仓库功能。 有关详细信息，请参阅[什么是 Azure Synapse Analytics（以前称为 SQL DW）？](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)。

适用于 SQL 的 Azure Defender 可通过以下方式保护专用 SQL 池：

- 用于检测威胁和攻击的高级威胁防护 
- 用于识别和修正安全错误配置的漏洞评估功能

针对 Azure Synapse Analytics SQL 池的 Azure Defender for SQL 支持会自动添加到 Azure 安全中心中的 Azure SQL 数据库捆绑中。 你可以在 Azure 门户的 Synapse 工作区页面中找到新的“适用于 SQL 的 Azure Defender”选项卡。

详细了解 [Azure Defender for SQL](defender-for-sql-introduction.md)。


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>全局管理员现在可以授予自己的租户级别权限

具有 Azure Active Directory“全局管理员”角色的用户可能要承担租户范围内的责任，但缺乏 Azure 权限，无法在 Azure 安全中心查看组织范围内的信息。 

若要向你自己分配租户级别的权限，请按照[授予自己租户范围的权限](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself)中的说明操作。


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>两项新的 Azure Defender 计划：适用于 DNS 的 Azure Defender 和适用于资源管理器的 Azure Defender（预览版）

我们为你的 Azure 环境添加了两项新的云原生广度威胁防护功能。

这些新的防护极大增强了你在遭到威胁行为体攻击后的复原能力，还大大增加了受 Azure Defender 保护的 Azure 资源数量。

- **适用于资源管理器的 Azure Defender** - 自动监视在你的组织中执行的所有资源管理操作。 有关详细信息，请参阅：
    - [适用于资源管理器的 Azure Defender 简介](defender-for-resource-manager-introduction.md)
    - [响应适用于资源管理器的 Azure Defender 警报](defender-for-resource-manager-usage.md)
    - [适用于资源管理器的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-resourcemanager)

- **适用于 DNS 的 Azure Defender** - 持续监视来自你的 Azure 资源的所有 DNS 查询。 有关详细信息，请参阅：
    - [适用于 DNS 的 Azure Defender 简介](defender-for-dns-introduction.md)
    - [响应适用于 DNS 的 Azure Defender 警报](defender-for-dns-usage.md)
    - [适用于 DNS 的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Azure 门户中的新安全警报页（预览版）

Azure 安全中心的安全警报页经过重新设计，可提供以下内容：

- **更好的警报会审体验** - 帮助减少警报疲劳，让你能够专注于相关度最高的威胁，列表包含可自定义的筛选器和分组选项
- **警报列表中的更多信息** - 例如 MITRE ATT&ACK 策略
- **用于创建示例警报的按钮** - 要评估 Azure Defender 功能并测试警报配置（对于 SIEM 集成、电子邮件通知和工作流自动化），可以从所有 Azure Defender 计划创建示例警报
- **与 Azure Sentinel 事件体验的一致性** - 对于同时使用这两种产品的客户，现在可以更直接地在它们之间进行切换
- **更好的大型警报列表性能**
- **警报列表键盘导航**
- **来自 Azure Resource Graph 的警报** - 可以在 Azure Resource Graph 中查询警报，它是适用于所有资源的类 Kusto API。 如果要构建自己的警报仪表板，这也很有用。 [详细了解 Azure Resource Graph](../governance/resource-graph/index.yml)。

若要访问新体验，请使用安全警报页顶部横幅中的“立即试用”链接。

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="带有新预览版警报体验链接的横幅":::

若要通过新的警报体验创建示例警报，请参阅[生成 Azure Defender 示例警报](security-center-alert-validation.md#generate-sample-azure-defender-alerts)。


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>在 Azure SQL 数据库和 SQL 托管实例中更新了安全中心体验 

通过 SQL 中的安全中心体验，可访问安全中心和 Azure Defender for SQL 的下列功能：

- **安全建议** - 安全中心会定期分析所有已连接的 Azure 资源的安全状态，以识别潜在的安全配置错误。 然后，它会提供建议来指导如何修正这些漏洞和提升组织的安全状况。
- **安全警报** - 一种检测服务，用于持续监视 Azure SQL 活动是否存在 SQL 注入、暴力攻击和特权滥用等威胁。 该服务会在安全中心触发面向操作的详细安全警报，并提供选项便于继续使用 Azure Sentinel（Microsoft 的 Azure 原生 SIEM 解决方案）进行调查。
- **结果** - 一种漏洞评估服务，可持续监视 Azure SQL 配置并帮助修正漏洞。 评估扫描会提供 Azure SQL 安全状态的概述以及详细的安全扫描结果。     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Azure 安全中心适用于 SQL 的安全功能在 Azure SQL 中可用":::


### <a name="asset-inventory-tools-and-filters-updated"></a>更新了资产清单工具和筛选器

Azure 安全中心的“清单”页面已刷新，它具有以下更改：

- 工具栏上添加了“指南和反馈”。 该操作会打开一个窗格，其中有指向相关信息和工具的链接。 
- 可用于资源的默认筛选器中添加了“订阅筛选器”。
- “打开查询”链接，它用于将当前筛选器选项作为 Azure Resource Graph 查询（以前称为“在 Resource Graph 资源管理器中查看”）打开。
- 每个筛选器都有了运算符选项。 现在，可从“=”之外的其他逻辑运算符中进行选择。 例如，你可能想要查找所有具有活动建议且标题包含“encrypt”字符串的资源。 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="资产清单筛选器中运算符选项的控件":::

有关清单的详细信息，请参阅[利用资产清单浏览和管理资源](asset-inventory.md)。


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>有关请求 SSL 证书的 Web 应用的建议不再属于安全功能分数

“Web 应用应请求一个 SSL 证书用于所有传入请求”这一建议已从“管理访问和权限”安全控制（最多值 4 分）移至“实现安全最佳做法”（不值任何分数） 。 

确保 Web 应用请求证书肯定会使其更安全。 但是，对于面向公众的 Web 应用，这是不相关的。 如果通过 HTTP 而不是 HTTPS 访问站点，不会收到任何客户端证书。 因此，如果应用程序需要客户端证书，则你不应允许通过 HTTP 对应用程序发出请求。 有关详细信息，请参阅[为 Azure 应用服务配置 TLS 相互身份验证](../app-service/app-service-web-configure-tls-mutual-auth.md)。

在此更改后，此建议现在已是推荐的最佳做法，不会影响你的分数。 

请在[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解每个安全控件中的建议。


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>建议页面包含用于环境、严重性和可用响应的新筛选器

Azure 安全中心会监视所有已连接的资源并生成安全建议。 可使用这些建议来强化你的混合云状况，并跟踪与组织、行业和国家/地区相关的策略和标准的合规性。

随着安全中心不断扩展其覆盖范围和功能，安全建议的列表每月都在扩充。 例如，请参阅[添加了 29 条预览建议，以扩大 Azure 安全基准的覆盖范围](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)。

随着列表的扩充，需要能够筛选出最感兴趣的建议。 11 月，我们在“建议”页面中添加了筛选器（请参阅[建议列表现包含筛选器](#recommendations-list-now-includes-filters)）。

本月添加的筛选器提供了一些选项，可根据以下条件优化建议列表：

- 环境 - 查看有关 AWS、GCP 或 Azure 资源（或任何组合）的建议
- 严重性 - 根据安全中心设置的严重性分类来查看建议
- 响应操作 - 根据安全中心响应选项的可用性来查看建议：快速修复、拒绝和强制实施

    > [!TIP]
    > “响应操作”筛选器替代了“可用的快速修复(是/否)”筛选器。 
    > 
    > 请详细了解每个响应选项：
    > - [快速修复修正](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="建议会按安全控制分组" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>连续导出获得新的数据类型和改进的 deployifnotexist 策略

借助 Azure 安全中心的连续导出工具，可导出安全中心的建议和警报，以便与环境中的其他监视工具一起使用。

“连续导出”使你可以完全自定义将要导出的内容，以及要导出到的位置 。 有关完整详细信息，请参阅[连续导出安全中心数据](continuous-export.md)。

这些工具已通过以下方式进行了增强和扩展：

- **连续导出的 deployifnotexist 策略已得到增强**。 策略目前执行以下操作：

    - **检查配置是否已启用。** 如果未启用，策略将显示为不合规，并将创建合规的资源。 若要详细了解所提供的 Azure Policy 模板，请参阅[设置连续导出](continuous-export.md#set-up-a-continuous-export)的“使用 Azure Policy 选项卡大规模部署”。

    - **支持导出安全结果。** 使用 Azure 策略模板时，可配置连续导出，使其包含结果。 这在导出具有子建议的建议时非常重要，例如漏洞评估扫描程序的结果或针对“应在计算机上安装系统更新”这一父建议的特定系统更新。
    
    - **支持导出安全功能分数数据。**

- **已添加合规性评估数据（预览）。** 现在，你可将对法规合规性评估的更新（包括针对任何自定义计划的更新）连续导出到 Log Analytics 工作区或事件中心。 此功能在国家云/主权云上不可用。

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="用于将法规合规性评估信息包含在连续导出数据中的选项。":::


## <a name="november-2020"></a>2020 年 11 月

11 月的更新包括：

- [添加了 29 条预览建议，以扩大 Azure 安全基准的覆盖范围](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [向安全中心的法规合规性仪表板添加了 NIST SP 800 171 R2](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [建议列表现包含筛选器](#recommendations-list-now-includes-filters)
- [自动预配体验得到改进和扩展](#auto-provisioning-experience-improved-and-expanded)
- [现可在连续导出中使用安全功能分数（预览）](#secure-score-is-now-available-in-continuous-export-preview)
- [“应在计算机上安装系统更新”建议现包含子建议](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Azure 门户中的“策略管理”页现在显示默认策略分配的状态](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

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

- 已正式发布（即不是预览版）
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


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>“应在计算机上安装系统更新”建议现包含子建议

“应在计算机上安装系统更新”建议已得到增强。 新版本包括针对每个缺失的更新的子建议，其中还引入了以下改进：

- 重新设计了 Azure 门户的 Azure 安全中心页面的体验。 “应在计算机上安装系统更新”的建议详细信息页包括发现结果列表，如下所示。 选择单个发现结果时，结果窗格将打开，并提供指向修正信息和受影响资源列表的链接。

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="在门户体验中打开其中一个子建议，了解更新的建议":::

- 丰富了 Azure Resource Graph (ARG) 的建议数据。 ARG 是一项 Azure 服务，专用于提供高效的资源探索。 可以使用 ARG 在一组给定的订阅中进行大规模查询，以便有效地控制环境。 

    对于 Azure 安全中心，你可以使用 ARG 和 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/) 来查询各种安全状态数据。

    以前，如果你在 ARG 中查询此建议，唯一提供的信息就是“需要在计算机上修正建议”。 以下查询的增强版本将返回按计算机分组的每个缺失的系统更新。

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Azure 门户中的“策略管理”页现在显示默认策略分配的状态

现在，你可以在 Azure 门户安全中心的“安全策略”页面中，查看订阅是否已分配到默认安全中心策略。

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Azure 安全中心的“策略管理”页显示默认策略分配":::

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

[详细了解启用了 Azure Arc 的服务器](../azure-arc/servers/index.yml)。


### <a name="azure-firewall-recommendation-added-preview"></a>添加了 Azure 防火墙建议（预览版）

添加了新的建议，即使用 Azure 防火墙保护所有虚拟网络。

“虚拟网络应受 Azure 防火墙保护”建议使用 Azure 防火墙限制虚拟网络的访问权限和防止潜在威胁。

了解有关 [Azure 防火墙](https://azure.microsoft.com/services/azure-firewall/)的详细信息。


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>“应在 Kubernetes 服务上定义已授权的 IP 范围”建议更新了快速修复

“应在 Kubernetes 服务上定义已授权的 IP 范围”建议现提供一个快速修复选项。

要详细了解此建议以及其他各项安全中心建议，请参阅[安全建议 - 参考指南](recommendations-reference.md)。

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="具有快速修复选项的“应在 Kubernetes 服务上定义已授权的 IP 范围”建议":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>法规合规性仪表板现在包含用于删除标准的选项

安全中心的法规合规性仪表板基于你满足特定合规控制和要求的情况来提供合规态势的见解。

该仪表板包含一组默认的法规标准。 如果提供的任何标准都与你的组织不相关，现在就可以简单地从订阅的 UI 中将其删除。 只能在“订阅”级别删除标准，而不能从管理组范围删除。

有关详细信息，请参阅[从仪表板中删除标准](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard)。


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>从 Azure Resource Graph (ARG) 中删除了 Microsoft.Security/securityStatuses 表

Azure Resource Graph 是 Azure 中的一项服务，旨在提供高效的资源浏览功能，它能够在一组给定的订阅中进行大规模查询，使你能够有效地管理环境。 

对于 Azure 安全中心，你可以使用 ARG 和 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/) 来查询各种安全状态数据。 例如：

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
- [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/)


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

有关详细信息，请参阅[利用资产清单浏览和管理资源](asset-inventory.md)。



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

Pod 安全策略（预览版）功能已设置为弃用，并且在 2020 年 10 月 15 日之后将不再提供，以支持 AKS 的 Azure Policy。

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