---
title: Azure VMware 解决方案限制
description: Azure VMware 解决方案限制。
ms.topic: include
ms.date: 03/24/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 02840f5d74a1490b0af71933f795385b70467d7b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014607"
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
| HCX 站点配对数 | 高级版 3 个，企业版 10 个 |
| AVS ExpressRoute 最大链接私有云数 | 4<br />使用的虚拟网络网关确定实际最大链接私有云数。  有关详细信息，请参阅[关于 ExpressRoute 虚拟网络网关](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| AVS ExpressRoute 端口速度 | 10 Gbps<br />使用的虚拟网络网关决定了实际带宽。 有关详细信息，请参阅[关于 ExpressRoute 虚拟网络网关](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| 通过 vWAN 公开的公共 IP 数 | 100 |
| vSAN 容量限制 | 75% 的总可用容量（保留 25% 用于 SLA）  |

有关其他 VMware 特定的限制，请使用 [VMware 配置最大值工具！](https://configmax.vmware.com/)。
