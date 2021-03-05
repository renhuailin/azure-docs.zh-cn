---
title: Azure VMware 解决方案限制
description: Azure VMware 解决方案限制。
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193596"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

下表描述了 Azure VMware 解决方案的最大限制。

| **资源** | **限制** |
| :-- | :-- |
| 每个私有云的群集 | 4 |
| 每个群集的最小节点数 | 3 |
| 每个群集的最大节点数 | 16 |
| 每个私有云的节点数 | 64 |
| 每个私有云的 vCenter | 1  |
| HCX 站点对 | 3 with Advanced edition，10企业版 |
| AVS ExpressRoute 最大链接 SDDCs | 4 |
| AVS ExpressRoute portspeed | 10 Gbps | 
| 通过 vWAN 公开的公共 Ip | 100 |
| vSAN 容量限制 | 75% 的可用 (保留适用于 SLA 的 25%)   |

有关其他 VMware 特定限制，请使用 [vmware 配置最大值工具！](https://configmax.vmware.com/)。
