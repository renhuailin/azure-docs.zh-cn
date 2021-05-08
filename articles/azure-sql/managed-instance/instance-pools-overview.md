---
title: 什么是 Azure SQL 托管实例池？
titleSuffix: Azure SQL Managed Instance
description: 了解 Azure SQL 托管实例池（预览版），这一特征提供了一种方便、经济高效的方法，可将较小的 SQL Server 数据库大规模迁移到云，并管理多个托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: bc345509db1c2a14afb0ae781eccad8f77395c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347058"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>什么是 Azure SQL 托管实例池（预览版）？
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例中的实例池可提供一种方便且经济高效的方式，可将较小 SQL Server 实例大规模迁移到云。

可以通过实例池按照总的迁移要求对计算资源进行预配置。 然后即可根据预配置的计算级别部署多个单独的托管实例。 例如，如果预先预配 8 vCore，则可以部署 2 个 2-vCore 和 1 个 4-vCore 实例，然后将数据库迁移到这些实例。 在推出实例池之前，当迁移到云端时，计算不太频繁的较小型的工作负载常常不得不整合到更大的托管实例中。 将几组数据库迁移到大型实例的这一需求通常要求进行仔细的容量规划和资源管理，还有额外的安全考量，也要在实例级别进行一些额外的数据整合。

另外，实例池支持原生 VNet 集成，让你能够在同一子网中部署多个实例池和多个单实例。

## <a name="key-capabilities"></a>关键功能

实例池具有下列优势：

1. 可托管包含 2 vCore 实例。 仅针对实例池中的实例 *\** 。
2. 实例部署时间短且可预测（不超过 5 分钟）。
3. 最小的 IP 地址分配。

下图说明了在一个虚拟网络子网中部署了多个托管实例的实例池。

![具有多个实例的实例池](./media/instance-pools-overview/instance-pools1.png)

通过实例池，可在同一虚拟机上部署多个实例，虚拟机的计算大小由为该实例池分配的 vCore 总数量而定。 通过这种体系结构，可将虚拟机分区到多个实例中，这可以是所支持的任何大小，包括 2 个 vCore（包含 2 个 vCore 的实例仅适用于实例池中的实例）。

