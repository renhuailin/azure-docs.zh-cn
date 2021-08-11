---
title: Azure VMware 解决方案限制
description: Azure VMware 解决方案限制。
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: ce0f346c66c7eb982f0891b82e710affdcc956a8
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109719235"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md and concepts-networking.md -->

下表介绍了 Azure VMware 解决方案的最大限制。

| **资源** | **限制** |
| :-- | :-- |
| 每个私有云的群集数 | 12 |
| 每个群集的最小节点数 | 3 |
| 每个群集的最大节点数 | 16 |
| 每个私有云的节点数 | 96 |
| 每个私有云的 vCenter 数 | 1  |
| HCX 站点配对数 | 高级版 3 个，企业版 10 个 |
| AVS ExpressRoute 最大链接私有云数 | 4<br />使用的虚拟网络网关确定实际最大链接私有云数。  有关详细信息，请参阅[关于 ExpressRoute 虚拟网络网关](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| AVS ExpressRoute 端口速度 | 10 Gbps<br />使用的虚拟网络网关决定了实际带宽。 有关详细信息，请参阅[关于 ExpressRoute 虚拟网络网关](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| 通过 vWAN 公开的公共 IP 数 | 100 |
| vSAN 容量限制 | 75% 的总可用容量（保留 25% 用于 SLA）  |

有关其他 VMware 特定限制，请使用 [VMware 配置最大值工具！](https://configmax.vmware.com/)。
