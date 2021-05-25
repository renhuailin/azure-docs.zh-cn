---
title: 使用 Azure Migrate 发现和评估工具创建 Azure VM 评估 | Microsoft Docs
description: 介绍如何使用 Azure Migrate 发现和评估工具创建 Azure VM 评估
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: f901fe23d2ff04e7ad9ba920dd90ebab8a39246c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786713"
---
# <a name="create-an-azure-vm-assessment"></a>创建 Azure VM 评估

本文介绍如何使用“Azure Migrate：发现和评估”为 VMware、Hyper-V 或物理/其他云环境中的本地服务器创建 Azure VM 评估。

[Azure Migrate](migrate-services-overview.md) 可帮助你迁移到 Azure。 Azure Migrate 提供了一个中心，用于跟踪 Azure 的本地基础结构、应用程序和数据的发现、评估以及迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。 

## <a name="before-you-start"></a>准备工作

- 确保已[创建](./create-manage-projects.md) Azure Migrate 项目。
- 如果已创建项目，请确保已[添加](how-to-assess.md)“Azure Migrate：发现和评估”工具。
- 若要创建评估，需要设置适用于 [VMware](how-to-set-up-appliance-vmware.md) 和 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 的 Azure Migrate 设备。 该设备用于发现本地服务器，并将元数据和性能数据发送到“Azure Migrate：发现和评估”。 [了解详细信息](migrate-appliance.md)。


## <a name="azure-vm-assessment-overview"></a>Azure VM 评估概述
有两种类型的调整大小条件可用于使用“Azure Migrate：发现和评估”创建 Azure VM 评估。

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据的评估 | **建议的 VM 大小**：基于 CPU 和内存利用率数据。<br/><br/> **建议的磁盘类型(标准或高级托管磁盘)** ：基于本地磁盘的 IOPS 和吞吐量。
**本地** | 基于本地大小的评估。 | **建议的 VM 大小**：基于本地 VM 大小<br/><br> **建议的磁盘类型**：基于为评估选择的存储类型设置。

[详细了解](concepts-assessment-calculation.md)评估。

## <a name="run-an-assessment"></a>运行评估

按如下述运行评估：

