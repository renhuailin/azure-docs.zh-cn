---
title: 适用于 Oracle 工作负载的 BareMetal SKU
description: 了解适用于 Oracle BareMetal 基础结构工作负载的 SKU。
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 42ff26b9ea9bcc022f1ddbf3dddcb041b2cea3a2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588847"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>适用于 Oracle 工作负载的 BareMetal SKU

在本文中，我们会了解适用于 Oracle 工作负载的专用 BareMetal 基础结构 SKU。

适用于 Oracle 的 BareMetal 基础结构 SKU 范围是从两个插槽到最多四个插槽。 还可以从各种 CPU 核心和内存大小中进行选择，以满足工作负载的要求。 下表汇总了可用 SKU 的功能。
 
| **Oracle 认证** **硬件** | **Model** | **内存总量** | **存储** | **可用性** |
| --- | --- | --- | --- | --- |
| 是 | Azure SAP HANA S32m - 2 x Intel® Xeon® I6234 处理器 16 CPU 核心数和 32 CPU 线程 | 1.5 TB | --- | 可用 |
| 是 | Azure SAP HANA S64m - 4 x Intel® Xeon® I6234 处理器 32 CPU 核心数和 64 CPU 线程 | 3.0 TB | --- | 可用 |
| 是 | Azure SAP HANA S96 - 2 x Intel® Xeon® E7-8890 v4 处理器 48 CPU 核心数和 96 CPU 线程 | 768 GB | 3.0 TB | 可用 |
| 是 | Azure SAP HANA S224 - 4 x Intel® Xeon® Platinum 8276 处理器 112 CPU 核心数和 224 CPU 线程 | 3.0 TB | 6.3 TB | 可用 |
| 是 | Azure SAP HANA S224m– - 4 x Intel® Xeon® Platinum 8276 处理器 112 CPU 核心数和 224 CPU 线程 | 6.0 TB | 10.5 TB | 可用 |

- CPU 核心数 = 服务器单元的非超线程 CPU 核心数的总和（物理处理器的总数）。 
- CPU 线程数 = 服务器单元的超线程 CPU 核心所提供的计算线程总和（逻辑处理器总数）。 大多数单元都默认配置为使用超线程技术。
- 服务器专用于客户。
- 客户具有根访问权限（无虚拟机监控程序）。
- 服务器不直接处于 Azure VNET 上。

## <a name="next-steps"></a>后续步骤

了解适用于 Oracle 的 BareMetal 基础结构提供的存储。

> [!div class="nextstepaction"]
> [适用于 Oracle 工作负载的 BareMetal 上的存储](oracle-baremetal-storage.md)
