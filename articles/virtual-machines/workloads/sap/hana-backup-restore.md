---
title: 在 Azure SAP HANA（大型实例）上进行 HANA 备份和还原 | Microsoft Docs
description: 了解如何在 HANA 大型实例上备份和还原 SAP HANA。
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
ms.date: 7/02/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b60ba5c169f660cd01f95f53fbc292a768f1b5e
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358932"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>在 HANA 大型实例上备份和还原 SAP HANA

>[!IMPORTANT]
>本文不会取代 SAP HANA 管理文档或 SAP 说明。 我们假定你具备 SAP HANA 管理和操作方面的专业知识，尤其是涉及到备份、还原、高可用性和灾难恢复主题。 本文中提供的是 SAP HANA Studio 的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。

本文将逐步介绍在 HANA 大型实例（也称为 BareMetal 基础结构）上备份和还原 SAP HANA 的步骤。 

本文中所述的部分流程已简化。 它们并不是要包含在操作手册中的详细步骤。 如果要为配置创建操作手册，请使用你的特定 HANA 版本和发行版本运行并测试你的流程。 之后可记录配置流程。

在操作数据库方面，一个要点是保护数据库免受灾难性事件的影响。 此类事件可能由多种因素导致，包括自然灾难、简单的人为失误，等等。 备份数据库时要确保能够将其还原到任意时间点（例如，某人删除关键数据之前），以此提供至关重要的保护功能。 数据库还原状态可最大限度接近中断之前的状态。

若要具备还原能力，必须执行两种类型的备份：

- 数据库备份：完整备份、增量备份或差异备份
- 事务日志备份

可以在应用程序级别执行完整数据库备份，或者使用存储快照执行备份。 存储快照不能代替事务日志备份。 事务日志备份对于将数据库还原到某个时间点或从已提交的事务中清空日志而言十分重要。 存储快照可以通过快速提供数据库的前滚映像来提升恢复速度。 

Azure 上的 SAP HANA（大型实例）提供两个备份和还原选项：

- 可以使用第三方数据保护工具创建备份。 此工具应能够创建与应用程序一致的快照，或须能够使用后备接口将多个会话流式传输到正确的备份位置。 有若干受支持的工具可供使用。 应与项目团队讨论和设计工具选择方式，以满足客户备份窗口要求。 在项目阶段测试备份和还原过程非常重要。
- 如下节所述，可以将存储快照备份与 Microsoft 提供的实用工具配合使用

>[!NOTE]
>在 HANA2.0 SPS4 推出之前，不支持创建多租户数据库容器数据库（多个租户）的数据库快照。
通过 SPS4，更高版本的 SAP 完全支持此快照功能。



## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 Azure SAP HANA（大型实例）的存储快照

Azure 上的 SAP HANA（大型实例）的底层存储基础结构支持卷的存储快照。 支持备份和还原卷，不过需要注意以下事项：

- 它不是创建完整的数据库备份，而是频繁创建存储卷的快照。
- 在 /hana/data 卷上触发存储快照之前，快照工具 (azacsnap) 启动 SAP HANA 快照。 此 SAP HANA 快照是存储快照还原之后，最终日志还原的一致性点。 
- 要使 HANA 快照成功运行，需要活动的 HANA 实例。 在使用 HANA 系统复制 (HSR) 的应用场景中，无法执行 HANA 快照的当前辅助节点上不支持存储快照。
- 成功运行存储快照后，将删除 SAP HANA 快照
- /hana/shared（包括 /usr/sap）等其他卷无需任何数据库交互即可创建快照

事务日志备份需频繁创建，并存储在 /hana/logbackups 卷或 Azure 中。 可以单独触发包含事务日志备份的 /hana/logbackups 卷来创建快照。 在这种情况下，不需要运行 HANA 数据快照。 /hana/logbackup 中的所有文件均一致处于“脱机”状态，因此可以随时将它们备份到其他备份位置进行存档。
如果因生产中断须将数据库还原到特定时间点，azacsnap 工具可将任何数据快照克隆到新卷，以恢复数据库（首选还原方式）或将快照还原到数据库所在的同一数据卷

>[!NOTE]
> 如果将较旧快照 (snaprevert) 还原到原始数据卷，则会删除所有较新创建的快照。 存储系统正在执行此操作的原因是较新创建的快照的数据点将失效。 应始终先还原最新快照，或最好将快照克隆到新卷。 克隆过程不会删除任何内容。


