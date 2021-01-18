---
title: Azure 磁盘备份概述
description: 了解 Azure 磁盘备份解决方案。
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: fea0dd9d01bdc7c31d724cedd89d1fe6891c650a
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557358"
---
# <a name="overview-of-azure-disk-backup-in-preview"></a>预览版中的 Azure 磁盘备份 (概述) 

>[!IMPORTANT]
>Azure 磁盘备份没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关区域可用性，请参阅 [支持矩阵](disk-backup-support-matrix.md)。
>
>[填写此窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以便注册预览版。

Azure 磁盘备份是一种基于云的本机备份解决方案，可保护托管磁盘中的数据。 这是一个简单、安全且经济高效的解决方案，使你可以通过几个步骤配置对托管磁盘的保护。 它确保你可以在灾难情况下恢复数据。

Azure 磁盘备份提供了一个全包式解决方案，该解决方案通过使用备份策略自动创建快照并将其保留为配置的持续时间，为托管磁盘提供快照生命周期管理。 你可以用零的基础结构成本管理磁盘快照，而无需自定义脚本处理或管理开销。 这是一个崩溃一致的备份解决方案，它使用 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots) 获取托管磁盘的时间点备份，每日支持多个备份。 它也是一个无代理的解决方案，不会影响生产应用程序的性能。 它支持 (包括共享磁盘) 的操作系统和数据磁盘的备份和还原，无论它们当前是否已附加到正在运行的 Azure 虚拟机。

如果需要包含数据磁盘的虚拟机的应用程序一致性备份，或者是从备份中还原整个虚拟机的选项，请还原文件或文件夹，或还原到次要区域，然后使用 [AZURE VM 备份](backup-azure-vms-introduction.md) 解决方案。 Azure 备份除了 [AZURE VM 备份](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) 解决方案外，还提供了使用磁盘备份对托管磁盘进行备份的并行支持。 当你需要一天的虚拟机的应用程序一致性备份，还需要更频繁地备份 OS 磁盘或处于崩溃一致性的特定数据磁盘时，此方法非常有用，并且不会影响生产应用程序的性能。

Azure 磁盘备份集成到了备份中心，在 Azure 中提供了一种 **统一的管理体验** ，使企业能够大规模控制、监视、操作和分析备份。

## <a name="key-benefits-of-disk-backup"></a>磁盘备份的主要优点

Azure 磁盘备份是无代理和崩溃一致的解决方案，它使用 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots) 并提供以下优点：

- 更频繁、更快速地备份，而不会中断虚拟机。
- 不会影响生产应用程序的性能。
- 无安全问题，因为它不需要运行自定义脚本或安装代理。
- 与备份整个虚拟机相比，用于备份特定磁盘的经济高效的解决方案。

Azure 磁盘备份解决方案在以下情况下很有用：

- 需要每天经常备份，无需应用程序静止
- 在群集方案中运行的应用： Windows Server 故障转移群集和 Linux 群集都写入共享磁盘
- 由于应用程序的安全或性能方面的考虑，特定需要进行无代理备份
- VM 的应用程序一致性备份并不可行，因为业务线应用不支持 (VSS) 卷影复制服务。

在以下情况下，请考虑 Azure 磁盘备份：

- 关键任务应用程序运行在 Azure 虚拟机上，该虚拟机要求每天有多个备份来满足恢复点目标，而不影响生产环境或应用程序性能
- 出于安全考虑，你的组织或行业法规会限制安装代理
- 在 Linux 虚拟机上执行自定义的 pre 或 post 脚本并调用冻结和解冻，以获得应用程序一致的备份，在生产工作负荷可用性上带来不必要的开销
- 在 Azure Kubernetes Service (AKS 群集上运行的容器化应用程序) 使用托管磁盘作为持久存储。 现在，必须通过难以管理的自动化脚本来备份托管磁盘。
- 托管磁盘包含关键业务数据，用作文件共享或包含数据库备份文件，并且你希望通过不投资 Azure VM 备份来优化备份成本
- 你有多个 Linux 和 Windows 单磁盘虚拟机 (即，只包含 OS 磁盘的虚拟机，不会) 承载 web 服务器或无状态计算机的数据磁盘，也不能用作具有应用程序配置设置的过渡环境，你需要使用经济高效的备份解决方案来保护 OS 磁盘。 例如，在升级或修补虚拟机之前触发快速按需备份
- 虚拟机正在运行 Azure VM 备份解决方案不支持的 OS 配置 (例如，Windows 2008 32 位服务器) 

## <a name="how-the-backup-and-restore-process-works"></a>备份和还原过程的工作方式

- 为 Azure 文件共享配置备份的第一步是创建 [备份保管库](backup-vault-overview.md)。 保管库提供跨不同工作负荷配置的备份的合并视图。

- 然后创建一个允许你配置备份频率和保持期的备份策略。

- 若要配置备份，请前往备份保管库，分配备份策略，选择需要备份的托管磁盘，并提供要在其中存储和管理快照的资源组。 Azure 备份会自动触发计划的备份作业，这些作业根据备份频率创建磁盘的增量快照。 根据备份策略指定的保持期，删除较旧的快照。

- Azure 备份使用托管磁盘的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) 。 增量快照是托管磁盘的一种经济高效的时间点备份，对自上一次快照以来磁盘的增量更改计费。 它们始终存储在最经济高效的存储中，而不考虑父磁盘的存储类型。 磁盘的第一个快照将占用磁盘的已用空间，而连续增量快照则在自上次拍摄快照后，将对磁盘进行增量更改。

- 配置托管磁盘的备份后，将在备份保管库中创建一个备份实例。 通过使用备份实例，可以查找备份操作的运行状况，触发按需备份，并执行还原操作。 你还可以通过使用备份中心来查看跨多个保管库和备份实例的备份的运行状况，这提供了单个窗格的玻璃视图。

- 若要还原，只需选择要从中还原磁盘的恢复点。 提供要从快照创建已还原磁盘的资源组。 Azure 备份提供即时还原体验，因为快照本地存储在订阅中。

- 备份保管库使用托管标识来访问其他 Azure 资源。 若要配置托管磁盘的备份并从过去的备份进行还原，备份保管库的托管标识需要一组对源磁盘的权限，创建和管理快照的快照资源组，以及要在其中还原备份的目标资源组。 可以使用 Azure RBAC)  (基于角色的访问控制向托管标识授予权限。 托管标识是特定类型的服务主体，只能与 Azure 资源一起使用。 了解有关 [托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的详细信息。

- 当前，Azure 磁盘备份支持托管磁盘的操作备份，不会将备份复制到备份保管库存储中。 有关支持的和不支持的方案以及区域可用性的详细列表，请参阅 [支持矩阵](disk-backup-support-matrix.md)。

## <a name="pricing"></a>定价

Azure 备份提供用于保护 Azure 磁盘的快照生命周期管理解决方案。 Azure 备份创建的磁盘快照存储在 Azure 订阅中的资源组中，并产生 **快照存储** 费用。 若要了解有关快照定价的更多详细信息，可以访问 [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) 。 由于快照不会复制到备份保管库，Azure 备份不会对 **受保护的实例** 收费计费，并且不会应用 **备份存储** 开销。 此外，增量快照会在上次快照之后占用增量更改，并始终存储在标准存储上，而不考虑父托管磁盘的存储类型，并根据标准存储的定价进行收费。 这使得 Azure 磁盘备份成为经济高效的解决方案。

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘备份支持矩阵](disk-backup-support-matrix.md)
