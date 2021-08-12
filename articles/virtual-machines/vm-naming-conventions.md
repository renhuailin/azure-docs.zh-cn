---
title: Azure VM 大小命名约定
description: 说明用于 Azure VM 大小的命名约定
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 9c0fcd7923d03ed4086e424e3a8ec86fc74df4af
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2021
ms.locfileid: "111526949"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Azure 虚拟机大小命名约定

本页概述了用于 Azure VM 的命名约定。 VM 使用这些命名约定来表示不同的功能和规格。

## <a name="naming-convention-explanation"></a>命名约定说明

[系列] + [子系列*] + [vCPU 数] + [受约束的 vCPU 数*] + [累加功能] + [加速器类型*] + [版本]      

|值 | 说明|
|---|---|
| 系列 | 指示 VM 系列| 
| *子系列 | 仅用于专门的 VM 区分|
| # 个 vCPU| 表示 VM 的 vCPU 数目 |
| 受约束的 vCPU 数| 仅用于某些 VM 大小。 表示实现[受约束 vCPU 大小](./constrained-vcpu.md)所需的 vCPU 数量 |
| 累加功能 | 一个或多个小写字母表示累加功能，例如： <br> a = 基于 AMD 的处理器 <br> d = 磁盘已满（存在本地临时磁盘）；这适用于较新的 Azure VM，请参阅 [Ddv4 和 Ddsv4 系列](./ddv4-ddsv4-series.md) <br> i = 独立的大小 <br> l = 内存不足；内存量低于内存密集型大小 <br> m = 内存密集型；特定大小的最大内存量 <br> t = 较小内存；特定大小中的最小内存量 <br> s = 支持高级存储，包括可能使用的[超级 SSD](./disks-types.md#ultra-disk)（注意：没有 s 属性的一些较新大小仍可支持高级存储，例如 M128、M64 等）<br> |
| *加速器类型 | 表示专用/GPU SKU 中的硬件加速器的类型。 只有自 2020 年第 3 季度起推出的新的专用/GPU SKU 才能在名称中包含硬件加速器。 |
| 版本 | 表示 VM 系列的版本 |

## <a name="example-breakdown"></a>细分示例

**[系列]**  +  **[子系列*]**  +  **[# 个 vCPU]**  +  **[附加功能]**  +  **[加速器类型*]**  +  **[版本]**

### <a name="example-1-m416ms_v2"></a>示例 1：M416ms_v2

|值 | 说明|
|---|---|
| 系列 | M | 
| # 个 vCPU | 416 |
| 累加功能 | m = 内存密集型 <br> s = 支持高级存储 |
| 版本 | v2 |

### <a name="example-2-nv16as_v4"></a>示例 2：NV16as_v4

|值 | 说明|
|---|---|
| 系列 | N | 
| 子系列 | V |
| # 个 vCPU | 16 |
| 累加功能 | a = 基于 AMD 的处理器 <br> s = 支持高级存储 |
| 版本 | v4 |

### <a name="example-3-nc4as_t4_v3"></a>示例 3：NC4as_T4_v3

|值 | 说明|
|---|---|
| 系列 | N | 
| 子系列 | C |
| # 个 vCPU | 4 |
| 累加功能 | a = 基于 AMD 的处理器 <br> s = 支持高级存储 |
| 加速器类型 | T4 |
| 版本 | v3 |

### <a name="example-4-m8-2ms_v2-constrained-vcpu"></a>示例 4：M8-2ms_v2（受约束 vCPU）

|值 | 说明|
|---|---|
| 系列 | M | 
| # 个 vCPU | 8 |
| 受约束（实际）的 vCPU 数 | 2 |
| 累加功能 | m = 内存密集型 <br> s = 支持高级存储 |
| 版本 | v2 |

## <a name="next-steps"></a>后续步骤

详细了解 Azure 中的可用 [VM 大小](./sizes.md)。
