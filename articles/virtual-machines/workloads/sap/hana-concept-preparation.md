---
title: Azure 上的 SAP HANA（大型实例）的灾难恢复原则和准备工作 | Microsoft Docs
description: 熟悉 Azure 上的 SAP HANA（大型实例）的灾难恢复原则和准备工作。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79991bcdd5174d2655f3c917d0898b0b87b0ca50
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217064"
---
# <a name="disaster-recovery-principles-and-preparation"></a>灾难恢复原则和准备工作

本文将讨论 HANA 大型实例（也称为 BareMetal 基础结构）的重要灾难恢复 (DR) 原则。 我们将逐步介绍为灾难恢复做好准备所需执行的步骤。 你还将了解如何在灾难中实现恢复时间目标 (RTO) 和恢复点目标 (RPO)。 

## <a name="dr-principles-for-hana-large-instances"></a>HANA 大型实例的 DR 原则

HANA 大型实例提供不同 Azure 区域中 HANA 大型实例戳之间的灾难恢复功能。 例如，假设在 Azure 美国西部区域部署 HANA 大型实例。 然后可将美国东部区域中的 HANA 大型实例用作灾难恢复单元。 不会自动配置灾难恢复，因为你需要为 DR 区域中的另一个 HANA 大型实例付费。 灾难恢复设置适用于纵向扩展和横向扩展设置。 

大多数客户使用 DR 区域中的单元运行非生产系统，这些系统使用已安装的 HANA 实例。 HANA 大型实例的 SKU 应与用于生产目的的 SKU 相同。 下图显示 Azure 生产区域与灾难恢复区域中的服务器单元之间的磁盘配置：

