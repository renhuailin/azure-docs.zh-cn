---
title: Azure NetApp 文件的服务级别 | Microsoft Docs
description: 介绍 Azure NetApp 文件的服务级别的吞吐量性能。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 7eac6a40476cffe875a03de49c9c9311ffbf4d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017060"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp 文件的服务级别
服务级别是容量池的属性。 根据分配给卷的配额，为容量池中的卷允许的最大吞吐量定义和区分服务级别。

## <a name="supported-service-levels"></a>支持的服务级别

Azure NetApp 文件支持三种服务级别：超级、高级和标准。 

* <a name="Ultra"></a>超级存储

    超级存储层提供每 1 TiB 预配容量高达 128 MiB/秒的吞吐量。 

* <a name="Premium"></a>高级存储

    高级存储层提供每 1 TiB 预配容量高达 64 MiB/秒的吞吐量。 

* <a name="Standard"></a>标准存储

    标准存储层提供每 1 TiB 预配容量高达 16 MiB/秒的吞吐量。

## <a name="throughput-limits"></a>吞吐量限制

卷的吞吐量限制由以下因素的组合确定：
* 卷所属的容量池的服务级别
* 分配给卷的配额  
* 容量池的 QoS 类型（自动或手动）  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>吞吐量限制了自动 QoS 容量池中的卷的示例

下图显示了自动 QoS 容量池中卷的吞吐量限制示例：

![服务级别说明](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* 在示例 1 中，自动 QoS 容量池（高级存储层分配到 2 TiB 配额）中的一个卷将分配限制为 128 MiB/秒（2 TiB * 64 MiB/秒）的吞吐量。 无论容量池大小或实际卷消耗情况如何，此方案均适用。

* 在示例 2 中，自动 QoS 容量池（高级存储层分配到 100 GiB 配额）中的一个卷将分配限制为 6.25 MiB/秒（0.09765625 TiB * 64 MiB/秒）的吞吐量。 无论容量池大小或实际卷消耗情况如何，此方案均适用。

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>吞吐量限制了手动 QoS 容量池中的卷的示例 

如果使用手动 QoS 容量池，可以单独为卷分配容量和吞吐量。 在手动 QoS 容量池中创建卷时，可以指定吞吐量（MiB/秒）值。 分配给手动 QoS 容量池中的卷的总吞吐量取决于池的大小和服务级别。 上限为（TiB x 服务级别吞吐量/TiB 中的容量池大小）。 例如，具有“超级”服务级别的 10-TiB 容量池可为卷提供 1280 MiB/秒 (10 TiB x 128 MiB/s/TiB) 的总吞吐容量。

例如，对于 SAP HANA 系统，此容量池可用于创建以下卷。 每个卷都提供单独的大小和吞吐量，以满足应用程序的要求：

* SAP HANA 数据卷：大小为 4 TiB，最多 704 MiB/秒
* SAP HANA 日志卷：大小为 0.5 TiB，最多 256 MiB/秒
* SAP HANA 数据卷：大小为 1 TiB，最多 64 MiB/秒
* SAP HANA 日志卷：大小为 4.5 TiB，最多 256 MiB/秒

下图说明了适用于 SAP HANA 卷的方案：

![QoS SAP HANA 卷方案](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md) 
- [创建容量池](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp 文件的服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/netapp/)
- [动态更改卷的服务级别](dynamic-change-volume-service-level.md) 
- [跨区域复制的服务级别目标](cross-region-replication-introduction.md#service-level-objectives)
