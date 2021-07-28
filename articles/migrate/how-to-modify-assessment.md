---
title: 自定义 Azure Migrate 的评估 | Microsoft Docs
description: 介绍如何自定义使用 Azure Migrate 创建的评估
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: e2fc240e04ec375f95bef74e3fd44381ff8cceb6
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108135736"
---
# <a name="customize-an-assessment"></a>自定义评估

本文介绍如何自定义 Azure Migrate 发现和评估工具创建的评估。

[Azure Migrate](migrate-services-overview.md) 提供一个中心用于跟踪本地应用、工作负荷与私有云/公有云 VM 的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

可以使用 Azure Migrate 发现和评估工具为准备迁移到 Azure 的本地 VMware VM 和 Hyper-V VM 创建评估。 发现和评估工具可以评估要迁移到 Azure IaaS 虚拟机和 Azure VMware 解决方案 (AVS) 的本地服务器。 

## <a name="about-assessments"></a>关于评估

使用发现和评估工具创建的评估是数据的时间点快照。 使用“Azure Migrate: 发现和评估”可以创建两种类型的评估。

**评估类型** | **详细信息**
--- | --- 
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md) 和[物理服务器](how-to-set-up-appliance-physical.md)。(concepts-assessment-calculation.md)
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。[了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

Azure Migrate 评估中的大小调整条件选项：

**调整大小标准** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | **Azure VM 评估**：VM 大小建议基于 CPU 和内存利用率数据。<br/><br/> 磁盘类型建议（标准 HDD/SSD 或高级托管磁盘）基于本地磁盘的 IOPS 和吞吐量。<br/><br/>**Azure SQL 评估**：Azure SQL 配置基于 SQL 实例和数据库的性能数据，其中包括：CPU 利用率、内存利用率、IOPS（数据和日志文件）、吞吐量和 IO 操作延迟<br/><br/>**Azure VMware 解决方案 (AVS) 评估**：AVS 节点建议基于 CPU 和内存利用率数据。
**按本地原样** | 不使用性能数据来提出建议的评估。 | **Azure VM 评估**：VM 大小建议基于本地 VM 大小<br/><br> 建议的磁盘类型基于在存储类型设置中选择要评估的内容。<br/><br/> **Azure VMware 解决方案 (AVS) 评估**：AVS 节点建议基于本地 VM 大小。

## <a name="how-is-an-assessment-done"></a>评估是如何执行的？

“Azure Migrate 发现和评估”中的评估是分三个阶段执行的。 评估的第一步是适用性分析，第二步是大小估计，最后一步是每月成本估计。 如果一台计算机通过了前一个阶段，则只能按顺序进入下一个阶段。 例如，如果一台计算机未通过 Azure 适用性检查，将被标记为不适合迁移到 Azure，并且不会进行大小调整和成本估算。 [了解详细信息。](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM 评估包括哪些内容？

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 位置。<br/> Azure VM 评估目前支持以下目标区域：澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、印度中部、美国中部、中国东部、中国北部、东亚、美国东部、美国东部 2、德国中部、德国东北部、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、东南亚、印度南部、英国南部、英国西部、US Gov 亚利桑那州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、美国中西部、西欧、印度西部、美国西部和美国西部 2。
**存储类型** | 可以使用此属性在 Azure 中指定要移到的磁盘类型。<br/><br/> 对于与本地相同的大小调整，可将目标存储类型指定为“高级托管磁盘”、“标准 SSD 托管磁盘”或“标准 HDD 托管磁盘”。 对于基于性能的大小调整，可将目标磁盘类型指定为“自动”、“高级托管磁盘”、“标准 HDD 托管磁盘”或“标准 SSD 托管磁盘”。<br/><br/> 将存储类型指定为自动时，将根据磁盘的性能数据（IOPS 和吞吐量）完成磁盘建议。 如果将存储类型指定为高级/标准磁盘，则评估将在选定的存储类型中推荐一个磁盘 SKU。 如果你要实现 99.9% 的单实例 VM SLA，可可能需要将存储类型指定为“高级托管磁盘”。 这可确保推荐评估中的所有磁盘作为高级托管磁盘。 Azure
预留实例 (RI) | 如果你在 Azure 中具有[预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，此属性可帮助你指定在评估中进行成本估算时要考虑到 RI 折扣。 目前仅支持 Azure Migrate 中的“即用即付”产品/服务。
**“大小调整”条件** | 适当调整 Azure 的 VM 大小时要使用的条件。 可以执行基于性能的大小调整，或者将 VM 大小调整为与本地相同，而不考虑性能历史记录 。
**性能历史记录** | 评估计算机性能数据时要考虑的持续时间。 仅当大小调整条件为“基于性能”时，此属性才适用。
**百分位使用率** | 进行适当大小调整时要考虑的性能样本集的百分位值。 仅当大小调整条件为“基于性能”时，此属性才适用。
**VM 系列** |     可以指定想要考虑进行适当大小调整的 VM 系列。 例如，如果你不打算将生产环境迁移到 Azure 中的 A 系列 VM，可以从列表或系列中排除 A 系列，则只在所选系列中完成适当大小调整。
**舒适因子** | Azure VM 评估在评估期间会考虑缓冲区（舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。
**产品/服务** | 加入的 [Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。
**货币** | 计费货币。
**折扣 (%)** | 基于 Azure 套餐获得的任何特定订阅的折扣。<br/> 默认设置是 0%。
**VM 运行时间** | 如果 VM 不会在 Azure 中全天候运行，则可指定运行持续时间（每月的天数和每天的小时数），然后系统就会进行相应的成本估算。<br/> 默认值为“每月 31 天和每天 24 小时”。
**Azure 混合权益** | 指定是否具有软件保证以及是否有资格享受 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 默认值为“是”。

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 解决方案 (AVS) 评估包括哪些内容？

AVS 评估包括以下内容：


| **属性** | **详细信息** |
| - | - |
| **目标位置** | 指定要迁移到的 AVS 私有云位置。<br/><br/> AVS 评估目前支持以下目标区域：美国东部、西欧、美国西部。 |
| **存储类型** | 指定要在 AVS 中使用的存储引擎。<br/><br/> 请注意，AVS 评估仅支持使用 vSAN 作为默认存储类型。 |
预留实例 (RI) | 此属性可帮助你在 AVS 中指定预留实例。 AVS 节点目前不支持 RI。 |
**节点类型** | 指定用于映射本地 VM 的 [AVS 节点类型](../azure-vmware/concepts-private-clouds-clusters.md)。 请注意，默认节点类型为 AV36。 <br/><br/> Azure Migrate 将为迁移到 AVS 的 VM 建议所需的节点数。 |
FTT 设置，RAID 级别 | 指定适用的“容许的故障数”和“RAID”组合。 所选 FTT 选项与本地 VM 磁盘要求共同决定 AVS 中所需的总 vSAN 存储。 |
**“大小调整”条件** | 设置适当调整 AVS 的 VM 大小时要使用的条件。 可以选择基于性能的大小调整或与本地相同的大小调整，而不考虑性能历史记录 。 |
**性能历史记录** | 设置评估计算机性能数据时要考虑的持续时间。 仅当大小调整条件基于性能时，此属性才适用。 |
**百分位使用率** | 指定进行适当大小调整时要考虑的性能样本集的百分位值。 仅当大小调整基于性能时，此属性才适用。|
**舒适因子** | Azure Migrate 在评估期间会考虑到缓冲（舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 |
**产品/服务** | 显示已注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。|
**货币** | 显示帐户的计费货币。 |
**折扣 (%)** | 列出基于 Azure 产品/服务获得的任何订阅特定折扣。 默认设置是 0%。 |
**Azure 混合权益** | 指定是否具有软件保证以及是否有资格享受 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 尽管这不会影响 Azure VMware 解决方案定价（因为定价基于节点），但客户仍可使用 Azure 混合权益在 AVS 中应用（基于 Microsoft 的）本地 OS 许可证。 其他软件 OS 供应商必须提供其自己的许可条款，例如 RHEL。 |
vCPU 过度订阅 | 指定 AVS 节点中与 1 个物理核心关联的虚拟核心数比率。 计算中的默认值为 AVS 中的 4 个 vCPU 比 1 个物理核心。 <br/><br/> API 用户可将此值设置为整数。 请注意，大于 4:1 的 vCPU 超额订阅可能会导致性能降低，但可用于 Web 服务器类型的工作负载。 |

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
产品/服务/许可计划 | 注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 目前只能选择“即用即付”或“即用即付开发/测试”。 请注意，可以通过在“即用即付”产品/服务的基础上应用预留容量和 Azure 混合权益来获得额外的折扣。
**服务层** | 最合适的服务层级选项，用于满足迁移到 Azure SQL 数据库和/或 Azure SQL 托管实例的业务需求：<br/><br/>如果希望 Azure Migrate 推荐最适合服务器的服务层级，请选择“推荐”。 服务层级可以是“常规用途”或“业务关键”。 <br/><br/> 如果希望 Azure SQL 配置专用于预算导向的工作负荷，请选择“常规用途”。 [了解详细信息](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> 如果希望 Azure SQL 配置专用于能够极其灵活地应对故障且能实现快速故障转移的低延迟工作负荷，请选择“业务关键”。 [了解详细信息](../azure-sql/database/service-tier-business-critical.md)
**货币** | 帐户的计费货币。
**折扣 (%)** | 基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。
**Azure 混合权益** | 指定是否已有 SQL Server 许可证。 <br/><br/> 如果已有许可证，并且许可证具有 SQL Server 订阅的有效软件保障，则在将许可证引入 Azure 时可以申请 Azure 混合权益。

## <a name="edit-assessment-properties"></a>编辑评估属性

若要在创建评估后编辑评估属性，请执行以下操作：

1. 在 Azure Migrate 项目中，单击“服务器”。
2. 在“Azure Migrate: 发现和评估”中，单击评估计数。
3. 在“评估”中单击相关评估，然后单击“编辑属性” 。
5. 根据上表自定义评估属性。
6. 单击“保存”更新评估。


还可以在创建评估时编辑评估属性。


## <a name="next-steps"></a>后续步骤

- [详细了解](concepts-assessment-calculation.md)如何计算 Azure VM 评估。
- [详细了解](concepts-azure-sql-assessment-calculation.md)如何计算 Azure SQL 评估。
- [详细了解](concepts-azure-vmware-solution-assessment-calculation.md)如何计算 Azure AVS 评估。