---
title: 指南和最佳做法
description: 了解将云和本地工作负载备份到云的最佳做法和指南
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: e2d65f256a69b397486675dc71efb71ce3e4263d
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605031"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>将云和本地工作负载备份到云

## <a name="introduction"></a>介绍

Azure 备份通过一种简单、安全、经济高效且不需要任何基础结构的解决方案来全面保护 Azure 中的数据资产。 它是 Azure 的内置数据保护解决方案，适用于各种工作负载。 它可帮助保护在云中运行的任务关键型工作负载，确保你的备份始终可用并在整个备份资产中以大规模的方式进行管理。

### <a name="intended-audience"></a>目标受众

本文的主要目标受众是 IT 和应用程序管理员，以及大型和中型组织的实施者，他们想要了解 Azure 内置数据保护技术与 Azure 备份的功能，以及如何高效实施用于更好地保护你的部署的解决方案。 本文假设你熟悉核心 Azure 技术、数据保护概念，并具有使用备份解决方案的经验。 本文中介绍的指南可让你更轻松地在 Azure 上使用已建立的模式设计备份解决方案，并避免出现已知问题。

### <a name="how-this-article-is-organized"></a>本文的组织方式

尽管在 Azure 上开始保护基础结构和应用程序十分容易，但当你确保基础 Azure 资源已经过正确设置并以最佳方式使用时，你可以加快实现价值的速度。 本文简要概述了用于以最佳方式配置 Azure 备份部署的设计注意事项和指南。 它剖析了核心组件（例如，恢复服务保管库、备份策略）和概念（例如治理），说明了如何理解它们以及其功能，并提供了详细产品文档的链接来帮助读者理解。

## <a name="architecture"></a>体系结构

