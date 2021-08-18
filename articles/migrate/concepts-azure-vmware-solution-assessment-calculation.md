---
title: Azure Migrate 中的 AVS 评估计算 | Microsoft Docs
description: 概述 Azure Migrate 服务中的 AVS 评估计算。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: ef1f069dbcf932475a062b309324a77932a88809
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750732"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>评估概述（迁移到 Azure VMware 解决方案）

[Azure Migrate](migrate-services-overview.md) 提供一个中心，以用于跟踪本地应用和工作负载的发现、评估及迁移。 它还在 Azure 中跟踪私有云和公有云实例。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品/服务。

Azure Migrate 中的发现和评估工具可以评估要迁移到 Azure 虚拟机和 Azure VMware 解决方案 (AVS) 的本地服务器。 本文提供了有关如何计算 Azure VMware 解决方案 (AVS) 评估的信息。

> [!NOTE]
> Azure VMware 解决方案 (AVS) 评估只能为 VMware VM 创建。

## <a name="types-of-assessments"></a>评估的类型

使用 Azure Migrate 创建的评估是数据的时间点快照。 使用“Azure Migrate: 服务器评估”可以运行

**评估类型** | **详细信息**
--- | --- 
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 使用这种评估类型，可以对 [VMware](how-to-set-up-appliance-vmware.md) 和 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 环境中的本地服务器以及要迁移到 Azure VM 的[物理服务器](how-to-set-up-appliance-physical.md)进行评估。
**Azure SQL** | 将本地 SQL Server 从 VMware 环境迁移到 Azure SQL 数据库或 Azure SQL 托管实例的评估。
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> 如果“发现和评估”工具上的 Azure VM 或 AVS 评估数不正确，请单击评估总数以导航到所有评估并重新计算 Azure VM 或 AVS 评估数。 然后，发现和评估工具就会显示该评估类型的正确计数。 

Azure VMware 解决方案 (AVS) 评估提供两个大小调整条件选项：

| **评估** | **详细信息** | **数据** |
| - | - | - |
| **基于性能** | 基于收集的本地 VM 性能数据的评估。 | 建议的节点大小：基于 CPU 和内存利用率数据，以及你为评估选择的节点类型、存储类型和 FTT 设置。 |
| **本地** | 基于本地大小的评估。 | 建议的节点大小：基于本地 VM 大小，以及你为评估选择的节点类型、存储类型和 FTT 设置。 |

## <a name="how-do-i-run-an-assessment"></a>如何运行评估？

可通过多种方法运行评估。

- 使用轻型 Azure Migrate 设备收集的服务器元数据来评估服务器。 设备会发现本地服务器。 然后它会将服务器元数据和性能数据发送到 Azure Migrate。 这样可以实现更高的精度。
- 使用以逗号分隔的值 (CSV) 格式导入的服务器元数据来评估服务器。

## <a name="how-do-i-assess-with-the-appliance"></a>如何使用设备进行评估？

如果部署 Azure Migrate 设备来发现本地服务器，请执行以下步骤：

1. 设置 Azure 及适合于 Azure Migrate 运行的本地环境。
2. 第一次评估时，创建一个 Azure 项目并向其中添加“发现和评估”工具。
3. 部署轻型 Azure Migrate 设备。 此设备将持续发现本地服务器，并向 Azure Migrate 发送服务器元数据和性能数据。 将设备部署为 VM。 无需在要评估的服务器上安装任何内容。

设备开始服务器发现后，可以将想要评估的服务器归集到一个组中，然后对评估类型为“Azure VMware 解决方案(AVS)”的组运行评估。

按照[此处](how-to-create-azure-vmware-solution-assessment.md)的步骤创建第一个 Azure VMware 解决方案 (AVS) 评估。

## <a name="how-do-i-assess-with-imported-data"></a>如何使用导入的数据进行评估？

如果要使用 CSV 文件来评估服务器，则不需要设备。 而是执行以下步骤：

