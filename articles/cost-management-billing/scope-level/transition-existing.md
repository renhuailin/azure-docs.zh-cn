---
title: 过渡到 Azure 混合权益的范围级别管理
description: 本文介绍了相关更改和多种过渡方案，说明如何过渡到 Azure 混合权益的范围级别管理。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.openlocfilehash: a813a8934ff66b10e0f3c7adce65887acebba6f8
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547222"
---
# <a name="transition-to-scope-level-management-of-azure-hybrid-benefit"></a>过渡到 Azure 混合权益的范围级别管理

过渡到 Azure 混合权益的范围级别管理时，不再需要在资源级别配置权益。 本文介绍了相关更改和多种过渡方案来说明结果。 为了更好地了解新的范围级别许可证管理体验如何向资源应用许可证和折扣，请参阅[什么是 Azure 混合权益的范围级别管理？](overview-azure-hybrid-benefit-scope.md)

## <a name="changes-to-individual-resource-configuration"></a>对单个资源配置所做的更改

使用新体验将许可证分配到订阅时，相关更改将显示在 Azure 门户中。 此后，无法在资源级别管理权益。 在范围级别所做的任何更改都会覆盖单个资源级别的设置。

:::image type="content" source="./media/transition-existing/sql-db-configure.png" alt-text="显示 SQL 许可证覆盖信息的屏幕截图。" lightbox="./media/transition-existing/sql-db-configure.png" :::

> [!NOTE]
> 如果使用 PowerShell、API 或在 Azure 门户以外将 Azure 混合权益设置更改为资源，则会保存设置更改。 但是，只要分配给范围的许可证涵盖了资源的订阅或计费帐户，就不会产生任何影响。 如果你之前通过删除所有许可证分配来选择退出 Azure 混合权益的范围级别管理，则你的许可折扣将恢复为由每个资源上的 Azure 混合权益设置确定。

## <a name="check-how-many-sql-licenses-you-use-before-transition"></a>检查过渡前使用的 SQL 许可证数量

当你加入 Azure 混合权益的范围级别管理体验时，你将看到为单个资源启用的当前 Azure 混合权益使用情况。 有关总体体验的详细信息，请参阅[为 Azure 混合权益创建 SQL Server 许可证分配](create-sql-license-assignments.md)。 如果你是订阅参与者且没有所需的计费管理员角色，则可以使用 PowerShell 脚本分析 Azure 中不同类型的 SQL Server 许可证的使用情况。 该脚本将生成在多个订阅或整个帐户中的使用情况的快照。 有关使用脚本的详细信息和示例，请参阅 [sql-license-usage PowerShell 脚本](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit)示例脚本。 运行该脚本后，请确定你的计费管理员并与其联系，以了解将 Azure 混合权益管理转为订阅或计费帐户范围级别的机会。

> [!NOTE]
> 该脚本包含对规范化核心许可证 (NCL) 的支持。 

## <a name="hadr-benefit-for-sql-server-vms"></a>适用于 SQL Server VM 的 HADR 权益

新的 Azure 门户体验完全支持 SQL Server VM 的高可用性和灾难恢复 (HADR) 权益。 如果 SQL Server VM 配置为 HADR 副本，则不需要执行任何其他操作。 有关 SQL Server VM HADR 权益的工作原理的详细信息，请参阅 [SQL Server HADR 和范围级别 Azure 混合权益共存](sql-server-hadr-licenses.md)。

## <a name="transition-scenario-examples"></a>过渡方案示例

查看最符合你的情况的以下过渡方案示例。

### <a name="migrate-sql-workloads-from-on-premises-to-the-cloud-using-sql-database-with-azure-hybrid-benefit"></a>结合使用 SQL 数据库和 Azure 混合权益将 SQL 工作负载从本地迁移到云

- 本地 SQL Server 工作负载 - 两个托管任务关键型 SQL Server Enterprise Edition 数据库的 16 核计算机将迁移到云。
- 用于迁移工作负载的 Azure 目标 - 分析后，确定工作负载将在两个 16 核 Azure SQL 托管实例上运行。
- 要分配给 Azure 的许可证 – 考虑到上述几点，32 个具有软件保障的 SQL Server Enterprise Edition 核心许可证将分配给 Azure。
- 建议的操作 - 使用新的 Azure 门户体验将 32 个 SQL Server Enterprise Edition 核心许可证分配给相应的订阅或整个计费帐户。
- 结果 - SQL Server 工作负载从本地到云的易于管理、成本优化的迁移。

