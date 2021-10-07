---
title: 有关 Azure Migrate 中的发现、评估和依赖项分析的问题
description: 获取有关 Azure Migrate 中的发现、评估和依赖项分析的常见问题的解答。
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: a882e0e1f0a5cf0dd9ea3690453887478319268e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538944"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>发现、评估和依赖项分析 - 常见问题

本文解答有关 Azure Migrate 中的发现、评估和依赖项分析的常见问题。 如果你遇到其他问题，请查看以下资源：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)
- 有关 [Azure Migrate 设备](common-questions-appliance.md)的问题
- 有关[服务器迁移](common-questions-server-migration.md)的问题
- 在 [Azure Migrate 论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureMigrate 中获取问题解答

## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Azure Migrate 的发现和评估功能支持哪些地理区域？

查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>使用一个设备可以发现多少个服务器？

使用单个设备最多可以发现 VMware 环境中的 10,000 个服务器、Hyper-V 环境中的 5,000 个服务器，以及 1,000 个物理服务器。 如果你有更多的服务器，请了解如何[扩展 Hyper-V 评估](scale-hyper-v-assessment.md)、[扩展 VMware 评估](scale-vmware-assessment.md)或[扩展物理服务器评估](scale-physical-assessment.md)。

## <a name="how-do-i-choose-the-assessment-type"></a>如何选择评估类型？

- 若要评估本地 [VMware](how-to-set-up-appliance-vmware.md) 和 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 环境中要迁移到 Azure VM 的服务器以及要迁移到 Azure VM 的[物理服务器](how-to-set-up-appliance-physical.md)，请使用“Azure VM 评估”。 [了解详细信息](concepts-assessment-calculation.md)
- 若要评估 VMware 环境中要迁移到 Azure SQL 数据库或 Azure SQL 托管实例的本地 SQL Server，请使用评估类型“Azure SQL”。 [了解详细信息](concepts-assessment-calculation.md)
- 评估 VMware 环境中在 IIS Web 服务器上运行的本地 ASP.NET Web 应用以便迁移到 Azure 应用服务时，请使用评估类型“Azure 应用服务”。 [了解详细信息](concepts-assessment-calculation.md)
- 若要评估要迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)，请使用“Azure VMware 解决方案 (AVS)”评估类型。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)
- 为了同时运行这两种类型的评估，可以使用具有 VMware 计算机的公共组。 如果是首次在 Azure Migrate 中运行 AVS 评估，建议创建一组新的 VMware 计算机。

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>为什么 Azure VM 和/或 AVS 评估报告中有些/所有服务器缺少性能数据？

对于“基于性能”的评估，当 Azure Migrate 设备无法收集本地服务器的性能数据时，评估报告导出会显示“PercentageOfCoresUtilizedMissing”或“PercentageOfMemoryUtilizedMissing”。 请检查：

- 是否在创建评估期间启动了服务器
- 是否只缺少内存计数器，并且你正尝试在 Hyper-V 环境中评估服务器。 在这种情况下，请在服务器上启用动态内存，并“重新计算”评估以反映最新的更改。 仅当服务器启用了动态内存时，设备才可以在 Hyper-V 环境中收集服务器的内存使用率值。

- 所有性能计数器是否均丢失；确保允许端口 443 (HTTPS) 上的出站连接。

    > [!Note]
    > 如果缺少任何性能计数器，则“Azure Migrate: 服务器评估”会回退到本地分配的核心/内存，并相应地建议一个 VM 大小。

## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>为什么我的 Azure SQL 评估中的部分/全部 SQL 实例/数据库缺少性能数据？

若要确保会收集性能数据，请检查：

- 是否在创建评估期间启动了 SQL Server
- Azure Migrate 中 SQL 代理的连接状态是否为“已连接”，并检查上一个检测信号 
- “已发现的 SQL 实例”边栏选项卡中所有 SQL 实例的 Azure Migrate 连接状态是否均为“已连接”
- 所有性能计数器是否均丢失；确保允许端口 443 (HTTPS) 上的出站连接