1. 设置 Azure 以便使用 Azure Migrate。
2. 第一次评估时，请创建一个 Azure 项目并向其中添加“发现和评估”工具。
3. 下载 CSV 模板并向其中添加服务器数据。
4. 将模板导入 Azure Migrate。
5. 发现通过导入添加的服务器后，将其归集到一个组中，然后对评估类型为“Azure VMware 解决方案(AVS)”的组运行评估。

## <a name="what-data-does-the-appliance-collect"></a>设备会收集哪些数据？

如果使用 Azure Migrate 设备进行评估，请了解针对 [VMware](migrate-appliance.md#collected-data---vmware) 收集的元数据和性能数据。

## <a name="how-does-the-appliance-calculate-performance-data"></a>设备如何计算性能数据？

如果是使用设备进行发现，则会通过以下步骤为计算设置收集性能数据：

1. 设备收集实时样本点。

   - VMware VM：每隔 20 秒收集一个样本点。
2. 设备将样本点组合起来，每隔 10 分钟创建一个数据点。 为了创建数据点，设备会选择所有样本中的峰值。 然后，将数据点发送到 Azure。
3. Azure Migrate 将存储上个月的所有 10 分钟数据点。
4. 创建评估时，该评估会确定要用于合理精简的相应数据点。 根据“性能历史记录”和“百分位使用率”的百分位值进行确定。

   - 例如，性能历史记录是一周，百分位使用率是第 95 百分位，则评估会对上一周的 10 分钟样本点进行排序。 按升序排序，并选取第 95 百分位值进行合理调整大小。
   - 第 95 百分位值可以确保忽略任何离群值，如果选取第 99 百分位，则可能包括这些离群值。
   - 如果希望选择该期间内的峰值使用率，并且不希望错过任何离群值，请为百分位使用率选择第 99 百分位。
5. 此值与舒适因子相乘，就得到了设备收集的这些指标的有效性能使用率数据：

   - CPU 使用率
   - RAM 利用率

将收集以下性能数据，但这些数据不会在 AVS 评估的大小建议中使用：

- 连接到 VM 的每个磁盘所对应的磁盘 IOPS 和吞吐量数据。
- 为每个连接到 VM 的网络适配器处理基于性能的调整大小所需要的网络 I/O。

## <a name="how-are-avs-assessments-calculated"></a>如何计算 AVS 评估？

AVS 评估使用本地服务器的元数据和性能数据来计算评估。 如果部署 Azure Migrate 设备，则评估将使用该设备收集的数据。 但如果运行的是使用 CSV 文件导入的评估，则需要提供用于计算的元数据。

计算分为三个阶段：

1. 计算 Azure VMware 解决方案 (AVS) 就绪性：本地 VM 是否适合迁移到 Azure VMware 解决方案 (AVS)。
2. 计算 AVS 节点数以及所有节点的利用率：运行 VMware VM 所需的估计 AVS 节点数，以及所有节点的预计 CPU、内存和存储利用率。
3. 每月成本估算：运行本地 VM 的所有 Azure VMware 解决方案 (AVS) 节点的每月估算成本。

这些计算就按上面所列顺序发生。 只有通过了前一个阶段，服务器才会进入下一个阶段。 例如，如果服务器通不过计算 AVS 就绪性阶段，则会直接标记为不适合迁移到 Azure。 将不会对该服务器执行大小调整和成本计算。

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 解决方案 (AVS) 评估包括哪些内容？

AVS 评估包括以下内容：

| **属性** | **详细信息** |
| - | - |
| **目标位置** | 指定要迁移到的 AVS 私有云位置。 |
| **存储类型** | 指定要在 AVS 中使用的存储引擎。 AVS 目前仅支持将 vSAN 用作默认存储类型，但今后会有更多的存储选项根据路线图推出。 |
| 预留实例 (RI) | 此属性帮助你在 AVS 中指定预留实例（如果已购买）和预留实例的期限。 用于计算成本。 |
| 节点类型 | 指定要在 Azure 中使用的 [AVS 节点类型](../azure-vmware/concepts-private-clouds-clusters.md)。 默认节点类型为 AV36。 将来可能会推出更多节点类型。  Azure Migrate 将为迁移到 AVS 的 VM 建议所需的节点数。 |
| FTT 设置，RAID 级别 | 指定“容许的故障数”和“RAID”的有效组合。 选定的 FTT 选项，与 RAID 级别和本地 VM 磁盘要求共同确定了 AVS 中所需的总 vSAN 存储。 计算后的总可用存储还包括：a) 为 vCenter 等管理对象预留的空间 b) 25% 的存储 slack，用于执行 vSAN 操作。 |
| **“大小调整”条件** | 设置用于确定 AVS 节点内存、CPU 和存储要求的条件。 可以选择基于性能的大小调整或与本地相同的大小调整，而不用考虑性能历史记录 。 若要简单地直接迁移，请选择设置为本地大小。 若要基于使用情况进行大小调整，请选择基于性能的大小调整。 |
| **性能历史记录** | 设置评估服务器性能数据时要考虑的持续时间。 仅当大小调整条件基于性能时，此属性才适用。 |
| **百分位使用率** | 指定进行适当大小调整时要考虑的性能样本集的百分位值。 仅当大小调整基于性能时，此属性才适用。 |
| **舒适因子** | Azure Migrate 在评估期间会考虑到缓冲（舒适因子）。 此缓冲区是基于 VM 的服务器利用率数据（CPU、内存和磁盘）应用的。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 |
| **产品/服务** | 显示已注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。 |
| **货币** | 显示帐户的计费货币。 |
| **折扣 (%)** | 列出基于 Azure 产品/服务获得的任何订阅特定折扣。 默认设置是 0%。 |
| **Azure 混合权益** | 指定你是否具有软件保障以及是否有资格享受 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 尽管这不会影响 Azure VMware 解决方案定价（因为定价基于节点），但客户仍可使用 Azure 混合权益在 AVS 中应用（基于 Microsoft 的）本地 OS 或 SQL 许可证。 其他软件 OS 供应商必须提供其自己的许可条款，例如 RHEL。 |
| vCPU 过度订阅 | 指定 AVS 节点中与一个物理核心关联的虚拟核心数比率。 计算中的默认值为 AVS 中的 4 个 vCPU:1 个物理核心。 API 用户可将此值设置为整数。 请注意，大于 4:1 的 vCPU 过度订阅可能会影响工作负载，具体取决于其 CPU 使用率。 调整大小时，我们始终假设所选核心的利用率达到 100%。 |
| 内存过度提交因子 | 指定群集上的内存过度提交比。 例如，值为 1 则表示使用 100% 的内存，值为 0.5 则表示使用 50%，值为 2 则表示会使用可用内存的 200%。 值只能从 0.5 增加到 10，小数点后最多一位。 |
| 重复数据消除和压缩因子 | 为工作负载指定期望的重复数据消除和压缩因子。 实际值可以从本地 vSAN 或存储配置获取。这些值因工作负荷而异。 值为 3 则表示 3 倍，所以对于 300GB 的磁盘，只会使用 100GB 存储。 值为 1 则表示不进行重复数据消除或压缩。 值只能从 1 增加到 10，小数点后最多一位。 |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Azure VMware 解决方案 (AVS) 适用性分析