## <a name="storage-snapshot-considerations"></a>存储快照注意事项

>[!NOTE]
>存储快照使用分配给 HANA 大型实例单元的存储空间。 计划存储快照以及要保留的存储快照数量时，请考虑以下方面。 

Azure 上的 SAP HANA（大型实例）的存储快照的特定机制包括：

- 特定的存储快照（在最初创建时）几乎不消耗存储空间。
- 随着数据内容以及存储卷上 SAP HANA 数据文件内容的不断更改，快照需要存储原始块内容以及数据更改。
- 因此，存储快照的大小会不断增长。 快照保留的时间越长，存储快照就会变得越大。
- 在存储快照的生存期内对 SAP HANA 数据库卷所做的更改越多，存储快照消耗的空间也就越大。

Azure 上的 SAP HANA（大型实例）为 SAP HANA 数据卷和日志卷使用固定的卷大小。 创建这些卷的快照会消耗卷空间。 你需要：

- 确定何时计划存储快照。
- 监视存储卷的空间消耗。 
- 管理所存储的快照数量。 

将大量数据导入 HANA 数据库或对 HANA 数据库进行其他重要更改时，可以禁用存储快照。 


以下部分是有关创建这些快照的信息及常规建议：

- 尽管硬件可以为每个卷保留 255 个快照，但实际保留的快照数应低于这个数字。 建议设为 250 个或更少。
- 创建存储快照之前，请监视并跟踪可用空间。
- 根据可用空间减少存储快照数量。 可以减少保留的快照数量，或者扩展卷。 可以购买更多存储空间（以 1 TB 为单位）。
- 在使用 SAP 平台迁移工具 (R3load) 将数据移入 SAP HANA，或者从备份还原 SAP HANA 数据库或执行其他活动期间，可以禁用 /hana/data 卷上的存储快照。 
- 在对 SAP HANA 表进行重大重组期间，应尽可能避免执行存储快照。
- 存储快照是使用 Azure SAP HANA（大型实例）灾难恢复功能的先决条件。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>使用自助服务存储快照的先决条件

请阅读文档[什么是 Azure 应用程序一致性快照工具](../../../azure-netapp-files/azacsnap-introduction.md)

实现这个工具有两种方法。 
1)  在数据库服务器上进行本地实现
2)  在“备份”VM 上进行远程实现

如果创建备份 VM，请确保该 VM 中安装有最新 HANA 客户端。 使用此方法时，azacsnap 必须能够打开与不同 VM 中运行的 HANA 实例的远程数据库连接。
需要向 Microsoft 支持部门申请 ssh 密钥和存储用户才能访问存储。 若没有 ssh 密钥和存储用户，则不能创建快照。

## <a name="download-and-set-up-azacsnap"></a>下载和设置 azacsnap

若要使用 HANA 大型实例设置存储快照，请首先下载并安装 azacsnap 工具，如 [Azure 应用程序一致性快照工具入门](../../../azure-netapp-files/azacsnap-get-started.md)中所述

Azacsnap 正在创建默认名为 azacsnap 的用户。 如果要使用其他名称，可以在安装过程中指定。 有关详细信息，请参阅上述文档。

## <a name="subsequent-next-steps"></a>后续步骤
按照 azacsnap 的文档执行以下操作：

- [安装 Azure 应用程序一致性快照工具](../../../azure-netapp-files/azacsnap-installation.md)
- [配置 Azure 应用程序一致的快照工具](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [测试 Azure 应用程序一致性快照工具](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [使用 Azure 应用程序一致的快照工具进行备份](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [使用 Azure 应用程序一致性快照工具获取详细信息](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [使用 Azure 应用程序一致性快照工具进行删除](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [使用 Azure 应用程序一致快照工具进行还原](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [使用 Azure 应用程序一致性快照工具进行灾难恢复](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [排查 Azure 应用程序一致性快照工具问题](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [有关使用 Azure 应用程序一致性快照工具的提示和技巧](../../../azure-netapp-files/azacsnap-tips.md)


## <a name="next-steps"></a>后续步骤

请阅读文章[什么是 Azure 应用程序一致性快照工具](../../../azure-netapp-files/azacsnap-introduction.md)

  



