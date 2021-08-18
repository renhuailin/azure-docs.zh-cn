---
title: BareMetal 基础结构上适用于 Oracle 的 SnapCenter 集成
description: 了解如何使用 Oracle 和 NetApp 的快照技术为 BareMetal 基础结构上的 Oracle 数据库创建操作恢复备份。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: d9cf6933287167da82b1a5ad4ab8848d5c34d404
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579882"
---
# <a name="snapcenter-integration-for-oracle-on-baremetal-infrastructure"></a>BareMetal 基础结构上适用于 Oracle 的 SnapCenter 集成

本操作指南帮助你使用 Oracle 和 NetApp 的快照技术为 Oracle 数据库创建操作恢复备份。 使用快照只是 Oracle 的多种数据保护方法中的一种。 在 BareMetal 基础结构上运行 Oracle 数据库时，快照可以减少停机时间和数据丢失。 

>[!IMPORTANT]
>SnapCenter 支持将 Oracle 用于 SAP 应用程序，但它不提供 SAP BR\*工具集成。

尽管 Oracle 为快照提供两种不同的备份方法，但 NetApp 的 SnapCenter 仅支持一种方法：热备份模式。 热备份模式是备份和创建 Oracle 快照的传统方式。 它需要与 Oracle 主机交互，以便暂时将数据库置于热备份模式，从而正确编录存档日志。 在热备份模式下还可以更好地与 RMAN 数据库交互，以便更密切地跟踪可用于恢复的快照。 

本指南中的文章可引导你在热备份模式下创建适用于 Oracle 的操作恢复和灾难恢复框架。 灾难恢复是指在发生灾难后恢复数据库或数据库的一部分。 例如，在驱动器出错或数据库损坏的情况下进行恢复。 操作恢复是指在发生非灾难性的状况后进行恢复。 例如，在丢失了几行不影响业务的数据的情况下进行恢复。

## <a name="snapcenter-database-organization"></a>SnapCenter 数据库组织方式
SnapCenter 将数据库组织成资源组。 资源组可以是采用相同保护策略的一个或多个数据库。 因此，你无需选择要包含在备份中的各个卷。

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-database-resource-group.png" alt-text="显示 SnapCenter 如何将数据库组织成资源组的示意图" border="false":::

## <a name="oracle-disaster-recovery-architecture"></a>Oracle 灾难恢复体系结构

Oracle 热备份划分为两种不同的备份，在还原数据文件后可以使用存档日志前滚。 数据文件和控制文件的保护每小时都会进行，但也可以接受更长的备份频率。 备份间隔越长，恢复时间就越长。  

SnapCenter 在默认的数据文件位置 (nfs01) 创建数据库的本地快照。 快照是在承载数据文件或控制文件的每个文件系统上创建的。 这些备份确保能够快速恢复数据库。 在发生多磁盘故障或站点故障时，它无法保护数据。 

“每小时”快照数取决于设置的备份策略。 Oracle 数据库具备在 2-5 天内从快照进行操作恢复的能力。
 
必须存在足够多的存档日志，并且必须使用这些日志从最新且有效的数据文件恢复点前滚 Oracle 数据库。 使用存档日志的快照可以降低 Oracle 数据库的恢复点目标 (RPO)。 针对存档日志生成快照的频率越短，RPO 就越低。 建议的存档日志快照间隔为 5 分钟或 15 分钟。 较短的 5 分钟间隔可实现最短的 RPO。  较短的间隔会提高复杂性，因为在恢复过程中必须管理更多的快照。

## <a name="next-steps"></a>后续步骤

了解如何设置 NetApp SnapCenter 以将流量从 Azure 路由到 Oracle BareMetal 基础结构服务器。

> [!div class="nextstepaction"]
> [设置 SnapCenter 以路由流量](set-up-snapcenter-to-route-traffic.md)