AVS 评估通过检查服务器属性来评估每个本地 VM 的 AVS 适用性。 它还将每个已评估的服务器分配到以下适用性类别之一：

- 已为 AVS 准备就绪：服务器可以照原样迁移到 Azure (AVS)，无需任何更改。 服务器将在 AVS 中启动，并完全在 AVS 中受支持。
- 有条件地准备就绪：VM 可能存在与当前 vSphere 版本的兼容性问题，并可能需要使用 VMware 工具和/或其他设置，才能在 AVS 中实现 VM 的完整功能。
- 未为 AVS 准备就绪：VM 不会在 AVS 中启动。 例如，如果本地 VMware VM 上附加了外部设备（例如 CD-ROM），则 VMware VMotion 操作将会失败（如果使用 VMware VMotion）。
- 就绪性未知：由于从本地环境收集的元数据不足，Azure Migrate 无法确定服务器的就绪情况。

评估将检查服务器属性，以确定本地服务器的 Azure 就绪情况。

### <a name="server-properties"></a>服务器属性

评估将检查本地 VM 的以下属性，以确定它是否可以在 Azure VMware 解决方案 (AVS) 上运行。

| **属性** | **详细信息** | **AVS 就绪状态** |
| - | - | - |
| Internet 协议 | Azure 目前不支持端到端 IPv6 Internet 寻址。 如果检测到服务器使用 IPv6，请与你当地的 MSFT AVS GBB 团队联系，以获取修正指南。 | Internet 协议有条件地准备就绪 |