1. 在“概述”页 >“Windows、Linux 和 SQL Server”中，单击“评估并迁移服务器”。  

   ![“评估和迁移服务器”按钮的位置](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. 在 **Azure Migrate：发现和评估** 中，单击 **评估** 并选择 **Azure VM**

    ![“评估”按钮的位置](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. 在“评估服务器” > “评估类型”中 
4. 在“发现源”中：

    - 如果使用设备发现了服务器，请选择 **从 Azure Migrate 设备中发现的服务器**。
    - 如果使用导入的 CSV 文件发现了服务器，请选择 **导入的服务器**。 
    
1. 单击“编辑”查看评估属性。

    ![用于查看评估属性的“查看全部”按钮的位置](./media/tutorial-assess-vmware-azure-vm/assessment-name.png)

1. 在“评估属性” > “目标属性”中 ：
    - 在“目标位置”中，选择要迁移到的 Azure 区域。
        - 大小和成本建议基于你指定的位置。 将目标位置从默认位置更改后，系统将提示你指定“预留实例”和“VM 序列” 。
        - 在 Azure 政府中，你可以以[这些区域](migrate-support-matrix.md#supported-geographies-azure-government)中的评估为目标
    - 在“存储类型”中，
        - 如果要在评估中使用基于性能的数据，请为 Azure Migrate 选择“自动”，以根据磁盘 IOPS 和吞吐量推荐存储类型。
        - 或者，选择在迁移 VM 时要使用的存储类型。
    - 在“预留实例”中，指定在迁移 VM 时是否要使用 VM 的“预留实例”。
        - 如果选择使用预留实例，则无法指定“折扣(%)”或“VM 运行时间” 。 
        - [了解详细信息](https://aka.ms/azurereservedinstances)。
 1. 在“VM 大小”中：
     - 在 **大小调整条件** 中，选择进行评估时是基于服务器配置数据/元数据，还是以基于性能的数据为基础。 如果使用性能数据：
        - 在“性能历史记录”中，指示要用于评估的数据持续时间
        - 在“百分位使用率”中，指定要用于性能示例的百分位数值。 
    - 在“VM 系列”中，指定要列入考虑的 Azure VM 系列。
        - 如果使用基于性能的评估，Azure Migrate 会为你建议一个值。
        - 根据需要调整设置。 例如，如果不需要将生产环境迁移到 Azure 中的 A 系列 VM，可以从系列的列表中排除 A 系列。
    - 在“舒适因子”中，指明要在评估过程中使用的缓冲区。 此帐户用于解决季节性使用情况、短期性能历史记录，以及未来使用量可能会增加等问题。 例如，如果使用舒适因子 2：
    
        **组件** | **有效利用率** | **添加舒适因子 (2.0)**
        --- | --- | ---
        核心数 | 2  | 4
        内存 | 8 GB | 16 GB
   
1. 在“定价”中：
    - 在“产品/服务”中，如果已注册，请指定 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 评估会估计该产品/服务的费用。
    - 在“货币”中，为帐户选择计费货币。
    - 在“折扣 (%)”中，添加基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。
    - 在“VM 运行时间”中，指定 VM 将运行的持续时间（每月天数/每天小时数）。
        - 这对于无法连续运行的 Azure VM 非常有用。
        - 成本估算基于指定的持续时间。
        - 默认为“每月 31 天/每天 24 小时”。
    - 在“EA 订阅”中，指定是否将企业协议 (EA) 订阅折扣考虑在内以进行成本估算。 
    - 在“Azure 混合权益”中，指定是否已有 Windows Server 许可证。 如果已有许可证，并且许可证具有 Windows Server 订阅的有效软件保障，则在将许可证引入 Azure 时，可以申请 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。

1. 如有更改，请单击“保存”。

    ![评估属性](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. 在“评估服务器”中，单击“下一个” 。

1. 在“选择要评估的服务器” > “评估名称”中，指定评估的名称 。 

1. 在“选择或创建组”中，选择“新建”并指定组名称 。 
    
     ![将 VM 添加到组](./media/tutorial-assess-vmware-azure-vm/assess-group.png)


1. 选择设备，然后选择要添加到组的 VM。 然后单击“下一步”。


1. 在“审阅 + 创建评估”中，查看评估详细信息，然后单击“创建评估”以创建组并运行评估 。

1. 创建评估后，在 **服务器** > **Azure Migrate：发现和评估** > **评估** 中查看。

1. 单击“导出评估”，将评估下载为 Excel 文件。
    > [!NOTE]
    > 对于基于性能的评估，建议在开始发现后至少等待一天，然后再创建评估。 这为收集具有较高置信度的性能数据提供了时间。 理想情况下，在开始发现后，等待指定的性能持续时间（日/周/月）进行高置信度评级。

## <a name="review-an-azure-vm-assessment"></a>查看 Azure VM 评估

Azure VM 评估说明：

- **Azure 迁移就绪性**：服务器是否适合迁移到 Azure。
- **每月成本估算**：在 Azure 中运行 VM 的估算每月计算和存储成本。
- **每月存储成本估算**：迁移后的磁盘存储估算成本。

### <a name="view-an-azure-vm-assessment"></a>查看 Azure VM 评估

1. 在“Windows、Linux 和 SQL Server” > “Azure Migrate：发现和评估”中，单击“Azure VM 评估”旁边的数字。  
2. 在“评估”中，选择某项评估将其打开。 例如（估算和费用仅用于示例）： 

    ![评估摘要](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>查看 Azure 迁移就绪性

1. 在“Azure 迁移就绪性”中，验证服务器是否已准备好迁移到 Azure。
2. 查看服务器状态：
    - **已做好 Azure 迁移准备**：对于评估中的 VM，Azure Migrate 将建议 VM 大小并显示估算成本。
    - **准备就绪但存在以下状况**：显示问题和建议的补救措施。
    - **尚未做好 Azure 迁移准备**：显示问题和建议的补救措施。
    - **就绪性未知**：当数据可用性问题导致 Azure Migrate 无法评估就绪性时使用。

3. 单击某种“Azure 迁移就绪性”状态。 可以查看服务器就绪性详细信息，并深入查看服务器详细信息，包括计算、存储和网络设置。



### <a name="review-cost-details"></a>查看成本详细信息

此视图显示在 Azure 中运行 VM 的估算计算和存储成本。

1. 查看每月计算和存储成本。 将聚合评估的组中所有服务器的成本。

    - 成本估算基于服务器及其磁盘和属性的大小建议。
    - 将显示估算的每月计算和存储成本。
    - 估算成本适用于作为 IaaS VM 运行的本地服务器。 Azure VM 评估不会考虑 PaaS 或 SaaS 成本。

2. 可以查看每月存储估算成本。 此视图显示评估的组的聚合存储成本，按不同类型的存储磁盘划分。
3. 可以深入查看特定服务器的详细信息。


### <a name="review-confidence-rating"></a>查看置信度分级

运行基于性能的评估时，会将一个置信度分级分配到评估。

![置信度分级](./media/how-to-create-assessment/confidence-rating.png)

- 将授予从 1 星（最低）到 5 星（最高）的分级。
- 置信度分级可帮助你判断评估所提供的大小建议的可靠性。
- 置信度分级基于计算评估所需的数据点的可用性。

评估的置信度分级如下。

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星




## <a name="next-steps"></a>后续步骤

- 了解如何使用[依赖项映射](how-to-create-group-machine-dependencies.md)创建高可信度组。
- [详细了解](concepts-assessment-calculation.md)如何计算评估。