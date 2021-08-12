---
title: 使用 Azure Migrate 创建 AVS 评估 | Microsoft Docs
description: 介绍如何使用 Azure Migrate 创建 AVS 评估
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f7014f8a403614740bdbc05abab4e7024f83c196
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570513"
---
# <a name="create-an-azure-vmware-solution-assessment"></a>创建 Azure VMware 解决方案评估

本文介绍如何使用“Azure Migrate：发现和评估”为 VMware 环境中的本地服务器创建 Azure VMware 解决方案评估。

[Azure Migrate](migrate-services-overview.md) 可帮助你迁移到 Azure。 Azure Migrate 提供了一个中心，用于跟踪 Azure 的本地基础结构、应用程序和数据的发现、评估以及迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

## <a name="before-you-start"></a>准备工作

- 确保已[创建](./create-manage-projects.md) Azure Migrate 项目。
- 如果已创建项目，请确保已[添加](how-to-assess.md)“Azure Migrate：发现和评估”工具。
- 要创建评估，需要为 [VMware](how-to-set-up-appliance-vmware.md) 设置一个 Azure Migrate 设备，该设备可发现本地服务器，并将元数据和性能数据发送到“Azure Migrate：发现和评估”。 [了解详细信息](migrate-appliance.md)。
- 此外，还可以逗号分隔值 (CSV) 格式[导入服务器元数据](./tutorial-discover-import.md)。


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Azure VMware 解决方案 (AVS) 评估概述

使用“Azure Migrate：发现和评估”可以创建三种类型的评估。

***评估类型** | **详细信息**
--- | --- 
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 使用这种评估类型，可以对 [VMware](how-to-set-up-appliance-vmware.md) 和 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 环境中的本地服务器以及要迁移到 Azure VM 的[物理服务器](how-to-set-up-appliance-physical.md)进行评估。
**Azure SQL** | 将本地 SQL Server 从 VMware 环境迁移到 Azure SQL 数据库或 Azure SQL 托管实例的评估。
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 使用这种评估类型，可以评估 [VMware 环境](how-to-set-up-appliance-vmware.md)中要迁移到 Azure VMware 解决方案 (AVS) 的本地服务器。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> 只能为 VMware 环境中的服务器创建 Azure VMware 解决方案 (AVS) 评估。


可以使用两种类型的大小调整标准来创建 Azure VMware 解决方案 (AVS) 评估：

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的本地服务器的性能数据进行评估。 | 建议的节点大小：基于 CPU 和内存利用率数据，以及你为评估选择的节点类型、存储类型和 FTT 设置。
**本地** | 基于本地大小的评估。 | 建议的节点大小：基于本地服务器大小，以及你为评估选择的节点类型、存储类型和 FTT 设置。


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>运行 Azure VMware 解决方案 (AVS) 评估

1.  在“概述”页 >“Windows、Linux 和 SQL Server”中，单击“评估并迁移服务器”。  
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Azure Migrate 的“概述”页":::

