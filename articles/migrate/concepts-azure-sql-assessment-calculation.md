---
title: Azure Migrate 发现和评估工具中的 Azure SQL 评估
description: 了解 Azure Migrate 发现和评估工具中的 Azure SQL 评估
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: c9319465c4e77eab294606ed046f7946948f2cc1
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108140364"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>评估概述（迁移到 Azure SQL）

本文概述使用 [Azure Migrate：发现和评估工具](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)将本地 SQL Server 实例从 VMware 环境迁移到 Azure SQL 数据库或托管实例的评估。

## <a name="whats-an-assessment"></a>什么是评估？
使用发现和评估工具进行评估，是指数据的时间点快照，衡量就绪情况并预估将本地服务器迁移到 Azure 的影响。

## <a name="types-of-assessments"></a>评估的类型

通过使用“Azure Migrate：发现和评估”工具，可以创建三种类型的评估。

**评估类型** | **详细信息**
--- | --- 
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 使用这种评估类型，可以对 [VMware](how-to-set-up-appliance-vmware.md) 和 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 环境中的本地服务器以及要迁移到 Azure VM 的[物理服务器](how-to-set-up-appliance-physical.md)进行评估。
**Azure SQL** | 将本地 SQL Server 从 VMware 环境迁移到 Azure SQL 数据库或 Azure SQL 托管实例的评估。
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> 如果发现和评估工具上的 Azure VM 或 AVS 评估数不正确，请单击评估总数以导航到所有评估并重新计算 Azure VM 或 AVS 评估数。 然后，发现和评估工具就会显示该评估类型的正确计数。 

Azure SQL 评估提供了一种调整大小标准：

**调整大小标准** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | Azure SQL 配置基于 SQL 实例和数据库的性能数据，其中包括：CPU 使用率、内存使用率、IOPS（数据和日志文件）、吞吐量和 IO 操作延迟。

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>如何评估我的本地 SQL Server？

