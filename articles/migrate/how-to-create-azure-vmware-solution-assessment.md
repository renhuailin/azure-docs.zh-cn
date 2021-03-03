---
title: 使用 Azure Migrate Server 评估创建 AVS 评估 |Microsoft Docs
description: 介绍如何使用 Azure Migrate Server 评估工具创建 AVS 评估
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e386db1ee2042d75a31d4a9de2a5174e904c6b5c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732966"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a> (AVS) 评估创建 Azure VMware 解决方案

本文介绍如何使用 Azure Migrate： Server 评估为本地 VMware Vm 创建 Azure VMware 解决方案 (AVS) 评估。

[Azure Migrate](migrate-services-overview.md) 可帮助你迁移到 Azure。 Azure Migrate 提供了一个中心，用于跟踪 Azure 的本地基础结构、应用程序和数据的发现、评估以及迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

## <a name="before-you-start"></a>准备工作

- 请确保已 [创建](./create-manage-projects.md) Azure Migrate 项目。
- 如果已创建项目，请确保已 [添加](how-to-assess.md) Azure Migrate： Server 评估工具。
- 若要创建评估，需要设置用于 [VMware](how-to-set-up-appliance-vmware.md)的 Azure Migrate 设备，该设备将发现本地计算机，并将元数据和性能数据发送到 Azure Migrate：服务器评估。 [了解详细信息](migrate-appliance.md)。
- 还可以 (CSV) 格式以逗号分隔 [的值导入服务器元数据](./tutorial-discover-import.md) 。


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Azure VMware 解决方案 (AVS) 评估概述

使用“Azure Migrate: 服务器评估”可以运行两种类型的评估。

