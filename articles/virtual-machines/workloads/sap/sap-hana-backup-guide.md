---
title: Azure 虚拟机上的 SAP HANA 备份指南 | Microsoft Docs
description: SAP HANA 备份指南介绍适用于 Azure 虚拟机上的 SAP HANA 的两种可行的主要备份方法。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0004afb5895d7549e5db8ad5e53b52fa17991520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667908"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA 备份指南

## <a name="getting-started"></a>入门

适用于 Azure 虚拟机上运行的 SAP HANA 的备份指南只介绍特定于 Azure 的主题。 若要了解常规 SAP HANA 备份相关项，请查看 SAP HANA 文档。 我们希望你熟悉健全有效备份策略的基本数据库备份策略、原因和动机，并了解你的公司对备份过程、备份保持期和还原过程的要求。

SAP HANA 受到了各种 Azure VM 类型的官方支持，例如 Azure M 系列。 有关 SAP HANA 认证的 Azure VM 和 HANA 大型实例单元的完整列表，请查看[查找认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 Microsoft Azure 提供了许多单元，其中 SAP HANA 在物理服务器上运行非虚拟化。 这项服务称为 [HANA 大型实例](hana-overview-architecture.md)。 本指南不介绍适用于 HANA 大型实例的备份过程和工具。 但仅限于 Azure 虚拟机。 有关 HANA 大型实例的备份/还原过程的详细信息，请阅读 [HLI 备份和还原](./hana-backup-restore.md)一文。

本文重点介绍适用于 Azure 虚拟机上的 SAP HANA 的三种可行的备份方法：

- 通过 [Azure 备份服务](../../../backup/backup-overview.md)进行 HANA 备份 
- 将 HANA 备份到 Azure Linux 虚拟机中的文件系统（参阅[文件级别的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)）
- 使用 Azure 存储 Blob 快照功能以手动方式或者使用 Azure 备份服务进行基于存储快照的 HANA 备份


SAP HANA 提供一个备份 API，第三方备份工具可以借助此 API 来直接与 SAP HANA 集成。 Azure 备份服务或 [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) 等产品使用此专用接口来触发 SAP HANA 数据库或重做日志备份。 


有关如何找到 Azure 上支持的 SAP 软件的信息，请参阅 [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)一文。

## <a name="azure-backup-service"></a>Azure 备份服务

显示的第一种情况是，Azure 备份服务使用 SAP HANA `backint` 接口从 SAP HANA 数据库执行流式备份。 或者，使用 Azure 备份服务更通用的功能创建应用程序一致性磁盘快照，并将其传输到 Azure 备份服务。

Azure 备份使用称为 [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) 的专有 SAP HANA 接口，集成并认证为适用于 SAP HANA 的备份解决方案。 有关该解决方案、其功能和可用 Azure 区域的更多详细信息，请参阅 [Azure VM 上的 SAP HANA 数据库备份的支持矩阵](../../../backup/sap-hana-backup-support-matrix.md#scenario-support)一文。 有关适用于 HANA 的 Azure 备份服务的详细信息和原则，请阅读[关于 Azure VM 中 SAP HANA 数据库备份](../../../backup/sap-hana-db-about.md)一文。 

利用 Azure 备份服务的第二种可能性是使用 Azure 高级存储的磁盘快照创建应用程序一致性备份。 其他 HANA 认证的 Azure 存储（如 [Azure 超级磁盘](../../disks-enable-ultra-ssd.md)和 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)）不支持通过 Azure 备份服务进行此类快照。 阅读以下文章：

- [在 Azure 中计划 VM 备份基础结构](../../../backup/backup-azure-vms-introduction.md)
- [Azure Linux VM 的应用程序一致性备份](../../../backup/backup-azure-linux-app-consistent.md) 

活动的顺序如下：

- Azure 备份需要执行一个快照前脚本，该脚本将应用程序（本例中为 SAP HANA）置于一致状态
- 确认此一致状态后，Azure 备份将执行磁盘快照
- 完成快照后，Azure 备份将撤消它在快照前脚本中执行的活动
- 成功执行后，Azure 备份会将数据流式传输到备份保管库

如果使用 SAP HANA，大多数客户对包含 SAP HANA 重做日志的卷使用 Azure 写入加速器。 Azure 备份服务会自动从快照中排除这些卷。 这种排除不会损害 HANA 的还原能力。 但会阻止使用几乎所有其他 SAP 支持的 DBMS 进行还原。

这种可能性的缺点是需要开发自己的快照前脚本和快照后脚本。 快照前脚本需要创建 HANA 快照并处理最终异常情况。 而快照后脚本需要再次删除 HANA 快照。 有关所需逻辑的更多详细信息，请从 [SAP 支持说明 #2039883](https://launchpad.support.sap.com/#/notes/2039883) 开始。 本文中“创建存储快照时的 SAP HANA 数据一致性”一节的注意事项完全适用于这种备份。

> [!NOTE]
> 在使用多个数据库容器的部署中对 SAP HANA 进行基于磁盘快照的备份，需要使用 HANA 2.0 SP04 的最低版本
> 

请参阅本文档后面关于存储快照的详细信息。

![此图显示了保存当前 VM 状态的两种可行方法](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>其他 HANA 备份方法
还有三个可以考虑的备份方法或路径：

- 针对基于 Azure NetApp 文件 (ANF) 的 NFS 共享进行备份。 ANF 同样能够创建用于存储备份的卷的快照。 考虑到最终需要写入备份的吞吐量，此解决方案可能很昂贵。 尽管可轻松建立，因为 HANA 可以将备份直接写入 Azure 本机 NFS 共享
- 针对标准 SSD 或 Azure 高级存储的 VM 附加磁盘执行 HANA 备份。 下一步，可以根据 Azure Blob 存储复制这些备份文件。 此策略可能在价格上有吸引力
- 针对标准 SSD 或 Azure 高级存储的 VM 附加磁盘执行 HANA 备份。 下一步，定期创建磁盘的快照。 在第一个快照之后，可以使用增量快照降低成本

![此图显示了用于在 VM 中创建 SAP HANA 文件备份的选项](media/sap-hana-backup-guide/other-hana-backup-paths.png)

此图显示了用于在 VM 中创建 SAP HANA 文件备份，然后使用不同的工具将备份存储在位于其他某个位置的 HANA 备份文件中的选项。 但是，所有不涉及第三方备份服务或 Azure 备份服务的解决方案都有几个共同的障碍。 其中一些可以列举出来，例如保留管理、自动还原过程，以及提供 Azure 备份服务或其他专用第三方备份套件和服务提供的自动时点恢复。 其中许多第三方服务能够在 Azure 上运行。 


## <a name="sap-resources-for-hana-backup"></a>用于 SAP HANA 备份的资源

### <a name="sap-hana-backup-documentation"></a>SAP HANA 备份文档

- [SAP HANA 管理简介](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [规划备份和恢复策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [使用 ABAP DBACOCKPIT 计划 HANA 备份](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [计划数据备份（SAP HANA 考核中心）](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- [SAP 说明 1642148](https://launchpad.support.sap.com/#/notes/1642148) 中有关 SAP HANA 备份的常见问题解答
- [SAP 说明 2039883](https://launchpad.support.sap.com/#/notes/2039883) 中有关 SAP HANA 数据库和存储快照的常见问题解答
- [SAP 说明 1820529](https://launchpad.support.sap.com/#/notes/1820529) 中所述的不适用于备份和恢复的网络文件系统

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>如何验证 SAP HANA 备份的正确性
对不同系统运行测试还原是绝对必要的，这与备份方法无关。 使用这种方案可以确保备份正确，并且内部的备份和还原过程能够按预期方式运行。 尽管还原备份可能会因为其基础结构要求而成为一项本地障碍，但通过暂时提供用于此目的的必要资源，在云中完成要容易得多。 HANA 提供的工具确实可以检查备份文件的还原能力。 但是，频繁还原练习的目的是测试数据库还原过程，并与操作人员一起训练该过程。

请注意，执行简单的还原并检查 HANA 是否正常运行并不足够。 应该运行表一致性检查，确保还原的数据库正常。 SAP HANA 提供 [SAP 说明 1977584](https://launchpad.support.sap.com/#/notes/1977584) 中所述的多种一致性检查。

在 SAP 网站上的 [Table and Catalog Consistency Checks](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)（表和目录一致性检查）中也能找到有关表一致性检查的信息。

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA 备份相比存储快照的优点和缺点

SAP 不会在 HANA 备份与存储快照之间做出优先选择， 而是列出各自的优点与缺点，使用户能够根据情况和适用的存储技术确定要使用哪个服务（请参阅[规划备份和恢复策略](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/ef085cd5949c40b788bba8fd3c65743e.html)）。

在 Azure 上，请注意这样一个事实：Azure Blob 快照功能无法在多个磁盘上提供文件系统一致性（请参阅[通过 PowerShell 使用 Blob 快照](/archive/blogs/cie/using-blob-snapshots-with-powershell)）。 

此外，必须了解频繁使用 Blob 快照产生的计费影响，如以下文章中所述：[Understanding How Snapshots Accrue Charges](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)（了解快照计费方式）— 与使用 Azure 虚拟磁盘时相比，快照产生的费用不是很明显。

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>创建存储快照时保持 SAP HANA 数据一致性

如前文所述，在创建存储快照时，需要说明 Azure 备份的快照备份功能、文件系统和应用程序一致性。 避免问题的最简单方法是关闭 SAP HANA 甚至整个虚拟机。 这对于生产实例是不可行的。

> [!NOTE]
> 在使用多个数据库容器的部署中对 SAP HANA 进行基于磁盘快照的备份，需要使用 HANA 2.0 SP04 的最低版本
> 

Azure 存储在快照过程中附加到 VM 的多个磁盘或卷上不提供文件系统一致性。 这意味着，快照过程中的应用程序一致性需要由应用程序（在本例中为 SAP HANA 本身）提供。 [SAP 说明 2039883](https://launchpad.support.sap.com/#/notes/2039883) 提供了有关通过存储快照创建 SAP HANA 备份的重要信息。 例如，使用 XFS 文件系统时，必须先运行 xfs\_freeze，才能启动存储快照，以提供应用程序一致性（有关 xfs\_freeze 的详细信息，请参阅 [xfs\_freeze(8) - Linux 手册页](https://linux.die.net/man/8/xfs_freeze)）。

假设某个 XFS 文件系统跨越四个 Azure 虚拟磁盘，以下步骤将提供表示 HANA 数据区域的一致快照：

1. 创建 HANA 数据快照准备
1. 冻结所有磁盘/卷的文件系统（例如，使用 xfs\_freeze）
1. 在 Azure 上创建所有必要的 Blob 快照
1. 解冻文件系统
1. 确认 HANA 数据快照（将删除快照）

使用 Azure 备份的功能执行应用程序一致快照备份时，你需要在快照前脚本中对步骤 #1 编写代码/脚本。 Azure 备份服务将执行步骤 #2 和 #3。 步骤 #4 和 #5 需要再次由快照后脚本中的代码提供。 如果不使用 Azure 备份服务，还需要自行对步骤 #2 和 #3 编写代码/脚本。
有关创建 HANA 数据快照的详细信息，请参阅以下文章：

- [HANA 数据快照]\(https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- 有关执行步骤 #1 的更多详细信息，请参阅[创建数据快照（本机 SQL）](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html)一文 
- 有关在步骤 #5 中确认/删除 HANA 数据快照的详细信息，请参阅[创建数据快照（本机 SQL）](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html)一文 

必须确认 HANA 快照。 由于使用了&quot;写入时复制&quot;，SAP HANA 在处于此快照准备模式时，可能不需要额外的磁盘空间。 此外，只有在确认 SAP HANA 快照后，才能启动新的备份。


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA 备份计划策略

SAP HANA 文章[规划备份和恢复策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)指明了一个基本备份计划。 依赖于 HANA 的相关 SAP 文档，以及使用其他 DBMS 定义 SAP HANA 的备份/还原策略和过程的经验。 不同类型备份的顺序和保持期在很大程度上依赖于你需要提供的 SLA。


### <a name="sap-hana-backup-encryption"></a>SAP HANA 备份加密

SAP HANA 提供数据和日志加密。 如果 SAP HANA 数据和日志未加密，则默认备份也不加密。 但是，SAP HANA 提供了一个单独的备份加密，如 [SAP HANA 备份加密](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html)中所述。 如果你运行的是较旧版本的 SAP HANA，则可能需要检查备份加密是否属于已提供的功能。  


## <a name="next-steps"></a>后续步骤
* [文件级别的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)介绍了基于文件的备份选项。
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