![Azure 备份体系结构](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>工作负载

Azure 备份为各种工作负载（本地和云）启用数据保护。 它是 Azure 中安全可靠的内置数据保护机制。 它可以跨多个工作负载无缝缩放其保护，而无需你支付任何管理开销。 还有多个自动化通道可用于启用此功能（通过 PowerShell、CLI、Azure 资源管理器模板和 REST API）。

* 可缩放、持久且安全的存储 - Azure 备份使用带有内置安全性和高可用性功能的可靠 Blob 存储。 你可以选择将 LRS、GRS 或 RA-GRS 存储用于备份数据。  

* 原生工作负载集成 - Azure 备份提供与 Azure 工作负载（VM、SAP HANA、Azure VM 中的 SQL，甚至 Azure 文件存储）的原生集成，无需你管理自动化或基础结构来部署代理、编写新脚本或预配存储。

### <a name="data-plane"></a>数据平面

* 自动化存储管理 - Azure 备份会自动预配和管理备份数据的存储帐户，以确保存储帐户随着备份数据的增长而扩展。

* 恶意删除保护 - 通过备份的软删除来防止有人意外或恶意尝试删除备份。 已删除的备份数据将免费存储 14 天，并允许其从该状态恢复。

* **安全的加密备份 -** Azure 备份利用 Azure 平台的内置安全功能（例如 Azure RBAC 和加密），确保你的备份数据以安全的方式存储。

* 备份数据生命周期管理 - Azure 备份会自动清理较旧的备份数据，以遵循保留策略。 你还可以将操作存储中的数据分层存储到保管库存储中。

### <a name="management-plane"></a>管理平面

* 访问控制 - 保管库（恢复服务和备份保管库）提供管理功能，可通过 Azure 门户、备份中心、保管库仪表板、SDK、CLI 进行访问，甚至还可以通过 REST API 进行访问。 它也是 Azure RBAC 边界，让你可以选择对备份访问权限进行限制，仅限经过授权的管理员进行访问。

* 策略管理 - 每个保管库中的 Azure 备份策略定义了应该何时触发备份，以及需要将备份保留多长时间。 你还可以管理这些策略，并将它们应用于多个项。

* 监视和报告 - Azure 备份与 Log Analytics 集成，还提供了通过 Workbooks 查看报告的功能。

* 快照管理 - Azure 备份为某些 Azure 原生工作负载（VM 和 Azure 文件存储）创建快照、管理这些快照，并允许从这些快照快速还原。 此选项可大大减少将数据恢复到原始存储的时间。

## <a name="vault-considerations"></a>保管库注意事项

Azure 备份使用保管库（恢复服务保管库和备份保管库）来安排和管理备份。 它还使用保管库来存储备份的数据。 有效的保管库设计有助于组织建立一个结构来组织和管理 Azure 中的备份资产，以便支持你的业务优先级。 创建保管库时，请注意以下准则：  

### <a name="align-to-subscription-design-strategy"></a>与订阅设计策略一致

由于保管库的作用域限定为订阅，因此请调整你的保管库设计，以符合订阅设计策略，例如“应用程序类别策略”，其中订阅是根据特定应用程序或服务，或者按应用程序原型的系列来分隔的。 有关详细信息，请参阅[此文](/azure/cloud-adoption-framework/decision-guides/subscriptions/)。

### <a name="single-or-multiple-vault"></a>单个或多个保管库

可以使用单个或多个保管库来组织和管理备份。 遵循以下指南：

* 如果工作负载全部由单个订阅和单个资源管理，你可以使用单个保管库来监视和管理你的备份资产。

* 如果工作负载分布在多个订阅中，则可以创建多个保管库，每个订阅一个保管库或多个保管库。
  * 备份中心使你可以在一个管理工具中管理与备份相关的所有任务。 [在此处了解更多信息]()。
  * 可以通过工作簿模板来自定义视图。 备份资源管理器就是这样一个用于 Azure VM 的模板。 [在此处了解更多信息](monitor-azure-backup-with-backup-explorer.md)。
  * 如需对多个保管库应用一致策略，则可以使用 Azure Policy 来跨多个保管库传播备份策略。 可以编写自定义 [Azure Policy 定义](../governance/policy/concepts/definition-structure.md)，以使用 [‘deployifnotexists’](../governance/policy/concepts/effects.md#deployifnotexists) 效果跨多个保管库传播一个备份策略。 还可以将此 Azure Policy 定义[分配](../governance/policy/assign-policy-portal.md)到特定的范围（订阅或 RG），以便它将一个“备份策略”资源部署到该 Azure Policy 分配的范围内的所有恢复服务保管库。 备份策略的设置（例如备份频率、保留期等）应作为 Azure Policy 分配中的参数由用户指定。

* 随着组织的占用量的增长，你可能想要跨订阅移动工作负载，原因包括：根据备份策略进行调整，合并保管库，权衡降低冗余度以节省成本（从 GRS 迁移到 LRS）。  Azure 备份支持跨 Azure 订阅移动恢复服务保管库，或将其移动到同一订阅中的其他资源组。 [在此处了解更多信息](backup-azure-move-recovery-services-vault.md)。

### <a name="review-default-settings"></a>查看默认设置

在保管库中配置备份之前，请检查“存储复制类型”和“安全设置”的默认设置是否满足你的需求。

* “存储复制类型”默认设置为“异地冗余”(GRS)。 配置备份后，将禁用修改选项。 请遵循[这些](backup-create-rs-vault.md#set-storage-redundancy)步骤检查和修改设置。

* “软删除”对新建的保管库默认为“已启用”，旨在防止意外或恶意删除备份数据。 请遵循[这些](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)步骤检查和修改设置。

* 跨区域还原可用于将 Azure VM 还原到次要区域（Azure 配对区域）中。 使用此选项可以执行钻取来满足审核或符合性要求，以及在主要区域发生灾难时还原 VM 或其磁盘。 CRR 是适用于任何 GRS 保管库的可选功能。 [在此处了解更多信息](backup-create-rs-vault.md#set-cross-region-restore)。

* 在完成保管库设计之前，请查看[保管库支持矩阵](backup-support-matrix.md#vault-support)，以了解可能会影响或限制你的设计选择的因素。

## <a name="backup-policy-considerations"></a>备份策略注意事项

Azure 备份策略有两个组件：计划（何时进行备份）和 *保留期*（要将备份保留多长时间）。 可以基于要备份的数据的类型、RTO/RPO 要求、运营或法规符合性需求和工作负载类型（例如，VM、数据库、文件）定义策略。 [在此处了解更多信息](backup-architecture.md#backup-policy-essentials)。

创建备份策略时，请注意以下准则：

### <a name="schedule-considerations"></a>“计划”注意事项

* 请考虑将需要相同计划开始时间、频率和保留设置的 VM 分组到单个策略中。

* 请确保备份计划开始时间在非高峰生产应用程序时间段内。

* 若要分配备份流量，请考虑在一天的不同时间备份不同的 VM，并确保时间不重叠。

### <a name="retention-considerations"></a>“保留期”注意事项

* 短期保留可以是“分钟”或“按天”。 “按月”、“按周”或“按年”的备份点保留称为“长期保留”。

* 长期保留：
  * 已计划（符合性要求）- 如果事先知道数据是从当前时间起的数年内所必需的，请使用长期保留。
  * 未计划（按需要求）- 如果事先不知道，则可以使用特定自定义保留设置进行按需备份（这些自定义保留设置不受策略设置的影响）。

* 使用自定义保留期按需备份 - 如果需要执行一个未通过备份策略计划的备份，则可以使用按需备份。 如果要执行的备份不适合计划备份，或者要执行细化的备份（例如，每天多个 IaaS VM 备份，因为计划备份只允许每天一个备份），则按需备份可能十分有用。 请务必注意，在计划的策略中定义的保留策略不适用于按需备份。

### <a name="optimize-backup-policy"></a>优化备份策略

* 随着业务需求的变化，你可能需要延长或缩短保留期。 执行此操作时，可能会出现以下情况：  
  * 如果延长保留期，则会根据新策略对现有的恢复点进行标记和保留。
  * 如果缩短保留期，恢复点会标记为要在下一清理作业中删除，并且随后会被删除。
  * 最新的保留规则适用于所有保留点（不包括按需保留点）。 因此，如果保留期已延长（例如，延长到 100天），则在进行备份时，如果又缩短了保留期（例如，从 100 天缩短到 7 天），将根据最后指定的保留期（即 7 天）保留所有备份数据。

* 使用 Azure 备份可以灵活地停止保护和管理备份：
  * *停止保护并保留备份数据*。 如果要停用数据源（VM、应用程序）或对其解除授权，但需要保留数据以用于审核或合规目的，则可以使用此选项停止所有将来的备份作业保护数据源，并保留已备份的恢复点。 然后，可以还原或恢复 VM 保护。
  * *停止保护并删除备份数据*。 此选项将使所有将来的备份作业停止保护你的 VM 并删除所有恢复点。 你将无法还原 VM，也无法使用“恢复备份”选项。

  * 如果恢复保护（对于已停止且其数据已保留的数据源），则将应用保留规则。 任何过期的恢复点将被删除（在计划时间）。

* 在完成策略设计之前，请务必注意可能会影响你的设计选择的下列因素。
  * 一个备份策略的作用域限定为一个保管库。
  * 每个策略的项数有限制（例如，100 个 VM）。 若要缩放，可以创建具有相同或不同计划的重复策略。
  * 不能选择性地删除特定的恢复点。
  * 不能完全禁用计划的备份并使数据源处于受保护状态。 可以使用策略配置的最低频率的备份是每周执行计划的备份一次。 一种替代方法是停止保护且保留数据，并在每次要执行备份时启用保护，然后执行按需备份，之后关闭保护，但保留备份数据。 [在此处了解更多信息](backup-azure-manage-vms.md#stop-protecting-a-vm)。

## <a name="security-considerations"></a>安全注意事项

为了帮助你保护你的备份数据并满足你的业务安全需求，Azure 备份提供了机密性、完整性和可用性保障，防范有人蓄意攻击和滥用你的宝贵数据与系统。 请注意以下适用于 Azure 备份解决方案的安全准则：

### <a name="authentication-and-authorization"></a>身份验证和授权

* Azure 基于角色的访问控制 (Azure RBAC) 用于进行精细的访问管理、在团队中进行职责分离，以及仅将执行作业所需的最低访问权限授予用户。 [在此处了解更多信息](backup-rbac-rs-vault.md)。

* Azure 备份提供三个用于控制备份管理操作的内置角色：备份参与者、操作员和读取者。 [在此处了解更多信息](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)。

* Azure 备份服务中内置了多个安全控制机制，用于防止、检测和应对安全漏洞（了解详细信息）

* 恢复服务保管库使用的存储帐户是隔离的，恶意用户无法对其进行访问。 仅允许通过 Azure 备份管理操作（例如还原）进行访问。

### <a name="encryption-of-data-in-transit-and-at-rest"></a>传输中数据和静态数据的加密

加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

* 在 Azure 中，Azure 存储与保管库之间传输的数据受 HTTPS 保护。 此数据保留在 Azure 网络中。

* 使用 Microsoft 托管的密钥自动加密备份数据。 或者，你可以使用自己的密钥，也称为[客户托管密钥](encryption-at-rest-with-cmk.md)。

* Azure 备份支持备份和还原已使用 Azure 磁盘加密 (ADE) 功能加密了其 OS/数据磁盘的 Azure VM。 [在此处了解更多信息](backup-azure-vms-encryption.md)。

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>防止意外删除备份数据

Azure 备份提供安全功能来帮助保护备份数据，即使是删除了备份数据，也能予以恢复。 在使用软删除的情况下，如果用户删除了（属于 VM、SQL Server 数据库、Azure 文件共享、SAP HANA 数据库）备份，备份数据将额外保留 14 天，使该备份项可以恢复，而不会丢失数据。 以“软删除”状态将备份数据额外保留 14 天不会对你产生任何费用。 [在此处了解更多信息](backup-azure-security-feature-cloud.md)。

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>可疑活动的监视和警报

Azure 备份提供内置监视和警报功能，用于查看和配置 Azure 备份相关事件的操作。 [在此处了解更多信息](security-overview.md#monitoring-and-alerts-of-suspicious-activity)。

### <a name="security-features-to-help-protect-hybrid-backups"></a>用于帮助保护混合备份的安全功能

Azure 备份服务使用 Microsoft Azure 恢复服务 (MARS) 代理将本地计算机中的文件、文件夹和卷/系统状态备份和还原到 Azure。 MARS 现在提供了安全功能：通信短语可用于在上传到 Azure 备份之前加密以及在从 Azure 备份下载之后解密，已删除的备份数据将从删除之日起额外保留 14 天，以及关键操作（例如 更改密码）只能由拥有有效 Azure 凭据的用户执行。 [在此处了解更多信息](backup-azure-security-feature.md)。

## <a name="network-considerations"></a>网络注意事项

Azure 备份需要将工作负载中的数据移到恢复服务保管库。 Azure 备份提供多种功能，以防止无意中泄露备份数据（例如，通过网络上的中间人攻击）。 遵循以下指南：

### <a name="internet-connectivity"></a>Internet 连接

* Azure VM 备份 - 存储和 Azure 备份服务之间的所有必需通信和数据传输均在 Azure 网络中发生，无需访问你的虚拟网络。 因此，放置在受保护网络中的 Azure VM 的备份不需要你授予对任何 IP 或 FQDN 的访问权限。

* Azure VM 上的 SAP HANA 数据库、Azure VM 上的 SQL Server 数据库 - 要求连接到 Azure 备份服务、Azure 存储和 Azure Active Directory。 这可以通过使用专用终结点，或允许访问所需的公共 IP 地址或 FQDN 来实现。 如果不允许正确连接到所需的 Azure 服务，则可能会导致诸如数据库发现、配置备份、执行备份和还原数据等操作失败。 有关在使用 NSG 标记、Azure 防火墙和 HTTP 代理时可参考的完整网络指南，请参阅这些 [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) 和 [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) 文章。

* 混合 - 对于所有关键操作（安装、配置、备份和还原），MARS（Microsoft Azure 恢复服务）代理都需要进行网络访问。 MARS 代理可以通过以下这些方式连接到 Azure 备份服务：使用公共对等互连（可用于旧线路）和 Microsoft 对等互连通过 [Azure ExpressRoute](install-mars-agent.md#azure-expressroute-support) 进行连接；使用[专用终结点](install-mars-agent.md#private-endpoint-support)进行连接；通过[具有适当访问控制的代理/防火墙](install-mars-agent.md#verify-internet-access)进行连接。

### <a name="private-endpoints-for-azure-backup"></a>Azure 备份的专用终结点

Azure [专用终结点](../private-link/private-endpoint-overview.md)是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 通过 Azure 备份，你可以使用专用终结点从恢复服务保管库安全地备份和还原数据。

* 为保管库启用了专用终结点后，这些专用终结点仅用于备份 Azure VM 中的 SQL 和 SAP HANA 工作负载并向 Azure VM 还原这些工作负载，以及进行 MARS 代理备份。  还可以使用保管库来备份其他工作负载（尽管它们不需要专用终结点）。 除了备份 SQL 和 SAP HANA 工作负载以及使用 MARS 代理进行备份，专用终结点还可用于针对 Azure VM 备份执行文件恢复。 [在此处了解更多信息](private-endpoints-overview.md#recommended-and-supported-scenarios)。

* Azure Active Directory 当前不支持专用终结点。 因此，在 Azure VM 中执行数据库备份和使用 MARS 代理进行备份时，需要允许 Azure Active Directory 所需的 IP 和 FQDN 从受保护的网络进行出站访问。 如果适用，还可以使用 NSG 标记和 Azure 防火墙标记来允许访问 Azure AD。 详细了解[此处的先决条件](./private-endpoints.md#before-you-start)。

## <a name="governance-considerations"></a>治理注意事项

Azure 中的治理主要通过 [Azure Policy](../governance/policy/overview.md) 和 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)来实现。 [Azure Policy ](../governance/policy/overview.md)允许你创建、分配和管理策略定义，以强制执行资源规则。 此功能可使这些资源符合企业标准。 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)可用于跟踪 Azure 资源和其他云提供商的云使用情况和开支。 此外，下列工具（例如 [Azure 价格计算器](https://azure.microsoft.com/pricing/calculator/)和 [Azure 顾问](../advisor/advisor-overview.md)）在成本管理过程中扮演着重要的角色。

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure 备份通过内置的 Azure Policy 支持两个关键方案

* 确保使用适当的保留设置自动备份新创建的业务关键型计算机。 Azure 备份提供了一个内置策略（使用 Azure Policy），可以将其分配给“订阅或资源组中指定位置的所有 Azure VM”。 将此策略分配到给定范围时，该范围中创建的所有新 VM 都将自动配置为备份到位于“同一位置和订阅中的现有保管库”。 用户可以指定备份的 VM 应关联的保管库和保留策略。 [在此处了解更多信息](backup-azure-auto-enable-backup.md)。

* 确保新创建的保管库已启用诊断功能以支持报告。 通常，为每个保管库手动添加诊断设置是一项繁琐的任务。 此外，创建的任何新保管库也需要启用诊断设置，以便你可以查看有关此保管库的报告。 为了简化大规模创建诊断设置的过程（以 Log Analytics 为目标），Azure 备份提供了内置的 Azure Policy。 此策略为每个订阅或资源组中的所有保管库添加 LA 诊断设置。 以下部分介绍了如何使用此策略。 [在此处了解更多信息](azure-policy-configure-diagnostics.md)。

### <a name="azure-backup-cost-considerations"></a>Azure 备份成本注意事项

Azure 备份服务的功能让你可以灵活地有效管理你的成本，并且仍然满足你的 BCDR（业务连续性和灾难恢复）业务要求。 遵循以下指南：

* 使用定价计算器可以通过调整各种杠杆来评估和优化成本。 [在此处了解详细信息](azure-backup-pricing.md)

* 备份资源管理器：使用“备份资源管理器”或“备份报告”来了解备份存储的增长情况并对其进行优化，停止对非关键工作负载或已删除 VM 进行备份。 可以从备份的角度获取你整个资产的聚合视图。 这不仅包括有关备份项的信息，还包括未为备份配置的资源的相关信息。 这可确保你永远不会错过保护不断增长的资产中的关键数据，并且你的备份会针对非关键工作负载或已删除的工作负载进行优化。

* 优化备份策略
  * 基于工作负载原型（例如，任务关键型、非关键型）优化计划和保留设置
  * 针对“即时还原”优化保留设置
  * 选择适当的备份类型来满足要求，同时考虑到 Azure 备份中的工作负载所支持的备份类型（完整备份、增量备份、日志备份、差异备份）。

* 选择性地备份磁盘：“排除磁盘”（预览功能）提供了一种高效且符合成本效益的选择，用于选择性地备份关键数据。 例如，你可以只备份一个磁盘（如果你不想备份附加到 VM 的其他磁盘）。 这在你有多个备份解决方案时也很有用。 例如，在使用工作负载备份解决方案（Azure VM 备份中的 SQL Server 数据库）来备份数据库或数据并且要对所选磁盘使用 Azure VM 级备份时。

* Azure 备份会创建 Azure VM 的快照，并将它们随磁盘一起存储，以提升恢复点的创建速度并加快还原操作的速度。 这称为“即时还原”。 默认情况下，“即时还原”快照保留两天。 此功能允许从这些快照执行还原操作，并可缩短还原时间。 它减少了从保管库转换数据和复制回数据所需的时间。 因此，可以查看 7 天内创建的快照的相应存储成本。 [在此处了解更多信息](backup-instant-restore-capability.md#configure-snapshot-retention)。

* Azure 备份保管库的“存储复制类型”默认设置为“异地冗余”(GRS)。 在保护备份项之后无法更改此选项。 异地冗余存储 (GRS) 提供了更高级别的数据持久性（与本地冗余存储 (LRS) 相比），允许选择使用跨区域还原，并且成本更高。 请在较低的成本与较高的数据持久性之间权衡取舍，并做出最适合你的情况的决定。 [在此处了解详细信息](backup-create-rs-vault.md#set-storage-redundancy)

* 如果要保护 VM 内运行的工作负载和 VM 自身，请查看是否需要这种双重保护。

## <a name="monitoring-and-alerting-considerations"></a>监视和警报注意事项

作为备份用户或管理员，你应该能够监视所有备份解决方案并获得有关重要情况的通知。 本部分详细介绍了 Azure 备份服务提供的监视和通知功能。

### <a name="monitoring"></a>监视

* Azure 备份针对操作（例如配置备份、备份、还原、删除备份等）提供 **内置的作业监视**。 这仅限用于保管库，并且最适用于监视单个保管库。 [在此处了解更多信息](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)。

* 如果需要大规模监视操作活动，则可使用 **备份资源管理器** 来提供整个备份资产的聚合视图，从而实现详细的深化分析和故障排除。 内置的 Azure Monitor 工作簿提供了一个中心位置，可帮助你跨租户、位置、订阅、资源组和保管库监视 Azure 中的整个备份资产的操作活动。 [在此处了解更多信息](monitor-azure-backup-with-backup-explorer.md)。
  * 使用它可识别未配置为要进行备份的资源，并确保你永不会错过保护不断增长的资产中的关键数据。
  * 该仪表板提供过去 7 天（最大值）的操作活动。 如果需要保留此数据，则可以导出为 Excel 文件并保留这些数据。
  * 如果你是 Azure Lighthouse 用户，则可以跨多个租户查看信息，从而实现无边界监视。

* 如果需要长期保留并查看操作活动，请使用 **报告**。 备份管理员的一个常见需求是根据时间跨度较长的数据获取有关备份的见解。 此类解决方案的用例包括：
  * 分配和预测需使用的云存储空间。
  * 审核备份和还原。
  * 确定不同粒度级别的关键趋势。

* 此外，
  * 你可以将数据（例如作业、策略等）发送到 Log Analytics 工作区。 这将启用 Azure Monitor 日志的功能以使数据可与 Azure Monitor 收集的其他监视数据进行关联、将多个 Azure 订阅和租户中的日志条目合并到一个位置以便一起分析、使用日志查询执行复杂的分析，并且获得有关日志条目的深入见解。 [在此处了解更多信息](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)。
  * 可以向事件中心发送数据，以向 Azure 外部发送条目，例如，发送到第三方 SIEM（安全信息和事件管理）或其他日志分析解决方案。 [在此处了解更多信息](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs)。
  * 如果要将日志数据保留 90 天以上以进行审核、静态分析或备份，可以将数据发送到 Azure 存储帐户。 如果只需将事件保留 90 天或更短的时间，则无需设置存档到存储帐户，因为活动日志事件保留在 Azure 平台中的时间是 90 天。 [了解详细信息](../azure-monitor/essentials/activity-log.md#send-to--azure-storage)。

### <a name="alerting"></a>警报

* 警报主要是一种获得通知（以便采取相关操作）的方法。 “备份警报”部分显示 Azure 备份服务生成的警报。

* Azure 备份提供了 **内置的警报** 通知机制，可通过电子邮件针对故障、警告和关键操作发出通知。 可以指定在生成警报时接收通知的个人电子邮件地址或通讯组列表。 还可以选择是要接收每个警报的通知，还是将这些警报分组成按小时摘要，然后接收通知。
  * 这些警报由服务定义，并针对有限的场景（备份/还原失败、停止保护并保留数据/停止保护并删除数据，等等）提供支持。 [在此处了解更多信息](backup-azure-monitoring-built-in-monitor.md#alert-scenarios)。
  * 如果执行了破坏性操作（例如“停止保护并删除数据”），那么，即使未针对恢复服务保管库配置通知，也会引发警报，并且会向订阅所有者、管理员和共同管理员发送电子邮件。
  * 某些工作负载可能会导致失败频发（例如，SQL Server 每 15 分钟发生一次失败）。 为了防止每次发生失败时都会引发大量的警报，警报会进行合并。 [在此处了解更多信息](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts)。
  * 内置的警报不能自定义，并且仅限于 Azure 门户中定义的电子邮件。

* 如果需要 **创建自定义警报**（例如，成功作业的警报），请使用 Log Analytics。 在 Azure Monitor 中，可以在 Log Analytics 工作区内创建你自己的警报。 混合工作负载 (DPM/MABS) 也可以将数据发送到 LA，并使用 LA 为 Azure 备份支持的工作负载提供常见警报。

* 还可以通过内置的恢复服务保管库 **活动日志** 获取通知。 但是，它只支持有限的场景，并且不适用于计划备份（与活动日志相比，资源日志更适用于该操作）之类的操作。 若要详细了解这些限制，以及如何使用 Log Analytics 工作区对 Azure 备份保护的所有工作负载进行大规模的监视和警报发送，请参阅此[文章](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)。

## <a name="next-steps"></a>后续步骤

建议你阅读以下文章，以便开始使用 Azure 备份：

* [Azure 备份概述](backup-overview.md)
* [常见问题解答](backup-azure-backup-faq.yml)
