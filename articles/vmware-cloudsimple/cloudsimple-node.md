---
title: Azure VMware Solution by CloudSimple - 节点概述
description: 了解 CloudSimple 概念，包括节点、预配节点、私有云和 VMware Solution by CloudSimple 节点 SKU。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140830"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple 节点概述

节点是私有云的构建基块。 节点是：

* 安装了 VMware ESXi 虚拟机监控程序的专用裸机计算主机  
* 可以预配或保留以创建私有云的计算单元
* 在 CloudSimple 服务可用的区域中可预配或保留的

从预配节点创建私有云。 若要创建私有云，需要至少三个相同 SKU 的节点。 若要扩展私有云，请添加其他节点。  可以向现有群集添加节点，也可以通过在 Azure 门户中预配节点并将其与 CloudSimple 服务关联来创建新群集。  所有预配节点都在 CloudSimple 服务下可见。  

## <a name="provisioned-nodes"></a>预配节点

预配节点提供即用即付容量。 预配节点可帮助你根据需要快速扩展 VMware 群集。 可以根据需要添加节点，也可以删除预配节点以纵向缩减 VMware 群集。 预配节点按月计费，并向在其中预配它们的订阅收取费用。

* 如果通过信用卡为 Azure 订阅付费，则该卡会立即付费。
* 如果采用账单付费的方式，则费用会显示在下次的账单上。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware Solution by CloudSimple 节点 SKU

以下节点类型可预配或预留。

| SKU           | CS28 - 节点                 | CS36 - 节点                 | CS36m - 节点                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| 区域        | 美国东部、美国西部            | 美国东部、美国西部            | 西欧                 |
| CPU           | 2 x 2.2 GHz，28 核 (56 HT) | 2 x 2.3 GHz，36 核 (72 HT) | 2 x 2.3 GHz，36 核 (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| 缓存磁盘    | 1.6-TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 容量磁盘 | 5.625 TB Raw                | 11.25 TB Raw                | 15.36 TB Raw                |
| 存储类型  | 全闪存                   | 全闪存                   | 全闪存                   |

## <a name="limits"></a>限制

以下节点限制适用于私有云。

| 资源 | 限制 |
|----------|-------|
| 要创建私有云的最小节点数 | 3 |
| 私有云群集中的最大节点数 | 16 |
| 私有云中的最大节点数 | 64 |
| 新群集上的最小节点数 | 3 |

## <a name="next-steps"></a>后续步骤

* 了解如何[预配节点](create-nodes.md)
* 了解[私有云](cloudsimple-private-cloud.md)
