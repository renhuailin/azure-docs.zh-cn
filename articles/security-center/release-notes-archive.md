---
title: Azure 安全中心的新增功能存档
description: 介绍 Azure 安全中心在六个月以前的新增功能和已更改的功能。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 08/03/2021
ms.author: memildin
ms.openlocfilehash: b826ed1ab9fbe2c55e8fd4737cb3a1c83bae1766
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734986"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Azure 安全中心的新增功能存档

[Azure 安全中心的新增功能](release-notes.md)发行说明主页包含过去六个月的更新，而本页包含更早以前的项目。

本页提供以下方面的信息：

- 新增功能
- Bug 修复
- 已弃用的功能

## <a name="february-2021"></a>2021 年 2 月

2 月的更新包括：

- [Azure 门户中的“新建安全警报”页面已正式发布 (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Kubernetes 工作负载保护建议已正式发布 (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Microsoft Defender for Endpoint 与 Azure Defender 的集成现在支持 Windows Server 2019 和 Windows 10 虚拟桌面 (WVD)（预览）](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [直接链接到建议详细信息页中的策略](#direct-link-to-policy-from-recommendation-details-page)
- [SQL 数据分类建议不再影响安全功能分数](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [工作流自动化可以由对法规合规性评估的更改触发（预览）](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [资产清单页增强功能](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Azure 门户中的“新建安全警报”页面已正式发布 (GA)

Azure 安全中心的安全警报页经过重新设计，可提供以下内容：

- 更好的警报会审体验 - 列表包含可自定义的筛选器和分组选项，有助于减少警报疲劳，让你能够更轻松地专注于相关度最高的威胁。
- 警报列表中包含更多信息 - 例如 MITRE ATT&ACK 策略。
- 用于创建示例警报的按钮 - 要评估 Azure Defender 功能并测试警报 配置（对于 SIEM 集成、电子邮件通知和工作流自动化），可以从所有 Azure Defender 计划创建示例警报。
- 与 Azure Sentinel 事件体验的一致性 - 对于同时使用这两种产品的客户，现在可以更直接地在它们之间进行切换。
- 更好的大型警报列表性能。
- 警报列表键盘导航。
- **来自 Azure Resource Graph 的警报** - 可以在 Azure Resource Graph 中查询警报，它是适用于所有资源的类 Kusto API。 如果要构建自己的警报仪表板，这也很有用。 [详细了解 Azure Resource Graph](../governance/resource-graph/index.yml)。
- 创建示例警报功能 - 若要通过新的警报体验创建示例警报，请参阅[生成 Azure Defender 示例警报](security-center-alert-validation.md#generate-sample-azure-defender-alerts)。

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure 安全中心的安全警报列表":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Kubernetes 工作负载保护建议已正式发布 (GA)

我们很高兴地宣布，已正式发布一组针对 Kubernetes 工作负载保护的建议。

为了确保 Kubernetes 工作负载在默认情况下是安全的，安全中心添加了 Kubernetes 级别的强化建议，其中包括具有 Kubernetes 准入控制的执行选项。

在 Azure Kubernetes 服务 (AKS) 群集上安装适用于 Kubernetes 的 Azure Policy 加载项后，将按照预先定义的一组最佳做法（显示为 13 条安全建议）监视对 Kubernetes API 服务器的每个请求，然后再将其保存到群集。 然后，可以配置为强制实施最佳做法，并规定将其用于未来的工作负载。

例如，可以规定不应创建特权容器，并且阻止以后的任何请求。

有关详细信息，请参阅[使用 Kubernetes 准入控制实现工作负载保护最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)。

> [!NOTE]
> 虽然建议之前为预览版，但它们当时未显示 AKS 群集资源运行不正常，而且在计算安全功能分数时没有纳入这些建议。 发布此 GA 后，计算分数时将纳入这些建议。 如果尚未对其进行修正，则可能会对安全功能分数造成轻微影响。 请尽量进行修正，具体请参阅[修正 Azure 安全中心内的建议](security-center-remediate-recommendations.md)。


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Microsoft Defender for Endpoint 与 Azure Defender 的集成现在支持 Windows Server 2019 和 Windows 10 虚拟桌面 (WVD) （预览）

Microsoft Defender for Endpoint 是一种整体的、云交付的终结点安全解决方案。 它提供基于风险的漏洞管理和评估以及终结点检测和响应 (EDR)。 有关将 Defender for Endpoint 与 Azure 安全中心一起使用的好处的完整列表，请参阅[使用安全中心的集成式 EDR 解决方案 (Microsoft Defender for Endpoint) 保护终结点](security-center-wdatp.md)。

为 Windows Server 上的服务器启用 Azure Defender 时，Defender for Endpoint 的许可证会包含在计划在中。 如果已为服务器启用 Azure Defender 并且订阅中有 Windows 2019 服务器，则它们会自动接收包含此更新的 Defender for Endpoint。 无需手动操作。 

支持现在已扩展为包括 Windows Server 2019 和 [Windows 虚拟桌面 (WVD)](../virtual-desktop/overview.md)。

> [!NOTE]
> 如果要在 Windows Server 2019 计算机上启用 Defender for Endpoint，请确保它满足[启用 Microsoft Defender for Endpoint 集成](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)中所述的先决条件。

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>直接链接到建议详细信息页中的策略

查看建议的详细信息时，能够查看基础策略通常会很有帮助。 对于策略支持的每条建议，建议详细信息页面上都有一个新链接：

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="链接到 Azure Policy 页面，了解支持建议的具体策略。":::

使用此链接可查看策略定义和计算逻辑。 

如果查看[安全建议参考指南](recommendations-reference.md)上的建议列表，你还将看到指向策略定义页面的链接：

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="直接从 Azure 安全中心建议参考页访问 Azure Policy 页面来了解具体策略。" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>SQL 数据分类建议不再影响安全功能分数
“应对 SQL 数据库中的敏感数据进行分类”建议不再影响安全功能分数。 这是“应用数据分类”安全控件中唯一的建议，因此该控件目前的安全功能分数值为 0。

有关安全中心中所有安全控件的完整列表及其分数和每个安全控件中的建议列表，请参阅[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)。


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>工作流自动化可以由对法规合规性评估的更改触发（预览）
我们向工作流自动化的触发器选项添加了第三种数据类型：对监管合规性评估的更改。

了解如何使用[自动响应安全中心触发器](workflow-automation.md)中的工作流自动化工具。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="使用对监管合规性评估的更改来触发工作流自动化。" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>资产清单页增强功能
安全中心的资产清单页在以下方面有所改进：

- 页面顶部的摘要现在包括“未注册的订阅”，显示未启用安全中心的订阅数。

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="资产清单页面顶部摘要中未注册订阅的计数。":::

- 筛选器进行了扩展和增强，包括：
    - **计数** - 每个筛选器都会显示满足每一类条件的资源数

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Azure 安全中心“资产清单”页的筛选器中的计数。":::

    - **包含豁免筛选器**（可选）- 将结果范围缩小为有/没有豁免的资源。 默认情况下不会显示此筛选器，但可从 **添加筛选器** 按钮进行访问。

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="在 Azure 安全中心的“资产清单”页中添加筛选器“包含豁免”":::

详细了解如何[利用资产清单浏览和管理资源](asset-inventory.md)。



## <a name="january-2021"></a>2021 年 1 月

一月的更新包括：

- [Azure 安全基准现在是 Azure 安全中心的默认策略计划](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [本地和多云计算机的漏洞评估已正式发布 (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [预览版中现可提供管理组的安全分数](#secure-score-for-management-groups-is-now-available-in-preview)
- [安全功能分数 API 已正式发布 (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [用于应用服务的 Azure Defender 添加了无关联的 DNS 保护](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [多云连接器已正式发布 (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
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

- [详细了解 Azure 安全基准](/security/benchmark/azure/introduction)
- [在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>本地和多云计算机的漏洞评估已正式发布 (GA)

10 月，我们随[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 的集成式漏洞评估扫描器（由 Qualys 提供支持）提供了扫描已启用 Azure Arc 的服务器的预览。

现已正式发布 (GA)。

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

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>安全功能分数 API 已正式发布 (GA)

现在可以通过[安全分数 API](/rest/api/securitycenter/securescores/) 访问分数。 通过 API 方法，可灵活地查询数据，久而久之构建自己的安全功能分数报告机制。 例如：

- 使用安全功能分数 API 获取特定订阅的分数
- 使用安全功能分数控件 API 列出安全控件和订阅的当前分数

若要了解可通过安全功能分数 API 实现的外部工具，请参阅 [GitHub 社区的安全功能分数区域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

详细了解 [Azure 安全中心的安全分数和安全控件](secure-score-security-controls.md)。


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>用于应用服务的 Azure Defender 添加了无关联的 DNS 保护

子域接管是组织常见的严重威胁。 当拥有指向已撤销的网站的 DNS 记录时，可能会发生子域接管。 这类 DNS 记录也称为“无关联的 DNS”项。 CNAME 记录特别容易受到此威胁的攻击。 

通过子域接管，威胁参与者可以将要流向组织的域的流量重定向到执行恶意活动的站点。

现在，当对应用服务网站解除授权时，用于应用服务的 Azure Defender 会检测到无关联的 DNS 条目。 此时，DNS 条目指向不存在的资源，并且网站容易受到子域接管攻击。 无论你的域是由 Azure DNS 托管还是由外部域注册器托管，都可使用这些保护；这些保护既适用于 Windows 上的应用服务，也适用于 Linux 上的应用服务。

了解详细信息：

- [应用服务警报引用表](alerts-reference.md#alerts-azureappserv) - 包括两个新的 Azure Defender 警报，它们在检测到无关联的 DNS 条目时触发
- [防止无关联的 DNS 条目并避免子域接管](../security/fundamentals/subdomain-takeover.md) - 了解子域接管威胁和无关联的 DNS 方面
- [用于应用服务的 Azure Defender 简介](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>多云连接器已正式发布 (GA)

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

有时，当你知道问题已被第三方工具解决，而安全中心未检测到时，资源将被列为运行不正常。 或者，建议会在你认为它不属于的范围内显示。 该建议可能不适用于特定的订阅。 或者，组织可能决定接受与特定资源或建议相关的风险。

使用此预览功能，现在可以针对建议创建免除，以执行以下操作：

- 免除某资源，以确保其将来不会被列入运行不正常的资源，并且不会影响安全分数。 该资源将被列为不适用，原因将显示“已免除”以及你选择的特定理由。

- 免除某订阅或管理组，以确保建议不会影响安全分数，并且将来不会针对该订阅或管理组显示。 这与现有资源以及将来创建的任何资源相关。 建议将标记有你针对所选范围选择的特定理由。

了解详细信息，请参阅[从安全评分中免除资源和建议](exempt-resource.md)。



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>用户现在可以向其全局管理员请求租户范围内的可见性

如果用户不具有查看安全中心数据的权限，他们现在将会看到一个链接，用于向其组织的全局管理员请求权限。 该请求包括他们想要的角色以及需要这样做的理由。

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="横幅通知用户他们可以请求租户范围内的权限。":::

了解详细信息，请参阅[当你的权限不足时请求租户范围内的权限](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)。


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>添加了 35 条预览建议，以扩大 Azure 安全基准的覆盖范围

[Azure 安全基准](/security/benchmark/azure/introduction)是 Azure 安全中心的默认策略计划。 

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

- [详细了解 Azure 安全基准](/security/benchmark/azure/introduction)
- [详细了解 Azure Database for MariaDB](../mariadb/overview.md)
- [详细了解 Azure Database for MySQL](../mysql/overview.md)
- [详细了解 Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>将经筛选的建议列表导出为 CSV 

2020 年 11 月，我们在“建议”页面中添加了筛选器（[建议列表现包含筛选器](release-notes-archive.md#recommendations-list-now-includes-filters)）。 12 月，我们扩展了这些筛选器（[建议页面包含用于环境、严重性和可用响应的新筛选器](release-notes-archive.md#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)）。 

随着此次公告的发布，我们将更改为“下载到 CSV”按钮的行为，使 CSV 导出仅包含经筛选的列表中当前显示的建议。 

例如，在下图中，可以看到列表已筛选为显示两个建议。 生成的 CSV 文件包括受这两个建议影响的每项资源的状态详细信息。   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="将经筛选的建议导出到 CSV 文件。":::

可在 [Azure 安全中心内的安全建议](security-center-recommendations.md)中了解详细信息。


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>在 Azure Policy 评估中，“不适用”资源现报告为“合规”

以前，为建议评估且发现不适用的资源在 Azure Policy 中显示为“不合规”。 任何用户操作都不能将资源状态更改为“合规”。 进行此更改后，为了显得更加清楚，将资源报告为“合规”。

唯一的影响是 Azure Policy 中合规资源的数量将增加。 Azure 安全中心的安全评分不受影响。


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>通过连续导出（预览版）导出安全分数和法规合规性数据的每周快照

我们已在[连续导出](continuous-export.md)工具中添加了新的预览功能，用于导出安全分数和法规合规性数据的每周快照。

定义连续导出时，请设置导出频率：

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="选择连续导出的频率。":::

- **流式处理** - 更新资源的运行状况时，将发送评估（如果没有更新，则不发送任何数据）。
- **快照** - 每周将发送所有法规合规性评估的当前状态的快照（这是面向安全分数和法规合规性数据每周快照的一项预览功能）。

请在[持续导出安全中心数据](continuous-export.md)中详细了解此功能的所有性能。

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
- 计算机上的 Azure Defender for SQL 服务器：将相同的保护扩展到混合、多云和本地环境中的 SQL 服务器

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

若要向你自己分配租户级别的权限，请按照[授予自己租户范围的权限](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)中的说明操作。


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

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="带有新预览版提醒体验链接的横幅。":::

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

确保 Web 应用请求的是肯定会增强其安全性的证书。 但是，对于面向公众的 Web 应用，这是不相关的。 如果通过 HTTP 而不是 HTTPS 访问站点，不会收到任何客户端证书。 因此，如果应用程序需要客户端证书，则你不应允许通过 HTTP 对应用程序发出请求。 有关详细信息，请参阅[为 Azure 应用服务配置 TLS 相互身份验证](../app-service/app-service-web-configure-tls-mutual-auth.md)。

在此更改后，此建议现在已是推荐的最佳做法，不会影响你的分数。 

请在[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解每个安全控件中的建议。


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>建议页面包含用于环境、严重性和可用响应的新筛选器

Azure 安全中心会监视所有已连接的资源并生成安全建议。 可使用这些建议来强化你的混合云状况，并跟踪与组织、行业和国家/地区相关的策略和标准的合规性。

随着安全中心不断扩展其覆盖范围和功能，安全建议的列表每月都在扩充。 例如，请参阅[添加了 29 条预览建议，以扩大 Azure 安全基准的覆盖范围](release-notes-archive.md#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)。

随着列表的扩充，需要筛选建议来找出最感兴趣的建议。 11 月，我们在“建议”页面中添加了筛选器（请参阅[建议列表现包含筛选器](release-notes-archive.md#recommendations-list-now-includes-filters)）。

本月添加的筛选器提供了一些选项，可根据以下条件优化建议列表：

- 环境 - 查看有关 AWS、GCP 或 Azure 资源（或任何组合）的建议
- 严重性 - 根据安全中心设置的严重性分类来查看建议
- **响应操作** - 根据安全中心响应选项的可用性来查看建议：修复、拒绝和强制实施

    > [!TIP]
    > “响应操作”筛选器替代了“可用的快速修复(是/否)”筛选器。 
    > 
    > 请详细了解每个响应选项：
    > - [修复按钮](security-center-remediate-recommendations.md#fix-button)
    > - [使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="按安全控制分组的建议。" lightbox="./media/release-notes/added-recommendations-filters.png":::

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

Azure 安全基准是 Microsoft 制定的一组 Azure 专属准则，适合基于常见合规框架的安全性与合规性最佳做法。 [详细了解 Azure 安全基准](/security/benchmark/azure/introduction)。

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

- [详细了解 Azure 安全基准](/security/benchmark/azure/introduction)
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

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="建议列表的筛选器。":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>自动预配体验得到改进和扩展

通过在新的和现有的 Azure VM 上安装所需的扩展，使 VM 能够受益于安全中心的保护，自动预配功能有助于降低管理开销。 

随着 Azure 安全中心的发展，更多的扩展得到了开发，安全中心可以监视更大的资源类型列表。 自动预配工具已经过扩展，现支持通过 Azure Policy 的功能使用其他扩展和资源类型。

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

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="在门户体验中打开其中一个子建议，了解更新的建议。":::

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

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Azure 安全中心的“策略管理”页面，其中显示了默认策略分配。":::



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

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="具有快速修复选项的“应在 Kubernetes 服务上定义授权 IP 范围”建议。":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>法规合规性仪表板现在包含用于删除标准的选项

安全中心的法规合规性仪表板基于你满足特定合规控制和要求的情况来提供合规态势的见解。

该仪表板包含一组默认的法规标准。 如果提供的任何标准都与你的组织不相关，那么现在简单操作一下就可在订阅的 UI 中将它们删除。 只能在“订阅”级别删除标准，而不能从管理组范围删除。

有关详细信息，请参阅[从仪表板中删除标准](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)。


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>从 Azure Resource Graph (ARG) 中删除了 Microsoft.Security/securityStatuses 表

Azure Resource Graph 是 Azure 中的一项服务，旨在提供高效的资源浏览功能，它能够在一组给定的订阅中进行大规模查询，使你能够有效地管理环境。 

对于 Azure 安全中心，你可以使用 ARG 和 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/) 来查询各种安全状态数据。 例如：

- 资产清单利用 (ARG)
- 我们提供了一个示例 ARG 查询，说明如何[在未启用多重身份验证 (MFA) 的情况下标识帐户](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

ARG 中提供了可以在查询中使用的数据表。

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph 浏览器和可用的表。":::

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

我们已发布安全中心门户页面的更新 UI。 新页面中有一个新的概述页面，还有安全功能分数、资产清单和 Azure Defender 的面板。

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

使用连续导出将警报和建议流式传输到 Azure 事件中心、Log Analytics 工作区或 Azure Monitor。 在此处可以将此数据与 SIEM（如 Azure Sentinel、Power BI、Azure 数据资源管理器等）集成。

安全中心的集成漏洞评估工具在“父”建议中将有关资源的发现结果作为可操作性建议返回，例如“应修正虚拟机中的漏洞”。 

现在选择建议并启用“包括安全性结果”选项时，可以通过连续导出来导出安全性结果。

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="在连续导出配置中包含安全检测结果切换开关。" :::

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

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="带有预览标志的建议。":::

[详细了解安全功能分数](secure-score-security-controls.md)。


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>建议现包含严重性指示器和刷新时间间隔

现在，建议的详细信息页面包括一个刷新时间间隔指示器（如相关），并且清楚显示了建议的严重性。

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="显示新鲜度和严重性的建议页面。":::


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

安全中心已添加对[安全默认值](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)的完全支持，即 Microsoft 的免费标识安全保护。

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
|**应在虚拟机上启用漏洞评估解决方案**|替换以下两条建议：<br> ***** 在虚拟机上启用内置漏洞评估解决方案（由 Qualys 提供支持）（现已弃用）（仅标准层显示此建议）<br> ***** 漏洞评估解决方案应安装在虚拟机上（现已弃用）（标准和免费层显示此建议）|
|**应修正虚拟机中的漏洞**|替换以下两条建议：<br>***** 修正虚拟机上发现的漏洞（由 Qualys 提供支持）（现已弃用）<br>***** 应通过漏洞评估解决方案修正漏洞（现已弃用）|
|||

现在可根据同一建议从 Qualys 或 Rapid7 等合作伙伴部署安全中心的漏洞评估扩展或专用许可解决方案（“BYOL”）。

此外，发现漏洞并报告到安全中心时，无论哪个漏洞评估解决方案标识了结果，都会有一条建议提醒你注意这些结果。

#### <a name="updating-dependencies"></a>更新依赖项

如果脚本、查询或自动化引用了先前的建议或策略密钥/名称，请使用下表更新引用：

##### <a name="before-august-2020"></a>2020 年 8 月之前

| 建议|范围|
|----|:----|
|**在虚拟机上启用内置漏洞评估解决方案（由 Qualys 提供支持）**<br>注册表项：550e890b-e652-4d22-8274-60b3bdb24c63|内置|
|修正虚拟机上发现的漏洞（由 Qualys 提供支持）<br>注册表项：1195afff-c881-495e-9bc5-1486211ae03f|内置|
|应在虚拟机上安装漏洞评估解决方案<br>注册表项：01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**应通过漏洞评估解决方案修复漏洞**<br>注册表项：71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
|||


|策略|范围|
|----|:----|
|**应对虚拟机启用漏洞评估**<br>策略 ID：501541f7-f7e7-4cd6-868c-4190fdad3ac9|内置|
|**应通过漏洞评估解决方案修正漏洞**<br>策略 ID：760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
|||


##### <a name="from-august-2020"></a>2020 年 8 月之后

|建议|范围|
|----|:----|
|**应在虚拟机上启用漏洞评估解决方案**<br>密钥：ffff0522-1e88-47fc-8382-2a80ba848f5d|内置 + BYOL|
|**应修正虚拟机中的漏洞**<br>注册表项：1195afff-c881-495e-9bc5-1486211ae03f|内置 + BYOL|
|||

|策略|范围|
|----|:----|
|[应对虚拟机启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>策略 ID：501541f7-f7e7-4cd6-868c-4190fdad3ac9 |内置 + BYOL|
|||


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
- [威胁防护功能中适用于 Azure Kubernetes 服务群集的安全警报](alerts-reference.md#alerts-k8scluster)
- [威胁防护功能中适用于 Azure Kubernetes 服务主机的安全警报](alerts-reference.md#alerts-containerhost)
- [容器的安全建议](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>更新了自适应应用程序控制，添加了新建议以及对路径规则中的通配符的支持

自适应应用程序控制功能已收到两个重要更新：

* 一项新的建议指出以前不允许的可能合法的行为。 “应更新自适应应用程序控制策略中的允许列表规则”这一新建议提示向现有策略添加新规则，以减少自适应应用程序控制违规警报的误报数。

* 路径规则现支持通配符。 在此更新中，可以使用通配符配置允许的路径规则。 支持以下两种方案：

    * 在路径末尾使用通配符允许该文件夹和子文件夹中的所有可执行文件

    * 如果在路径中间使用通配符，则可在变化的文件夹名称中具有已知的可执行文件名称（例如，使用已知可执行文件的个人用户文件夹、自动生成的文件夹名称等）。


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
- [SQL 计算机（Azure、其他云和本地）的高级数据安全（预览）](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [将 Log Analytics 代理部署到 Azure Arc 计算机的两条新建议（预览）](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [大规模创建连续导出和工作流自动化配置的新策略](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [使用 NSG 保护非面向 Internet 的虚拟机的新建议](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [启用威胁防护和高级数据安全性的新策略](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>安全功能分数 API（预览）

可以通过[安全功能分数 API](/rest/api/securitycenter/securescores/)（当前处于预览阶段）立即访问分数。 通过 API 方法，可灵活地查询数据，久而久之构建自己的安全功能分数报告机制。 例如，可以使用安全功能分数 API 来获取特定订阅的分数。 此外，还可以使用安全功能分数控件 API 列出安全控件和订阅的当前分数。

有关使用安全功能分数 API 实现的外部工具的示例，请参阅 [GitHub 社区的安全功能分数区域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。

详细了解 [Azure 安全中心的安全分数和安全控件](secure-score-security-controls.md)。



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>SQL 计算机（Azure、其他云和本地）的高级数据安全（预览）

Azure 安全中心的 SQL 计算机高级数据安全现在保护托管在 Azure 中、其他云环境，甚至本地计算机上的 SQL Server。 这会扩展 Azure 原生 SQL Server 的保护，以完全支持混合环境。

高级数据安全可为任何位置的 SQL 计算机提供漏洞评估和高级威胁防护。

设置包含两个步骤：

1. 将 Log Analytics 代理部署到 SQL Server 的主机计算机，以提供与 Azure 帐户的连接。

1. 在安全中心的“定价和设置”页中启用可选捆绑。

详细了解 [SQL 计算机的高级数据安全](defender-for-sql-usage.md)。



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>将 Log Analytics 代理部署到 Azure Arc 计算机的两条新建议（预览）

添加了两条新建议，以帮助将 [Log Analytics 代理](../azure-monitor/agents/log-analytics-agent.md)部署到 Azure Arc 计算机，并确保其受 Azure 安全中心的保护：

- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上(预览)
- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上(预览)

这些新建议将出现在“应在计算机上安装监视代理”这一现有（相关）建议所在的四个安全控制中：修正安全配置、应用自适应应用程序控制、应用系统更新，以及启用 Endpoint Protection。

建议还包括快速修复功能，以帮助加快部署过程。 

有关这两项新建议的详细信息，请参阅[计算和应用建议](recommendations-reference.md#recs-compute)。

若要详细了解 Azure 安全中心如何使用代理，请参阅[什么是 Log Analytics 代理？](./faq-data-collection-agents.yml#what-is-the-log-analytics-agent-)

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

有关详细详细，请参阅[网络建议](recommendations-reference.md#recs-networking)表。




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


## <a name="may-2020"></a>2020 年 5 月

5 月的更新包括以下内容：
- [警报抑制规则（预览版）](#alert-suppression-rules-preview)
- [虚拟机漏洞评估现已正式发布](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [对实时 (JIT) 虚拟机 (VM) 访问权限的更改](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [自定义建议已移至单独的安全控件](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [已添加开关，可在控件中显示建议或以简单列表的形式显示](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [扩展了“实现安全最佳做法”这一安全控件](#expanded-security-control-implement-security-best-practices)
- [具有自定义元数据的自定义策略现已正式发布](#custom-policies-with-custom-metadata-are-now-generally-available)
- [故障转储分析功能正在迁至无文件攻击检测中](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>警报抑制规则（预览版）

这项新功能目前为预览版，它可帮助缓解警报疲劳。 可使用规则来自动隐藏已知无害或已知与你组织中的正常活动相关的警报。 这可让你专注于最相关的威胁。 

仍将生成与你启用的抑制规则相匹配的警报，但它们的状态将设置为“已取消”。 你可在 Azure 门户中查看状态，也可在安全中心查看安全警报。

抑制规则定义了自动取消警报所应遵循的条件。 通常，使用抑制规则来：

- 取消已标识为“误报”的警报

- 取消限制过于频繁地触发而失去作用的警报

详细了解如何[取消来自 Azure 安全中心威胁防护服务的警报](alerts-suppression-rules.md)。


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>虚拟机漏洞评估现已正式发布

安全中心的标准层现包含集成的虚拟机漏洞评估，该扩展免费提供。 该扩展由 Qualys 提供支持，但将检测结果直接报告回安全中心。 你无需具备 Qualys 许可证，甚至还不需要 Qualys 帐户 - 所有操作都在安全中心内无缝执行。

这一新的解决方案可持续扫描你的虚拟机来找出漏洞，并在安全中心显示检测结果。 

若要部署该解决方案，请使用新的安全建议：

“在虚拟机上启用内置漏洞评估解决方案（由 Qualys 提供支持）”

详细了解[安全中心集成的虚拟机漏洞评估](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)。



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>对实时 (JIT) 虚拟机 (VM) 访问权限的更改

安全中心包含一项可选功能，可保护 VM 的管理端口。 这可抵御最常见形式的暴力攻击。

本次更新就此功能进行了以下更改：

- 重命名了推荐你在 VM 上启用 JIT 的建议。 之前称为“应在虚拟机上应用实时网络访问控制”，而现在叫做“应通过即时网络访问控制来保护虚拟机的管理端口”。

- 建议仅在有管理端口打开时才触发。

详细了解 [JIT 访问功能](security-center-just-in-time.md)。


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>自定义建议已移至单独的安全控件

安全功能分数增强版引入的其中一个安全控制是“实现安全最佳做法”。 为订阅创建的所有自定义建议已自动放入该控件中。 

为便于查找自定义建议，我们已将这些建议移到一个名为“自定义建议”的专用安全控件中。 此控件不会影响你的安全功能分数。

要详细了解安全控件，请参阅 [Azure 安全中心的安全功能分数增强版（预览版）](secure-score-security-controls.md)。


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>已添加开关，可在控件中显示建议或以简单列表的形式显示

安全控件是相关安全建议的逻辑组。 它们反映了易受攻击的攻击面。 控件是一组安全建议，附有帮助你实施这些建议的说明。

若要立即查看组织对每个攻击面的保护情况，请查看每个安全控件的分数。

默认情况下，你的建议显示在安全控件中。 通过本次更新，你还可以采用列表形式显示它们。 若要以简单列表的形式查看它们，且列表按受影响的资源的运行状况排序，请使用新的“按控件分组”开关。 开关位于门户中列表的上面。

安全控件及其开关是新的安全功能分数体验的一部分。 请记得在门户中提供反馈。

要详细了解安全控件，请参阅 [Azure 安全中心的安全功能分数增强版（预览版）](secure-score-security-controls.md)。

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="建议的“按控件分组”开关。":::

### <a name="expanded-security-control-implement-security-best-practices"></a>扩展了“实现安全最佳做法”这一安全控件 

安全功能分数增强版引入的其中一个安全控制是“实现安全最佳做法”。 如果建议在此控件中显示，则不影响安全功能分数。 

通过本次更新，已将三项建议从它们原先所在的控件移动到这个最佳做法控件中。 我们采取此步骤的原因是我们判定这三项建议的风险比最初设想的要低。

此外，还引入了两项新建议，它们也添加到了此控件中。

移动的三项建议如下：

- **应在对订阅拥有读取权限的帐户上启用 MFA**（原先位于“启用 MFA”控件中）
- **应从订阅中删除具有读取权限的外部帐户**（原先位于“管理访问和权限”控件中）
- **只多只为订阅指定 3 个所有者**（原先位于“管理访问和权限”控件中）

添加到控件中的两项新建议如下：

- **应在 Windows 虚拟机上安装来宾配置扩展（预览版）** - 如果使用 [Azure Policy 来宾配置](../governance/policy/concepts/guest-configuration.md)，则可在虚拟机中查看服务器和应用程序设置（仅限 Windows）。

- **应在计算机上启用 Windows Defender 攻击防护（预览版）** - Windows Defender 攻击防护采用 Azure Policy 来宾配置代理。 攻击防护服务具有 4 个组件，旨在锁定设备来阻隔各种攻击途径，并阻止恶意软件攻击中常用的行为，同时让企业能够平衡其安全风险和生产力要求（仅限 Windows）。

要详细了解 Windows Defender 攻击防护，可参阅[创建和部署攻击防护策略](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)。

要详细了解安全控件，请参阅[安全功能分数增强版（预览版）](secure-score-security-controls.md)。



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>具有自定义元数据的自定义策略现已正式发布

自定义策略现显示在安全中心的建议体验、安全功能分数和法规符合性标准仪表板中。 此功能现已正式发布，可用于在安全中心扩大你组织的安全评估范围。 

在 Azure 策略中创建自定义计划，向该计划添加策略并将它加入 Azure 安全中心，然后将它作为建议直观呈现。

现在，我们还添加了可编辑自定义建议元数据的选项。 元数据选项中有严重级别、修正步骤和威胁信息等。  

详细了解[利用详细信息增强自定义建议](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information)。



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>故障转储分析功能正在迁至无文件攻击检测中 

我们正在将 Windows 故障转储分析 (CDA) 检测功能集成到[无文件攻击检测](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers)中。 无文件攻击检测分析改进了 Windows 计算机的以下安全警报：“发现代码注入”、“检测到伪装 Windows 模块”、“发现 Shell 代码”和“检测到可疑代码段”。

该转换的一些优势如下：

- **主动及时检测恶意软件** - 使用 CDA 方法时，会等到故障发生后再运行分析来查找恶意项目。 使用无文件攻击检测后，可在内存中威胁正在运行时主动识别它们。 

- **警报信息更丰富** - 来自无文件攻击检测的安全警报包含 CDA 中不提供的丰富信息，例如有效网络连接信息。 

- **警报聚合** - CDA 在一个故障转储中检测到多个攻击模式时，会触发多个安全警报。 而无文件攻击检测将从同一进程中确定的所有攻击模式组合到一个警报中，免去了关联多个警报的必要性。

- **降低了对 Log Analytics 工作区的要求** - 包含潜在敏感数据的故障转储将无法上传到 Log Analytics 工作区。






## <a name="april-2020"></a>2020 年 4 月

4 月的更新包括：
- [动态符合性包现已正式发布](#dynamic-compliance-packages-are-now-generally-available)
- [标识建议现包含在 Azure 安全中心的免费层中](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>动态符合性包现已正式发布

Azure 安全中心的法规符合性仪表板现包含动态符合性包（现已正式发布），可跟踪更多行业和法规标准。

可通过安全中心的安全策略页面将动态符合性包添加到订阅或管理组中。 加入标准或基准后，该标准会出现在法规符合性仪表板中，所有关联的符合性数据都映射为评估。 还将提供已加入的所有标准的摘要报表供下载。

现在，你可添加如下标准：

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official 和 UK NHS**
- **加拿大联邦 PBMM**
- **Azure CIS 1.1.0（新版）** ，它是 Azure CIS 1.1.0 的完整表示形式

此外，我们还添加了 Azure 安全基准，它是 Microsoft 创作的特定于 Azure 的准则，适合基于常见符合性框架的安全与合规最佳做法。 其他标准一经提供就将在仪表板中受到支持。  
 
详细了解如何[在法规符合性仪表板中自定义一组标准](update-regulatory-compliance-packages.md)。


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>标识建议现包含在 Azure 安全中心的免费层中

Azure 安全中心免费层中针对标识和访问的安全建议现已正式发布。 这是我们努力使云安全状态管理 (CSPM) 功能免费而取得的成果之一。 截至目前，这些建议仅在标准定价层中提供。

标识和访问建议的示例包括：

- “应在对订阅拥有所有者权限的帐户上启用多重身份验证。”
- “最多只能为订阅指定 3 个所有者。”
- “应从订阅中删除弃用的帐户。”

如果你有订阅在免费定价层，则此更改将影响它们的安全功能分数，因为它们之前从未接受过标识和访问安全性评估。

详细了解[标识和访问建议](recommendations-reference.md#recs-identityandaccess)。

详细了解[管理订阅上的多重身份验证 (MFA) 实施](security-center-identity-access.md)。



## <a name="march-2020"></a>2020 年 3 月

3 月的更新包括：

- [工作流自动化现已正式发布](#workflow-automation-is-now-generally-available)
- [Azure 安全中心与 Windows Admin Center 的集成](#integration-of-azure-security-center-with-windows-admin-center)
- [Azure Kubernetes 服务保护](#protection-for-azure-kubernetes-service)
- [改进了实时体验](#improved-just-in-time-experience)
- [弃用了两项针对 Web 应用的安全建议](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>工作流自动化现已正式发布

Azure 安全中心现已正式发布工作流自动化功能。 它可用于在安全警报和建议上自动触发逻辑应用。 此外，也可对提供了快速修复选项的警报和所有建议执行手动触发。

每个安全计划都包含事件响应的多个工作流。 这些流程可能包含通知相关利益干系人、启动更改管理进程，以及应用特定的修正步骤。 安全专家建议你尽可能多地将这些流程自动化。 自动化可减少开销，还可确保根据你预定义的要求快速、一致地执行处理步骤，从而增强安全性。

若要详细了解用于运行工作流的自动和手动安全中心功能，请参阅[工作流自动化](workflow-automation.md)。

详细了解如何[创建逻辑应用](../logic-apps/logic-apps-overview.md)。


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure 安全中心与 Windows Admin Center 的集成

现可将本地 Windows 服务器从 Windows Admin Center 直接移动到 Azure 安全中心。 安全中心然后变成你的单一管理界面，可在这里查看 Windows Admin Center 各项资源（包括本地服务器、虚拟机和其他 PaaS 工作负载）的安全信息。

在将服务器从 Windows Admin Center 直接移动到 Azure 安全中心后，你将能够：

- 在 Windows Admin Center 的安全中心扩展中查看安全警报和建议。
- 在 Azure 门户的安全中心内（或通过 API）查看安全状态和检索 Windows Admin Center 托管服务器的其他详细信息。

详细了解[如何将 Azure 安全中心与 Windows Admin Center 集成](windows-admin-center-integration.md)。


### <a name="protection-for-azure-kubernetes-service"></a>Azure Kubernetes 服务保护

Azure 安全中心正在扩展其容器安全功能，现可保护 Azure Kubernetes 服务 (AKS)。

常见的开源平台 Kubernetes 已被广泛采用，现在已成为容器业务流程方面的行业标准。 尽管得到了广泛实施，但在如何保护 Kubernetes 环境方面，人们仍然缺少了解。 要抵御容器化应用程序的攻击面，需要具备专业知识来确保基础结构已安全配置且受到持续监视，防范潜在威胁。

安全中心的防范包括：

- **发现和可见性** - 在安全中心内注册的订阅中持续发现托管的 AKS 实例。
- **安全建议** - 帮助你遵守 AKS 安全最佳做法的可操作建议。 这些建议包含在安全功能分数中，确保被视为组织的安全状态的一部分。 你可能会看到的一个与 AKS 相关的建议示例是，“应使用基于角色的访问控制来限制对 Kubernetes 服务群集的访问”。
- **威胁防护** - 通过对 AKS 部署的持续分析，Azure 安全中心会提醒你注意在主机和 AKS 群集级别检测到的威胁和恶意活动。

详细了解 [Azure Kubernetes 服务与安全中心的集成](defender-for-kubernetes-introduction.md)。

详细了解[安全中心内的容器安全功能](container-security.md)。


### <a name="improved-just-in-time-experience"></a>改进了实时体验

增强了 Azure 安全中心内保护管理端口的实时工具的功能、操作和 UI，如下所示： 

- **“理由”字段** - 通过 Azure 门户的实时页面请求对虚拟机 (VM) 的访问时，现有一个新的可选字段可用来输入请求原因。 可在活动日志中跟踪在此字段中输入的信息。 
- **自动清除冗余实时 (JIT) 规则** - 无论何时更新 JIT 策略，都会自动运行清理工具来检查整个规则集的有效性。 该工具还将查看你策略中的规则与 NSG 中的规则之间是否存在不匹配的情况。 如果清理工具发现不匹配的情况，它将确定原因，并在确定可安全操作的情况下，删除不再需要的内置规则。 清理工具绝不会删除你创建的规则。 

详细了解 [JIT 访问功能](security-center-just-in-time.md)。


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>弃用了两项针对 Web 应用的安全建议

即将弃用下面两项与 Web 应用相关的安全建议： 

- 应加强 IaaS NSG 上 Web 应用的规则。
    （相关策略：应该强化 IaaS 上 Web 应用程序的 NSG 规则）

- 应限制对应用服务的访问。
    （相关策略：应限制对应用服务的访问 [预览版]）

这些建议将不再在安全中心的建议列表中显示。 相关策略将不再包含在名为“安全中心默认设置”的计划中。

详细了解[安全建议](recommendations-reference.md)。




## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-preview"></a>面向 Linux 的无文件攻击检测（预览版）

随着攻击者越来越多地采用更隐蔽的方法来避免被发现，除了 Windows 之外，Azure 安全中心还扩大了无文件攻击检测范围，现涵盖 Linux。 无文件攻击利用软件漏洞、将恶意有效负载注入良性系统进程，并隐藏在内存中。 这些技术：

- 最大程度地减少或消除了磁盘上恶意软件的痕迹
- 大大降低了基于磁盘的恶意软件扫描解决方案的检测机会

为了应对这种威胁，Azure 安全中心于 2018 年 10 月发布了面向 Windows 的无文件攻击检测，现在还将该检测扩展到了 Linux 上。 



## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score-preview"></a>安全功能分数增强版（预览版）

Azure 安全中心的安全功能分数增强版现提供预览版。 在此版本中，多个建议被组合到安全控件中，可更好地反映出你易受攻击的攻击面（例如限制对管理端口的访问）。

请在预览阶段熟悉安全功能分数的更改之处，确定可帮助你进一步保护环境的其他修正措施。

详细了解[安全功能分数增强版（预览版）](secure-score-security-controls.md)。



## <a name="november-2019"></a>2019 年 11 月

11 月的更新包括：
 - [针对北美区域 Azure Key Vault 的威胁防护（预览版）](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [针对 Azure 存储的威胁防护包括恶意软件信誉屏蔽](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [使用逻辑应用实现工作流自动化（预览版）](#workflow-automation-with-logic-apps-preview)
 - [批量资源快速修复功能已推出正式版](#quick-fix-for-bulk-resources-generally-available)
 - [扫描容器映像的漏洞（预览版）](#scan-container-images-for-vulnerabilities-preview)
 - [其他监管合规标准（预览版）](#additional-regulatory-compliance-standards-preview)
 - [针对 Azure Kubernetes 服务的威胁防护（预览版）](#threat-protection-for-azure-kubernetes-service-preview)
 - [虚拟机漏洞评估（预览版）](#virtual-machine-vulnerability-assessment-preview)
 - [Azure 虚拟机上的 SQL 服务器的高级数据安全性（预览版）](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [支持自定义策略（预览版）](#support-for-custom-policies-preview)
 - [在面向社区和合作伙伴的平台中扩大 Azure 安全中心的涵盖范围](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [支持导出建议和警报的高级集成（预览版）](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [从 Windows 管理中心将本地服务器加入安全中心（预览版）](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>针对北美区域 Azure Key Vault 的威胁防护（预览版）

Azure Key Vault 是一个基本服务，它通过提供集中管理云中密钥、机密、加密密钥和策略的功能，来保护数据和提高云应用程序的性能。 由于 Azure Key Vault 存储敏感数据和业务关键数据，因此必须保证密钥保管库及其存储的数据的最高安全性。

Azure 安全中心对 Azure Key Vault 的威胁防护的支持提供额外的安全情报层，用于检测以非寻常和可能有害的方式访问或恶意利用密钥保管库的企图。 此新保护层使得客户无需成为安全专家或管理安全监视系统，就能应对其密钥保管库受到的威胁。 此功能在北美区域推出了公共预览版。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>针对 Azure 存储的威胁防护包括恶意软件信誉屏蔽

针对 Azure 存储的威胁防护提供由 Microsoft 威胁情报支持的新检测，可以使用哈希信誉分析来检测将恶意软件上传到 Azure 存储的行为，并可以检测从活动的 Tor 出口节点（一个匿名代理）进行的可疑访问。 现在，你可以使用 Azure 安全中心查看在各个存储帐户中检测到的恶意软件。


### <a name="workflow-automation-with-logic-apps-preview"></a>使用逻辑应用实现工作流自动化（预览版）

在集中管理安全性和 IT/运营的组织的环境中发现差异时，这些组织可以实施内部工作流程来驱动所需的操作。 在许多情况下，这些工作流是可重复的流程，而自动化可以在组织内部大幅简化流程。

目前我们正在安全中心内引入一项新功能，它可以让客户创建利用 Azure 逻辑应用的自动化配置，并创建可根据具体 ASC 发现结果自动触发这些配置的策略（例如“建议”或“警报”）。 可将 Azure 逻辑应用配置为执行逻辑应用连接器大型社区所支持的任何自定义操作，或使用安全中心提供的某个模板，例如，发送电子邮件或开具 ServiceNow&trade; 票证。

若要详细了解用于运行工作流的自动和手动安全中心功能，请参阅[工作流自动化](workflow-automation.md)。

若要了解如何创建逻辑应用，请参阅 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>批量资源快速修复功能已推出正式版

由于用户在安全评分中要执行许多任务，有效修正大型机群中的问题可能会变得困难。

为了简化对安全错误配置的修正，快速修正针对批量资源的建议并提高安全评分，请使用“快速修复”修正措施。

此操作允许你选择要对其应用修正的资源，并启动一个将代表你对设置进行配置的修正操作。

现在，安全中心建议页上已向客户推出了快速修复正式版。

在[安全建议参考指南](recommendations-reference.md)中了解哪些建议启用了快速修复。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>扫描容器映像的漏洞（预览版）

Azure 安全中心现在可以扫描 Azure 容器注册表中容器映像的漏洞。

映像扫描的工作原理是分析容器映像文件，然后查看是否存在任何已知漏洞（由 Qualys 提供支持）。

将新容器映像推送到 Azure 容器注册表时，会自动触发扫描本身。 发现的漏洞将以安全中心建议的形式显示，其中包括 Azure 安全评分，以及有关如何修补这些漏洞以减小允许的受攻击面的信息。


### <a name="additional-regulatory-compliance-standards-preview"></a>其他监管合规标准（预览版）

“监管合规”仪表板基于安全中心评估结果提供合规态势的见解。 该仪表板会显示你的环境是否符合特定法规标准和行业基准指定的控制措施与要求，并提供有关如何符合这些要求的规范性建议。

目前，监管合规仪表板支持四项内置标准：Azure CIS 1.1.0、PCI-DSS、ISO 27001 和 SOC-TSP。 我们现在宣布推出其他受支持标准的公共预览版：NIST SP 800-53 R4、SWIFT CSP CSCF v2020、加拿大联邦 PBMM、UK Official 和 UK NHS。 此外，我们正在发布 Azure CIS 1.1.0 的更新版本，其中涵盖了标准中的更多控制措施，并增强了扩展性。

[详细了解如何在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>针对 Azure Kubernetes 服务的威胁防护（预览版）

Kubernetes 很快就成了在云中部署和管理软件的新标准。 只有少量的用户对 Kubernetes 拥有丰富的经验；很多用户只是注重一般的工程和管理，而忽略了安全方面。 需要精心配置 Kubernetes 环境以使其保持安全，确保不会公开任何以容器为中心的受攻击面，避免为攻击者打开后门。 安全中心正在将其在容器领域的支持扩展到 Azure 中发展速度最快的服务之一 - Azure Kubernetes 服务 (AKS)。

此公共预览版中的新功能包括：

- **发现和可见性** - 在安全中心的已注册订阅中持续发现 AKS 托管实例。
- **安全功能分数建议** - 可操作的项目，用于帮助客户遵守 AKS 的安全最佳做法，并提高其安全功能分数。 建议包括诸如“应使用基于角色的访问控制来限制对 Kubernetes 服务群集的访问”之类的项目。
- **威胁检测** - 基于主机和群集的分析，例如“检测到特权容器”。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>虚拟机漏洞评估（预览版）

安装在虚拟机中的应用程序可能经常出现漏洞，导致虚拟机遭到入侵。 我们宣布，安全中心标准层包含针对虚拟机的内置漏洞评估，且不收取额外的费用。 由 Qualys 提供支持的漏洞评估公共预览版可让你持续扫描虚拟机上安装的所有应用程序以找出有漏洞的应用程序，并在安全中心门户体验中显示扫描结果。 安全中心负责处理所有部署操作，不需要用户额外付出精力。 接下来，我们计划提供漏洞评估选项来支持客户实现其独特的业务需求。

[详细了解针对 Azure 虚拟机的漏洞评估](deploy-vulnerability-assessment-vm.md)。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 虚拟机上的 SQL 服务器的高级数据安全性（预览版）

Azure 安全中心对 IaaS VM 上运行的 SQL 数据库的威胁防护和漏洞评估支持现已推出预览版。

[漏洞评估](../azure-sql/database/sql-vulnerability-assessment.md)是一项易于配置的服务，可以发现、跟踪并帮助修正潜在的数据库漏洞。 此服务可让你查看以 Azure 安全评分提供的安全态势，并包含用于解决安全问题和增强数据库防御能力的步骤。

[高级威胁防护](../azure-sql/database/threat-detection-overview.md)检测异常活动，这些活动指示有人企图以非寻常或可能有害的方式访问或恶意利用你的 SQL 服务器。 它会持续监视数据库中的可疑活动，并针对异常的数据库访问模式提供操作导向的安全警报。 这些警报提供可疑活动的详细信息，以及有助于调查和缓解威胁的建议操作。


### <a name="support-for-custom-policies-preview"></a>支持自定义策略（预览版）

Azure 安全中心现在支持自定义策略（预览版）。

我们的客户一直希望在安全中心内扩大其当前安全评估的涵盖范围，以便能够基于他们在 Azure Policy 中创建的策略执行自己的安全评估。 由于支持自定义策略，这种愿望已得到实现。

安全中心建议体验、安全评分和监管合规标准仪表板中将会包含这些新策略。 由于支持自定义策略，现在可以在 Azure Policy 中创建一个自定义计划，然后将其作为策略添加到安全中心，并将其作为建议可视化。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>在面向社区和合作伙伴的平台中扩大 Azure 安全中心的涵盖范围

使用安全中心不仅可以接收来自 Microsoft 的建议，还可以接收来自合作伙伴（例如 Check Point、Tenable 和 CyberArk）的现有解决方案中的建议，它们以后会提供更多的集成。  安全中心的简单加入流可以将你现有的解决方案连接到安全中心，使你能够在一个地方查看安全状况建议、运行统一的报告，以及根据内置的建议和合作伙伴的建议利用安全中心的所有功能。 你还可以将安全中心建议导出到合作伙伴产品。

[详细了解 Microsoft 智能安全关联](https://www.microsoft.com/security/partnerships/intelligent-security-association)。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>支持导出建议和警报的高级集成（预览版）

若要在安全中心的顶层实现企业级方案，现在可以在除 Azure 门户或 API 以外的其他位置使用安全中心警报和建议。 可直接将这些警报和建议导出到事件中心与 Log Analytics 工作区。 下面是可以围绕这些新功能创建的一些工作流：

- 由于可以导出到 Log Analytics 工作区，因此你可以使用 Power BI 创建自定义仪表板。
- 借助导出到事件中心的功能，可将安全中心警报和建议导出到第三方 SIEM、导出到第三方解决方案，或导出到 Azure 数据资源管理器。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>从 Windows 管理中心将本地服务器加入到安全中心（预览版）

Windows 管理中心是一个管理门户，适用于未在 Azure 中部署的 Windows Server，为它们提供多项 Azure 管理功能（例如备份和系统更新）。 我们最近添加了一项功能，允许直接从 Windows 管理中心体验加入这些非 Azure 服务器，使其受 ASC 保护。

使用此新体验，用户可以直接在 Windows 管理中心体验中将 WAC 服务器加入 Azure 安全中心，并查看其安全警报和建议。


## <a name="september-2019"></a>2019 年 9 月

9 月的更新包括：

 - [使用自适应应用程序控制管理规则的功能已改进](#managing-rules-with-adaptive-application-controls-improvements)
 - [使用 Azure Policy 控制容器安全建议](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>使用自适应应用程序控制管理规则的功能已改进

使用自适应应用程序控制管理虚拟机规则的体验已得到改进。 Azure 安全中心的自适应应用程序控制可帮助你控制哪些应用程序能够在你的虚拟机上运行。 除了对规则管理做出了一般性的改进外，在添加新规则时，你还可以通过一个新功能来控制要保护哪些文件类型。

[详细了解自适应应用程序控制](security-center-adaptive-application.md)。


### <a name="control-container-security-recommendation-using-azure-policy"></a>使用 Azure Policy 控制容器安全建议

现在可以通过 Azure Policy 启用或禁用 Azure 安全中心提供的用于修复容器安全相关漏洞的建议。

若要查看已启用的安全策略，请从安全中心打开“安全策略”页。


## <a name="august-2019"></a>2019 年 8 月

8 月的更新包括：

 - [Azure 防火墙的实时 (JIT) VM 访问](#just-in-time-jit-vm-access-for-azure-firewall)
 - [提升安全态势的一键式修正（预览版）](#single-click-remediation-to-boost-your-security-posture-preview)
 - [跨租户管理](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure 防火墙的实时 (JIT) VM 访问 

Azure 防火墙的实时 (JIT) VM 访问现已推出正式版。 使用此功能可以确保 Azure 防火墙保护的环境以及 NSG 保护的环境的安全。

JIT VM 访问使用 NSG 和 Azure 防火墙规则，仅在需要时才提供对 VM 的受控访问，以此减少受到网络容量耗尽攻击的风险。

为 VM 启用 JIT 时，可创建一个策略来确定要保护的端口、端口保持打开状态的时间，以及可从哪些已批准的 IP 地址访问这些端口。 此策略可帮助你控制用户在请求访问权限时可执行哪些操作。

请求将记录在 Azure 活动日志中，因此你可以轻松监视和审核访问。 此实时页面还可帮助你快速识别已启用 JIT 的现有 VM，以及建议启用 JIT 的 VM。

[详细了解 Azure 防火墙](../firewall/overview.md)。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>提升安全态势的一键式修正（预览版）

安全评分是一个可帮助你评估工作负荷安全状况的工具。 它会评审你的安全建议并确定其优先级，以便你知道要首先执行哪些建议。 这可帮助你找到最严重的安全漏洞，以确定调查优先级。

为了简化对安全错误配置的修正并帮助你快速提高安全评分，我们添加了一项新功能，允许你通过一次单击执行对大量资源的修正建议。

此操作允许你选择要对其应用修正的资源，并启动一个将代表你对设置进行配置的修正操作。

在[安全建议参考指南](recommendations-reference.md)中了解哪些建议启用了快速修复。


### <a name="cross-tenant-management"></a>跨租户管理

安全中心现在可通过 Azure Lighthouse 支持跨租户管理方案。 此功能使你能够在安全中心查看和管理多个租户的安全态势。 

[详细了解跨租户管理体验](security-center-cross-tenant-management.md)。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>网络建议的更新

Azure 安全中心 (ASC) 已推出新的网络建议，并改进了一些现有的建议。 现在，使用安全中心可以确保进一步为资源提供更好的网络保护。 

[详细了解网络建议](recommendations-reference.md#recs-networking)。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>自适应网络强化 - 正式版

公有云中运行的工作负荷面对的最大受攻击面之一是与公共 Internet 之间的连接。 我们的客户发现，他们很难知道要部署哪些网络安全组 (NSG) 规则来确保仅在所需的源范围内提供 Azure 工作负荷。 使用此功能，安全中心可以了解 Azure 工作负荷的网络流量和连接模式，并为面向 Internet 的虚拟机提供 NSG 规则建议。 这有助于我们的客户更好地配置其网络访问策略，并限制受到攻击的风险。 

[详细了解自适应网络强化](security-center-adaptive-network-hardening.md)。