### <a name="guest-operating-system"></a>来宾操作系统

目前，AVS 评估在进行适用性分析过程中不会检查操作系统。 在本地 VM 上运行的所有操作系统都可能可以在 Azure VMware 解决方案 (AVS) 上运行。

除了检查 VM 属性以外，评估还会检查服务器的来宾操作系统，以确定它是否可以在 Azure 上运行。

## <a name="sizing"></a>调整大小

服务器已标记为“已为 AVS 准备就绪”后，AVS 评估将给出节点大小调整建议，这涉及到确定相应的本地 VM 要求和所需的 AVS 节点总数。 这些建议因指定的评估属性而异。

- 如果评估使用基于性能的大小调整，则 Azure Migrate 会考虑服务器的性能历史记录，给出针对 AVS 的适当大小调整建议。 如果你过度分配了本地 VM，但利用率较低，并且你希望在 AVS 中适当调整 VM 大小以节省成本，则此方法特别有帮助。 此方法可帮助你在迁移过程中优化大小。
- 如果你不想要在调整 VM 大小时考虑性能数据，并希望将本地服务器按原样迁移到 AVS，可以将大小调整条件设置为*“与本地相同”*。 然后，评估将根据本地配置调整 VM 大小，而不考虑利用率数据。

### <a name="ftt-sizing-parameters"></a>FTT 大小调整参数

AVS 中使用的存储引擎为 vSAN。 vSAN 存储策略定义了服务器的存储要求。 这些策略保证了 VM 所需的服务级别，因为它们可确定如何将存储分配给 VM。 可用的 FTT-RAID 组合为：

| **允许的故障数 (FTT)** | **RAID 配置** | **需要的最少主机数** | **大小调整注意事项** |
| - | - | - | - |
| 1 | RAID-1（镜像） | 3 | 100GB VM 将使用 200GB。 |
| 1 | RAID-5（擦除编码） | 4 | 100GB VM 将使用 133.33 GB |
| 2 | RAID-1（镜像） | 5 | 100GB VM 将使用 300GB。 |
| 2 | RAID-6（擦除编码） | 6 | 100GB VM 将使用 150GB。 |
| 3 | RAID-1（镜像） | 7 | 100GB VM 将使用 400GB。 |

### <a name="performance-based-sizing"></a>基于性能的大小

对于基于性能的大小调整，Azure Migrate 设备将分析本地环境，以收集 CPU、内存和磁盘的性能数据。 因此，AVS 的基于性能的大小调整将考虑已分配的磁盘空间，并使用内存和 CPU 的所选百分位利用率。 例如，如果为 VM 分配了 4 个 vCore，但仅使用了其中的 25%，则 AVS 会将该 VM 的 vCore 数调整为 1 个。

**性能数据收集步骤：**

