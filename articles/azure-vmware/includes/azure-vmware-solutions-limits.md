---
title: Azure VMware 解决方案限制
description: Azure VMware 解决方案限制。
ms.topic: include
ms.date: 09/02/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 7a8479a5262c1db61dbb03558632ebebac1a6983
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638157"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md and concepts-networking.md -->

下表介绍了 Azure VMware 解决方案的最大限制。

| **资源** | **限制** |
| :-- | :-- |
| 每个私有云的群集数 | 12 |
| 每个群集的最小主机数 | 3 |
| 每个群集的最大主机数 | 16 |
| 每个私有云的主机数 | 96 |
| 每个私有云的 vCenter 数 | 1  |
| HCX 站点配对数 | 25（任何版本） |
| Azure VMware 解决方案 ExpressRoute 最大链接私有云数 | 4<br />使用的虚拟网络网关确定实际最大链接私有云数。  有关详细信息，请参阅[关于 ExpressRoute 虚拟网络网关](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| Azure VMware 解决方案 ExpressRoute 端口速度 | 10 Gbps<br />使用的虚拟网络网关决定了实际带宽。 有关详细信息，请参阅[关于 ExpressRoute 虚拟网络网关](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| 通过 vWAN 公开的公共 IP 数 | 100 |
| vSAN 容量限制 | 75% 的总可用容量（保留 25% 用于 SLA）  |

有关其他 VMware 特定的限制，请使用 [VMware 配置最大值工具！](https://configmax.vmware.com/)。