![从磁盘角度的 DR 设置配置](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

如此概览图中所示，需要订购另一组磁盘卷。 与 DR 站点中的 HANA 大型实例服务器关联的目标磁盘卷的大小与生产卷的大小相同。 

以下卷从生产区域复制到 DR 站点：

- /hana/data
- /hana/logbackups 
- /hana/shared（包括 /usr/sap）

不会复制 /hana/log 卷。 在从这些卷还原时不需要 SAP HANA 事务日志。

## <a name="hana-large-instance-storage-replication"></a>HANA 大型实例存储复制 

HANA 大型实例基础结构中的 DR 功能基于其存储复制。 存储卷发生更改时，在存储端使用的功能并不是以异步方式复制的恒定更改流。 它是一种依赖于定期创建这些卷的快照的机制。 然后，已复制的快照和尚未复制的新快照之间的差量会转移到 DR 站点中的目标磁盘卷。 这些快照存储在这些卷上。 如果发生灾难恢复故障转移，需要在这些卷上还原这些快照。  

卷中完整数据的首次传输应发生在数据量小于快照之间的差量之前。 然后，DR 站点中的卷将包含在生产站点中拍摄的所有卷快照。 最终可以使用该 DR 系统回到较早的状态，从而复原丢失的数据而无需回滚生产系统。

如果对于 MCOD 部署，一个 HANA 大型实例上有多个独立的 SAP HANA 实例，所有的 SAP HANA 实例应将存储复制到 DR 端。

在生产站点中使用 HANA 系统复制实现高可用性，并对 DR 站点使用基于存储的复制时，会将两个节点的卷从主站点复制到 DR 实例。 在 DR 站点购买额外存储（大小与主节点相同）以容纳从主节点和次要节点到 DR 的复制。 

>[!NOTE]
>HANA 大型实例存储复制功能会复制存储快照并为其建立镜像。 如果不按照[备份和还原](hana-backup-restore.md)中介绍的方式拍摄存储快照，则无法复制到 DR 站点。 执行存储快照是将存储复制到灾难恢复站点的先决条件。

## <a name="preparation-of-the-disaster-recovery-scenario"></a>灾难恢复场景的准备
此 DR 场景假设在生产 Azure 区域中的 HANA 大型实例上运行了一个生产系统。 对于下面的步骤，假设该 HANA 系统的 SID 为“PRD”。 另外，DR Azure 区域中的 HANA 大型实例上正在运行一个非生产系统。 其 SID 为“TST”。 下图显示了此配置：

![DR 设置开始](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

假设尚未订购服务器实例（已设置额外存储卷集）。 然后，Azure 上的 SAP HANA 服务管理会附加额外的卷。 它们是运行 TST HANA 实例的 HANA 大型实例的生产副本的目标。 你将需要提供生产 HANA 实例的 SID。 在 Azure 上的 SAP HANA 服务管理部门确认已附加这些卷后，你需要将这些卷装载到 HANA 大型实例上。

![DR 设置后续步骤](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

下一步是在 DR Azure 区域中运行 TST HANA 实例的 HANA 大型实例上安装第二个 SAP HANA 实例。 新安装的 SAP HANA 实例需有相同的 SID。 创建的用户需要与生产实例具有相同的 UID 和组 ID。 有关详细信息，请阅读[备份和还原](hana-backup-restore.md)。 如果安装成功，需要：

- 执行[备份和还原](hana-backup-restore.md)中介绍的存储快照准备工作的步骤 2。
- 如果之前未执行该步骤，请为 HANA 大型实例的 DR 单元创建公钥。 请参阅[备份和还原](hana-backup-restore.md)中介绍的存储快照准备工作的步骤 3。
- 使用新的 HANA 实例维护 HANABackupCustomerDetails.txt 并测试是否可正确连接到存储。  
- 在 DR Azure 区域中的 HANA 大型实例上停止新安装的 SAP HANA 实例。
- 卸载这些 PRD 卷并联系 Azure 上的 SAP HANA 服务管理部门。 卷无法一直保持在单元上，因为它们在作为存储复制目标运行时不可访问。  

![关系图显示生产 Azure 区域中的 PRD 卷与 DR Azure 区域中的 PRD 卷之间的复制关系。](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

运营团队在生产区域中的 PRD 卷与 DR 区域中的 PRD 卷之间建立复制关系。

>[!IMPORTANT]
>不会复制 /hana/log 卷，因为在灾难恢复站点中将复制的 SAP HANA 数据库还原到一致状态时不需要该卷。

接下来，设置存储快照备份计划，以在发生灾难时实现 RTO 和 RPO。 若要最大程度降低 RPO，请在 HANA 大型实例服务中设置以下复制间隔：
- 组合快照（快照类型为 **hana**）涵盖的卷每隔 15 分钟复制到灾难恢复站点中的等效存储卷目标。
- 对于事务日志备份卷（快照类型为 **logs**），请设置为每隔 3 分钟复制到灾难恢复站点中的等效存储卷目标。

将 RPO 降到最低：
- 每 30 分钟至 1 小时拍摄一个 hana 类型存储快照。 有关详细信息，请参阅[使用 Azure 应用程序一致性快照工具进行备份](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)。
- 每 5 分钟进行一次 SAP HANA 事务日志备份。
- 每 5-15 分钟拍摄一次 logs 类型存储快照。 使用此间隔周期，RPO 应能达到大约 15-25 分钟。

如果使用此设置，事务日志备份序列、存储快照以及 HANA 事务日志备份卷、/hana/data 和 /hana/shared（包括 /usr/sap）的复制可能如下图中的数据所示：

 ![事务日志备份快照和时间轴上的快照镜像之间的关系](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

若要在灾难恢复方案中实现更好的 RPO，可将 Azure 上的 SAP HANA（大型实例）中的 HANA 事务日志备份复制到其他 Azure 区域。 若要进一步降低 RPO，请执行以下步骤：

1. 以尽可能高的频率将 HANA 事务日志备份到 /hana/logbackups。
1. 使用 rsync 将事务日志备份复制到 NFS 共享托管的 Azure 虚拟机上。 这些虚拟机 (VM) 位于 Azure 生产区域和 DR 区域中的 Azure 虚拟网络中。 将这两个 Azure 虚拟网络都连接到线路，该线路将 HANA 生产大型实例连接到 Azure。 有关详细信息，请参阅[使用 HANA 大型实例实现灾难恢复的网络注意事项](hana-overview-high-availability-disaster-recovery.md#network-considerations-for-disaster-recovery-with-hana-large-instances)。 
1. 将事务日志备份保留在附加到 NFS 导出存储的 VM 区域中。
1. 在灾难故障转移方案中，使用在 DR 站点中 NFS 共享上最近创建的事务日志备份去补充在 /hana/logbackups 卷上找到的事务日志备份。 
1. 对可保存到 DR 区域的最新备份执行事务日志备份还原。

当 HANA 大型实例操作确认正在设置复制关系并启动执行存储快照备份时，即表示已开始复制数据。

![建立复制之前的 DR 设置步骤](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

在复制过程中，不会还原 DR Azure 区域中的 PRD 卷上的快照。 只会存储这些快照。 如果以这种状态装载卷，则呈现在 DR Azure 区域中的服务器上安装 PRD SAP HANA 实例后卸载这些卷时的状态。 此外，这些卷还会呈现尚未还原的存储备份。

如果发生故障转移，还可以选择还原到较旧的存储快照而不是最新的存储快照。

## <a name="next-steps"></a>后续步骤

了解灾难恢复故障转移过程。

> [!div class="nextstepaction"]
> [灾难恢复故障转移过程](hana-failover-procedure.md)
