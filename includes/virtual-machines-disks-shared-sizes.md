---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/03/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6d7d9389e00b3b0f23d590563a242ae0c12e2463
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740100"
---
目前只有超级磁盘和高级 SSD 可以启用共享磁盘。 不同的磁盘大小可能具有不同的 `maxShares` 限制，设置 `maxShares` 值时不能超过此限制。 对于高级 SSD，支持共享其磁盘的磁盘大小为 P15 及以上大小。

对于每个磁盘，你可以定义一个 `maxShares` 值，该值表示可同时共享该磁盘的最大节点数。 例如，如果计划设置节点数为 2 的故障转移群集，可以设置 `maxShares=2`。 最大值是一个上限。 只要节点数低于指定 `maxShares` 值，节点就可以加入或退出群集（装载或卸载磁盘）。

> [!NOTE]
> 仅当磁盘与所有节点分离时，才能设置或编辑 `maxShares` 值。

### <a name="premium-ssd-ranges"></a>高级 SSD 范围

下表按高级 SSD 大小说明了允许的 `maxShares` 最大值：

|磁盘大小  |maxShares 限制  |
|---------|---------|
|P1、P2、P3、P4、P6、P10、P15、P20     |3         |
|P30、P40、P50     |5         |
|P60、P70、P80     |10         |

磁盘的 IOPS 和带宽限制不受 `maxShares` 值的影响。 例如，无论 maxShares = 1 还是 maxShares > 1，P15 磁盘的最大 IOPS 都是 1100。

### <a name="standard-ssd-ranges"></a>标准 SSD 范围

下表按标准 SSD 大小说明了允许的 `maxShares` 最大值：

|磁盘大小  |maxShares 限制  |
|---------|---------|
|E1、E2、E3、E4、E6、E10、E15、E20     |3         |
|E30、E40、E50     |5         |
|E60、E70、E80     |10         |

磁盘的 IOPS 和带宽限制不受 `maxShares` 值的影响。 例如，无论 maxShares = 1 还是 maxShares > 1，E15 磁盘的最大 IOPS 都是 500。

### <a name="ultra-disk-ranges"></a>超级磁盘范围

`maxShares` 最小值为 1，`maxShares` 最大值为 5。 超级磁盘没有大小限制，任何大小的超级磁盘都可以使用 `maxShares` 的任何值，上限为最大值（包括最大值）。