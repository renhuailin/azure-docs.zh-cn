---
title: Azure Migrate 发现和评估工具中的 Azure SQL 评估
description: 了解 Azure Migrate 发现和评估工具中的 Azure SQL 评估
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: d1ea328575cf07a22ce39549c34d5cd21e916427
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054514"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a> (迁移到 Azure SQL) 的评估概述

本文概述了使用 [Azure Migrate：发现和评估工具](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-assessment-tool)将本地 SQL Server 实例从 VMware 环境迁移到 Azure SQL 数据库或托管实例的评估。

> [!Note]
> 在 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估现在为预览版。 若要试用此功能，请使用 [**此链接**](https://aka.ms/AzureMigrate/SQL) 在 **澳大利亚东部** 区域中创建一个项目。 如果你已有一个澳大利亚东部的项目，并且想要尝试此功能，请确保已在门户上完成这些 [**先决条件**](how-to-discover-sql-existing-project.md) 。

## <a name="whats-an-assessment"></a>什么是评估？
发现和评估工具的评估是数据的时间点快照，并衡量准备情况并估计将本地服务器迁移到 Azure 的影响。

## <a name="types-of-assessments"></a>评估类型

有三种类型的评估可使用 Azure Migrate：发现和评估工具创建。

**评估类型** | **详细信息**
--- | --- 
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 你可以使用此评估类型评估 [VMware](how-to-set-up-appliance-vmware.md) 和 [hyper-v](how-to-set-up-appliance-hyper-v.md) 环境中的本地服务器，以及迁移到 Azure vm 的 [物理服务器](how-to-set-up-appliance-physical.md) 。
**Azure SQL** | 评估将本地 SQL server 从 VMware 环境迁移到 Azure SQL 数据库或 Azure SQL 托管实例。
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

Azure SQL 评估提供了一种调整大小标准：

**调整大小标准** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | Azure SQL 配置基于 SQL 实例和数据库的性能数据，其中包括： CPU 使用率、内存使用率、IOPS (数据和日志文件) 、吞吐量和 IO 操作延迟。

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>如何实现评估我的本地 SQL server 吗？

可以使用轻型 Azure Migrate 设备收集的配置和使用率数据来评估本地 SQL Server 实例。 设备将发现本地 SQL server 实例和数据库，并将配置和性能数据发送到 Azure Migrate。 [了解详细信息](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>如何实现与设备进行评估？
如果要部署 Azure Migrate 设备来发现本地服务器，请执行以下步骤：
1.  设置 Azure 和本地环境，以使用 Azure Migrate。
2.  对于第一次评估，请创建 Azure Migrate 项目，并向其添加 Azure Migrate：发现和评估工具。
3.  部署轻型 Azure Migrate 设备。 设备持续发现本地服务器并将配置和性能数据发送到 Azure Migrate。 将设备部署为 VM 或物理服务器。 无需在要评估的服务器上安装任何内容。

设备开始发现后，你可以将你想要评估的服务器收集到一个组中，并对评估类型为 **AZURE SQL** 的组运行评估。

按照我们的教程评估 [SQL Server 实例](tutorial-assess-sql.md) ，尝试执行这些步骤。

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>设备如何计算 SQL 实例和数据库的性能数据？

设备会收集计算设置的性能数据，步骤如下：
1. 设备将收集实时示例点。 对于 SQL server，每隔30秒收集一次示例点。
2. 设备将每30秒收集的样本数据点聚合10分钟。 为了创建数据点，设备从所有示例中选择最大值。 它向 Azure 发送每个计数器的最大值、平均值和方差。
3. Azure Migrate 存储上个月的所有10分钟数据点。
4. 创建评估时，Azure Migrate 会确定要用于合理精简的相应数据点。 标识基于性能历史记录和百分比利用率的百分位值。
    - 例如，如果性能历史记录为一周，百分比利用率为95%，则评估将对过去一周的10分钟示例点进行排序。 它将按升序排序，并选取合理精简的第95百分位值。
    - 95% 的值可以确保忽略任何离群值，如果选择了99% 百分点值，则可能会包含这些离群值。
    - 如果要选择该时间段的高峰使用量，而不想错过任何离群值，请选择99% 百分位以实现百分比利用率。
5. 此值与舒适因素相乘，以获取设备收集的这些指标的有效性能利用率数据：
    - CPU 使用率 (%)
    - 内存使用率 (% ) 
    - 读取 IO/秒并写入 IO/s (数据和日志文件) 
    - 读取 MB/秒并 (吞吐量写入 MB/秒) 
    - IO 操作延迟

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>用于创建和自定义 Azure SQL 评估的属性有哪些？

Azure SQL 评估属性中包含以下是：

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 区域。 Azure SQL 配置和成本建议基于你指定的位置。
**目标部署类型** | 要对其运行评估的目标部署类型：选择 " **建议**" （如果你希望 AZURE MIGRATE 评估 SQL server 是否准备好迁移到 AZURE sql MI 和 AZURE sql DB，并建议使用最适合的目标部署选项、目标层、Azure SQL 配置和每月估算。 如果要评估 SQL 服务器仅迁移到 Azure SQL 数据库并查看目标层、Azure SQL DB 配置和每月估计值，请选择 " **AZURE SQL 数据库**"。 如果要评估 SQL 服务器仅迁移到 Azure SQL 数据库并查看目标层、Azure SQL MI 配置和每月估计值，请选择 " **AZURE SQL mi**"。
**预留容量** | 指定保留容量以便评估中的成本估算。 如果选择 "保留容量" 选项，则不能指定 "折扣 (% ) "。
**调整大小标准** | 此属性用于正确调整 Azure SQL 配置的大小。 它的默认值为 **基于性能** ，这意味着评估将收集 SQL Server 实例和数据库性能指标，以推荐最佳大小的 Azure sql 托管实例和/或 Azure sql 数据库层/配置建议。
**性能历史记录** | 性能历史记录指定评估性能数据时使用的持续时间。
**百分位使用率** | 百分点利用率指定用于合理精简的性能示例的百分位数值。
**舒适因子** | 评估过程中使用的缓冲区。 它可解决季节性使用情况、简短性能历史记录等问题，并可能会在将来的使用量上增加。 例如，利用率为20% 的10核实例通常会生成一个两核的实例。 使用2.0，结果是一个四核的实例。
**产品/服务/许可计划** | 你注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/) 。 目前只能选择 "即用即付" 和 "即用即付" 开发/测试。 请注意，你可以通过在即用即付产品/服务上应用预留的容量和 Azure 混合权益来提供额外的折扣。
**服务层** | 最合适的服务层选项可满足迁移到 Azure SQL 数据库和/或 Azure SQL 托管实例的业务需要：如果希望 Azure Migrate 为服务器推荐最适合的服务层，请选择 " **推荐** "。 这可能是常规用途或业务关键。 如果需要为面向预算的工作负荷设计的 Azure SQL 配置，请选择 " **常规用途** "。 [了解更多](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) 如果希望将 Azure SQL 配置用于低延迟的工作负荷，并且具有高延迟的故障转移和快速故障转移，请选择 **业务关键** 。 [了解详细信息](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**货币** | 帐户的计费货币。
**折扣 (%)** | 在 Azure 产品/服务上收到的任何特定于订阅的折扣。 默认设置是 0%。
**Azure 混合权益** | 指定是否已有 SQL Server 许可证。 如果你这样做，并且它们已在 SQL Server 订阅的活动软件保障中涵盖，则在将许可证带到 Azure 时，你可以申请 Azure 混合权益。

查看使用 Azure Migrate 创建评估的[最佳实践](best-practices-assessment.md)。

## <a name="calculate-readiness"></a>计算准备情况

> [!NOTE]
> 评估只包含处于 "联机" 状态的数据库。 如果数据库处于任何其他状态，则评估将忽略此类数据库的准备情况、大小和成本计算。 如果你希望评估此类数据库，请在一段时间内更改数据库的状态并重新计算评估。

### <a name="azure-sql-readiness"></a>Azure SQL 就绪

针对 SQL 实例和数据库的 azure SQL 准备情况基于 Azure SQL 数据库和 Azure SQL 托管实例的功能兼容性检查：
- Azure SQL 评估会将源 SQL Server 工作 (负载所使用的 SQL Server 实例功能视为 SQL 代理作业、链接服务器 ) 等，以及用户数据库架构 (表、视图、触发器、存储过程等，从而确定兼容性问题。
- 如果未找到兼容性问题，则会将准备情况标记 **为 "适用于** 目标部署类型 (Azure sql 数据库或 azure sql 托管实例) 
- 如果存在不严重的兼容性问题（如降级或不受支持的功能，这些功能不会阻止迁移到特定目标部署类型），则准备就绪标记为 " **就绪** " ("超链接" 和 "蓝色信息" 图标) ，其中包含 **警告** 详细信息和建议的修正指南。
- 如果存在可能会阻止迁移到特定目标部署类型的任何兼容性问题，则将使用 **问题** 详细信息和建议的修正指南将准备情况标记为 "**未就绪**"。
    - 如果 SQL 实例中甚至有一个数据库尚未准备好用于特定的目标部署类型，该实例将被标记为 " **未准备好** " 该部署类型。
- 如果发现仍在进行中或 SQL 实例或数据库存在任何发现问题，则准备情况将标记为 " **未知** "，因为评估无法计算该 sql 实例的准备情况。

### <a name="recommended-deployment-type"></a>推荐的部署类型

如果根据 Azure SQL 评估属性中的 **建议** 选择 "目标部署类型"，请 Azure Migrate 推荐与 SQL 实例兼容的 Azure sql 部署类型。 迁移到 Microsoft 推荐的目标将减少整体迁移工作量。 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>基于 Azure SQL 准备情况的建议部署类型

 **Azure SQL 数据库准备情况** | **Azure SQL MI 准备情况** | **推荐的部署类型** | **Azure SQL 配置和计算成本估算？**
 --- | --- | --- | --- |
 就绪 | 就绪 | Azure SQL DB 或 Azure SQL MI | 是
 就绪 | 尚未就绪或未知 | Azure SQL DB | 是
 尚未就绪或未知 | 就绪 | Azure SQL MI | 是
 未准备就绪 | 未准备就绪 | 可能适用于 Azure VM | 否
 尚未就绪或未知 | 尚未就绪或未知 | 未知 | 否

> [!NOTE]
> 如果 **在评估属性中选择** 了 "推荐的部署类型"，并且源 SQL Server 适用于 AZURE sql DB 单数据库和 azure sql 托管实例，则此评估会建议使用特定选项来优化成本，并将其置于大小和性能边界内。

#### <a name="potentially-ready-for-azure-vm"></a>可能适用于 Azure VM

如果 SQL 实例尚未准备好使用 Azure SQL 数据库和 Azure SQL 托管实例，则建议的部署类型会被标记为可 *供 AZURE VM* 使用。
- 建议用户在评估类型为 "Azure VM" 的 Azure Migrate 中创建评估，以确定该实例正在运行的服务器是否已准备好迁移到 Azure VM。 请注意：
    - Azure Migrate 中的 Azure VM 评估目前是直接提升和移动的，不考虑在 Azure 虚拟机上运行 SQL 实例和数据库的特定性能指标。 
    - 当你在服务器上运行 Azure VM 评估时，建议的大小和成本估计值将适用于服务器上运行的所有实例，并可使用服务器迁移工具迁移到 Azure VM。 在迁移之前，请查看 Azure 虚拟机上的 SQL Server 的 [性能指导原则](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) 。


## <a name="calculate-sizing"></a>计算调整大小

### <a name="azure-sql-configuration"></a>Azure SQL 配置

评估确定准备情况和推荐的 Azure SQL 部署类型后，它会计算特定的服务层和 Azure SQL 配置 (SKU 大小) ，可满足或超过本地 SQL 实例性能：
1. 在发现过程中，Azure Migrate 收集 SQL 实例的配置和性能，其中包括：
    - Vcore (分配) 和 CPU 利用率 (% ) 
        - SQL 实例的 CPU 利用率是指 SQL server 上实例使用的已分配 CPU 的百分比
        - 数据库的 CPU 使用率是数据库在 SQL 实例上使用的已分配 CPU 的百分比
    - 内存 (分配) 和内存利用率 (% ) 
    - 读取 IO/秒并写入 IO/s (数据和日志文件) 
        - 通过添加读取 IO/秒并写入在该实例中发现的所有数据库，可在 SQL 实例级别读取 IO/秒并写入 IO/s。
    - 读取 MB/秒并 (吞吐量写入 MB/秒) 
    - IO 操作延迟
    - 数据库大小和数据库文件组织总数
        - 通过添加所有数据文件和日志文件来计算数据库大小。
1. 该评估聚合了所有配置和性能数据，并尝试在各种 Azure SQL 服务层和配置中找到最佳匹配项，并选择可匹配或超过 SQL 实例性能要求的配置，从而优化成本。

### <a name="confidence-ratings"></a>置信度评级 
每个 Azure SQL 评估都与置信度分级相关联。 评级范围为一 (最小) 到五个 () 星。 置信度评级有助于您估算 Azure Migrate 提供的大小建议的可靠性。
- 置信度评级分配给评估。 评级基于计算评估所需的数据点的可用性。
- 对于基于性能的大小调整，评估将收集所有 SQL 实例和数据库的性能数据，其中包括：
    - CPU 使用率 (%)
    - 内存使用率 (% ) 
    - 读取 IO/秒并写入 IO/s (数据和日志文件) 
    - 读取 MB/秒并 (吞吐量写入 MB/秒) 
    - IO 操作延迟
    
如果其中的任何利用率均不可用，则大小建议可能不可靠。
下表显示了评估置信度评级，它取决于可用数据点的百分比：

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星

#### <a name="low-confidence-ratings"></a>低置信度分级
下面是评估降低置信度的几个原因：
- 你未在创建评估的持续时间内分析环境。 例如，如果你创建了 "性能持续时间" 设置为一天的评估，则必须在开始发现所有数据点后等待至少一天。
- 评估无法在评估期内收集部分或全部服务器的性能数据。 若要获得更高的置信度，请确保：
    - 服务器在评估期间开机
    - 允许端口443上的出站连接
    - 如果 Azure Migrate 中的 SQL 代理 Azure Migrate 连接状态为 "已连接"，则检查上一检测信号 
    - 如果 "已发现的 SQL 实例" 边栏选项卡中所有 SQL 实例 Azure Migrate 连接状态均为 "已连接"

    请重新计算评估以反映置信度评级的最新更改。
- 某些数据库或实例是在计算评估期间创建的。 例如，假设你为上个月的性能历史记录创建了评估，但某些数据库或实例仅在一周前创建。 在这种情况下，新服务器的性能数据将不可用于整个持续时间，置信度级别将会降低。

> [!NOTE]
> 由于 Azure SQL 评估是基于性能的评估，因此，如果任何评估的置信度小于五星级，我们建议你至少等待一天，让设备对环境进行配置，然后重新计算评估。 否则，基于性能的大小调整可能不可靠。

## <a name="calculate-monthly-costs"></a>计算每月成本
完成规模调整建议后，Azure SQL 评估使用内部定价 API 计算推荐的 Azure SQL 配置的计算和存储成本。 它在所有实例中聚合计算和存储成本，以计算每月总计算成本。 
### <a name="compute-cost"></a>计算成本
- 为了计算 Azure SQL 配置的计算成本，评估考虑以下属性：
    - SQL 许可证 Azure 混合权益
    - 预留容量
    - Azure 目标位置
    - 货币
    - 产品/服务/许可计划
    - 折扣 (%)

### <a name="storage-cost"></a>存储成本
- 存储成本估算仅包括数据文件和日志文件。 
- 为了计算 Azure SQL 配置的存储成本，评估考虑以下属性：
    - Azure 目标位置
    - 货币
    - 产品/服务/许可计划
    - 折扣 (%)
- 评估中不包含备份存储成本。
- **Azure SQL 数据库**
    - 成本估算至少增加了5GB 的存储成本，并为存储增加了额外的存储成本。 [了解详细信息](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Azure SQL 托管实例**
    - 对于第一个 32 GB/实例/月的存储，不会增加存储成本，而是以32GB 为增量增加额外的存储成本。 [了解详细信息](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>后续步骤
- [查阅](best-practices-assessment.md)关于创建评估的最佳做法。 
- 了解如何运行 [AZURE SQL 评估](how-to-create-azure-sql-assessment.md)。
