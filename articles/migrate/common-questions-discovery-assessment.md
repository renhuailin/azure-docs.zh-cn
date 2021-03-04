---
title: Azure Migrate 中的发现、评估和依赖项分析问题
description: 获取有关 Azure Migrate 中的发现、评估和依赖关系分析的常见问题的解答。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 6c4dfed27a105fad951ae12ca053b6d86772717a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032562"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>发现、评估和依赖关系分析-常见问题

本文解答了有关 Azure Migrate 中的发现、评估和依赖关系分析的常见问题。 如果你有其他问题，请查看以下资源：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)
- 关于[Azure Migrate 设备](common-questions-appliance.md)的问题
- 有关[服务器迁移](common-questions-server-migration.md)的问题
- 在[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中获取问题答案


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Azure Migrate 的发现和评估支持哪些地区？

查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>可以使用一个设备发现多少个 Vm？

使用单个设备，最多可以发现10000个 VMware Vm，最多可达5000个 Hyper-v Vm，最多可达1000个物理服务器。 如果有更多计算机，请阅读 [扩展 hyper-v 评估](scale-hyper-v-assessment.md)、 [扩展 VMware 评估](scale-vmware-assessment.md)或 [缩放物理服务器评估](scale-physical-assessment.md)。

## <a name="how-do-i-choose-the-assessment-type"></a>如何选择评估类型？

- 若要评估本地 [VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和 [物理服务器](how-to-set-up-appliance-physical.md)以迁移到 AZURE vm，请使用 **Azure VM 评估**。 [了解详细信息](concepts-assessment-calculation.md)

- 如果要从 VMware 环境评估本地 SQL Server 以便迁移到 Azure SQL 数据库或 Azure SQL 托管实例，请使用评估类型 " **AZURE SQL** "。 [了解详细信息](concepts-assessment-calculation.md)

    > [!Note]
    > 在 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估现在为预览版。 若要试用此功能，请使用 [**此链接**](https://aka.ms/AzureMigrate/SQL) 在 **澳大利亚东部** 区域中创建一个项目。 如果你已有一个澳大利亚东部的项目，并且想要尝试此功能，请确保已在门户上完成这些 [**先决条件**](how-to-discover-sql-existing-project.md) 。

- 如果要评估本地 [VMware vm](how-to-set-up-appliance-vmware.md)以便迁移到 [Azure VMWARE 解决方案 (avs)](../azure-vmware/introduction.md)使用此评估类型，请使用 **Azure VMware 解决方案 (AVS)** 评估。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

- 为了同时运行这两种类型的评估，可以使用具有 VMware 计算机的公共组。 请注意，如果是首次在 Azure Migrate 中运行 AVS 评估，建议创建一组新的 VMware 计算机。
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>为什么 Azure VM 和/或 AVS 评估报表中的某些/所有服务器缺少性能数据？

对于“基于性能”的评估，当 Azure Migrate 设备无法收集本地 VM 的性能数据时，评估报告导出会显示“PercentageOfCoresUtilizedMissing”或“PercentageOfMemoryUtilizedMissing”。 请检查：

- 是否在创建评估期间启动了 VM
- 如果仅缺少内存计数器，则尝试评估 Hyper-v Vm。 在此方案中，请在 Vm 上启用动态内存，并通过 "重新计算" 评估来反映最新的更改。 仅当 VM 启用了动态内存时，设备才可以收集 Hyper-v Vm 的内存使用率值。

- 如果所有性能计数器都丢失，请确保端口443上的出站连接 (允许使用 HTTPS) 。

    > [!Note]
    > 如果缺少任何性能计数器，Azure Migrate：服务器评估将回退到本地分配的内核/内存，并相应地建议 VM 大小。


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>为什么 Azure SQL 评估中的某些/所有 SQL 实例/数据库缺少性能数据？

若要确保收集性能数据，请检查：

- 如果在创建评估的持续时间内启动了 SQL Server，
- 如果 Azure Migrate 中的 SQL 代理的连接状态为 "已连接"，则检查上一检测信号 
- 如果 "已发现的 SQL 实例" 边栏选项卡中所有 SQL 实例 Azure Migrate 连接状态均为 "已连接"
- 如果所有性能计数器都丢失，请确保端口443上的出站连接 (允许使用 HTTPS) 

如果缺少任何性能计数器，Azure SQL 评估会为该实例/数据库推荐最小的 Azure SQL 配置。

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>为什么我的评估的置信度分级较低？

根据计算评估所需的[可用数据点](./concepts-assessment-calculation.md#ratings)的百分比，为“基于性能”的评估计算置信度评级。 下面是为什么评估可能会获得较低置信度分级的原因：

- 在创建评估的过程中，你没有对环境进行分析。 例如，如果创建性能持续时间设置为一周的评估，则在对所有数据点启用发现之后，需要等待至少一周才能收集。 如果无法等待持续时间，请将性能持续时间更改为较短的时间段，并 **重新计算** 评估。
 
- 评估无法在评估期内收集部分或全部服务器的性能数据。 若要获得更高的置信度，请确保： 
    - 服务器在评估期间开机
    - 允许端口443上的出站连接
    - 为 Hyper-v 服务器启用动态内存 
    - Azure Migrate 中代理的连接状态为 "已连接"，并检查上一检测信号
    - 对于 Azure SQL 评估，在 "发现的 SQL 实例" 边栏选项卡中，所有 SQL 实例 Azure Migrate 连接状态均为 "已连接"

    请 **重新计算** 评估，以反映置信度的最新更改。

- 对于 Azure VM 和 AVS 评估，启动发现后，很少有服务器都已创建。 例如，如果你要为最后一个月的性能历史记录创建评估，但在环境中仅创建了几个服务器。 在这种情况下，新服务器的性能数据将不可用于整个持续时间，置信度级别将会降低。 [了解详细信息](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- 对于 Azure SQL 评估，启动发现后创建了很少的 SQL 实例或数据库。 例如，如果您正在为最后一个月的性能历史记录创建评估，但在环境中只创建了一个星期的 SQL 实例或数据库。 在这种情况下，新服务器的性能数据将不可用于整个持续时间，置信度级别将会降低。 [了解详细信息](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="i-want-to-try-out-the-new-azure-sql-assessment-feature-in-azure-migrate"></a>我想在 Azure Migrate 中试用新的 Azure SQL 评估功能
若要试用此功能，请使用 [此链接](https://go.microsoft.com/fwlink/?linkid=2155668L) 在 **澳大利亚东部** 区域中创建一个项目。
- 若要开始，请参阅 [发现](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) 和 [评估](https://docs.microsoft.com/azure/migrate/tutorial-assess-sql) 教程。
- 请注意，在 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估目前处于预览状态。

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>创建 Azure SQL 评估时，无法看到某些服务器

- 只能在运行 SQL 实例的服务器上执行 Azure SQL 评估。 如果看不到想要评估的服务器和 SQL 实例，请等待一段时间让发现完成，并创建评估。 
- 如果在创建评估时无法看到之前创建的组，请从组中删除任何非 VMware 服务器或没有 SQL 实例的任何服务器。
- 如果是首次在 Azure Migrate 中运行 Azure SQL 评估，则建议创建一组新的服务器。

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>我想了解如何计算实例的就绪性？
在针对 Azure SQL 数据库或 Azure SQL 托管实例)  (的目标 Azure SQL 部署类型执行功能兼容性检查后，已计算 SQL 实例的准备情况。 [了解详细信息](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>为什么所有的 SQL 实例都标记为未知？
如果你的发现是最近启动的，并且仍在进行，则你可能会看到某些或所有 SQL 实例的就绪状态为 "未知"。 建议你等待一段时间让设备对环境进行配置，然后重新计算评估。
SQL 发现每24小时执行一次，可能需要等待最多一天，以反映最新的配置更改。 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>为什么某些 SQL 实例的就绪标记为未知？
出现这种情况的原因可能是： 
- 发现仍在进行中。 建议你等待一段时间让设备对环境进行配置，然后重新计算评估。
- 你需要在 "错误和通知" 边栏选项卡中解决一些发现问题。

SQL 发现每24小时执行一次，可能需要等待最多一天，以反映最新的配置更改。

## <a name="my-assessment-is-in-outdated-state"></a>我的评估处于过时状态

### <a name="azure-vmavs-assessment"></a>Azure VM/AVS 评估
如果对组中已评估的 Vm 进行本地更改，则将评估标记为过时。 由于以下属性中的一个或多个更改，可将评估标记为 "过时"：
- 处理器核心数
- 分配的内存
- 启动类型或固件
- 操作系统名称、版本和体系结构
- 磁盘数目
- 网络适配器的数目
- 磁盘大小更改 (GB 已分配) 
- Nic 属性更新。 示例： Mac 地址更改、IP 地址添加等。

请 **重新计算** 评估，以反映评估中的最新更改。

### <a name="azure-sql-assessment"></a>Azure SQL 评估
如果对组中已评估的本地 SQL 实例和数据库进行了更改，则评估将标记为 **过时**：
- 已在服务器中添加或删除 SQL 实例
- 已在 SQL 实例中添加或删除 SQL 数据库
- SQL 实例中数据库的总大小已更改，超过20%
- 处理器核心数和/或分配的内存的变化

请 **重新计算** 评估，以反映评估中的最新更改。

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>为什么建议使用特定目标部署类型？
Azure Migrate 推荐与 SQL 实例兼容的特定 Azure SQL 部署类型。 迁移到 Microsoft 建议目标可减少总体迁移工作。 在考虑 SQL 实例及其管理的数据库的性能特征后，建议使用此 Azure SQL 配置 (SKU) 。 如果有多个 Azure SQL 配置有资格，我们建议采用这种配置，这是最具成本效益的。 [了解详细信息](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>如果我的 SQL 实例已准备好进行 Azure SQL DB 和 Azure SQL MI，应选择哪个部署目标？ 
如果你的实例可用于 Azure SQL 数据库和 Azure SQL MI，则建议 Azure SQL 配置的估计成本低于此目标部署类型。

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>为什么我的实例在 Azure SQL 评估中被标记为可能已准备好 Azure VM？
如果 **建议** 在 "评估" 属性中选择的目标部署类型为 "Azure sql 数据库" 和 "azure sql" 托管实例的 sql 实例，则可能会发生这种情况。 建议用户在 Azure 迁移中创建评估类型为 **AZURE vm** 的评估，以确定该实例正在运行的服务器是否已准备好迁移到 Azure vm。
建议用户在评估类型为 **AZURE vm** 的 Azure Migrate 中创建评估，以确定该实例正在运行的服务器是否已准备好迁移到 Azure VM：
- Azure Migrate 中的 Azure VM 评估目前为直接迁移，不考虑在 Azure 虚拟机上运行 SQL 实例和数据库的特定性能指标。 
- 当你在服务器上运行 Azure VM 评估时，建议的大小和成本估计值将适用于服务器上运行的所有实例，并可使用服务器迁移工具迁移到 Azure VM。 在迁移之前，请查看 Azure 虚拟机上的 SQL Server 的 [性能指导原则](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) 。

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>即使实例是评估的一部分，也无法在 "我的评估" 中看到一些数据库

Azure SQL 评估只包含处于 "联机" 状态的数据库。 如果数据库处于任何其他状态，则评估将忽略此类数据库的准备情况、大小和成本计算。 如果你希望评估此类数据库，请在一段时间内更改数据库的状态并重新计算评估。

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>我想比较在 Azure VM 上运行 SQL 实例的成本与 Azure SQL Database/Azure SQL 托管实例

可以在 **AZURE SQL** 评估中使用的同一组上创建类型为 **azure VM** 的评估。 然后，可以并排比较这两个报表。 不过，Azure Migrate 中的 Azure VM 评估目前是直接的，并且不会考虑在 Azure 虚拟机上运行 SQL 实例和数据库的特定性能指标。 当你在服务器上运行 Azure VM 评估时，建议的大小和成本估计值将适用于服务器上运行的所有实例，并可使用服务器迁移工具迁移到 Azure VM。 在迁移之前，请查看 Azure 虚拟机上的 SQL Server 的 [性能指导原则](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) 。

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>Azure SQL 评估中的存储成本为零
对于 Azure SQL 托管实例，不会为第一个 32 GB/实例/月存储添加存储成本，而是以32GB 为增量增加额外的存储成本。 [了解详细信息](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>创建 Azure VMware 解决方案时，无法看到某些组 (AVS) 评估

- 可以对只有 VMware 计算机的组进行 AVS 评估。 如果要执行 AVS 评估，请从组中删除任何非 VMware 计算机。
- 如果是首次在 Azure Migrate 中运行 AVS 评估，建议创建一组新的 VMware 计算机。

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Azure 政府版中看不到一些 VM 类型

支持评估和迁移的 VM 类型取决于 Azure 政府版中的可用性。 可以 [查看和比较](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) Azure 政府版中的 VM 类型。

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>VM 的大小已更改。 能否再次运行评估？

Azure Migrate 设备不断地收集有关本地环境的信息。  评估是本地 Vm 的时间点快照。 如果更改了要评估的 VM 上的设置，请使用 "重新计算" 选项，以使用最新更改更新评估。

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>在多租户环境中发现 Vm 如何实现？

- **VMware**：如果环境是跨租户共享的，并且你不希望在另一个租户的订阅中发现租户的 vm，请创建 VMware vCenter 服务器凭据，这些凭据只能访问你想要发现的 vm。 然后，在 Azure Migrate 设备中启动发现时使用这些凭据。
- **Hyper-v**：发现使用 hyper-v 主机凭据。 如果 Vm 共享相同的 Hyper-v 主机，当前没有办法分离发现。  

## <a name="do-i-need-vcenter-server"></a>我是否需要 vCenter Server？

是的，Azure Migrate 需要 VMware 环境中 vCenter Server 来执行发现。 Azure Migrate 不支持发现不受 vCenter Server 管理的 ESXi 主机。

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Azure VM 评估中的调整大小选项有哪些？

利用按本地大小调整，Azure Migrate 不会考虑 VM 性能数据以进行评估。 Azure Migrate 根据本地配置评估 VM 大小。 通过基于性能的大小调整，大小基于利用率数据。

例如，如果本地 VM 具有四个核心，8 GB 的内存为50% 的 CPU 使用率，50% 的内存使用率：
- 本地大小调整会推荐一个具有四个核心和 8 GB 内存的 Azure VM SKU。
- 基于性能的大小调整建议使用具有两个核心和 4 GB 内存的 VM SKU，因为会考虑利用率百分比。

同样，磁盘大小取决于大小标准和存储类型：
- 如果大小调整条件基于性能并且存储类型为 "自动"，则 Azure Migrate 在 (标准或高级) 标识目标磁盘类型时，将使用该磁盘的 IOPS 和吞吐量值。
- 如果大小调整条件基于性能并且存储类型为 "高级"，则 Azure Migrate 建议基于本地磁盘大小的高级磁盘 SKU。 当调整大小为 "本地" 并且存储类型为 "标准" 或 "高级" 时，将对磁盘大小应用相同的逻辑。

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>性能历史记录和利用率是否会影响 Azure VM 评估的大小？

是的，性能历史记录和利用率会影响 Azure VM 评估中的大小。

### <a name="performance-history"></a>性能历史记录

对于基于性能的大小调整，Azure Migrate 会收集本地计算机的性能历史记录，并使用它在 Azure 中建议 VM 大小和磁盘类型：

1. 设备会持续分析本地环境，每隔20秒收集一次实时利用率数据。
2. 设备将汇总收集的20秒样本，并使用它们每15分钟创建一个数据点。
3. 若要创建数据点，设备将从所有20秒的示例中选择峰值值。
4. 设备会将数据点发送到 Azure。

### <a name="utilization"></a>使用率

当你在 Azure 中创建评估时，根据性能持续时间和设置的性能历史记录百分比值，Azure Migrate 计算有效利用率值，然后将其用于调整大小。

例如，如果将 "性能持续时间" 设置为 "1"，将 "百分点" 值设置为 "95%"，则 Azure Migrate 按升序对过去一天发送的每15分钟的样本点进行排序。 它将第95百分位值作为有效利用率。

使用第95百分位值可确保忽略离群值。 如果 Azure Migrate 使用99% 百分点值，则可能会包含离群值。 若要在不丢失任何离群值的情况下选取高峰用量，请将 Azure Migrate 设置为使用99% 百分点值。


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>与发现源作为设备相比，基于导入的评估与评估有何不同？

基于导入的 Azure VM 评估是使用 CSV 文件导入到 Azure Migrate 中的计算机创建的评估。 导入只需要四个字段：服务器名称、内核、内存和操作系统。 下面是一些要注意的事项： 
 - 对于启动类型参数上基于导入的评估，准备情况条件不太严格。 如果未提供启动类型，则假定计算机具有 BIOS 启动类型，并且计算机未标记为有 **条件就绪**。 在 "将发现源作为设备进行评估" 中，如果缺少启动类型，则就绪标记将被标记为有 **条件就绪** 。 准备情况计算的这一差异是因为在完成基于导入的评估时，用户可能不会在迁移规划的早期阶段中包含所有信息。 
 - 基于性能的导入评估使用用户提供的使用值进行适当大小的计算。 由于用户提供了利用率值，因此在 "评估属性" 中禁用了 " **性能历史记录** " 和 " **百分比利用率** " 选项。 在 "使用发现源作为设备进行评估" 中，从设备收集的性能数据中选取所选的百分位值。

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>为什么基于导入的 AVS 评估中建议的迁移工具标记为未知？

对于通过 CSV 文件导入的计算机，AVS 评估中的默认迁移工具是未知的。 但对于 VMware 计算机，建议使用 VMware 混合云扩展 (HCX) 解决方案。 [了解详细信息](../azure-vmware/tutorial-deploy-vmware-hcx.md)。


## <a name="what-is-dependency-visualization"></a>什么是依赖项可视化？

依赖关系可视化可以帮助你评估要迁移的 Vm 组，从而更有把握地进行迁移。 依赖关系可视化在运行评估之前交叉检查计算机的依赖关系。 它有助于确保不会遗留任何内容，并有助于避免在迁移到 Azure 时出现意外中断。 Azure Migrate 使用 Azure Monitor 中的“服务映射”解决方案来实现依赖项可视化。 [了解详细信息](concepts-dependency-visualization.md)。

> [!NOTE]
> 基于代理的依赖项分析在 Azure 政府版中不可用。 你可以使用无代理依赖项分析

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>代理和无代理之间的区别是什么？

无代理可视化和基于代理的可视化对象之间的差异在表中进行了总结。

**要求** | **无代理** | **基于代理**
--- | --- | ---
支持 | 此选项目前为预览版，仅适用于 VMware Vm。 [查看](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 支持的操作系统。 | 公开上市 (GA) 。
Agent | 无需在要交叉检查的计算机上安装代理。 | 要在要分析的每台本地计算机上安装的代理： [Microsoft Monitoring agent (MMA) ](../azure-monitor/agents/agent-windows.md)和 [依赖关系代理](../azure-monitor/agents/agents-overview.md#dependency-agent)。 
先决条件 | [查看](concepts-dependency-visualization.md#agentless-analysis) 先决条件和部署要求。 | [查看](concepts-dependency-visualization.md#agent-based-analysis) 先决条件和部署要求。
Log Analytics | 不需要。 | Azure Migrate 在 [Azure Monitor 日志](../azure-monitor/logs/log-query-overview.md)中使用[服务映射](../azure-monitor/vm/service-map.md)解决方案来进行依赖关系可视化。 [了解详细信息](concepts-dependency-visualization.md#agent-based-analysis)。
工作原理 | 捕获启用了依赖关系可视化的计算机上的 TCP 连接数据。 发现后，它会按五分钟的间隔收集数据。 | 计算机上安装的服务映射代理收集有关每个进程的 TCP 进程和入站/出站连接的数据。
数据 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。<br/><br/> 为 Log Analytics 查询收集和提供连接、延迟和数据传输信息的数目。 
可视化效果 | 可在一小时到30天内查看单服务器的依赖关系图。 | 单个服务器的依赖关系图。<br/><br/> 仅可在一小时内查看地图。<br/><br/> 一组服务器的依赖关系图。<br/><br/> 在映射视图中添加和删除组中的服务器。
数据导出 | 过去30天的数据可以下载 CSV 格式。 | 可以通过 Log Analytics 查询数据。


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>是否需要为无代理依赖项分析部署设备？

是的，必须部署 [Azure Migrate 设备](migrate-appliance.md) 。

## <a name="do-i-pay-for-dependency-visualization"></a>我是否需要为依赖关系可视化付费？

否。 了解有关 [Azure Migrate 定价](https://azure.microsoft.com/pricing/details/azure-migrate/)的详细信息。

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>对于基于代理的依赖项可视化，我应该安装什么？

若要使用基于代理的依赖项可视化，请在要评估的每台本地计算机上下载并安装代理：

- [Microsoft Monitoring Agent (MMA) ](../azure-monitor/agents/agent-windows.md)
- [依赖关系代理](../azure-monitor/agents/agents-overview.md#dependency-agent)
- 如果计算机未建立 internet 连接，请下载并在其上安装 Log Analytics 网关。

仅当使用基于代理的依赖项可视化时，才需要这些代理。

## <a name="can-i-use-an-existing-workspace"></a>是否可以使用现有的的工作区？

是的，对于基于代理的依赖项可视化，你可以将现有工作区附加到迁移项目，并将其用于依赖项可视化。 

## <a name="can-i-export-the-dependency-visualization-report"></a>是否可以导出依赖项可视化报表？

不能，不能导出基于代理的可视化对象中的依赖关系可视化报告。 但 Azure Migrate 使用服务映射，你可以使用 [服务映射 REST API](/rest/api/servicemap/machines/listconnections) 来检索 JSON 格式的依赖项。

## <a name="can-i-automate-agent-installation"></a>我可以自动安装代理吗？

对于基于代理的依赖项可视化：

- 使用 [脚本安装依赖关系代理](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent)。
- 对于 MMA，请 [使用命令行或自动化](../azure-monitor/agents/log-analytics-agent.md#installation-options)，或使用 [脚本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。
- 除了脚本以外，还可以使用部署工具（如 Microsoft Endpoint Configuration Manager 和 [Intigua](https://www.intigua.com/intigua-for-azure-migration) ）来部署代理。

## <a name="what-operating-systems-does-mma-support"></a>MMA 支持哪些操作系统？

- 查看 [MMA 支持的 Windows 操作系统](../azure-monitor/agents/log-analytics-agent.md#installation-options)的列表。
- 查看 [MMA 支持的 Linux 操作系统](../azure-monitor/agents/log-analytics-agent.md#installation-options)的列表。

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>是否可将依赖项显示超过一小时？

对于基于代理的可视化效果，可将依赖项可视化最多一小时。 您可以返回到历史记录中的某一特定日期，但可视化效果的最大持续时间为一小时。 例如，你可以在依赖关系映射中使用时间段来查看昨天的依赖项，但你只能查看一个小时窗口的依赖关系。 不过，您可以使用 Azure Monitor 日志来 [查询依赖项数据](./how-to-create-group-machine-dependencies.md) 的持续时间较长。

对于无代理可视化，你可以查看单个服务器的依赖关系映射，持续时间为1小时到30天之间。

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>能否直观显示超过10个 Vm 的组的依赖项？

可以可视化最多为10个 Vm 的组的 [依赖项](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) 。 如果组具有10个以上的 Vm，则建议将组拆分为较小的组，然后将依赖项可视化。

## <a name="next-steps"></a>后续步骤

阅读 [Azure Migrate 概述](migrate-services-overview.md)。