**评估类型** | **详细信息**
--- | --- 
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 可以使用此评估类型评估本地 [VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和 [物理服务器](how-to-set-up-appliance-physical.md) ，以便迁移到 Azure。[了解更多](concepts-assessment-calculation.md)
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。[了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> 只能为 VMware Vm 创建 Azure VMware 解决方案 (AVS) 评估。


可以使用两种类型的大小调整条件来创建 Azure VMware 解决方案 (AVS) 评估：

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于本地 Vm 的收集的性能数据进行评估。 | **推荐的节点大小**：基于 CPU 和内存使用率数据，以及你为评估选择的节点类型、存储类型和 FTT 设置。
**本地** | 基于本地大小的评估。 | **推荐的节点大小**：根据本地 VM 大小以及你为评估选择的节点类型、存储类型和 FTT 设置。


## <a name="run-an-azure-vmware-solution-avs-assessment"></a> (AVS) 评估中运行 Azure VMware 解决方案

1. 在“服务器”页 >“Windows 和 Linux 服务器”中，单击“评估和迁移服务器”  。

   ![“评估和迁移服务器”按钮的位置](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. 在 **Azure Migrate:** 服务器评估”中，单击“评估”。

1. 在 "**评估服务器**  >  **评估类型**" 中，选择 " **Azure VMware 解决方案 (AVS)**。

1. 在“发现源”中：

    - 如果使用设备发现了计算机，请选择“从 Azure Migrate 设备中发现的计算机”。
    - 如果使用导入的 CSV 文件发现了计算机，请选择“导入的计算机”。 
    
1. 单击“编辑”查看评估属性。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="用于选择评估设置的页面":::
 

1. 在“评估属性” > “目标属性”中 ：

    - 在“目标位置”中，选择要迁移到的 Azure 区域。
       - 大小和成本建议基于你指定的位置。
   - “存储类型”默认为“vSAN” 。 这是用于 AVS 私有云的默认存储类型。
   - 预留实例当前不支持 AVS 节点。
1. 在“VM 大小”中：
    - “节点类型”默认为“AV36” 。 Azure Migrate 会建议将 VM 迁移到 AVS 时所需的节点。
    - 在“FTT 设置，RAID 级别”中，选择“允许的故障数”和“RAID”的组合。  选定的 FTT 选项，与本地 VM 磁盘要求共同决定 AVS 中所需的总 vSAN 存储。
    - 在“CPU 超额订阅”中，指定与 AVS 节点中一个物理内核关联的虚拟核心的比率。 超过 4:1 的超额订阅可能会导致性能降低，但可应用于 Web 服务器类型的工作负载。
    - 在 " **内存过载**" 中，指定群集上的提交内存的比率。 值为1表示100% 的内存使用情况，0.5 例如50%，2则使用可用内存的200%。 只能将介于0.5 到10之间的值添加到一个小数位数。
    - 在 " **重复重复和压缩因子**" 中，为工作负荷指定预期的重复重复和压缩系数。 可以从本地 vSAN 或存储配置获取实际值，这可能因工作负荷而异。 值为3表示只使用300GB 磁盘的3倍。 如果值为1，则表示不重复或压缩。 只能将介于1到10之间的值添加到一个小数位。
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
    - 在“产品/服务”中，会显示已注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)，“服务器评估”会估计该产品/服务的费用。
    - 在“货币”中，为帐户选择计费货币。
    - 在“折扣 (%)”中，添加基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。

1. 如有更改，请单击“保存”。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="评估属性":::

1. 在“评估服务器”中，单击“下一个” 。

1. 在“选择要评估的计算机” > “评估名称”中，指定评估的名称 。 
 
1. 在“选择或创建组”中，选择“新建”并指定组名称 。 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="将 VM 添加到组":::
 
1. 选择设备，然后选择要添加到组的 VM。 然后单击“下一步”。

1. 在“审阅 + 创建评估”中，查看评估详细信息，然后单击“创建评估”以创建组并运行评估 。

    > [!NOTE]
    > 对于基于性能的评估，建议在开始发现后至少等待一天，然后再创建评估。 这为收集具有较高置信度的性能数据提供了时间。 理想情况下，在开始发现后，等待指定的性能持续时间（日/周/月）进行高置信度评级。


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>查看 Azure VMware 解决方案 (AVS) 评估

Azure VMware 解决方案 (AVS) 评估介绍：

- **Azure VMware 解决方案 (AVS) 准备情况**：本地 vm 是否适用于迁移到 Azure VMware 解决方案 (AVS) 。
- **AVS 节点数**：运行 vm 所需的每个 avs 节点数估算。
- **跨 AVS 节点的使用率**：在所有节点上预计的 CPU、内存和存储利用率。
    - 利用率包括以下群集管理开销中的前期分解，如 vCenter Server、NSX Manager (大型) 、NSX 边缘，如果部署了 HCX，则 HCX Manager 和 IX 设备使用 ~ 44vCPU (11 CPU) ，在压缩和重复数据删除之前，需要 75GB RAM 和722GB 存储。
- **每月成本估计**：运行本地 vm 的所有 Azure VMware 解决方案 (AVS) 节点的每月估算成本。


### <a name="view-an-assessment"></a>查看评估

1. 在“迁移目标” >  “服务器”中，单击“Azure Migrate:   服务器评估”中的“评估”。

2. 在“评估”中，单击某项评估将其打开。

    :::image type="content" source="./media/how-to-create-avs-assessment/avs-assessment-summary.png" alt-text="AVS 评估摘要":::

### <a name="review-azure-vmware-solution-avs-readiness"></a>查看 Azure VMware 解决方案 (AVS) 准备情况

1. 在 **Azure 准备情况** 下，验证 vm 是否已准备好迁移到 AVS。

2. 查看 VM 状态：
    - **适用于 AVS**：计算机可以按原样迁移到 AZURE (AVS) ，无需任何更改。 它将在具有完全 AVS 支持的 AVS 中开始。
    - **准备就绪**： vm 可能会在当前 vSphere 版本中出现兼容性问题，还可能需要 VMware 工具和或其他设置，然后才能在 AVS 中实现 VM 的完整功能。
    - **尚未准备好用于 avs**： VM 将不会在 avs 中启动。 例如，如果本地 VMware VM 附带附加的外部设备（例如 cd-rom），则如果使用 VMware VMotion) ，VMotion 操作将失败 (。
    - **准备情况未知**： Azure Migrate 无法确定计算机的就绪性，因为从本地环境收集的元数据不足。

3. 查看建议的工具：
    - **VMWARE HCX 或 Enterprise**：对于 vmware 计算机，Vmware 混合云扩展 (HCX) 解决方案是将本地工作负荷迁移到 Azure VMware 解决方案 (AVS) 私有云的建议迁移工具。 [了解详细信息](../azure-vmware/tutorial-deploy-vmware-hcx.md)。
    - **未知**：对于通过 CSV 文件导入的计算机，默认迁移工具是未知的。 但对于 VMware 计算机，建议使用 VMware 混合云扩展 (HCX) 解决方案。 

4. 单击 **AVS 就绪** 状态。 可以查看 VM 就绪性详细信息，并深入查看 VM 详细信息，包括计算、存储和网络设置。

### <a name="review-cost-details"></a>查看成本详细信息

此视图显示在 Azure VMware 解决方案 (AVS) 中运行 Vm 的预计成本。

1. 查看每月总成本。 将聚合评估的组中所有 VM 的成本。 

    - 成本估算基于所有 Vm 的资源需求所需的 AVS 节点数。
    - 由于 Azure VMware 解决方案的定价 (AVS) 按节点进行，因此总成本不会产生计算成本和存储成本分布。
    - 估算成本适用于在 AVS 上运行的本地 VM。 AVS 评估不会考虑 PaaS 或 SaaS 成本。
    
2. 可以查看每月存储估算成本。 此视图显示评估的组的聚合存储成本，按不同类型的存储磁盘划分。

3. 可以深入查看特定 VM 的详细信息。


### <a name="review-confidence-rating"></a>查看置信度分级

运行基于性能的评估时，会将一个置信度分级分配到评估。

![置信度分级](./media/how-to-create-assessment/confidence-rating.png)

- 将授予从 1 星（最低）到 5 星（最高）的分级。
- 置信度分级可帮助你判断评估所提供的大小建议的可靠性。
- 置信度分级基于计算评估所需的数据点的可用性。
- 对于基于性能的大小调整，服务器评估中的 AVS 评估需要 CPU 和 VM 内存的利用率数据。 收集以下性能数据，但不在 AVS 评估的大小建议中使用：
  - 附加到 VM 的每个磁盘的磁盘 IOPS 和吞吐量数据。
  - 为附加到 VM 的每个网络适配器处理基于性能的大小调整的网络 i/o。

评估的置信度分级如下。

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星

[了解](concepts-azure-vmware-solution-assessment-calculation.md) 有关性能数据的详细信息 


## <a name="next-steps"></a>后续步骤

- 了解如何使用 [依赖关系映射](how-to-create-group-machine-dependencies.md) 创建高可信度组。
- [了解](concepts-azure-vmware-solution-assessment-calculation.md) 有关如何计算 AVS 评估的详细信息。