1. 在 **Azure Migrate：发现和评估** 中，单击 **评估**。

   ![“评估”按钮的位置](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. 在“评估服务器” > “评估类型”中，选择“Azure VMware 解决方案 (AVS)”  。

1. 在“发现源”中：

    - 如果使用设备发现了服务器，请选择 **从 Azure Migrate 设备中发现的服务器**。
    - 如果使用导入的 CSV 文件发现了服务器，请选择 **导入的服务器**。 
    
1. 单击“编辑”查看评估属性。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="用于选择评估设置的页面":::
 

1. 在“评估属性” > “目标属性”中 ：

    - 在“目标位置”中，选择要迁移到的 Azure 区域。
       - 大小和成本建议基于你指定的位置。
   - “存储类型”默认为“vSAN” 。 这是用于 AVS 私有云的默认存储类型。
   - 预留实例当前不支持 AVS 节点。
1. 在“VM 大小”中：
    - “节点类型”默认为“AV36” 。 Azure Migrate 会建议将服务器迁移到 AVS 时所需的节点。
    - 在“FTT 设置，RAID 级别”中，选择“允许的故障数”和“RAID”的组合。  选定的 FTT 选项，与本地服务器磁盘要求共同决定 AVS 中所需的总 vSAN 存储。
    - 在“CPU 超额订阅”中，指定与 AVS 节点中一个物理内核关联的虚拟核心的比率。 超过 4:1 的超额订阅可能会导致性能降低，但可应用于 Web 服务器类型的工作负载。
    - 在“内存过载系数”中，指定群集上内存过载的比率。 例如，值为 1 则表示使用 100% 的内存，值为 0.5 则表示使用 50%，值为 2 则表示会使用可用内存的 200%。 值只能从 0.5 增加到 10，小数点后最多一位。
    - 在“重复数据消除和压缩系数”中，为工作负载指定期望的重复数据消除和压缩系数。 可以从本地 vSAN 或存储配置获取实际值，并且这个值可能会因工作负载而异。 值为 3 则表示 3 倍，所以对于 300GB 的磁盘，只会使用 100GB 存储。 值为 1 则表示不进行重复数据消除或压缩。 值只能从 1 增加到 10，小数点后最多一位。
1. 在“节点大小”中： 
    - 在“大小调整条件”中，选择进行评估时是基于静态元数据，还是以基于性能的数据为基础。 如果使用性能数据：
        - 在“性能历史记录”中，指示要用于评估的数据持续时间
        - 在“百分位使用率”中，指定要用于性能示例的百分位数值。 
    - 在“舒适因子”中，指明要在评估过程中使用的缓冲区。 此帐户用于解决季节性使用情况、短期性能历史记录，以及未来使用量可能会增加等问题。 例如，如果使用舒适因子 2：
    
        **组件** | **有效利用率** | **添加舒适因子 (2.0)**
        --- | --- | ---
        核心数 | 2  | 4
        内存 | 8 GB | 16 GB  

1. 在“定价”中：
    - 在 **套餐** 中，显示注册的 [Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 评估会估计该套餐的费用。
    - 在“货币”中，为帐户选择计费货币。
    - 在“折扣 (%)”中，添加基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。

1. 如有更改，请单击“保存”。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="评估属性":::

1. 在“评估服务器”中，单击“下一个” 。

1. 在“选择要评估的服务器” > “评估名称”中，指定评估的名称 。 
 
1. 在“选择或创建组”中，选择“新建”并指定组名称 。 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="向组中添加服务器":::
 
1. 选择设备，然后选择要添加到该组的服务器。 然后单击“下一步”。

1. 在“审阅 + 创建评估”中，查看评估详细信息，然后单击“创建评估”以创建组并运行评估 。

    > [!NOTE]
    > 对于基于性能的评估，建议在开始发现后至少等待一天，然后再创建评估。 这为收集具有较高置信度的性能数据提供了时间。 理想情况下，在开始发现后，等待指定的性能持续时间（日/周/月）进行高置信度评级。


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>查看 Azure VMware 解决方案 (AVS) 评估

Azure VMware 解决方案 (AVS) 评估介绍：

- Azure VMware 解决方案 (AVS) 就绪性：本地服务器是否适合迁移到 Azure VMware 解决方案 (AVS)。
- AVS 节点数：运行服务器所需的 AVS 节点的估计数目。
- 跨 AVS 节点的利用率：跨所有节点的预计 CPU、内存和存储利用率。
    - 利用率包括以下群集管理开销中的前期因素，例如 vCenter Server、NSX Manager（大型）、NSX Edge；如果部署了 HCX，则 HCX Manager 和 IX 设备将消耗约 44 个 vCPU（11 个 CPU），75GB RAM 和 722GB 存储，然后再进行压缩和重复数据删除。
- 每月成本估算：运行本地 VM 的所有 Azure VMware 解决方案 (AVS) 节点的每月估算成本。


### <a name="view-an-assessment"></a>查看评估

1. 在 **Windows、Linux 和 SQL Server** > **Azure Migrate：发现和评估** 中，单击 ** Azure VMware 解决方案** 旁边的数字。

1. 在“评估”中，选择某项评估将其打开。 例如（估算和费用仅用于示例）： 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="AVS 评估摘要":::

1. 查看评估摘要。 你还可以编辑评估属性，或重新计算评估。

### <a name="review-azure-vmware-solution-avs-readiness"></a>查看 Azure VMware 解决方案 (AVS) 就绪情况

1. 在“Azure 迁移就绪性”中，验证服务器是否已准备好迁移到 Azure。

2. 查看服务器状态：
    - 已为 AVS 准备就绪：服务器可以照原样迁移到 Azure (AVS)，无需任何更改。 服务器将在 AVS 中启动，并完全在 AVS 中受支持。
    - 有条件的准备就绪：服务器可能存在与当前 vSphere 版本的兼容性问题，并可能需要使用 VMware 工具和/或其他设置，才能在 AVS 中实现服务器的完整功能。
    - 未为 AVS 准备就绪：服务器不会在 AVS 中启动。 例如，如果本地服务器上附加了外部设备（例如 CD-ROM），则 VMotion 操作将会失败（如果使用 VMware VMotion）。
    - 就绪性未知：由于从本地环境收集的元数据不足，Azure Migrate 无法确定服务器的就绪情况。

3. 查看建议的工具：
    - VMware HCX 或 Enterprise：对于 VMware 服务器，若要将本地工作负载迁移到 Azure VMware 解决方案 (AVS) 私有云，建议使用 VMware 混合云扩展 (HCX) 解决方案作为迁移工具。 [了解详细信息](../azure-vmware/tutorial-deploy-vmware-hcx.md)。
    - 未知：对于通过 CSV 文件导入的服务器，默认迁移工具是未知的。 但对于 VMware 服务器，建议使用 VMware 混合云扩展 (HCX) 解决方案。 

4. 单击某个“AVS 迁移就绪性”状态。 可以查看 VM 就绪性详细信息，并深入查看 VM 详细信息，包括计算、存储和网络设置。

### <a name="review-cost-details"></a>查看成本详细信息

此视图显示在 Azure VMware 解决方案 (AVS) 中运行服务器的估算成本。

1. 查看每月总成本。 将聚合评估的组中所有服务器的成本。 

    - 成本估算基于所需的 AVS 节点数，并考虑所有服务器的总资源要求。
    - 由于 Azure VMware 解决方案 (AVS) 是按节点定价的，因此总成本不含计算成本和存储成本。
    - 成本估算适用于在 AVS 上运行的本地服务器。 AVS 评估不会考虑 PaaS 或 SaaS 成本。
    
2. 可以查看每月存储估算成本。 此视图显示评估的组的聚合存储成本，按不同类型的存储磁盘划分。

3. 可以深入查看特定服务器的详细信息。


### <a name="review-confidence-rating"></a>查看置信度分级

运行基于性能的评估时，会将一个置信度分级分配到评估。

![置信度分级](./media/how-to-create-assessment/confidence-rating.png)

- 将授予从 1 星（最低）到 5 星（最高）的分级。
- 置信度分级可帮助你判断评估所提供的大小建议的可靠性。
- 置信度分级基于计算评估所需的数据点的可用性。
- 对于基于性能的大小调整，AVS 评估需要 CPU 和服务器内存的利用率数据。 将收集以下性能数据，但这些数据不会在 AVS 评估的大小建议中使用：
  - 连接到服务器的每个磁盘所对应的磁盘 IOPS 和吞吐量数据。
  - 为每个连接到服务器的网络适配器处理基于性能的调整大小所需要的网络 I/O。

评估的置信度分级如下。

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星

[详细了解](concepts-azure-vmware-solution-assessment-calculation.md)性能数据 


## <a name="next-steps"></a>后续步骤

- 了解如何使用[依赖项映射](how-to-create-group-machine-dependencies.md)创建高可信度组。
- [详细了解](concepts-azure-vmware-solution-assessment-calculation.md)如何计算 AVS 评估。