如果丢失任何性能计数器，Azure SQL 评估会为该实例/数据库推荐最小的 Azure SQL 配置。

## <a name="why-confidence-rating-is-not-available-for-azure-app-service-assessments"></a>为什么置信度评级不适用于 Azure 应用服务评估？

不会为 Azure 应用服务评估捕获性能数据，因此，你不会看到此评估类型的置信度评级。 在执行评估计算时，Azure 应用服务评估会考虑 Web 应用的配置数据。

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>为什么我的评估的置信度分级较低？

根据计算评估所需的[可用数据点](./concepts-assessment-calculation.md#ratings)的百分比，为“基于性能”的评估计算置信度评级。 下面是为什么评估可能会获得较低置信度分级的原因：

- 在创建评估的过程中，你没有对环境进行分析。 例如，如果创建性能持续时间设置为一周的评估，则在对所有数据点启用发现之后，需要等待至少一周才能收集。 如果无法等待这么久，请将性能持续时间缩短，并“重新计算”评估。
- 评估无法在评估期内收集部分或全部服务器的性能数据。 若要获得高置信度分级，请确保： 
    - 服务器在评估期间处于开机状态
    - 允许端口 443 上的出站连接
    - 为 Hyper-V 服务器启用了动态内存
    - Azure Migrate 中代理的连接状态为“已连接”，并请检查上一个检测信号
    - 对于 Azure SQL 评估，“已发现的 SQL 实例”边栏选项卡中所有 SQL 实例的 Azure Migrate 连接状态均为“已连接”

    请重新计算评估以反映置信度评级的最新更改。

- 对于 Azure VM 和 AVS 评估，启动发现后，创建了几乎很少的服务器。 例如，如果针对最后一个月的性能历史记录创建评估，但仅仅在一周前在环境中创建了几乎很少的服务器。 在这种情况下，整个评估过程中将不会用到新服务器的性能数据，而且置信度分级会较低。 [了解详细信息](./concepts-assessment-calculation.md#confidence-ratings-performance-based)
- 对于 Azure SQL 评估，很少有 SQL 实例或数据库是在发现开始后创建的。 例如，如果针对最后一个月的性能历史记录创建评估，但仅仅在一周前在环境中创建了几乎很少的 SQL 实例或数据库。 在这种情况下，整个评估过程中将不会用到新服务器的性能数据，而且置信度分级会较低。 [了解详细信息](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="the-number-of-azure-vm-or-avs-assessments-on-the-discovery-and-assessment-tool-are-incorrect"></a>发现和评估工具上的 Azure VM 或 AVS 评估数不正确

 若要更正此情况，请单击“评估总数”以导航到所有评估，并重新计算 Azure VM 或 AVS 评估数。 然后，发现和评估工具就会显示该评估类型的正确计数。

## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>我想试用全新 Azure SQL 评估

目前，对 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估功能以预览版提供。 请先查看[此教程](tutorial-discover-vmware.md)。 如果要在现有项目中试用此功能，请确保已完成本文中的[先决条件](how-to-discover-sql-existing-project.md)。

## <a name="i-want-to-try-out-the-new-azure-app-service-assessment"></a>我想试用全新 Azure 应用服务评估

目前，对 VMware 环境中运行的 .NET Web 应用的发现和评估功能以预览版提供。 请先查看[此教程](tutorial-discover-vmware.md)。 如果要在现有项目中试用此功能，请确保已完成本文中的[先决条件](how-to-discover-sql-existing-project.md)。

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>创建 Azure SQL 评估时，我看不到某些服务器

- 只能在发现了 SQL 实例的服务器上执行 Azure SQL 评估。 如果看不到想要评估的服务器和 SQL 实例，请等待一段时间让发现完成，然后再创建评估。
- 如果在创建评估时无法看到之前创建的组，请从组中删除所有非 VMware 服务器或所有没有 SQL 实例的服务器。
- 如果是首次在 Azure Migrate 中运行 Azure SQL 评估，建议创建一组新的服务器。

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-app-service-assessment"></a>创建 Azure 应用服务评估时，我看不到某些服务器

- Azure 应用服务评估只能在已发现 Web 服务器角色的服务器上完成。 如果看不到想要评估的服务器，请等待一段时间以完成发现，然后再创建评估。
- 如果在创建评估时看不到之前创建的组，请从组中删除所有非 VMware 服务器或所有没有 Web 应用的服务器。
- 如果是首次在 Azure Migrate 中运行 Azure 应用服务评估，建议创建一组新的服务器。

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>我的实例的就绪性是如何计算得出的？

在对目标 Azure SQL 部署类型（Azure SQL 数据库或 Azure SQL 托管实例）执行功能兼容性检查后，计算了你的 SQL 实例的就绪性。 [了解详细信息](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="i-want-to-understand-how-was-the-readiness-for-my-web-apps-is-computed"></a>我的 Web 应用的就绪性是如何计算得出的？

Web 应用的就绪性是通过运行一系列技术检查来计算的，以确定你的 Web 应用是否会在 Azure 应用服务中成功运行。 [此处](https://github.com/Azure/App-Service-Migration-Assistant/wiki/Readiness-Checks)介绍了这些检查。

## <a name="why-is-my-web-app-marked-as-ready-with-conditions-or-not-ready-in-my-azure-app-service-assessment"></a>为什么我的 Web 应用在 Azure 应用服务评估中标记为“就绪(有条件)”或“未就绪”？

当给定的 Web 应用的一个或多个技术检查失败时，就会出现此情况。 你可以单击 Web 应用的就绪性状态，以了解有关检查失败的详细信息和修正情况。

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>我的所有 SQL 实例的就绪性为何标记为未知？

如果你的发现是最近启动的并仍在进行，则你可能会看到某些或所有 SQL 实例的就绪性标记为未知。 建议稍等一段时间，待设备对环境进行分析，然后再重新计算评估。
SQL 发现每 24 小时执行一次；可能需要等待一天，最新的配置更改才会反映出来。

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>我的某些 SQL 实例的就绪性为何标记为未知？

可能的原因包括：

- 发现仍在进行。 建议稍等一段时间，待设备对环境进行分析，然后再重新计算评估。
- 你需要在“错误和通知”边栏选项卡中解决一些与发现相关的问题。

SQL 发现每 24 小时执行一次；可能需要等待一天，最新的配置更改才会反映出来。

## <a name="my-assessment-is-in-outdated-state"></a>我的评估处于“过时”状态

### <a name="azure-vmavs-assessment"></a>Azure VM/AVS 评估

如果对已评估的组中的服务器进行了本地更改，则评估将标记为过时。 可能会由于在以下属性中进行了一项或多项更改而将评估标记为“过时”：

- 处理器核心数
- 分配的内存
- 启动类型或固件
- 操作系统名称、版本和体系结构
- 磁盘数目
- 网络适配器数目
- 磁盘大小更改（分配的 GB）
- NIC 属性更新。 例如，更改 Mac 地址、添加 IP 地址等。

请重新计算评估以在评估中反映最新更改。

### <a name="azure-sql-assessment"></a>Azure SQL 评估

如果对已评估的组中的本地 SQL 实例和数据库进行了更改，则评估将标记为“过时”：

- 已在服务器中添加或删除 SQL 实例
- 已在 SQL 实例中添加或删除 SQL 数据库
- SQL 实例中的总数据库大小更改超过 20%
- 更改了处理器核心数和/或分配的内存

请重新计算评估以在评估中反映最新更改。

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>为何建议使用特定的目标部署类型？

Azure Migrate 建议使用与你的 SQL 实例兼容的特定 Azure SQL 部署类型。 迁移到 Microsoft 建议的目标可减少整体迁移工作量。 考虑到你的 SQL 实例及其管理的数据库的性能特征，建议使用此 Azure SQL 配置 (SKU)。 如果多个 Azure SQL 配置都符合条件，则建议使用最具成本效益的配置。 [了解详细信息](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>如果我的 SQL 实例已可用于 Azure SQL DB 和 Azure SQL MI，我应选择哪个部署目标？

如果你的实例已可用于 Azure SQL DB 和 Azure SQL MI，那么建议使用 Azure SQL 配置预估成本更低的目标部署类型。

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>我的实例为何在 Azure SQL 评估中被标记为“可能已可用于 Azure VM”？

如果在评估属性中选择建议使用的目标部署类型，并且 SQL 实例尚不可用于 Azure SQL 数据库和 Azure SQL 托管实例，则可能会发生这种情况。 建议用户在 Azure 迁移中创建评估类型为 Azure VM 的评估，以决定正在运行实例的服务器是否已可迁移到 Azure VM。
建议用户在 Azure Migrate 中创建评估类型为“Azure VM”的评估，以决定正在运行实例的服务器是否已可迁移到 Azure VM：

- Azure Migrate 中的 Azure VM 评估目前侧重于直接迁移，不考虑在 Azure 虚拟机上运行 SQL 实例和数据库的特定性能指标。
- 当你在服务器上运行 Azure VM 评估时，建议的大小和成本估计值将适用于该服务器上运行的所有实例，并且可通过服务器迁移工具迁移到 Azure VM。 在迁移之前，请查看 Azure 虚拟机中的 SQL Server 的[性能原则](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md)。

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>即使实例是评估的一部分，我在我的评估中也看不到某些数据库

Azure SQL 评估只包含处于联机状态的数据库。 如果数据库处于任何其他状态，评估将不计算此类数据库的就绪性、大小和成本。 如果你希望评估此类数据库，请更改数据库的状态，在一段时间后再重新计算评估。

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>我想比较在 Azure VM 上运行 SQL 实例与在 Azure SQL 数据库/Azure SQL 托管实例上运行 SQL 实例的成本

可在 Azure SQL 评估中使用的同一组上创建类型为 Azure VM 的评估 。 然后，就可并行比较这两个报表。 不过，Azure Migrate 中的 Azure VM 评估目前侧重于直接迁移，不考虑在 Azure 虚拟机上运行 SQL 实例和数据库的特定性能指标。 当你在服务器上运行 Azure VM 评估时，建议的大小和成本估计值将适用于该服务器上运行的所有实例，并且可通过服务器迁移工具迁移到 Azure VM。 在迁移之前，请查看 Azure 虚拟机中的 SQL Server 的[性能原则](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md)。

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>Azure SQL 评估中的存储成本为零

对于 Azure SQL 托管实例，第一个 32 GB/实例/月存储没有增加存储成本，而是以 32 GB 为增量增加额外的存储成本。 [了解详细信息](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>在创建 Azure VMware 解决方案 (AVS) 评估时看不到某些组

- 可以对只有 VMware 计算机的组进行 AVS 评估。 如果要执行 AVS 评估，请从组中删除任何非 VMware 计算机。
- 如果是首次在 Azure Migrate 中运行 AVS 评估，建议创建一组新的 VMware 计算机。

## <a name="queries-regarding-ultra-disks"></a>有关超级磁盘的查询

### <a name="can-i-migrate-my-disks-to-ultra-disk-using-azure-migrate"></a>能否使用 Azure Migrate 将磁盘迁移到超级磁盘？

否。 目前，Azure Migrate 和 Azure Site Recovery 均不支持迁移到超级磁盘。 可在[此处](../virtual-machines/disks-enable-ultra-ssd.md?tabs=azure-portal#deploy-an-ultra-disk)找到部署超级磁盘的步骤

### <a name="why-are-the-provisioned-iops-and-throughput-in-my-ultra-disk-more-than-my-on-premises-iops-and-throughput"></a>为何超级磁盘中的预配 IOPS 和吞吐量超过本地 IOPS 和吞吐量？

根据[官方定价页](https://azure.microsoft.com/pricing/details/managed-disks/)，超级磁盘的费用根据预配的大小、预配的 IOPS 和预配的吞吐量进行计算。 根据提供的示例：

如果预配了 200 GiB 的超级磁盘（IOPS 为 20,000，1,000 MB/秒），并在 20 小时后将其删除，则它会映射到 256 GiB 的磁盘大小，并且将按 256 GiB 大小收取 20 个小时（IOPS 为 20,000，1,000 MB/秒）的费用。

要预配的 IOPS =（发现的吞吐量）*1024/256

### <a name="does-the-ultra-disk-recommendation-consider-latency"></a>超级磁盘建议是否考虑延迟？

否，目前仅磁盘大小、总吞吐量和总 IOPS 用于大小调整和成本计算。

### <a name="i-can-see-m-series-supports-ultra-disk-but-in-my-assessment-where-ultra-disk-was-recommended-it-says-no-vm-found-for-this-location"></a>我可以看到 M 系列支持超级磁盘，但在推荐超级磁盘的评估中，它显示“找不到此位置的 VM”？

并非所有支持超级磁盘的 VM 大小都存在于所有支持超级磁盘的区域，所以可能会出现此情况。 更改目标评估区域，获取此服务器的 VM 大小。

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>在 Azure 政府版中看不到某些 VM 类型和大小

评估和迁移支持的 VM 类型和大小取决于它们在 Azure 政府版位置中的可用性。 可以[查看并比较](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) Azure 政府版中的 VM 类型。

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>我的服务器大小已更改。 是否可以再次运行评估？

Azure Migrate 设备会持续收集有关本地环境的信息。  评估是本地服务器的时间点快照。 如果更改了要评估的服务器上的设置，请使用“重新计算”选项，以使用最新更改来更新评估。

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>如何发现多租户环境中的服务器？

- VMware：如果环境在多个租户之间共享，而你不希望发现另一个租户的订阅中的租户服务器，请创建只能访问你要发现的服务器的 VMware vCenter Server 凭据。 然后，在 Azure Migrate 设备中启动发现时使用这些凭据。
- Hyper-V：发现使用 Hyper-V 主机凭据。 如果服务器共享同一个 Hyper-V 主机，则目前没有办法可以区分发现。  

## <a name="do-i-need-vcenter-server"></a>是否需要 vCenter Server？

是，Azure Migrate 需要使用 VMware 环境中的 vCenter Server 来执行发现。 Azure Migrate 不支持发现不受 vCenter Server 管理的 ESXi 主机。

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Azure VM 评估中有哪些大小调整选项？

使用与本地相同的大小调整选项时，Azure Migrate 在评估中不考虑服务器性能数据。 Azure Migrate 根据本地配置评估 VM 大小。 使用基于性能的大小调整选项时，将基于利用率数据调整大小。

例如，如果本地服务器有 4 个核心和 8 GB 内存，CPU 利用率为 50%，内存利用率为 50%：

- 与本地相同的大小调整会推荐一个具有 4 个核心和 8 GB 内存的 Azure VM SKU。
- 基于性能的大小调整会推荐具有 2 个核心和 4 GB 内存的 VM SKU，因为它考虑到了利用率百分比。

同样，磁盘大小调整取决于大小调整条件和存储类型：

- 如果大小调整条件“基于性能”并且存储类型为“自动”，则 Azure Migrate 在标识目标磁盘类型（“标准”、“高级”或“超级磁盘”）时，将考虑磁盘的 IOPS 和吞吐量值。
- 如果大小调整条件“与本地相同”并且存储类型为“高级”，则 Azure Migrate 会基于本地磁盘大小推荐一个高级磁盘 SKU。 当大小调整条件为“与本地相同”并且存储类型为“标准”、“高级”或“超级磁盘”时，将对磁盘大小调整应用相同的逻辑。

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>性能历史记录和利用率是否影响 Azure VM 评估中的大小调整？

是，性能历史记录和利用率会影响 Azure VM 评估中的大小调整。

### <a name="performance-history"></a>性能历史记录

仅对于基于性能的大小调整而言，Azure Migrate 会收集本地计算机的性能历史记录，然后使用它来针对 Azure 中的 VM 大小和磁盘类型提出建议：

1. 设备持续分析本地环境，每隔 20 秒收集一次实时利用率数据。
2. 设备汇总收集的 20 秒样本，并每隔 15 分钟使用这些样本创建单个数据点。
3. 为了创建数据点，设备会选择所有 20 秒样本中的峰值。
4. 设备将数据点发送到 Azure。

### <a name="utilization"></a>使用率

在 Azure 中创建评估时，Azure Migrate 会根据设置的性能持续时间和性能历史记录百分位值计算有效利用率值，然后使用该值进行大小调整。

例如，如果将性能持续时间设置为 1 天，将百分位值设置为第 95 百分位，则 Azure Migrate 将按升序排序收集器在过去一天发送的 15 分钟样本点。 它选取第 95 百分位值作为有效利用率。

使用第 95 百分位值可确保忽略离群值。 如果 Azure Migrate 使用第 99 百分位，则可能会包含离群值。 若要选取峰值使用率且不缺少任何离群值，请将 Azure Migrate 设置为使用第 99 百分位。

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>基于导入的评估与使用发现源作为设备的评估有何不同？

基于导入的 Azure VM 评估是使用通过 CSV 文件导入到 Azure Migrate 中的计算机创建的评估。 只有四个字段是必须导入的字段：服务器名称、核心数、内存和操作系统。 下面是需要注意的几个事项：

 - 在基于导入的评估的启动类型参数中，就绪性条件不太严格。 如果未提供启动类型，则假定计算机采用 BIOS 启动类型，并且计算机未标记为“有条件就绪”。 在使用发现源作为设备的评估中，如果缺少启动类型，则就绪性将标记为“有条件就绪”。 就绪性计算之所以存在这种差异，是因为在完成基于导入的评估后，用户在早期的迁移规划阶段中尚未在计算机上准备好所有信息。
 - 基于性能的导入评估使用用户为适当大小调整计算而提供的利用率值。 由于利用率值由用户提供，因此在评估属性中禁用了“性能历史记录”和“百分位利用率”选项 。 在使用发现源作为设备的评估中，所选百分位值选取自设备所收集的性能数据。

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>在基于导入的 AVS 评估中，建议的迁移工具为何标记为未知？

对于通过 CSV 文件导入的计算机，AVS 评估中的默认迁移工具是未知的。 不过，对于 VMware 计算机，建议使用 VMware 混合云扩展 (HCX) 解决方案。 [了解详细信息](../azure-vmware/install-vmware-hcx.md)。

## <a name="what-is-dependency-visualization"></a>什么是依赖项可视化？

依赖项可视化可帮助你以更高的置信度评估要迁移的服务器组。 在你运行评估之前，依赖项可视化会交叉检查计算机依赖项。 它可以帮助确保不会遗留任何项，并有助于避免在迁移到 Azure 时出现意外的服务中断。 Azure Migrate 使用 Azure Monitor 中的“服务映射”解决方案来实现依赖项可视化。 [了解详细信息](concepts-dependency-visualization.md)。

> [!NOTE]
> Azure 政府版中不提供基于代理的依赖项分析。 可以使用无代理依赖项分析

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>基于代理和无代理有什么区别？

下表中汇总了无代理可视化和基于代理的可视化的差别。

**要求** | **无代理** | **基于代理**
--- | --- | ---
支持 | 此选项目前为预览版，仅适用于 VMware 环境中的服务器。 [查看](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)支持的操作系统。 | 已推出正式版 (GA)。
Agent | 无需在要交叉检查的计算机上安装代理。 | 需要在要分析的每台本地计算机上安装的代理：[Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md) 和 [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent)。 
先决条件 | [查看](concepts-dependency-visualization.md#agentless-analysis)先决条件和部署要求。 | [查看](concepts-dependency-visualization.md#agent-based-analysis)先决条件和部署要求。
Log Analytics | 不需要。 | Azure Migrate 在 [Azure Monitor 日志](../azure-monitor/logs/log-query-overview.md)中使用[服务映射](../azure-monitor/vm/service-map.md)解决方案来进行依赖关系可视化。 [了解详细信息](concepts-dependency-visualization.md#agent-based-analysis)。
工作原理 | 在已启用依赖项可视化的计算机上捕获 TCP 连接数据。 发现后，每隔五分钟收集一次数据。 | 计算机上安装的服务映射代理收集有关 TCP 进程的数据以及每个进程的入站/出站连接。
数据 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。<br/><br/> 收集连接数、延迟和数据传输信息并用于 Log Analytics 查询。 
可视化效果 | 可在一小时到 30 天内查看单个服务器的依赖项映射。 | 单个服务器的依赖项映射。<br/><br/> 仅可在一小时内查看映射。<br/><br/> 一组服务器的依赖项映射。<br/><br/> 从映射视图中添加和删除组中的服务器。
数据导出 | 过去 30 天的数据可以 CSV 格式下载。 | 可以通过 Log Analytics 查询数据。

## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>是否需要为无代理依赖项分析部署设备？

是，必须部署 [Azure Migrate 设备](migrate-appliance.md)。

## <a name="do-i-pay-for-dependency-visualization"></a>依赖项可视化是否收费？

否。 详细了解 [Azure Migrate 定价](https://azure.microsoft.com/pricing/details/azure-migrate/)。

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>需要安装哪个代理进行基于代理的依赖项可视化？

若要使用基于代理的依赖项可视化，请在要评估的每台本地计算机上下载并安装以下代理：

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [依赖项代理](../azure-monitor/agents/agents-overview.md#dependency-agent)
- 如果你的计算机未连接到 Internet，请在计算机上下载并安装 Log Analytics 网关。

仅当使用基于代理的依赖项可视化时，才需要这些代理。

## <a name="can-i-use-an-existing-workspace"></a>是否可以使用现有的的工作区？

是，对于基于代理的依赖项可视化，可将现有的工作区附加到迁移项目，并将该工作区用于依赖项可视化。

## <a name="can-i-export-the-dependency-visualization-report"></a>是否可以导出依赖项可视化报表？

否，无法导出基于代理的可视化中的依赖项可视化报告。 但是，Azure Migrate 使用服务映射，你可以使用[服务映射 REST API](/rest/api/servicemap/machines/listconnections) 来检索 JSON 格式的依赖项。

## <a name="can-i-automate-agent-installation"></a>是否可以自动完成代理安装？

对于基于代理的依赖项可视化：

- 使用[脚本安装 Dependency Agent](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent)。
- 对于 MMA，[使用命令行或自动化](../azure-monitor/agents/log-analytics-agent.md#installation-options)，或使用[脚本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。
- 除脚本以外，还可以使用 Microsoft Endpoint Configuration Manager 和 Intigua 等部署工具来部署代理。

## <a name="what-operating-systems-does-mma-support"></a>MMA 支持哪些操作系统？

- 查看 [MMA 支持的 Windows 操作系统](../azure-monitor/agents/log-analytics-agent.md#installation-options)列表。
- 查看 [MMA 支持的 Linux 操作系统](../azure-monitor/agents/log-analytics-agent.md#installation-options)列表。

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>是否可以可视化依赖项一小时以上？

对于基于代理的可视化，最长可以可视化依赖项一小时。 可以返回到历史记录中最长一个月前的特定日期，但可视化的最长持续时间为一小时。 例如，可以使用依赖项映射中的持续时间来查看昨天的依赖项，但只能查看一小时时段的依赖项。 不过，可以使用 Azure Monitor 日志以更长的持续时间[查询依赖项数据](./how-to-create-group-machine-dependencies.md)。

对于无代理可视化，可以查看单个服务器的持续时间为 1 小时到 30 天的依赖项映射。

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>是否可以可视化包含 10 个以上服务器的组的依赖项？

可以[可视化最多包含 10 个服务器的组的依赖项](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping)。 如果你的组包含 10 个以上的服务器，我们建议将该组拆分为较小的组，然后将依赖项可视化。

## <a name="next-steps"></a>后续步骤

阅读 [Azure Migrate 概述](migrate-services-overview.md)。