1. 对于 VMware VM，Azure Migrate 设备每隔 20 秒收集一个实时样本点。
2. 设备每隔 10 分钟汇总收集的样本点，并将过去 10 分钟的最大值发送到 Azure Migrate。
3. Azure Migrate 存储过去一个月的所有 10 分钟样本点。 然后，它根据为“性能历史记录”和“百分位利用率”指定的评估属性，确定用于适当调整大小的适当数据点 。 例如，如果性能历史记录设置为 1 天，百分位利用率为第 95 百分位，则 Azure Migrate 将使用过去一天的 10 分钟样本点，按升序对其排序，并选取第 95 百分位值来适当调整大小。
4. 将此值与舒适因子相乘，可以获得设备收集的每个指标（CPU 利用率和内存利用率）的有效性能利用率数据。

确定有效利用率值后，将按如下所述处理存储、网络和计算大小调整。

存储大小调整：除了客户选择的 FTT 设置以外，Azure Migrate 还使用总本地 VM 磁盘空间作为计算参数来确定 AVS vSAN 存储要求。 FTT（容许的故障数）、在每个 FTT 选项中指定的最小节点数、VM 磁盘要求将共同确定所需的总 vSAN 存储。 目前不会考虑存储利用率，逻辑只会检查为每个 VM 分配的存储。

网络大小调整：AVS 评估目前不考虑任何网络设置。

计算大小调整：计算存储要求后，AVS 评估将考虑 CPU 和内存要求，以根据节点类型确定 AVS 所需的节点数。

- AVS 评估根据大小调整条件检查基于性能的 VM 数据或本地 VM 配置。 舒适因子设置允许指定群集的增长因子。 目前，默认情况下会启用超线程，因此 36 个核心节点将具有 72 个 vCore。 结合每台物理计算机使用 4 个 vCore 和不超过 80% 利用率的 VMware 标准，来确定每个群集的 CPU 阈值，以便在不影响群集可用性的情况下进行维护或故障处理。 目前没有任何替代方式可以更改过度订阅值，我们可能会在将来的版本中提供此功能。

### <a name="as-on-premises-sizing"></a>按本地大小调整

如果你使用“与本地相同”的大小调整方法，则 AVS 评估不会考虑 VM 和磁盘的性能历史记录， 而是根据本地分配的大小分配 AVS 节点。 AVS 中的默认存储类型为 vSAN。

[详细了解](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment)如何查看 Azure VMware 解决方案评估。

### <a name="cpu-utilization-on-avs-nodes"></a>AVS 节点上的 CPU 利用率

CPU 利用率假设使用了 100% 的可用核心数。 若要减少所需节点数，可以根据工作负载的特征和临场经验增大过度订阅，例如，从 4:1 增大至 6:1。 与针对磁盘不同，AVS 不会针对 CPU 利用率施加任何限制，而是由客户负责确保其群集以最佳方式运行，因此，如果需要“热运行”，请相应地做出调整。 若要为增长留出更多空间，请减小过度订阅或增大增长因子的值。

CPU 利用率还考虑到了 vCenter、NSX 管理器和其他较小资源中的管理开销。

### <a name="memory-utilization-on-avs-nodes"></a>AVS 节点上的内存利用率

内存利用率显示所有节点中的总内存与服务器或工作负载中的要求。 可以过度订阅内存，同样，AVS 不会施加任何限制，由客户负责确保为其工作负载运行最佳性能的群集。

内存利用率还考虑到了 vCenter、NSX 管理器和其他较小资源中的管理开销。

### <a name="storage-utilization-on-avs-nodes"></a>AVS 节点上的存储利用率

存储利用率是按以下顺序计算的：

1. VM 所需的大小（按原样分配，或基于性能的已用空间）
2. 应用增长因子（如果有）
3. 添加管理开销并应用 FTT 比
4. 应用重复数据消除和压缩因子
5. 对 vSAN 应用所需的 25% slack
6. 计算出总存储中可供 VM 使用的存储（包括管理开销）。

3 节点群集上的可用存储是基于默认存储策略 (Raid-1) 并使用丰富预配计算得出的。 例如，根据擦除编码或 Raid-5 计算时，至少需要 4 个节点。 请注意，在 AVS 中，存储策略必须由管理员更改才能留出更多空间。

