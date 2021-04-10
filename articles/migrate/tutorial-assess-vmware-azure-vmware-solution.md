---
title: 使用 Azure Migrate 评估要迁移到 Azure VMware 解决方案 (AVS) 的 VMware 服务器
description: 了解如何使用 Azure Migrate 来评估要迁移到 AVS 的 VMware 环境中的服务器。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 31bf3909012231996bd340cfa4d388f0fe20a4f5
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782109"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>教程：评估 VMware 服务器以便迁移到 AVS

在迁移到 Azure 的过程中，将评估本地工作负载，以衡量云的就绪性、确定风险以及估算成本和复杂性。

本文介绍如何使用 Azure Migrate 来评估发现的 VMware 虚拟机/服务器，以便迁移到 Azure VMware 解决方案 (AVS)。 AVS 是一项托管服务，可用于在 Azure 中运行 VMware 平台。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
- 基于服务器元数据和配置信息运行评估。
- 基于性能数据运行评估。

> [!NOTE]
> 教程显示尝试方案的最快路径，并尽可能使用默认选项。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。



## <a name="prerequisites"></a>必备条件

在按照本教程来评估要迁移到 AVS 的服务器之前，请确保已发现要评估的服务器：

- 若要使用 Azure Migrate 设备发现服务器，请[遵循本教程](tutorial-discover-vmware.md)。 
- 若要使用导入的 CSV 文件发现服务器，请[遵循本教程](tutorial-discover-import.md)。


## <a name="decide-which-assessment-to-run"></a>确定要运行的评估


确定在运行评估时，所使用的大小调整标准是基于按原样在本地收集的服务器配置数据/元数据，还是基于动态的性能数据。

**评估** | **详细信息** | 建议
--- | --- | ---
**按本地原样** | 基于服务器配置数据/元数据进行评估。  | AVS 中建议的节点大小基于本地 VM/服务器大小，以及在评估中针对节点类型、存储类型和允许的故障数设置所指定的设置。
**基于性能** | 基于收集的动态性能数据进行评估。 | AVS 中建议的节点大小基于 CPU 和内存利用率数据，以及在评估中针对节点类型、存储类型和允许的故障数设置所指定的设置。

> [!NOTE]
> Azure VMware 解决方案 (AVS) 评估只能为 VMware VM/服务器创建。

## <a name="run-an-assessment"></a>运行评估

按如下述运行评估：

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
    - 在 **套餐** 中，显示注册的[Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 评估会估计该套餐的费用。
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

## <a name="review-an-assessment"></a>查看评估

AVS 评估描述：

- AVS 就绪情况：本地服务器是否适合迁移到 Azure VMware 解决方案 (AVS)。
- AVS 节点数：运行服务器所需的 AVS 节点的估计数目。
- 跨 AVS 节点的利用率：跨所有节点的预计的 CPU、内存和存储利用率。
    - 利用率包括以下群集管理开销中的前期因素，例如 vCenter Server、NSX Manager（大型）、NSX Edge，如果部署了 HCX，则 HCX Manager 和 IX 设备将消耗约 44 个 vCPU（11 个 CPU），75GB RAM 和 722GB 存储，然后再进行压缩和重复数据删除。 
- 每月成本估算：运行本地服务器的所有 Azure VMware 解决方案 (AVS) 节点的每月估算成本。

## <a name="view-an-assessment"></a>查看评估

若要查看评估，请执行以下操作：

1. 在 **Windows、Linux 和 SQL Server** > **Azure Migrate：发现和评估** 中，单击 ** Azure VMware 解决方案** 旁边的数字。

1. 在“评估”中，选择某项评估将其打开。 例如（估算和费用仅用于示例）： 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="AVS 评估摘要":::

1. 查看评估摘要。 你还可以编辑评估属性，或重新计算评估。
 

### <a name="review-readiness"></a>查看就绪情况

1. 单击“Azure 迁移就绪性”。
2. 在 **Azure 迁移就绪性** 中，查看迁移就绪性状态。

    - **AVS 已就绪**：服务器可以照原样迁移到 Azure AVS，无需任何更改。 服务器将在 AVS 中启动，并提供完整的 AVS 支持。
    - **准备就绪但存在以下状况**：服务器可能出现与当前 vSphere 版本的兼容性问题。 在 AVS 中获得全部功能之前，可能需要先安装 VMware 工具或其他设置。
    - **AVS 未就绪**：VM 不会在 AVS 中启动。 例如，如果本地 VMware 服务器附加了外部设备（如 CD-ROM），并且您正在使用 VMware VMotion，则 VMotion 操作会失败。
 - **就绪性未知**：由于从本地环境收集的元数据不足，Azure Migrate 无法确定服务器就绪情况。

3. 查看建议的工具。

    - VMware HCX 或 Enterprise：对于 VMware 服务器，若要将本地工作负载迁移到 Azure VMware 解决方案 (AVS) 私有云，建议使用 VMware 混合云扩展 (HCX) 解决方案作为迁移工具。 了解详情。
    - 未知：对于通过 CSV 文件导入的服务器，默认迁移工具是未知的。 但对于 VMware 服务器，建议使用 VMware 混合云扩展 (HCX) 解决方案。
4. 单击某种“AVS 迁移就绪性”状态。 可以查看服务器就绪性详细信息，并深入查看服务器详细信息，包括计算、存储和网络设置。

### <a name="review-cost-estimates"></a>查看成本估算

评估摘要显示估算出来的在 Azure 中运行服务器的计算和存储成本。 

1. 查看每月总成本。 将聚合评估的组中所有服务器的成本。

    - 成本估算基于所需的 AVS 节点数，并考虑所有服务器的总资源要求。
    - 由于 AVS 是按节点定价的，因此总成本不含计算成本和存储成本。
    - 成本估算适用于在 AVS 上运行的本地服务器。 AVS 评估不会考虑 PaaS 或 SaaS 成本。

2. 查看每月存储估算。 此视图显示评估的组的聚合存储成本，按不同类型的存储磁盘划分。 
3. 可以深入查看特定服务器的成本详细信息。

### <a name="review-confidence-rating"></a>查看置信度分级

服务器评估为基于性能的评估分配置信度评级。 评级从一星（最低）到五星（最高）。

![置信度分级](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

置信度评级用于判断评估中大小建议的可靠性。 此评级基于对评估进行计算时所需数据点的可用性。

> [!NOTE]
> 如果基于 CSV 文件创建评估，则不会分配置信度评级。

置信度评级如下所示。

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星

[详细了解](concepts-assessment-calculation.md#confidence-ratings-performance-based)置信度评级。

## <a name="next-steps"></a>后续步骤

- 使用[依赖项映射](concepts-dependency-visualization.md)查找服务器依赖项。
- 设置[无代理](how-to-create-group-machine-dependencies-agentless.md)或[基于代理](how-to-create-group-machine-dependencies.md)的依赖项映射。