可以使用轻型 Azure Migrate 设备收集的配置和使用率数据来评估本地 SQL Server 实例。 设备将发现本地 SQL Server 实例和数据库，并将配置和性能数据发送到 Azure Migrate。 [了解详细信息](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>如何使用设备进行评估？
如果部署 Azure Migrate 设备来发现本地服务器，请执行以下步骤：
1.  设置 Azure 及适合于 Azure Migrate 运行的本地环境。
2.  第一次评估时，请创建一个 Azure Migrate 项目并向其中添加“Azure Migrate：发现和评估”工具。
3.  部署轻型 Azure Migrate 设备。 此设备将持续发现本地服务器，并向 Azure Migrate 发送配置和性能数据。 将设备部署为 VM 或物理服务器。 无需在要评估的服务器上安装任何内容。

设备开始发现后，可以将想要评估的服务器归集到一个组中，然后对评估类型为“Azure SQL”的组运行评估。

按照我们的教程评估 [SQL Server 实例](tutorial-assess-sql.md)，尝试执行这些步骤。

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>设备如何计算 SQL 实例和数据库的性能数据？

设备通过以下步骤收集计算设置的性能数据：
1. 设备收集实时示例点。 对于 SQL Server，每隔 30 秒收集一个示例点。
2. 设备在 10 分钟内聚合每 30 秒收集的示例数据点。 为了创建数据点，设备会选择所有示例中的峰值。 它向 Azure 发送每个计数器的最大值、平均值和方差。
3. Azure Migrate 存储上个月的所有 10 分钟数据点。
4. 创建评估时，Azure Migrate 会确定要用于合理调整大小的相应数据点。 根据性能历史记录和百分位使用率的百分位值进行识别。
    - 例如，性能历史记录是一周，百分位使用率是第 95 百分位，则评估会对上一周的 10 分钟示例点进行排序。 按升序排序，并选取第 95 百分位值进行合理调整大小。
    - 第 95 百分位值可以确保忽略任何离群值，如果选取第 99 百分位，则可能包括这些离群值。
    - 如果希望选择该期间内的峰值使用率，并且不希望错过任何离群值，请为百分位使用率选择第 99 百分位。
5. 此值与舒适因子相乘，就得到了设备收集的这些指标的有效性能使用率数据：
    - CPU 使用率 (%)
    - 内存使用率 (%)
    - 读取 IO/秒和写入 IO/秒（数据和日志文件）
    - 读取 MB/秒和写入 MB/秒（吞吐量）
    - IO 操作延迟

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>哪些属性用于创建和自定义 Azure SQL 评估？

下面是 Azure SQL 评估属性包含的内容：

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 区域。 Azure SQL 配置和成本建议基于你指定的位置。
目标部署类型 | 要对其运行评估的目标部署类型： <br/><br/> 如果希望 Azure Migrate 评估要迁移到 Azure SQL MI 和 Azure SQL DB 的 SQL Server 的就绪状态，并推荐最合适的目标部署选项、目标层级、Azure SQL 配置和每月估算值，请选择“推荐”。<br/><br/>如果只希望评估要迁移到 Azure SQL 数据库的 SQL Server，并查看目标层级、Azure SQL DB 配置和每月估算值，请选择“Azure SQL DB”。<br/><br/>如果只希望评估要迁移到 Azure SQL 数据库的 SQL Server，并查看目标层级、Azure SQL MI 配置和每月估算值，请选择“Azure SQL MI”。
**预留容量** | 指定预留容量，以便在评估的成本估算中将它们考虑在内。<br/><br/> 如果选择了预留容量选项，则不能指定“折扣(%)”。
**调整大小标准** | 此属性用于正确调整 Azure SQL 配置的大小。 <br/><br/> 它默认为“基于性能”，这意味着评估将收集 SQL Server 实例和数据库性能指标，以推荐最佳大小的 Azure SQL 托管实例和/或 Azure SQL 数据库层/配置建议。
**性能历史记录** | 性能历史记录指定评估性能数据时使用的持续时间。
**百分位使用率** | 百分位使用率指定用于合理调整大小的性能示例的百分位值。
**舒适因子** | 评估过程中使用的缓冲区。 此因子用于解决季节性使用情况、短期性能历史记录以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核实例通常相当于一个双核实例。 而如果舒适因子是 2.0，则结果是一个四核实例。
**产品/服务/许可计划** | 注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 目前只能选择“即用即付”或“即用即付开发/测试”。 请注意，可以通过在“即用即付”产品/服务的基础上应用预留容量和 Azure 混合权益来获得额外的折扣。
**服务层** | 最合适的服务层级选项，用于满足迁移到 Azure SQL 数据库和/或 Azure SQL 托管实例的业务需求：<br/><br/>如果希望 Azure Migrate 推荐最适合服务器的服务层级，请选择“推荐”。 服务层级可以是“常规用途”或“业务关键”。 <br/><br/> 如果希望 Azure SQL 配置专用于预算导向的工作负荷，请选择“常规用途”。 [了解详细信息](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> 如果希望 Azure SQL 配置专用于能够极其灵活地应对故障且能实现快速故障转移的低延迟工作负荷，请选择“业务关键”。 [了解详细信息](../azure-sql/database/service-tier-business-critical.md)
**货币** | 帐户的计费货币。
**折扣 (%)** | 基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。
**Azure 混合权益** | 指定是否已有 SQL Server 许可证。 <br/><br/> 如果已有许可证，并且许可证具有 SQL Server 订阅的有效软件保障，则在将许可证引入 Azure 时可以申请 Azure 混合权益。

关于如何使用 Azure Migrate 创建评估，请[查看最佳做法](best-practices-assessment.md)。

## <a name="calculate-readiness"></a>计算就绪情况

> [!NOTE]
此评估仅包括处于联机状态的数据库。 如果数据库处于任何其他状态，评估将不计算此类数据库的就绪性、大小和成本。 如果你希望评估此类数据库，请更改数据库的状态，在一段时间后再重新计算评估。

### <a name="azure-sql-readiness"></a>Azure SQL 就绪情况

针对 SQL 实例和数据库的 Azure SQL 就绪情况基于使用 Azure SQL 数据库和 Azure SQL 托管实例进行的功能兼容性检查：
1. Azure SQL 评估考虑源 SQL Server 工作负荷当前所使用的 SQL Server 实例功能（SQL 代理作业、链接服务器等）以及用户数据库架构（表、视图、触发器、存储过程等）来确定兼容性问题。
1. 如果未找到兼容性问题，则会将目标部署类型（Azure SQL 数据库或 Azure SQL 托管实例）的就绪情况标记为“就绪”
1. 如果存在不严重的兼容性问题，如不会阻止迁移到特定目标部署类型的降级或不受支持的功能，则就绪情况标记为“就绪”（超链接和蓝色信息图标），其中包含“警告”详细信息和建议的修正指导。
1. 如果存在可能会阻止迁移到特定目标部署类型的任何兼容性问题，则就绪情况标记为“未就绪”，其中包含“问题”详细信息和建议的修正指导。
    - SQL 实例中即使有一个数据库未准备好用于特定目标部署类型，该实例都将针对该部署类型标记为“未就绪”。
1. 如果发现仍在进行中或 SQL 实例或数据库存在任何发现问题，则就绪情况标记为“未知”，因为评估无法计算该 SQL 实例的就绪情况。

### <a name="recommended-deployment-type"></a>推荐的部署类型

如果在 Azure SQL 评估属性中选择目标部署类型为“建议”，Azure Migrate 将建议与 SQL 实例兼容的 Azure SQL 部署类型。 迁移到 Microsoft 建议的目标可减少整体迁移工作量。 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>根据 Azure SQL 就绪情况建议的部署类型

 **Azure SQL DB 就绪状态** | **Azure SQL MI 就绪状态** | **推荐的部署类型** | **是否计算 Azure SQL 配置和估算成本？**
 --- | --- | --- | --- |
 就绪 | 就绪 | Azure SQL DB 或 <br/>Azure SQL MI | 是
 就绪 | 未就绪或<br/> Unknown | Azure SQL DB | 是
 未就绪或<br/>Unknown | 就绪 | Azure SQL MI | 是
 未准备就绪 | 未准备就绪 | 可能为 Azure VM 准备就绪 | 否
 未就绪或<br/>Unknown | 未就绪或<br/>未知 | 未知 | 否

> [!NOTE]
> 如果在评估属性中建议的部署类型选择为“建议”，并且源 SQL Server 同时适用于 Azure SQL DB 单数据库和 Azure SQL 托管实例，则此评估会建议使用优化成本并在大小和性能范围内的特定选项。

#### <a name="potentially-ready-for-azure-vm"></a>可能为 Azure VM 准备就绪

如果 SQL 实例未准备好用于 Azure SQL 数据库和 Azure SQL 托管实例，则建议的部署类型会被标记为“可能为 Azure VM 准备就绪”。
- 建议用户在 Azure Migrate 中创建评估类型为“Azure VM”的评估，以决定正在运行实例的服务器是否已可迁移到 Azure VM。 请注意：
    - Azure Migrate 中的 Azure VM 评估目前侧重于直接迁移，不考虑在 Azure 虚拟机上运行 SQL 实例和数据库的特定性能指标。 
    - 当你在服务器上运行 Azure VM 评估时，建议的大小和成本估计值将适用于该服务器上运行的所有实例，并且可通过服务器迁移工具迁移到 Azure VM。 在迁移之前，请查看 Azure 虚拟机中的 SQL Server 的[性能原则](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md)。


## <a name="calculate-sizing"></a>计算调整大小

### <a name="azure-sql-configuration"></a>Azure SQL 配置

评估确定就绪情况和建议的 Azure SQL 部署类型后，会计算可满足或超过本地 SQL 实例性能的特定服务层级和 Azure SQL 配置（SKU 大小）：
1. 在发现过程中，Azure Migrate 收集 SQL 实例的配置和性能，其中包括：
    - vCore（分配的）和 CPU 使用率 (%)
        - SQL 实例的 CPU 使用率是指 SQL Server 上实例使用的已分配 CPU 的百分比
        - 数据库的 CPU 使用率是指 SQL 实例上数据库使用的已分配 CPU 的百分比
    - 内存（分配的）和内存使用率 (%)
    - 读取 IO/秒和写入 IO/秒（数据和日志文件）
        - 通过添加在 SQL 实例中发现的所有数据库的读取 IO/秒和写入 IO/秒，计算在该实例级别的读取 IO/秒和写入 IO/秒。
    - 读取 MB/秒和写入 MB/秒（吞吐量）
    - IO 操作延迟
    - 总数据库大小和数据库文件组织
        - 通过添加所有数据文件和日志文件来计算数据库大小。
1. 该评估聚合了所有配置和性能数据，并尝试在各种 Azure SQL 服务层级和配置中找到最佳匹配项，并选取可匹配或超过 SQL 实例性能要求的配置，从而优化成本。

### <a name="confidence-ratings"></a>置信度分级 
每个 Azure SQL 评估都与置信度分级相关联。 分级范围从一星（最低）到五星（最高）。 置信度分级可帮助评估 Azure Migrate 所提供的大小建议的可靠性。
- 置信度分级是分配给评估。 此分级是基于对评估进行计算时所需数据点的可用性。
- 对于基于性能的大小调整，评估将收集所有 SQL 实例和数据库的性能数据，其中包括：
    - CPU 使用率 (%)
    - 内存使用率 (%)
    - 读取 IO/秒和写入 IO/秒（数据和日志文件）
    - 读取 MB/秒和写入 MB/秒（吞吐量）
    - IO 操作延迟
    
如果其中的任一使用率数值不可用，则大小建议可能不可靠。
下表显示了评估置信度分级，具体取决于可用数据点的百分比：

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星

#### <a name="low-confidence-ratings"></a>低置信度评级
以下列出了评估可能获得低置信度分级的一些原因：
- 在创建评估的过程中，没有对环境进行分析。 例如，如果在创建评估时性能持续时间设置为一天，那么开始发现后必须等待至少一天，才能收集到所有的数据点。
- 评估无法在评估期内收集部分或全部服务器的性能数据。 若要获得高置信度分级，请确保：
    - 服务器在评估期间处于开机状态
    - 允许端口 443 上的出站连接
    - Azure Migrate 中 SQL 代理的 Azure Migrate 连接状态是否为“已连接”，并检查上一个检测信号 
    - “已发现的 SQL 实例”边栏选项卡中所有 SQL 实例的 Azure Migrate 连接状态是否均为“已连接”

    请重新计算评估以反映置信度评级的最新更改。
- 某些数据库或实例是在计算评估期间创建的。 例如，假设针对上一个月的性能历史记录创建了评估，但有些数据库或实例是在一周前刚刚创建。 在这种情况下，整个评估过程中将不会用到新服务器的性能数据，而且置信度分级会较低。

> [!NOTE]
> 由于 Azure SQL 评估是基于性能的评估，因此，如果任何评估的置信度分级低于五星，建议等待至少一天，以便设备对环境进行分析，然后重新计算评估。 否则，基于性能的调整大小可能不可靠。

## <a name="calculate-monthly-costs"></a>计算每月成本
完成调整大小建议后，Azure SQL 评估使用内部定价 API 计算建议的 Azure SQL 配置的计算和存储成本。 它在所有实例中聚合计算和存储成本，以计算每月总计算成本。 
### <a name="compute-cost"></a>计算成本
- 为了计算 Azure SQL 配置的计算成本，评估考虑以下属性：
    - 适用于 SQL 许可证的 Azure 混合权益
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
    - 成本估算中至少增加了 5GB 的存储成本，并以 1GB 为增量增加了额外的存储成本。 [了解详细信息](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Azure SQL 托管实例**
    - 第一个 32 GB/实例/月存储没有增加存储成本，而是以 32GB 为增量增加额外的存储成本。 [了解详细信息](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>后续步骤
- [查阅](best-practices-assessment.md)关于创建评估的最佳做法。 
- 了解如何运行 [Azure SQL 评估](how-to-create-azure-sql-assessment.md)。