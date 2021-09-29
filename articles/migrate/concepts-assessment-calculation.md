---
title: Azure Migrate 中的 Azure VM 评估
description: 了解 Azure Migrate 中的评估
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/28/2021
ms.openlocfilehash: 0a5591ddad8410c2cea7018fa2f4d4f13a3afa86
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124735946"
---
# <a name="assessment-overview-migrate-to-azure-vms"></a>评估概述（迁移到 Azure VM）

本文概述了 [Azure Migrate：发现和评估](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)工具中的评估。 该工具可对 VMware 虚拟环境和 Hyper-V 环境中的本地服务器以及要迁移到 Azure 的物理服务器进行评估。

## <a name="whats-an-assessment"></a>什么是评估？

使用发现和评估工具进行评估，衡量将本地服务器迁移到 Azure 的就绪性并预估其影响。

> [!NOTE]
> 在 Azure 政府版中，查看[支持的目标](migrate-support-matrix.md#supported-geographies-azure-government)评估位置。 请注意，评估中的 VM 大小建议将使用专用于政府云区域的 VM 系列。 [深入了解](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) VM 类型的相关信息。

## <a name="types-of-assessments"></a>评估的类型

使用“Azure Migrate：发现和评估”，可以创建三种类型的评估。

***评估类型** | **详细信息**
--- | ---
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 使用这种评估类型，可以对 [VMware](how-to-set-up-appliance-vmware.md) 和 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 环境中的本地服务器以及要迁移到 Azure VM 的[物理服务器](how-to-set-up-appliance-physical.md)进行评估。
**Azure SQL** | 将本地 SQL Server 从 VMware 环境迁移到 Azure SQL 数据库或 Azure SQL 托管实例的评估。
**Azure 应用服务** | 关于将本地 Web 应用从 VMware 环境迁移到 Azure 应用服务的评估。
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> 如果“发现和评估”工具上的 Azure VM 或 AVS 评估数不正确，请单击评估总数以导航到所有评估并重新计算 Azure VM 或 AVS 评估数。 然后，“发现和评估”工具将显示该评估类型的正确计数。 

使用 Azure Migrate 创建的评估是数据的时间点快照。 Azure VM 评估提供了两个调整大小条件选项：

**评估类型** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | VM 大小建议是基于 CPU 和 RAM 使用率数据。<br/><br/> 磁盘类型建议是基于每秒的输入/输出操作 (IOPS) 和本地磁盘的吞吐量。 磁盘类型包括 Azure 标准 HDD、Azure 标准 SSD、Azure 高级磁盘和 Azure 超级磁盘。
**按本地原样** | 不使用性能数据提出建议的评估 | VM 大小建议是基于本地服务器大小。<br/><br> 建议的磁盘类型是基于为评估选择的存储类型。

## <a name="how-do-i-run-an-assessment"></a>如何运行评估？

可通过多种方法运行评估。

- 使用轻型 Azure Migrate 设备收集的服务器元数据来评估服务器。 设备会发现本地服务器。 然后将服务器元数据和性能数据发送到 Azure Migrate。
- 使用以逗号分隔的值 (CSV) 格式导入的服务器元数据来评估服务器。

## <a name="how-do-i-assess-with-the-appliance"></a>如何使用设备进行评估？

如果部署 Azure Migrate 设备来发现本地服务器，请执行以下步骤：

1. 设置 Azure 及适合于 Azure Migrate 运行的本地环境。
1. 第一次评估时，创建一个 Azure 项目并向其中添加“发现和评估”工具。
1. 部署轻型 Azure Migrate 设备。 此设备将持续发现本地服务器，并向 Azure Migrate 发送服务器元数据和性能数据。 将设备部署为 VM 或物理服务器。 无需在要评估的服务器上安装任何软件。

设备开始服务器发现后，可以将想要评估的服务器集中到一个组中，然后使用评估类型“Azure VM”运行组评估。

按照我们有关 [VMware](./tutorial-discover-vmware.md)、[Hyper-V](./tutorial-discover-hyper-v.md) 或[物理服务器](./tutorial-discover-physical.md)的教程操作，尝试这些步骤。

## <a name="how-do-i-assess-with-imported-data"></a>如何使用导入的数据进行评估？

如果要使用 CSV 文件来评估服务器，则不需要设备。 而是执行以下步骤：

1. 设置 Azure 以使用 Azure Migrate
1. 第一次评估时，创建一个 Azure 项目并向其中添加“发现和评估”工具。
1. 下载 CSV 模板并向其中添加服务器数据。
1. 将模板导入 Azure Migrate
1. 发现通过导入添加的服务器后，将其集中到一个组中，然后使用评估类型“Azure VM”运行组评估。

## <a name="what-data-does-the-appliance-collect"></a>设备会收集哪些数据？

如果使用 Azure Migrate 设备进行评估，请了解针对 [VMware](migrate-appliance.md#collected-data---vmware) 和 [Hyper-V](migrate-appliance.md#collected-data---hyper-v) 收集的元数据和性能数据。

## <a name="how-does-the-appliance-calculate-performance-data"></a>设备如何计算性能数据？

如果是使用设备进行发现，则会通过以下步骤为计算设置收集性能数据：

1. 设备收集实时样本点。

    - **VMware VM**：每 20 秒收集一个样本点。
    - **Hyper-V VM**：每 30 秒收集一个样本点。
    - **物理服务器**：每 5 分钟收集一个样本点。

1. 设备将这些样本点组合在一起，为 VMware 和 Hyper-V 服务器每 10 分钟创建一个数据点，为物理服务器每 5 分钟创建一个数据点。 为了创建数据点，设备会从所有样本中选择峰值。 然后，将数据点发送到 Azure。
1. 评估存储上个月的所有 10 分钟数据点。
1. 创建评估时，该评估会确定用于合理调整大小的相应数据点。 根据“性能历史记录”和“百分位使用率”的百分位值进行确定。

    - 例如，性能历史记录是一周，百分位使用率是第 95 百分位，则评估会对上一周的 10 分钟样本点进行排序。 按升序排序，并选取第 95 百分位值进行合理调整大小。
    - 第 95 百分位值可以确保忽略任何离群值，如果选取第 99 百分位，则可能包括这些离群值。
    - 如果希望选择该期间内的峰值使用率，并且不希望错过任何离群值，请为百分位使用率选择第 99 百分位。

1. 此值与舒适因子相乘，就得到了设备收集的这些指标的有效性能使用率数据：

    - CPU 使用率
    - RAM 使用率
    - 磁盘 IOPS（读取和写入）
    - 磁盘吞吐量（读取和写入）
    - 网络吞吐量（传入和传出）

## <a name="how-are-azure-vm-assessments-calculated"></a>如何计算 Azure VM 评估？

该评估使用本地服务器的元数据和性能数据来计算评估。 如果部署 Azure Migrate 设备，则评估使用该设备收集的数据。 但如果运行的是使用 CSV 文件导入的评估，则需要提供用于计算的元数据。

计算分为以下三个阶段：

1. **计算 Azure 迁移就绪性**：评估服务器是否适合迁移到 Azure。
1. **计算调整大小建议**：估算计算、存储和网络调整大小。
1. **计算每月成本**：计算迁移后每月在 Azure 中运行服务器的预估计算和存储成本。

计算按上述顺序进行。 服务器只有通过了前一个阶段，才会进入下一个阶段。 例如，服务器未通过 Azure 就绪性阶段，则会标记为不适合 Azure。 将不会对该服务器执行调整大小和成本计算。

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM 评估有哪些内容？

Azure VM 评估中包括以下内容：

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的位置。 评估目前支持以下目标 Azure 区域：<br/><br/> 澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、印度中部、美国中部、中国东部、中国北部、东亚、美国东部、美国东部 2、德国中部、德国东北部、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、东南亚、印度南部、英国南部、英国西部、US Gov 亚利桑那州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、美国中西部、西欧、印度西部、美国西部和美国西部 2。
目标存储磁盘（按原样调整大小） | 要用于 Azure 中存储的磁盘类型。 <br/><br/> 将目标存储磁盘指定为高级托管、标准 SSD 托管、标准 HDD 托管或超级磁盘类型。
目标存储磁盘（基于性能的调整大小） | 将目标存储磁盘的类型指定为自动、高级托管、标准 HDD 托管、标准 SSD 托管或超级磁盘类型。<br/><br/> 自动：磁盘建议是基于磁盘的性能数据，即 IOPS 和吞吐量。<br/><br/>**高级或标准或超级磁盘**：评估建议选择存储类型中的磁盘 SKU。<br/><br/> 如果希望单实例 VM 服务级别协议 (SLA) 达到 99.9%，请考虑使用高级托管磁盘。 这样可确保将评估中的所有磁盘都推荐为高级托管磁盘。<br/><br/> 如果希望运行数据密集型工作负载，这类工作负载需要高吞吐量、高 IOPS 和高一致性且低延迟的磁盘存储，请考虑使用超级磁盘。<br/><br/> Azure Migrate 仅支持使用托管磁盘进行迁移评估。
**Azure 虚拟机预留实例** | 指定[预留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，以便在评估的成本估算中将它们考虑在内。<br/><br/> 选择“预留实例”时，“折扣 (%)”和“VM 运行时间”属性不适用。<br/><br/> Azure Migrate 当前仅支持对即用即付产品/服务选择“Azure 虚拟机预留实例”。
**调整大小标准** | 用于调整 Azure VM 大小。<br/><br/> 使用按原样调整大小或基于性能的调整大小。
**性能历史记录** | 搭配基于性能的调整大小。 性能历史记录指定了评估性能数据时使用的持续时间。
**百分位使用率** | 搭配基于性能的调整大小。 百分位使用率指定了用于合理调整大小的性能样本的百分位值。
**VM 系列** | 要考虑用于合理调整大小的 Azure VM 系列。 例如，如果不需要将生产环境迁移到 Azure 中的 A 系列 VM，可以从系列的列表中排除 A 系列。
**舒适因子** | 评估过程中使用的缓冲区。 它应用于 VM 的 CPU、RAM、磁盘和网络数据。 此因子用于解决季节性使用情况、短期性能历史记录以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个双核 VM。 而如果舒适因子是 2.0，则结果是一个四核 VM。
**产品/服务** | 注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 评估会估计该产品/服务的费用。
**货币** | 帐户的计费货币。
**折扣 (%)** | 基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。
**VM 运行时间** | 不连续运行的 Azure VM 每月的持续时间（以天为单位）和每天的持续时间（以小时为单位）。 成本估算就是基于该持续时间。<br/><br/> 默认值是每月 31 天和每天 24 小时。
**Azure 混合权益** | 指定是否具有软件保证以及是否有资格享受 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 如果设置的默认值为“是”，则对于 Windows VM，应考虑非 Windows 操作系统的 Azure 价格。
**EA 订阅** | 指定将将企业协议 (EA) 订阅用于成本估算。 考虑适用于该订阅的折扣。 <br/><br/> 将预留实例、折扣 (%) 和 VM 运行时间属性保留默认设置。


关于使用 Azure Migrate 创建评估，请[查看最佳做法](best-practices-assessment.md)。

## <a name="calculate-readiness"></a>计算就绪性

并非所有服务器都适合在 Azure 中运行。 Azure VM 评估会对所有本地服务器进行评估，并为其分配一个就绪性类别。

- Azure 迁移就绪：服务器可以照原样迁移到 Azure，无需任何更改。 它将在 Azure 中启动并具有完整的 Azure 支持。
- Azure 迁移有条件就绪：服务器可能在 Azure 中启动，但可能没有完整的 Azure 支持。 例如，Azure 不支持运行旧版 Windows Server 的服务器。 在将这些服务器迁移到 Azure 之前，必须谨慎。 若要解决任何就绪性问题，请遵循评估所建议的修正指南。
- Azure 迁移未就绪：服务器不会在 Azure 中启动。 例如，如果本地服务器的磁盘存储超过 64 TB，Azure 将无法托管该服务器。 请遵循修正指南，在迁移之前解决问题。
- 就绪性未知：由于元数据不足，Azure Migrate 无法确定计算机就绪性。

为了计算就绪性，评估会评审下表中汇总的服务器属性和操作系统设置。

### <a name="server-properties"></a>服务器属性

对于 Azure VM 评估，评估会评审本地 VM 的以下属性，以确定它是否可以在 Azure VM 上运行。

属性 | 详细信息 | Azure 迁移就绪性状态
--- | --- | ---
**启动类型** | Azure 支持启动类型为 BIOS（而非 UEFI）的 VM。 | 如果启动类型为 UEFI，则状态为有条件就绪
**核心数** | 每个服务器的内核数不得超过 128 个，这是 Azure VM 支持的最大数量。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内核数以进行比较。 如果评估设置中指定了舒适因子，则将已利用的内核数乘以此舒适因子。<br/><br/> 如果没有任何性能历史记录，Azure Migrate 将使用已分配的内核数乘以舒适因子。 | 如果内核数在限制范围内，则状态为就绪
**RAM** | 每个服务器的 RAM 不得超过 3,892 GB，这是 Azure M 系列 Standard_M128m&nbsp;<sup>2</sup> VM 所支持的最大存储量。 [了解详细信息](../virtual-machines/sizes.md)。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑比较已利用的 RAM。 如果指定了舒适因子，则将已利用的 RAM 乘以此舒适因子。<br/><br/> 如果没有历史记录，则使用已分配的 RAM 乘以舒适因子。<br/><br/> | 如果 RAM 量在限制范围内，则状态为就绪
**存储磁盘** | 分配的磁盘大小不得超过 64 TB。<br/><br/> 连接到服务器的磁盘（包括操作系统磁盘）数量不得超过 65 个。 | 如果磁盘大小和数量处于限制范围内，则状态为就绪
**联网** | 连接到服务器的网络接口 (NIC) 数量不得超过 32 个。 | 如果 NIC 数量在限制范围内，则状态为就绪

### <a name="guest-operating-system"></a>来宾操作系统

对于 Azure VM 评估中，除了评审 VM 属性，评估还会查看服务器的来宾操作系统，以确定它是否可以在 Azure 上运行。

> [!NOTE]
> 为了处理 VMware VM 的来宾分析，评估会使用在 vCenter Server 中专为该 VM 指定的操作系统。 但 vCenter Server 不提供适用于 Linux VM 操作系统的内核版本。 如果要发现版本，需要设置[应用程序发现](./how-to-discover-applications.md)。 然后，设备会使用在设置应用发现时指定的来宾凭据来发现版本信息。


评估使用以下逻辑，基于操作系统确定 Azure 迁移就绪性：

**操作系统** | **详细信息** | **Azure 迁移就绪性状态**
--- | --- | ---
Windows Server 2016 和所有 SP | Azure 提供完全支持。 | Azure 迁移已就绪。
Windows Server 2012 R2 和所有 SP | Azure 提供完全支持。 | Azure 迁移已就绪。
Windows Server 2012 和所有 SP | Azure 提供完全支持。 | Azure 迁移已就绪。
Windows Server 2008 R2 和所有 SP | Azure 提供完全支持。| Azure 迁移已就绪。
Windows Server 2008（32 位和 64 位） | Azure 提供完全支持。 | Azure 迁移已就绪。
Windows Server 2003 和 Windows Server 2003 R2 | 这些操作系统的支持日期已结束，需要[自定义支持协议 (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) 以获取 Azure 支持。 | Azure 迁移已有条件就绪。 请考虑在迁移到 Azure 之前升级操作系统。
Windows 2000、Windows 98、Windows 95、Windows NT、Windows 3.1 和 MS-DOS | 这些操作系统的支持日期已结束。 服务器可能在 Azure 中启动，但 Azure 不提供任何操作系统支持。 | Azure 迁移已有条件就绪。 建议在迁移到 Azure 之前升级操作系统。
Windows 7、Windows 8 和 Windows 10 | Azure 仅支持 [Visual Studio 订阅](../virtual-machines/windows/client-images.md)。 | Azure 迁移已有条件就绪。
Windows 10 专业版 | Azure 提供了对[多租户托管权限](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md)的支持。 | Azure 迁移已有条件就绪。
Windows Vista 和 Windows XP Professional | 这些操作系统的支持日期已结束。 服务器可能在 Azure 中启动，但 Azure 不提供任何操作系统支持。 | Azure 迁移已有条件就绪。 建议在迁移到 Azure 之前升级操作系统。
Linux | 请参阅 Azure 予以认可的 [Linux 操作系统](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 操作系统可能在 Azure 中启动。 不过还是建议在迁移到 Azure 之前，先将操作系统升级到经认可的版本。 | 如果版本受到认可，则为 Azure 已就绪。<br/><br/>如果版本不受认可，则为 Azure 迁移有条件就绪。
Oracle Solaris、Apple macOS 和 FreeBSD 等其他操作系统 | Azure 不认可这些操作系统。 服务器可能在 Azure 中启动，但 Azure 不提供任何操作系统支持。 | Azure 迁移已有条件就绪。 建议在迁移到 Azure 之前，安装受支持的操作系统。  
vCenter Server 中指定为“其他”的 OS | 在此情况下，Azure Migrate 无法确定操作系统。 | 就绪性未知。 确保 Azure 支持 VM 内部运行的操作系统。
32 位操作系统 | 服务器可能在 Azure 中启动，但 Azure 可能不提供完全支持。 | Azure 迁移已有条件就绪。 请考虑在迁移到 Azure 之前，升级到 64 位操作系统。

## <a name="calculating-sizing"></a>计算调整大小

将服务器标记为“Azure 迁移就绪”后，评估将在 Azure VM 评估中做出调整大小建议。 这些建议标识了 Azure VM 和磁盘 SKU。 调整大小计算取决于使用的是按原样本地调整大小还是基于性能的调整大小。

### <a name="calculate-sizing-as-is-on-premises"></a>计算调整大小（按原样本地）

 如果使用的是按原样本地调整大小，则评估不会考虑 Azure VM 评估中 VM 和磁盘的性能历史记录。

- 计算调整大小：评估将根据本地分配的大小分配 Azure VM SKU。
- 存储和磁盘调整大小：评估查看评估属性中指定的存储类型，并建议相应的磁盘类型。 可能的存储类型是“标准 HDD”、“标准 SSD”、“高级”和“超级磁盘”。 默认存储类型为“高级”。
- 网络调整大小：评估考虑本地服务器上的网络适配器。

### <a name="calculate-sizing-performance-based"></a>计算调整大小（基于性能）

如果 Azure VM 评估中使用的是基于性能的调整大小，则评估按如下所示给出调整大小建议：

- 评估考虑服务器的性能历史记录，以确定 Azure 中的 VM 大小和磁盘类型。
- 如果使用 CSV 文件导入服务器，则会使用指定的值。 如果本地服务器过度分配、使用率较低，以及希望合理调整 Azure VM 大小以节约成本，则这种方法特别有帮助。
- 如果不想要使用性能数据，请按照上一节中所述，将调整大小条件重置为按原样本地。

#### <a name="calculate-storage-sizing"></a>计算存储调整大小

对于 Azure VM 评估中的存储调整大小，Azure Migrate 尝试将连接到服务器的每个磁盘映射到 Azure 磁盘。 调整大小的工作方式如下：

1. 评估增加磁盘的读取和写入 IOPS，以达到要求的总 IOPS。 同样，还会添加读取和写入吞吐量值，以达到每个磁盘的总吞吐量。 对于基于导入的评估，可以选择在导入的文件中提供总 IOPS、总吞吐量 以及磁盘总数，而不指定单独的磁盘设置。 如此一来，将跳过单独磁盘调整大小，直接使用提供的数据来计算调整大小，并选择适当的 VM SKU。

1. 如果已将存储类型指定为自动，则所选类型将基于有效 IOPS 和吞吐量的值。 评估会决定是将磁盘映射到 Azure 中的“标准 HDD”、“标准 SSD”、“高级”磁盘还是映射到“超级”磁盘。 如果存储类型设置为这些磁盘类型之一，则评估将尝试在所选存储类型内查找磁盘 SKU。
1. 选择磁盘的方法如下所示：
    - 如果评估找不到具有所需 IOPS 和吞吐量的磁盘，则会将服务器标记为不适合 Azure。
    - 如果评估找到了一组合适的磁盘，则评估选择支持评估设置中指定位置的磁盘。
    - 如果有多个合格的磁盘，则评估选择成本最低的磁盘。
    - 如果有任何磁盘的性能数据不可用，则使用配置磁盘大小在 Azure 中查找标准 SSD 盘。

##### <a name="ultra-disk-sizing"></a>调整超级磁盘大小

就超级磁盘而言，特定的磁盘大小拥有一定的 IOPS 和吞吐量范围，因此调整大小时使用的逻辑与标准磁盘和高级磁盘不同：
1. 计算了三个超级磁盘大小： 
    - 找到一个可以满足磁盘大小要求的磁盘（磁盘 1）
    - 找到一个可以满足总 IOPS 要求的磁盘（磁盘 2）
        - 要预配的 IOPS =（源磁盘吞吐量）*1024/256
    - 找到一个可以满足总吞吐量要求的磁盘（磁盘 3）
1. 在三个磁盘中，找到一个最大磁盘，并将其四舍五入到下一个可用的[超级磁盘产品/服务](../virtual-machines/disks-types.md#disk-size)。 这是已预配的超级磁盘大小。
1. 预配的 IOPS 是使用以下逻辑计算的：
    - 如果发现源吞吐量在超级磁盘大小的允许范围内，则预配的 IOPS 等于源磁盘 IOPS
    - 否则，预配的 IOPS 使用要预配的 IOPS =（源磁盘吞吐量）*1024/256 进行计算
1. 预配的吞吐量范围取决于预配的 IOPS


#### <a name="calculate-network-sizing"></a>计算网络调整大小

对于 Azure VM 评估，评估尝试查找支持连接到本地服务器的网络适配器的数量和所需性能的 Azure VM。

- 为了使本地服务器获得有效的网络性能，评估将对经过所有网络适配器的服务器流出（网络流出）数据传输率求和。 然后乘以舒适因子。 根据结果值，找到可支持所需网络性能的 Azure VM。
- 除了网络性能，评估还会考虑 Azure VM 是否可支持所需的网络适配器数量。
- 如果网络性能数据不可用，评估将只考虑对 VM 调整大小有影响的网络适配器。

#### <a name="calculate-compute-sizing"></a>计算运算调整大小

在计算了存储和网络需求后，评估将考虑 CPU 和 RAM 需求，以确定 Azure 中合适的 VM 大小。

- Azure Migrate 会查看有效的已使用内核和 RAM，以查找合适的 Azure VM 大小。
- 如果找不到合适的大小，服务器将标记为不适合 Azure。
- 如果找到了合适的大小，Azure Migrate 将应用存储和网络计算。 然后应用位置和定价层设置，以提供最终的 VM 大小建议。
- 如果有多个合格的 Azure VM 大小，建议选择成本最低的那一个。

## <a name="confidence-ratings-performance-based"></a>置信度分级（基于性能）

在 Azure Migrate 中，每个基于性能的 Azure VM 评估都与一个置信度分级相关联。 分级范围从一星（最低）到五星（最高）。 置信度分级可帮助评估 Azure Migrate 所提供的大小建议的可靠性。

- 置信度分级是分配给评估。 此分级基于对评估进行计算时所需数据点的可用性。
- 对于基于性能的调整大小，评估需要：
    - CPU 和 RAM 的使用率数据。
    - 连接到服务器的每个磁盘所对应的磁盘 IOPS 和吞吐量数据。
    - 为每个连接到服务器的网络适配器处理基于性能的调整大小所需要的网络 I/O。

如果其中的任一使用率数值不可用，则大小建议可能不可靠。

> [!NOTE]
> 对于使用导入的 CSV 文件进行评估的服务器，不会分配置信度分级。 另外，分级也不适用于按原样本地评估。

### <a name="ratings"></a>评级

下表显示了评估置信度分级，具体取决于可用数据点的百分比：

   **数据点的可用性** | **置信度分级**
   --- | ---
   0-20% | 1 星
   21-40% | 2 星
   41-60% | 3 星
   61-80% | 4 星
   81-100% | 5 星

### <a name="low-confidence-ratings"></a>低置信度评级

以下列出了评估可能获得低置信度分级的一些原因：

- 在创建评估的过程中，没有对环境进行分析。 例如，如果在创建评估时性能持续时间设置为一天，那么开始发现后必须等待至少一天，才能收集到所有的数据点。
- 评估无法在评估期内收集部分或全部服务器的性能数据。 若要获得高置信度分级，请确保： 
    - 服务器在评估期间处于开机状态
    - 允许端口 443 上的出站连接
    - 为 Hyper-V 服务器启用动态内存 
    
    请重新计算评估以反映置信度评级的最新更改。

- 有些服务器是在计算评估期间创建。 例如，假设针对上一个月的性能历史记录创建了评估，但有些服务器是在一周前刚刚创建。 在这种情况下，整个评估过程中将无法使用新服务器的性能数据，而且置信度分级会较低。

> [!NOTE]
> 如果任何评估的置信度分级低于五星，建议等待至少一天，以便设备对环境进行分析，然后重新计算评估。 否则，基于性能的调整大小可能不可靠。 在这种情况下，建议将评估切换为本地调整大小。

## <a name="calculate-monthly-costs"></a>计算每月成本

完成调整大小建议后，Azure Migrate 中的 Azure VM 评估将计算迁移后的计算和存储成本。

### <a name="compute-cost"></a>计算成本
Azure Migrate 使用建议的 Azure VM 大小和 Azure 计费 API 来计算服务器的每月成本。

计算中需要考虑的因素包括：
- 操作系统
- 软件保障
- 预留实例
- VM 运行时间
- 位置
- 货币设置

评估会将所有服务器的成本求和，以计算每月的总计算成本。

### <a name="storage-cost"></a>存储成本
服务器的每月存储成本等于连接到该服务器上的所有磁盘的每月成本求和。

#### <a name="standard-and-premium-disk"></a>标准磁盘和高级磁盘
标准磁盘或高级版磁盘的成本根据所选/建议的磁盘大小进行计算。 

#### <a name="ultra-disk"></a>超级磁盘 

超级磁盘的成本根据预配的大小、预配的 IOPS 和预配的吞吐量进行计算。 [了解详细信息](https://azure.microsoft.com/pricing/details/managed-disks/)

使用下列逻辑计算成本： 
- 磁盘大小成本的计算方法是将预配磁盘大小乘以磁盘容量的每小时价格
- 预配 IOPS 成本的计算方法是将预配的 IOPS 乘以每小时预配的 IOPS 价格
- 预配的吞吐量成本的计算方法是将预配的吞吐量乘以每小时预配吞吐量价格
- 超级磁盘 VM 预留费用不会添加到总成本中。 [了解详细信息](https://azure.microsoft.com/pricing/details/managed-disks/)

评估通过将所有服务器的存储成本求和，计算每月总存储成本。 目前，这项计算中不考虑在评估设置中指定的优惠。

成本以在评估设置中指定的币种显示。

## <a name="next-steps"></a>后续步骤

[查阅](best-practices-assessment.md)关于创建评估的最佳做法。 

- 了解如何对 [VMware](./tutorial-discover-vmware.md) 和 [Hyper-V ](./tutorial-discover-hyper-v.md) 环境中运行的服务器以及[物理服务器](./tutorial-discover-physical.md)运行评估。
- 了解如何评估[使用 CSV 文件导入](./tutorial-discover-import.md)的服务器。
- 了解如何设置[依赖项可视化](concepts-dependency-visualization.md)。