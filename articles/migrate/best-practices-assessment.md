---
title: Azure Migrate 发现和评估工具中的评估最佳做法
description: 使用 Azure Migrate 发现和评估工具创建评估的提示。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/28/2021
ms.openlocfilehash: cf8f4adabf07ec349c2b5890d0f3a6cfd6bbb192
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732660"
---
# <a name="best-practices-for-creating-assessments"></a>创建评估的最佳做法

[Azure Migrate](./migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。

本文汇总了使用 Azure Migrate 发现和评估工具创建评估时的最佳做法。

使用“Azure Migrate：发现和评估”工具创建的评估是数据的时间点快照。 使用“Azure Migrate：发现和评估”可以创建四种类型的评估：

**评估类型** | **详细信息**
--- | ---
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 使用这种评估类型，可以对 [VMware](how-to-set-up-appliance-vmware.md) 和 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 环境中的本地服务器以及要迁移到 Azure 的[物理服务器](how-to-set-up-appliance-physical.md)进行评估。 [了解详细信息](concepts-assessment-calculation.md)
**Azure SQL** | 将本地 SQL Server 从 VMware 环境迁移到 Azure SQL 数据库或 Azure SQL 托管实例的评估。 [了解详细信息](concepts-azure-sql-assessment-calculation.md)
**Azure 应用服务** | 评估将运行在 IIS Web 服务器上的本地 ASP.NET Web 应用从 VMware 环境迁移到 Azure 应用服务。 [了解详细信息](concepts-azure-webapps-assessment-calculation.md)
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> 如果“发现和评估”工具上的 Azure VM 或 AVS 评估数不正确，请单击评估总数以导航到所有评估并重新计算 Azure VM 或 AVS 评估数。 然后，“发现和评估”工具就会显示该评估类型的正确计数。 

### <a name="sizing-criteria"></a>调整大小标准
Azure Migrate 评估中的调整大小标准选项：

**调整大小标准** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | **Azure VM 评估**：VM 大小建议基于 CPU 和内存利用率数据。<br/><br/> 磁盘类型建议（标准 HDD/SSD、高级托管磁盘或超级磁盘）基于本地磁盘的 IOPS 和吞吐量。<br/><br/>Azure SQL 评估：Azure SQL 配置基于 SQL 实例和数据库的性能数据，其中包括：CPU 利用率、内存利用率、IOPS（数据和日志文件）、吞吐量和 IO 操作延迟<br/><br/>**Azure VMware 解决方案 (AVS) 评估**：AVS 节点建议基于 CPU 和内存利用率数据。
**按本地原样** | 不使用性能数据来提出建议的评估。 | **Azure VM 评估**：VM 大小建议基于本地 VM 大小<br/><br> 建议的磁盘类型基于在存储类型设置中选择要评估的内容。<br/><br/> Azure 应用服务评估：评估建议基于本地 Web 应用配置数据。<br/><br/> **Azure VMware 解决方案 (AVS) 评估**：AVS 节点建议基于本地 VM 大小。

#### <a name="example"></a>示例
例如，如果本地 VM 的四个内核的利用率为 20%，8 GB 内存的利用率为 10%，Azure VM 评估将如下所示：

- **基于性能的评估**：
    - 确定有效核心和基于核心的内存 (4 x 0.20 = 0.8)，以及内存 (8 GB x 0.10 = 0.8) 利用率。
    - 应用评估属性中指定的舒适因子（假设 1.3x）以获取用于调整大小的值。 
    - 建议使用 Azure 中能够支持 ~1.04 核心 (0.8 x 1.3) 和 ~1.04 GB (0.8 x 1.3) 内存的最接近的 VM 大小。

- **按原样（作为本地）评估**：
    -  建议使用具有四个核心的 VM；8 GB 内存。


## <a name="best-practices-for-creating-assessments"></a>创建评估的最佳做法

Azure Migrate 设备会持续分析本地环境，并将元数据和性能数据发送到 Azure。 遵循以下最佳做法来评估使用设备发现的服务器：

- **创建按原样评估**：一旦服务器显示在 Azure Migrate 门户中，即可立即创建按原样评估。 不能使用“作为本地”调整大小标准创建 Azure SQL 评估。 Azure 应用服务评估默认为“作为本地”。
- **创建基于性能的评估**：设置发现后，建议等待至少一天，再运行基于性能的评估：
    - 收集性能数据需要时间。 等待至少一天可确保在运行评估之前有足够的性能数据点。
    - 运行基于性能的评估时，请确保分析评估期间的环境。 例如，如果创建评估时将性能持续时间设置为一周，则在开始发现之后，需要至少等待一周，才会收集所有数据点。 如果不这样做，评估将不会获得五星评级。
- **重新计算评估**：由于评估是时间点快照，因此不会自动使用最新数据更新。 若要使用最新数据更新评估，需要进行重新计算。

遵循以下最佳做法，评估通过 .CSV 文件导入到 Azure Migrate 的服务器：

- **创建按原样评估**：一旦服务器显示在 Azure Migrate 门户中，即可立即创建按原样评估。
- **创建基于性能的评估**：这有助于更好地估算成本，尤其是在本地过度预配服务器容量时。 但是，基于性能的评估的准确度取决于为服务器指定的性能数据。 
- **重新计算评估**：由于评估是时间点快照，因此不会自动使用最新数据更新。 若要使用最新导入的数据更新评估，需要进行重新计算。
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>适用于 AVS 评估的 FTT 调整大小参数

AVS 中使用的存储引擎为 vSAN。 vSAN 存储策略定义了虚拟机的存储要求。 这些策略保证了 VM 所需的服务级别，因为它们可确定如何将存储分配给 VM。 以下是可用的 FTT-Raid 组合： 

**允许的故障数 (FTT)** | **RAID 配置** | **需要的最少主机数** | **大小调整注意事项**
--- | --- | --- | ---
1 | RAID-1（镜像） | 3 | 100GB VM 将使用 200GB。
1 | RAID-5（擦除编码） | 4 | 100GB VM 将使用 133.33 GB
2 | RAID-1（镜像） | 5 | 100GB VM 将使用 300GB。
2 | RAID-6（擦除编码） | 6 | 100GB VM 将使用 150GB。
3 | RAID-1（镜像） | 7 | 100GB VM 将使用 400GB。

## <a name="best-practices-for-confidence-ratings"></a>可信度评级的最佳做法

运行基于性能的评估时，对评估进行 1 星（最低）到 5 星（最高）的可信度评级。 有效使用可信度评级：

- Azure VM 和 AVS 评估需要：
    - 每台服务器的 CPU 和内存利用率数据
    - 每个附加到本地服务器的磁盘的读/写 IOPS/吞吐量数据
    - 每个附加到服务器的网络适配器的网络流入/流出量信息。
     
- Azure SQL 评估需要评估的 SQL 实例和数据库的性能数据，其中包括：
    - CPU 和内存利用率数据
    - 数据和日志文件的读/写 IOPS/吞吐量数据
    - IO 操作延迟

根据所选持续时间的可用数据点百分比，下表提供了评估的可信度评级。

   **数据点可用性** | **置信度分级**
   --- | ---
   0%-20% | 1 星
   21%-40% | 2 星
   41%-60% | 3 星
   61%-80% | 4 星
   81%-100% | 5 星

## <a name="common-assessment-issues"></a>常见评估问题

下面介绍了如何解决影响评估的一些常见环境问题。

###  <a name="out-of-sync-assessments"></a>不同步评估

如果在创建评估后在组中添加或删除服务器，创建的评估将标记为“不同步”。请再次运行评估（“重新计算”）以反映组更改。

### <a name="outdated-assessments"></a>过时的评估

#### <a name="azure-vm-assessment-and-avs-assessment"></a>Azure VM 评估和 AVS 评估

如果对已评估的组中的本地服务器进行了更改，则评估将标记为“过时”。 可能会由于在以下属性中进行了一项或多项更改而将评估标记为“过时”：

- 处理器核心数
- 分配的内存
- 启动类型或固件
- 操作系统名称、版本和体系结构
- 磁盘数目
- 网络适配器数目
- 磁盘大小更改（分配的 GB）
- NIC 属性更新。 例如：更改 Mac 地址、添加 IP 地址等。

再次运行评估（“重新计算”）以反映更改。

#### <a name="azure-sql-assessment"></a>Azure SQL 评估

如果对已评估的组中的本地 SQL 实例和数据库进行了更改，则评估将标记为“过时”。 由于以下一个或多个原因而将评估标记为“过时”：

- 已在服务器中添加或删除 SQL 实例
- 已在 SQL 实例中添加或删除 SQL 数据库
- SQL 实例中的总数据库大小更改超过 20%
- 处理器核心数改变
- 分配的内存改变
  
    再次运行评估（“重新计算”）以反映更改。

#### <a name="azure-app-service-assessment"></a>Azure 应用服务评估

如果对已评估的组中的本地 Web 应用进行了更改，则评估将标记为“过时”。 由于以下一个或多个原因而将评估标记为“过时”：

- 已在服务器中添加或删除 Web 应用
- 对现有 Web 应用的配置做出更改。
  
    再次运行评估（“重新计算”）以反映更改。

### <a name="low-confidence-rating"></a>低可信度评级

由于以下原因，评估时并非所有数据点都可用：

- 在创建评估的过程中，你没有对环境进行分析。 例如，如果创建性能持续时间设置为一周的评估，则在对所有数据点启用发现之后，需要等待至少一周才能收集。 如果无法等待这么久，请将性能持续时间缩短，并“重新计算”评估。
 
- 评估无法在评估期内收集部分或全部服务器的性能数据。 若要获得高置信度分级，请确保： 
    - 服务器在评估期间处于开机状态
    - 允许端口 443 上的出站连接
    - 为 Hyper-V 服务器启用了动态内存 
    - Azure Migrate 中代理的连接状态为“已连接”，并请检查上一个检测信号
    - 对于 Azure SQL 评估，“已发现的 SQL 实例”边栏选项卡中所有 SQL 实例的 Azure Migrate 连接状态均为“已连接”

    请重新计算评估以反映置信度评级的最新更改。

- 对于 Azure VM 和 AVS 评估，启动发现后，创建了几乎很少的服务器。 例如，如果针对最后一个月的性能历史记录创建评估，但仅仅在一周前在环境中创建了几乎很少的服务器。 在这种情况下，整个评估过程中将不会用到新服务器的性能数据，而且可信度评级会较低。

- 对于 Azure SQL 评估，很少有 SQL 实例或数据库是在发现开始后创建的。 例如，如果针对最后一个月的性能历史记录创建评估，但仅仅在一周前在环境中创建了几乎很少的 SQL 实例或数据库。 在这种情况下，整个评估过程中将不会用到新服务器的性能数据，而且可信度评级会较低。

### <a name="migration-tool-guidance-for-avs-assessments"></a>用于 AVS 评估的迁移工具指南

在 Azure VMware 解决方案 (AVS) 评估的 Azure 迁移就绪性报告中，可以看到以下推荐工具： 
- VMware HCX 或 Enterprise：对于 VMware 服务器，若要将本地工作负载迁移到 Azure VMware 解决方案 (AVS) 私有云，建议使用 VMware 混合云扩展 (HCX) 解决方案作为迁移工具。 [了解详细信息](../azure-vmware/install-vmware-hcx.md)。
- **未知**：对于通过 CSV 文件导入的服务器，默认迁移工具是未知的。 但对于 VMware 环境中的服务器，建议使用 VMWare 混合云扩展 (HCX) 解决方案。


## <a name="next-steps"></a>后续步骤

- [了解](concepts-assessment-calculation.md)如何计算评估。
- [了解](how-to-modify-assessment.md)如何自定义评估。
