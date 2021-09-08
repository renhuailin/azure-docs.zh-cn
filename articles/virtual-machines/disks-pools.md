---
title: Azure 磁盘池（预览版）概述
description: 了解 Azure 磁盘池（预览版）。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ea06b3c4fdf9be626df99f4a06f4190ce86df4b3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433271"
---
# <a name="azure-disk-pools-preview"></a>Azure 磁盘池（预览版）

Azure 磁盘池（预览版）是一种 Azure 资源，可让应用程序和工作负荷从单一终结点访问一组托管磁盘。 磁盘池可公开 Internet 小型计算机系统接口 (iSCSI) 目标，以允许通过 iSCSI 对此池中的磁盘进行数据访问。 每个磁盘池可以有一个 iSCSI 目标，并且每个磁盘都可以作为 iSCSI LUN 公开。 可以将磁盘池下的磁盘连接到 Azure VMware 解决方案主机作为数据存储。 这样可以独立于 Azure VMware 解决方案主机来扩展存储。 配置数据存储后，可在其上创建卷，然后将卷附加到 VMware 实例。

## <a name="how-it-works"></a>工作原理

创建实验室时，会自动创建一个资源组。 此托管资源组包含磁盘池操作所需的所有 Azure 资源。 这些资源组的命名约定为：MSP_(resource-group-name)_(diskpool-name)\_(region-name)。

将托管磁盘添加到磁盘池时，磁盘会附加到托管 iSCSI 控制器。 可以将多个托管磁盘作为存储目标添加到磁盘池，每个存储目标都作为磁盘池 iSCSI 目标下的 iSCSI LUN 显示。 磁盘池为 Azure VMware 解决方案提供本机支持。 Azure VMware 解决方案群集可以连接到磁盘池，其将包含该环境中的所有 Azure VMware 解决方案主机。 下图显示了磁盘池如何与 Azure VMware 解决方案配合使用。

:::image type="content" source="media/disks-pools/disk-pool-diagram.png" alt-text="图描述了磁盘池的工作方式，每个超级磁盘可由每个 iSCSI 控制器通过 iSCSI 进行访问，而 Azure VMware 解决方案主机可以通过 iSCSI 访问 iSCSI 控制器。":::

## <a name="restrictions"></a>限制

在预览版中，磁盘池有以下限制：

- 只能将高级 SSD 或超级磁盘添加到磁盘池。
- 当前不支持使用[区域冗余存储 (ZRS)](disks-redundancy.md#zone-redundant-storage-for-managed-disks) 的磁盘。 

### <a name="regional-availability"></a>区域可用性

磁盘池当前在以下区域可用：

- 澳大利亚东部
- 加拿大中部
- 美国中部
- 美国东部
- 美国西部 2
- 日本东部
- 北欧
- 西欧
- 东南亚
- 英国南部


## <a name="billing"></a>计费

部署磁盘池时，有两个主要方面会产生计费成本：

- 添加到磁盘池的磁盘
- 部署在磁盘池附带的托管资源组中的 Azure 资源。 这些资源是：
    - 虚拟机。
    - 托管磁盘。
    - 一个网络接口。
    - 一个用于诊断日志和指标的存储帐户。
        
将对此管理资源组中的资源以及作为实际数据存储的各个磁盘进行计费。 例如，如果给磁盘池添加了一个 P30 磁盘，则将对该 P30 磁盘以及托管资源组中部署的所有资源进行计费。 除这些资源和磁盘外，磁盘池没有额外的服务费用。 有关托管资源组的详细信息，请参阅[工作原理](#how-it-works)一节。

请参阅 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)，了解 VM 和磁盘的区域定价，以评估磁盘池的成本。 如果有，可以在 Azure 预留中考虑磁盘池消耗的 Azure 资源。


## <a name="next-steps"></a>后续步骤

请参阅[磁盘池规划指南](disks-pools-planning.md)。
