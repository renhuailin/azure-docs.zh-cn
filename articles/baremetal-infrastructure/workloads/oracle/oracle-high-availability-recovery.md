---
title: 在 Azure BareMetal 基础结构上恢复 Oracle 数据库
description: 了解如何在 Azure BareMetal 基础结构上恢复 Oracle 数据库。
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: fc7464474d01314a52a77e0f28b1df160a9f42a0
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589713"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>在 Azure BareMetal 基础结构上恢复 Oracle 数据库

尽管单一技术无法防范所有故障情形，但合并功能使数据库管理员能够在几乎所有情形下恢复其数据库。

## <a name="causes-of-database-failure"></a>数据库故障的原因

数据库故障可能会由于多种原因而发生，但通常分为以下几个类别：

- 数据操作错误。
- 丢失联机重做日志。
- 丢失数据库控制文件。
- 丢失数据库数据文件。
- 物理数据损坏。

## <a name="choose-your-method-of-recovery"></a>选择恢复方法

恢复的类型取决于故障的类型。 假设删除了对象或错误地修改了数据。 此时最快的解决方案通常是执行闪回数据库操作。 在其他情况下，通过 Azure NetApp 文件快照进行恢复可能会提供所需的恢复。 下图的决策树表示常见故障和恢复方案（如果已实现上述所有数据保护选项）。

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="数据库恢复决策树的图。" lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

请注意，此示例决策树只是从数据库管理员的角度进行查看。 每个部署可能具有不同的要求，这些要求可能会更改选择顺序。 例如，通过 Data Guard 执行数据库角色切换到其他区域可能会对应用程序性能产生负面影响。 它可以为快照恢复方法提供较低 RTO。 若要确保满足 RTO/RPO 要求，建议执行这些操作并创建记录的过程，以便在需要时执行它们。

## <a name="next-steps"></a>后续步骤

详细了解 BareMetal 基础结构：

- [什么是 Azure 中的 BareMetal 基础结构？](../../concepts-baremetal-infrastructure-overview.md)
- [在 Azure 中连接 BareMetal 基础结构实例](../../connect-baremetal-infrastructure.md)