## <a name="confidence-ratings"></a>置信度评级

Azure Migrate 中每个基于性能的评估都与一个置信度评级相关联，评级范围为一星（最低）到五星（最高）。

- 为评估分配置信度时，会考虑到进行评估计算时所需数据点的可用性。
- 对评估的置信度分级可以用来评估 Azure Migrate 提供的大小建议的可靠性。
- 置信度评级不适用于“与本地相同”的评估。
- 对于基于性能的大小调整，AVS 评估需要 CPU 和 VM 内存的利用率数据。 将收集以下数据，但这些数据不会在 AVS 的大小调整建议中使用：

  - 连接到 VM 的每个磁盘所对应的磁盘 IOPS 和吞吐量数据。
  - 为每个连接到 VM 的网络适配器处理基于性能的调整大小所需要的网络 I/O。

  如果 vCenter Server 中未提供其中的任何一个利用率数字，则大小建议可能不可靠。

按如下所述根据可用的数据点百分比，给出评估的置信度评级。

| **数据点的可用性** | **置信度分级** |
| - | - |
| 0-20% | 1 星 |
| 21-40% | 2 星 |
| 41-60% | 3 星 |
| 61-80% | 4 星 |
| 81-100% | 5 星 |

### <a name="low-confidence-ratings"></a>低置信度评级

以下列出了评估可能获得低置信度分级的一些原因：

- 在创建评估的过程中，没有对环境进行分析。 例如在创建评估时，性能持续时间设置为一天，那么开始发现后必须等待至少一天，才能收集到所有的数据点。
- 评估无法在评估期内收集部分或全部服务器的性能数据。 若要获得较高的置信度评级，请确保：

  - VM 在评估期间处于开机状态
  - 允许端口 443 上的出站连接
  - 为 Hyper-V VM 启用了动态内存

  请重新计算评估以反映置信度评级的最新更改。
- 某些 VM 是在计算评估期间创建的。 例如，针对上一个月的性能历史记录创建评估，但有些 VM 只是在一周前创建的。 在这种情况下，新 VM 的性能数据在整个过程中都不可用，并且置信度分级会较低。

> [!NOTE]
> 如果任何评估的置信度评级低于五星，建议等待至少一天，以便设备对环境进行分析，然后重新计算评估。 否则，基于性能调整大小可能不可靠。 在这种情况下，建议将评估切换为本地调整大小。

## <a name="monthly-cost-estimation"></a>每月成本估计

完成大小调整建议后，Azure Migrate 会将所需 AVS 节点数乘以节点价格，计算出在 AVS 中运行本地工作负载的总成本。 每个 VM 的成本是将总成本除以评估中的 VM 数计算得出的。

- 计算中考虑到了所需节点数、节点类型和位置。
- 它将所有节点的成本求和，计算出每月总成本。
- 成本以在评估设置中指定的币种显示。

由于 Azure VMware 解决方案 (AVS) 是按节点定价的，因此总成本不含计算成本和存储成本。 [了解详细信息](../azure-vmware/introduction.md)

## <a name="migration-tool-guidance"></a>迁移工具指导

在 Azure VMware 解决方案 (AVS) 评估的 Azure 迁移就绪性报告中，可以看到以下推荐工具：

- VMware HCX 或 Enterprise：对于 VMware 服务器，若要将本地工作负载迁移到 Azure VMware 解决方案 (AVS) 私有云，建议使用 VMware 混合云扩展 (HCX) 解决方案作为迁移工具。 [了解详细信息](../azure-vmware/install-vmware-hcx.md)。
- 未知：对于通过 CSV 文件导入的服务器，默认迁移工具是未知的。 不过，对于 VMware 服务器，建议使用 VMware 混合云扩展 (HCX) 解决方案。

## <a name="next-steps"></a>后续步骤

为 [AVS VMware VM](how-to-create-azure-vmware-solution-assessment.md) 创建评估。