初始部署后，针对池中实例的管理操作要快很多。 这是因为[虚拟群集](connectivity-architecture-overview.md#high-level-connectivity-architecture)（专用的一组虚拟机）的部署或扩展与托管实例的预配分开执行。

池中的所有实例都共享同一个虚拟机，因此 IP 分配总数不由所部署的实例数决定，这便于在 IP 范围较窄的子网中进行部署。

每个池都有固定的 IP 分配，只有 9 个 IP 地址（不包含子网中的 5 个 IP 地址，它们预留供自己使用）。 有关详细信息，请参阅[单实例的子网大小要求](vnet-subnet-determine-size.md)。

## <a name="application-scenarios"></a>应用程序方案

下表列出了应考虑实例池的主要使用场景：

- 同时迁移一组 SQL Server 实例，其中大部分实例较小（例如 2 个或 4 个 vCore）。
- 有必要保证实例创建或缩放时间短或可预测的场景。 例如，在需要实例级功能的多租户 SaaS 应用程序环境中部署新的租户。
- 有必要保证成本固定或开支限制的场景 。 例如，运行大小固定（或不常变化）的共享开发测试或演示环境，其中你要根据需要定期部署托管实例。
- 有必要在 VNet 子网中保证最小 IP 地址分配的场景。 池中的所有实例共享一个虚拟机，因此与单实例的情况相比，所分配的 IP 地址数要少。

## <a name="architecture"></a>体系结构

实例池具有与常规（单一）托管实例类似的体系结构。 为了支持 [Azure 虚拟网络中的部署](../../virtual-network/virtual-network-for-azure-services.md)并为客户提供隔离和安全性，实例池还依赖于[虚拟群集](connectivity-architecture-overview.md#high-level-connectivity-architecture)。 虚拟群集表示在客户的虚拟网络子网中部署的一组专用的独立虚拟机。

这两种部署模型的主要区别在于，可通过实例池在同一虚拟机节点上部署多个 SQL Server 进程，它们是使用 [Windows 作业对象](/windows/desktop/ProcThread/job-objects)管理的资源，而单实例始终在虚拟机节点上单独存在。

下图显示了一个实例池和在同一子网中部署的两个单独的实例，还展示了这两种部署模型的体系结构详细信息：

![实例池和两个单独的实例](./media/instance-pools-overview/instance-pools2.png)

每个实例池都在下面创建一个单独的虚拟群集。 池中的实例和同一子网中部署的单实例不共享分配给 SQL Server 进程和网关组件的计算资源，这保证了性能的可预测性。

## <a name="resource-limitations"></a>资源限制

有多个针对实例池和池中实例的资源限制：

- 实例池只可在 Gen5 硬件上使用。
- 池中的托管实例有专用 CPU 和 RAM，因此所有实例的聚合 vCore 数必须小于或等于分配给该池的 vCore 数。
- 池中创建的实例都需遵守各项[实例级别的限制](resource-limits.md#service-tier-characteristics)。
- 除了实例级别的限制，还在实例池级别施加了两种限制：
  - 每个池的总存储大小 (8 TB)。
  - 每个池子的用户数据库总数。 这一限制取决于池 Vcore 值：
    - 8 Vcore 池最多支持 200 个数据库，
    - 16 Vcore 池最多支持 400 个数据库，
    - 24 Vcore 和更大的 Vcore 池最多支持 500 个数据库。
- 无法为实例池中部署的实例设置 AAD 管理员，因此无法使用 AAD 身份验证。

所有实例中的总存储分配量和数据库数量不得超过实例池公开的限制。

- 实例池支持 8、16、24、32、40、64 和 80 个 vCore。
- 池中的托管实例支持 2、4、8、16、24、32、40、64 和 80 个 vCore。
- 池中的托管实例支持存储大小介于 32 GB 到 8 TB 之间，以下情况除外：
  - 包含 2 个 vCore 的实例支持大小介于 32 GB 到 640 GB 之间，
  - 包含 4 个 vCore 的实例支持大小介于 32 GB 到 2 TB 之间。
- 池中的每个托管实例的用户数据库上限为 100，但 2 vCore 实例除外，每个 2 vCore 实例最多支持 50 个用户数据库。

[服务层级属性](resource-limits.md#service-tier-characteristics)与实例池资源关联，因此池中的所有实例必须与该池的服务层级位于相同的服务层级。 目前，只提供“常规用途”服务层级（有关当前预览版中的限制，请查看下列部分）。

### <a name="public-preview-limitations"></a>公共预览版限制

公共预览版设有以下限制：

- 目前，只提供“常规用途”服务层级。
- 在公共预览版期间，无法缩放实例池，因此必须在部署之前仔细规划容量。
- 尚不支持通过 Azure 门户创建和配置实例池。 仅支持通过 PowerShell 对实例池执行各项操作。 此外，仅支持通过 PowerShell 在预创建的池中进行初步实例部署。 部署到池之后，可通过 Azure 门户来更新托管实例。
- 在池外部创建的托管实例不可移动到现有池中，在池中创建的实例不可作为单个实例从池中移出，也不可移到另一个池中。
- [预留容量](../database/reserved-capacity-overview.md)实例定价不可用。

## <a name="sql-features-supported"></a>支持的 SQL 功能

池中创建的托管实例支持[单个托管实例中所支持的兼容性级别和功能](sql-managed-instance-paas-overview.md#sql-features-supported)。

池中部署的每个托管实例具有单独的 SQL 代理实例。

在实例级别配置可选功能或要求你选择特定值的功能（例如实例级别排序、时区、数据流量的公共终结点、故障转移组），池中每个实例的这些功能可有所不同。

## <a name="performance-considerations"></a>性能注意事项

虽然池中的托管实例的确具有专用的 vCore 和 RAM，但它们共享本地磁盘（针对 tempdb 用途）和网络资源。 不太可能遇到相邻干扰，但如果池中的多个实例同时间的资源消耗量都大，则可能会遇到。 如果观察到这种行为，请考虑将这些实例部署到更大的池中，或将它们部署为单个实例。

## <a name="security-considerations"></a>安全注意事项

由于池中部署的实例共享同一个虚拟机，因此你可能需要考虑禁用会带来较高安全风险的功能，或者严格控制对这些功能的访问权限。 这些功能包括 CLR 集成、本机备份和还原、数据库电子邮件等等。

## <a name="instance-pool-support-requests"></a>实例池支持请求

在 [Azure 门户](https://portal.azure.com)中创建和管理实例池的支持请求。

如果遇到与实例池部署（创建或删除）相关的问题，请一定要在“问题子类型”字段中指定“实例池” 。

![实例池支持请求](./media/instance-pools-overview/support-request.png)

如果遇到与池中单个实例或数据库相关的问题，则应为 Azure SQL 托管实例创建常规支持票证。

要创建更大型的 SQL 托管实例部署（无论是否用实例池），你都可能需要调高区域配额。 有关详细信息，请参阅[请求增加 Azure SQL 数据库的配额](../database/quota-increase-request.md)。 实例池的部署逻辑会将池级别消耗的 vCore 总数与你的配额进行比较，确定你是否可在不进一步增加配额的情况下创建新资源。

## <a name="instance-pool-billing"></a>实例池计费

可在实例池中单独缩放计算和存储。 客户需对两部分内容付费：与池资源关联的计算（以 vCore 数衡量），以及与每个实例关联的存储量（以 GB 为单位，每个实例前 32 GB 免费）。

无论池中部署了多少实例，都会对该池中的 vCore 收费。

在计算价格（以 vCore 数衡量）方面，提供了两种定价选项：

  1. 包含许可证：包含 SQL Server 许可证的价格。 这适用于选择不应用带软件保障的现有 SQL Server 许可证的客户。
  2. Azure 混合权益：包含适用于 SQL Server 的 Azure 混合权益的折扣价格。 客户可通过使用带软件保障的现有 SQL Server 许可证来选择采用此价格。 相关资格及其他详细信息，请参阅 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。

必须对池中的单个实例设置相同的定价选项。 父池中的所有实例必须采用“包含许可证”价格或“Azure 混合权益”价格。 池创建后，可更改该池的许可证模型。

> [!IMPORTANT]
> 如果为实例指定与池中模型不同的许可证模型，则采用池的价格，忽略实例级别的值。

如果在[有资格享受开发测试权益的订阅](https://azure.microsoft.com/pricing/dev-test/)上创建实例池，则自动在 Azure SQL 托管实例上享受高达 55% 的折扣费率。

有关实例池的完整详细信息，请参阅 [SQL 托管实例定价页面](https://azure.microsoft.com/pricing/details/sql-database/managed/)上的“实例池”部分。

## <a name="next-steps"></a>后续步骤

- 要开始使用实例池，请参阅 [SQL 托管实例池操作指南](instance-pools-configure.md)。
- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](instance-create-quickstart.md)。
- 有关功能和比较列表，请参阅 [Azure SQL 常用功能](../database/features-comparison.md)。
- 有关 VNet 配置的详细信息，请参阅 [SQL 托管实例 VNet 配置](connectivity-architecture-overview.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关使用 Azure 数据库迁移服务进行迁移的教程，请参阅[使用数据库迁移服务进行 SQL 托管实例迁移](../../dms/tutorial-sql-server-to-managed-instance.md)。
- 有关使用内置故障排除智能对 SQL 托管实例数据库性能进行的高级监视，请参阅[使用 Azure SQL Analytics 监视 Azure SQL 托管实例](../../azure-monitor/insights/azure-sql.md)。
- 有关定价信息，请参阅 [SQL 托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