> [!NOTE] 
> Azure 混合权益允许在长达 180 天的时间内继续在本地使用分配给 Azure 的许可证，因为工作负载会进行迁移、测试和部署。

### <a name="simplify-license-management-by-transitioning-to-scope-level-management-of-azure-hybrid-benefit"></a>通过过渡到 Azure 混合权益的范围级别管理来简化许可证管理

- SQL Server 资源正在运行 - 一个选择了 Azure 混合权益的 64 核 Azure SQL 数据库业务关键正在运行。
- 可分配给 Azure 的许可证 – 你的采购团队确认未在本地使用的具有软件保障的 SQL Server Enterprise Edition 核心许可证超过 64 个。
- 建议的操作 - 使用 Azure 混合权益的新范围级别管理体验将 64 个 SQL Server Enterprise Edition 核心许可证分配给 Azure。 或者，如果使用情况预计很快就会增加，请根据需要分配更多许可证。
- 结果 – 过渡过程将涵盖 SQL Server 软件成本，因此，不应对关联的计费进行任何更改。

### <a name="save-more-by-assigning-more-sql-server-licenses-to-cover-more-azure-sql-resources"></a>通过分配更多 SQL Server 许可证来涵盖更多 Azure SQL 资源，从而节省更多成本

- SQL Server 资源正在运行 - 两个 16 核 Azure SQL 数据库业务关键正在运行，其中一个选择了 Azure 混合权益。
- 可分配给 Azure 的许可证 – 根据你的采购团队得知，48 个具有软件保障的 SQL Server Enterprise Edition 核心许可证未在本地或 Azure 中使用。
- 建议的操作 - 使用 Azure 混合权益的范围级别管理体验分配所有 48 个 SQL Server Enterprise Edition 核心许可证。 与资源级别 Azure 混合权益选择相比，增加了 16 个。
- 结果 - 由于在 Azure 外部购买的许可证数量的使用变多，因此应减少 Azure 中的计费金额。 此外，由于软件保障的每年成本低于 SQL Server 按即用即付收费的每年成本，因此降低 SQL Server 使用情况的总成本。

### <a name="restore-compliance-when-excessive-azure-hybrid-benefit-usage-is-found"></a>找到过多的 Azure 混合权益使用情况时还原合规性

- SQL Server 资源正在运行 - 三个 8 核 SQL 数据库常规用途和一个 16 核 SQL Server VM Enterprise 正在运行，全部选择了 Azure 混合权益。 需要涵盖的 Azure 混合权益许可证数量为 24 个 Standard Edition 核心 + 16 个 SQL Server Enterprise 核心。 或者，88 个 SQL Server Standard Edition（+ 0 个 SQL Server Enterprise Edition）-或者- 22 个 SQL Server Enterprise（+ 0 个 SQL Server Standard Edition）也会涵盖。 这是因为，对于所有 Azure SQL 资源类型，一个 SQL Server Enterprise Edition 核心许可证和四个 SQL Server Standard Edition 核心许可证可以涵盖相同的 Azure 混合权益使用情况。 若要详细了解这种灵活性，请参阅[什么是 Azure 混合权益的范围级别管理？](overview-azure-hybrid-benefit-scope.md)一文中的 Azure 混合权益规则。
- 可分配给 Azure 的许可证 – 根据你的采购团队得知，64 个具有软件保障的 SQL Server Standard Edition 核心许可证未在本地或 Azure 中使用。 这小于所需的 22 个 SQL Server Enterprise 或 88 个 SQL Server Standard Edition 核心许可证。
- 建议的操作 - 若要恢复合规性，请使用 Azure 混合权益的范围级别管理体验确定具有软件保障的 6 个 SQL Server Enterprise 或 24 个 SQL Server Standard Edition 核心许可证，并将其与已确认的 64 个 SQL Server Standard 核心许可证分配给 Azure。
- 结果 - 消除了不合规性，并以最佳方式使用 Azure 混合权益来最大限度地降低成本。
- 替代操作 - 仅将可用的 64 个 SQL Server Standard Edition 核心许可证分配给 Azure。 你将符合要求，但由于这些许可证不足以涵盖所有 Azure SQL 使用情况，因此你会遇到一些即用即付费用。
## <a name="next-steps"></a>后续步骤

- 按照[管理和优化 SQL Server 的 Azure 混合权益](tutorial-azure-hybrid-benefits-sql.md)教程进行操作。
- 通过[创建 SQL Server 许可证分配](create-sql-license-assignments.md)，转到范围级别许可证管理。
- 查看[范围级别 Azure 混合权益管理常见问题解答](faq-azure-hybrid-benefit-scope.yml)。