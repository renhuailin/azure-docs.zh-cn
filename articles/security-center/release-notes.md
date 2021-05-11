---
title: Azure 安全中心的发行说明
description: 介绍 Azure 安全中心的新增功能和已更改的功能
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/27/2021
ms.author: memildin
ms.openlocfilehash: 35873711753400132f47933f009365cc6383429b
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108123476"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 安全中心的新增功能

安全中心正在积极开发中，并不断得到改进。 为及时了解最新开发成果，此页提供了有关新功能、bug 修复和已弃用功能的信息。

本页面会频繁更新，请经常回来查看。 

若要了解即将在安全中心推出的计划性更改，请参阅[即将推出的对 Azure 安全中心的重要更改](upcoming-changes.md)。 

> [!TIP]
> 如果要查找 6 个月之前的项目，可查看 [Azure 安全中心的新增功能存档](release-notes-archive.md)。

## <a name="april-2021"></a>2021 年 4 月

4 月的更新包括：
- [最近拉取的容器注册表映像现在将每周重新扫描（正式发布）](#recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability)
- [使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署（预览版）](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview)
- [关于启用适用于 DNS 和资源管理器的 Azure Defender 的建议（预览版）](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview)
- [添加了三个监管合规性标准：Azure CIS 1.3.0、CMMC 级别 3 和限制性的新西兰 ISM](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [与来宾配置相关的四个新建议（预览版）](#four-new-recommendations-related-to-guest-configuration-preview)
- [CMK 建议移动到最佳做法安全控制](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 Azure Defender 警报已弃用](#11-azure-defender-alerts-deprecated)
- [“应用系统更新”安全控制中的两个建议已弃用](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [从 Azure Defender 仪表板中删除了适用于计算机上的 SQL 的 Azure Defender 磁贴](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [21 条建议在不同安全控件之间进行了调动](#21-recommendations-moved-between-security-controls)


### <a name="recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability"></a>最近拉取的容器注册表映像现在将每周重新扫描（正式发布）

适用于容器注册表的 Azure Defender 包含内置漏洞扫描程序。 此扫描程序会立即扫描推送到注册表中的任何映像以及在过去 30 天内拉取的任何映像。

每天都会发现新漏洞。 通过此更新，将每周重新扫描一次过去 30 天内从注册表拉取的容器映像。 这可确保在映像中识别新发现的漏洞。

扫描按映像收费，因此重新扫描不产生额外费用。

可在[使用适用于容器注册表的 Azure Defender 来扫描映像是否存在漏洞](defender-for-container-registries-usage.md)中详细了解此扫描程序。


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview"></a>使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署（预览版）

Azure Defender for Kubernetes 正在扩展其威胁防护功能，以便在部署时保护其群集。 这已通过集成[已启用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md) 及其新的[扩展功能](../azure-arc/kubernetes/extensions.md)来实现。 

在非 Azure Kubernetes 群集上启用 Azure Arc 后，根据 Azure 安全中心提供的新建议，仅需单击几下，即可将 Azure Defender 扩展部署到其中。

使用建议（启用 Azure Arc 的 Kubernetes 群集应已安装 Azure Defender 的扩展）和扩展保护在其他云提供程序中部署的 Kubernetes 群集，但不支持其托管的 Kubernetes 服务。

Azure 安全中心、Azure Defender 和启用了 Azure Arc 的 Kubernetes 之间的集成将带来以下影响：

- 轻松地将 Azure Defender 扩展预配到未受保护的 Azure Arc 启用 Kubernetes 群集（手动和大规模）
- 从 Azure Arc 门户监视 Azure Defender 扩展及其预配状态
- 请转到 Azure Arc 门户的新“安全性”页中，查看所报告的安全中心安全建议
- 请转到 Azure Arc 门户的新“安全性”页中，查看所报告的 Azure Defender 标识安全威胁
- 启用 Azure Arc 的 Kubernetes 群集可以集成到 Azure 安全中心平台和体验

有关详细信息，[请参阅通过本地和多云 Kubernetes 群集使用 Azure Defender for Kubernetes](defender-for-kubernetes-azure-arc.md)。

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="有关为已启用 Azure Arc 的 Kubernetes 群集部署 Azure Defender 扩展的 Azure 安全中心建议。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview"></a>关于启用适用于 DNS 和资源管理器的 Azure Defender 的建议（预览版）

添加了两项新建议，以简化启用[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 和[适用于 DNS 的 Azure Defender](defender-for-dns-introduction.md)：

- **应启用适用于资源管理器的 Azure Defender** - 适用于资源管理器的 Defender 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。
- **应启用适用于 DNS 的 Azure Defender** - 适用于 DNS 的 Defender 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。

启用 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：https://aka.ms/pricing-security-center。

> [!TIP]
> 预览版建议不会显示资源运行不正常，并且在计算安全功能分数时不会包含这些建议。 请尽量修正这些建议，以便在预览期结束之后，借助这些建议提高安全功能分数。 如需详细了解如何响应这些建议，请参阅[修正 Azure 安全中心的建议](security-center-remediate-recommendations.md)。


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>添加了三个监管合规性标准：Azure CIS 1.3.0、CMMC 级别 3 和限制性的新西兰 ISM

我们添加了适用于 Azure 安全中心的三个标准。 使用监管合规性仪表板，现在可以根据以下标准来跟踪合规性：

- [CIS Microsoft Azure 基础基准检验 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC 级别 3](../governance/policy/samples/cmmc-l3.md)
- [受限于新西兰 ISM](../governance/policy/samples/new-zealand-ism.md)

可以按照[在监管合规性仪表板中自定义标准集](update-regulatory-compliance-packages.md)中所述，将这些标准分配给订阅。

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="添加了三个标准，用于 Azure 安全中心的监管合规性仪表板。" lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

了解详细信息，请参阅：
- [在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)
- [教程：提高合规性](security-center-compliance-dashboard.md)
- [常见问题解答 - 法规合规性仪表板](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-preview"></a>与来宾配置相关的四个新建议（预览版）

Azure 的[来宾配置扩展](../governance/policy/concepts/guest-configuration.md) 向安全中心报告，以帮助确保强化虚拟机的来宾内设置。 已启用 Arc 的服务器不需要该扩展，因为已连接 Arc 的计算机代理中包含该扩展。 扩展需要计算机上系统托管标识。

我们已将四个新建议添加到安全中心，以便充分利用此扩展。

- 这两个建议会提示安装该扩展及其所需的系统托管标识：
    - **应在计算机上安装来宾配置扩展**
    - **应使用系统分配的托管标识来部署虚拟机的来宾配置扩展**

- 当扩展已安装并正在运行时，它将开始审核你的计算机，此时系统将提示你强化设置，例如配置操作系统和环境设置。 这两个建议将提示你按如下所述强化 Windows 和 Linux 计算机：
    - **应在计算机上启用 Windows Defender 攻击防护**
    - **对 Linux 虚拟机进行身份验证需要 SSH 密钥**

详细[了解 Azure Policy 的来宾配置](../governance/policy/concepts/guest-configuration.md)。

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK 建议移动到最佳做法安全控制

每个组织的安全计划都包含数据加密要求。 默认情况下，通过服务托管密钥对 Azure 客户的数据进行静态加密。 但是，客户管理的密钥 (CMK) 通常需要满足法规符合性标准。 通过 CMK 能够使用自己创建并拥有的 [Azure Key Vault](../key-vault/general/overview.md) 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。

Azure 安全中心的安全控制是相关安全建议的逻辑组，反映了你易受攻击的攻击面。 对于每个控制，可以看到为所有资源修正该控制中列出的所有建议后，安全评分可以增加的最高分数。 “实现安全最佳做法”这一安全控制得分为零。 因此，此控制中的建议不会影响安全评分。

下面列出的建议将移到“实现安全最佳做法”这一安全控制，以更好地反应它们的可选性质。 这一移动确保了这些建议都处于最适当的控制之下，以满足其目标。

- Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据
- Azure 机器学习工作区应使用客户管理的密钥 (CMK) 进行加密
- 认知服务帐户应启用使用客户管理的密钥 (CMK) 进行数据加密
- 容器注册表应使用客户管理的密钥 (CMK) 进行加密
- SQL 托管实例应使用客户管理的密钥进行静态数据加密
- SQL Server 应使用客户管理的密钥进行静态数据加密
- 存储帐户应使用客户管理的密钥 (CMK) 进行加密

请在[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解每个安全控件中的建议。


### <a name="11-azure-defender-alerts-deprecated"></a>11 Azure Defender 警报已弃用

下面列出的 11 个 Azure Defender 警报已弃用。

- 新警报将取代下面两种警报并扩大覆盖范围：

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | 预览版 - 检测到 MicroBurst 工具包“Get-AzureDomainInfo”函数运行 |
    | ARM_MicroBurstRunbook    | 预览版 - 检测到 MicroBurst 工具包“Get-AzurePasswords”函数运行  |
    |                          |                                                                          |

- 下面 9 种警报与已弃用的 Azure Active Directory 标识保护连接器 (IPC) 相关：

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | 不熟悉的登录属性 |
    | AnonymousLogin      | 匿名 IP 地址          |
    | InfectedDeviceLogin | 受恶意软件感染的 IP 地址     |
    | ImpossibleTravel    | 异常位置登录               |
    | MaliciousIP         | 恶意 IP 地址          |
    | LeakedCredentials   | 凭据泄露            |
    | PasswordSpray       | 密码喷射                |
    | LeakedCredentials   | Azure AD 威胁智能  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > 这 9 种 IPC 警报绝不是安全中心警报。 它们是 Azure Active Directory (AAD) 标识保护连接器 (IPC) 的一部分，此连接器将这些警警报发送给安全中心。 在过去 2 年中，只看到这些警报的客户就是在 2019 或更早版本中配置了导出（从连接器到 ASC）。 AAD IPC 继续将其显示其专属的警报系统中，并在 Azure Sentinel 中继续提供。 唯一的变化是它们不再出现在安全中心。

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>“应用系统更新”安全控制中的两项建议已弃用 

下面这两个建议已弃用，这些更改可能会对安全分数产生轻微影响：

- 应重启计算机来应用系统更新
- **应在计算机上安装监视代理**。 此建议仅与本地计算机相关，其中某些逻辑将转移到另一条建议，即“应在计算机上解决 Log Analytics 代理运行状况问题”

建议检查连续导出和工作流自动化配置，以查看这些建议是否包括在其中。 此外，任何仪表板或其他可能使用它们的监视工具都应该相应地进行更新。

有关这些建议的详细信息，请参阅[安全建议参考页面](recommendations-reference.md)。

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>从 Azure Defender 仪表板中删除了适用于计算机上的 SQL 的 Azure Defender 磁贴

Azure Defender 仪表板的覆盖区包括对应于环境相关 Azure Defender 计划的磁贴。 由于报告受保护和未受保护资源数量时出现问题，我们决定在解决此问题前暂时删除适用于计算机上的 SQL 的 Azure Defender 的资源覆盖状态。


### <a name="21-recommendations-moved-between-security-controls"></a>21 条建议在不同安全控件之间进行了调动 

以下建议已移动到其他的安全控件。 安全控件是相关安全建议的逻辑组，反映了你易受攻击的攻击面。 这一调动确保了每个建议都处于最适当的控制之下，以满足其目标。

请在[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解每个安全控件中的建议。

|建议 |更改和影响  |
|---------|---------|
|应对 SQL Server 启用漏洞评估<br>应对 SQL 托管实例启用漏洞评估<br>应立即修正 SQL 数据库的漏洞<br>应修正 VM 中的 SQL 数据库漏洞     |从“修正漏洞”（得 6 分）<br>变为“修正安全配置“（得 4 分）。<br>根据你的环境，这些建议会减少对评分的影响。|
|应该为你的订阅分配了多个所有者<br>自动化帐户变量应进行加密<br> IoT 设备 - 经审核的进程已停止发送事件<br> IoT 设备 - 操作系统基线验证失败<br> IoT 设备 - 需要进行 TLS 加密套件升级<br> IoT 设备 - 打开设备上的端口<br> IoT 设备 - 在其中一个链中找到了宽容防火墙策略<br> IoT 设备 - 在输入链中找到了宽容防火墙规则<br> IoT 设备 - 在输出链中找到了宽容防火墙规则<br>应启用 IoT 中心的诊断日志<br> IoT 设备 - 代理正在发送未充分利用的消息<br>IoT 设备 - 默认 IP 筛选策略应为“拒绝”<br>IoT 设备 - IP 筛选器规则为“大范围 IP”<br>IoT 设备 - 应调整代理消息间隔和大小<br>IoT 设备 - 完全相同的身份验证凭据<br>IoT 设备 - 经审核的进程停止发送事件<br>IoT 设备 - 应修复操作系统 (OS) 基线配置|移到 **实施安全最佳做法**。<br>如果某条建议移到“实施安全最佳做法”控制（不得分），则这条建议不再影响安全分数。|
|||


## <a name="march-2021"></a>2021 年 3 月

3 月的更新包括：

- [集成到安全中心的 Azure 防火墙管理](#azure-firewall-management-integrated-into-security-center)
- [SQL 漏洞评估现在包含“禁用规则”体验（预览）](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [集成到安全中心的 Azure Monitor 工作簿以及提供的三个模板](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [法规合规性仪表板现在包含 Azure 审核报告（预览）](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [可以在 Azure Resource Graph 中通过“在 ARG 中浏览”查看建议数据](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [更新部署工作流自动化的的策略](#updates-to-the-policies-for-deploying-workflow-automation)
- [两条旧版建议不再将数据直接写入 Azure 活动日志](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [建议页面功能增强](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>集成到安全中心的 Azure 防火墙管理

打开 Azure 安全中心时，首先显示的是概述页面。 

此交互式仪表板提供了混合云工作负载安全状况的统一视图。 此外，它还显示安全警报、覆盖范围信息等等。

在帮助你通过中心体验查看安全状态的过程中，我们已将 Azure 防火墙管理器集成到此仪表板中。 你现在可以检查所有网络的防火墙覆盖状态，并从安全中心开始集中管理 Azure 防火墙策略。

有关此仪表板的详细信息，请参阅 [Azure 安全中心的概述页面](overview-page.md)。

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="安全中心的概述仪表板，其中包含 Azure 防火墙的磁贴":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>SQL 漏洞评估现在包含“禁用规则”体验（预览）

安全中心包含内置漏洞扫描仪，有助于发现、跟踪和修正潜在的数据库漏洞。 评估扫描结果概述了 SQL 计算机的安全状态以及任何安全发现结果的详细信息。

如果组织需要忽略发现结果，而不是修正漏洞，则可以选择禁用发现结果。 禁用发现结果不会影响安全分数，也不会产生有害的噪音。

有关详细信息，请参阅[禁用特定发现结果](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)。



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>集成到安全中心的 Azure Monitor 工作簿以及提供的三个模板

在 Ignite Spring 2021 召开过程中，我们宣布在安全中心中推出集成式 Azure Monitor 工作簿。

可以利用新集成开始使用安全中心的库中的现成模板。 通过使用工作簿模板，可以访问并生成动态、可视化报告，以跟踪组织的安全状况。 此外，还可以根据安全中心数据或任何其他受支持的数据类型创建新的工作簿，并且快速部署来自安全中心 GitHub 社区的社区工作簿。

提供了三个模板报告：

- 一段时间内的安全功能分数 - 跟踪订阅的分数以及对资源建议的更改
- 系统更新 - 按资源、OS 和严重性等查看缺失的系统更新
- 漏洞评估发现结果 - 查看对 Azure 资源进行漏洞扫描的发现结果

如需了解如何使用这些报告或生成自己的报告，请参阅[创建安全中心数据的丰富的交互式报告](custom-dashboards-azure-workbooks.md)。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="一段时间内的安全功能分数报告":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>法规合规性仪表板现在包含 Azure 审核报告（预览）

现在可以从法规合规性仪表板的工具栏下载 Azure 和 Dynamics 认证报告。 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="法规合规性仪表板的工具栏":::

可以选择用于相关报告类型（PCI、SOC 和 ISO 等）的选项卡，然后使用筛选器来查找所需的特定报告。

有关详细信息，请参阅[管理法规合规性仪表板中的标准](update-regulatory-compliance-packages.md)。

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="筛选可用 Azure 审核报告的列表":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>可以在 Azure Resource Graph 中通过“在 ARG 中浏览”查看建议数据

建议详细信息页现在包含“在 ARG 中浏览”工具栏按钮。 请使用此按钮来打开 Azure Resource Graph 查询，并浏览、导出和共享建议的数据。

使用 Azure Resource Graph (ARG)，可以通过可靠的筛选、分组和排序功能，快速访问你的云环境中的资源信息。 这是以编程方式或从 Azure 门户中查询 Azure 订阅中的信息的一种快速且有效的方式。

详细了解 [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)。

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="在 Azure Resource Graph 中浏览建议数据。":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>对部署工作流自动化的的策略的更新

自动执行组织的监视和事件响应流程可以显著缩短调查和缓解安全事件所需的时间。

我们提供三个 Azure Policy“DeployIfNotExist”策略，这些策略可以创建并配置工作流自动化过程，以便你可以在组织内部署自动化：

|目标  |策略  |策略 ID  |
|---------|---------|---------|
|安全警报的工作流自动化|[为 Azure 安全中心警报部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|安全建议的工作流自动化|[为 Azure 安全中心建议部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|用于法规合规性的工作流自动化发生更改|[部署 Azure 安全中心合规工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

以下是对这些策略的功能进行的两项更新：

- 分配后，它们将通过强制执行保持启用状态。
- 现在可以自定义这些策略，并更新任意参数，即使它们已部署，也是如此。 例如，如果用户想添加另一个评估密钥或编辑现有的评估密钥，则可以执行此操作。

开始使用[工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

了解如何[自动响应安全中心触发器](workflow-automation.md)。


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>两条旧版建议不再将数据直接写入 Azure 活动日志 

安全中心将几乎所有安全建议的数据传递到 Azure 顾问，后者又将数据写入 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)。

对于其中两条建议，数据将同时直接写入 Azure 活动日志。 通过这项更改，安全中心会停止将这些旧版安全建议的数据直接写入活动日志。 而我们要将数据导出到 Azure 顾问，就像我们针对所有其他建议所做的那样。

这两条旧版建议为：
- 应在计算机上解决 Endpoint Protection 运行状况问题
- 应该修复计算机上安全配置中的漏洞

如果你一直是在活动日志的“TaskDiscovery 类型的建议”类别中访问这两条建议的信息，现在不再可以这样操作。


### <a name="recommendations-page-enhancements"></a>建议页面功能增强 

我们发布了改进版本的建议列表，以便直观地显示更多信息。

此页面现在显示：

1. 每个安全控制措施的最高分数和当前分数。
1. 图标（代替标记），如“修复”和“预览” 。
1. 一个新列，显示与每条建议相关的[策略计划](security-policy-concept.md)，在禁用“按控制措施分组”时显示。

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Azure 安全中心的建议页的功能增强 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Azure 安全中心的建议“平面”列表的功能增强 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

可在 [Azure 安全中心内的安全建议](security-center-recommendations.md)中了解详细信息。


## <a name="february-2021"></a>2021 年 2 月

2 月的更新包括：

- [Azure 门户中的新安全警报页发布了正式版 (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Kubernetes 工作负载保护建议发布了正式版 (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Microsoft Defender for Endpoint 与 Azure Defender 的集成现在支持 Windows Server 2019 和 Windows 10 虚拟桌面 (WVD)（预览）](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [直接链接到建议详细信息页中的策略](#direct-link-to-policy-from-recommendation-details-page)
- [SQL 数据分类建议不再影响安全功能分数](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [工作流自动化可以由对法规合规性评估的更改触发（预览）](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [资产清单页增强功能](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Azure 门户中的新安全警报页发布了正式版 (GA)

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


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Kubernetes 工作负载保护建议发布了正式版 (GA)

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

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="链接到 Azure Policy 页面，了解支持建议的特定策略":::

使用此链接可查看策略定义和计算逻辑。 

如果查看[安全建议参考指南](recommendations-reference.md)上的建议列表，你还将看到指向策略定义页面的链接：

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="直接从 Azure 安全中心建议参考页访问 Azure Policy 页面来了解特定策略" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>SQL 数据分类建议不再影响安全功能分数
“应对 SQL 数据库中的敏感数据进行分类”建议不再影响安全功能分数。 这是“应用数据分类”安全控件中唯一的建议，因此该控件目前的安全功能分数值为 0。

有关安全中心中所有安全控件的完整列表及其分数和每个安全控件中的建议列表，请参阅[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)。

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>工作流自动化可以由对法规合规性评估的更改触发（预览）
我们向工作流自动化的触发器选项添加了第三种数据类型：对监管合规性评估的更改。

了解如何使用[自动响应安全中心触发器](workflow-automation.md)中的工作流自动化工具。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="使用对监管合规性评估的更改来触发工作流自动化" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>资产清单页增强功能
安全中心的资产清单页在以下方面有所改进：

- 页面顶部的摘要现在包括“未注册的订阅”，显示未启用安全中心的订阅数。

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="资产清单页面顶部摘要中未注册订阅的计数":::

- 筛选器进行了扩展和增强，包括：
    - **计数** - 每个筛选器都会显示满足每一类条件的资源数

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Azure 安全中心“资产清单”页的筛选器中的计数":::

    - **包含豁免筛选器**（可选）- 将结果范围缩小为有/没有豁免的资源。 默认情况下不会显示此筛选器，但可从 **添加筛选器** 按钮进行访问。

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="在 Azure 安全中心的“资产清单”页中添加筛选器“包含豁免”":::

详细了解如何[利用资产清单浏览和管理资源](asset-inventory.md)。

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

- [详细了解 Azure 安全基准](/security/benchmark/azure/introduction)
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

确保 Web 应用请求的是肯定会增强其安全性的证书。 但是，对于面向公众的 Web 应用，这是不相关的。 如果通过 HTTP 而不是 HTTPS 访问站点，不会收到任何客户端证书。 因此，如果应用程序需要客户端证书，则你不应允许通过 HTTP 对应用程序发出请求。 有关详细信息，请参阅[为 Azure 应用服务配置 TLS 相互身份验证](../app-service/app-service-web-configure-tls-mutual-auth.md)。

在此更改后，此建议现在已是推荐的最佳做法，不会影响你的分数。 

请在[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解每个安全控件中的建议。


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>建议页面包含用于环境、严重性和可用响应的新筛选器

Azure 安全中心会监视所有已连接的资源并生成安全建议。 可使用这些建议来强化你的混合云状况，并跟踪与组织、行业和国家/地区相关的策略和标准的合规性。

随着安全中心不断扩展其覆盖范围和功能，安全建议的列表每月都在扩充。 例如，请参阅[添加了 29 条预览建议，以扩大 Azure 安全基准的覆盖范围](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)。

随着列表的扩充，需要筛选建议来找出最感兴趣的建议。 11 月，我们在“建议”页面中添加了筛选器（请参阅[建议列表现包含筛选器](#recommendations-list-now-includes-filters)）。

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

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="建议列表的筛选器":::


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