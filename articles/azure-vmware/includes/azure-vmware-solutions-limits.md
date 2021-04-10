---
title: Azure VMware 解决方案限制
description: Azure VMware 解决方案限制。
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622267"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

下表介绍了 Azure VMware 解决方案的最大限制。

| **资源** | **限制** |
| :-- | :-- |
| 每个私有云的群集数 | 12 |
| 每个群集的最小节点数 | 3 |
| 每个群集的最大节点数 | 16 |
| 每个私有云的节点数 | 64 |
| 每个私有云的 vCenter 数 | 1  |
| HCX 站点配对数 | 高级版 3 个，企业版 10 个 |
| AVS ExpressRoute 最大链接 SDDC 数 | 4 |
| AVS ExpressRoute 端口速度 | 10 Gbps<br />使用的虚拟网络网关决定了实际带宽。 有关详细信息，请参阅[关于 ExpressRoute 虚拟网络网关](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| 通过 vWAN 公开的公共 IP 数 | 100 |
| vSAN 容量限制 | 75% 的总可用容量（保留 25% 用于 SLA）  |

有关其他 VMware 特定限制，请使用 [VMware 配置最大值工具！](https://configmax.vmware.com/)。
