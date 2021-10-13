---
title: Azure VMware 解决方案网络和连接
description: Azure VMware 解决方案网络和连接说明。
ms.topic: include
ms.date: 08/10/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: add146bb5b64e12b09bec89c080e1222154d4e2f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638206"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md) 用于将私有云连接到本地环境。 它直接在 Microsoft Enterprise Edge (MSEE) 级别连接线路。 要实现该连接，你的订阅中必须存在一个使用 ExpressRoute 线路（连接到本地）的虚拟网络 (vNet)。  原因是 vNet 网关（ExpressRoute 网关）无法传输流量，这意味着可以将两条线路附加到同一个网关，但网关不会将流量从一条线路发送到另一条线路。

每个 Azure VMware 解决方案环境是其自己的 ExpressRoute 区域（其自己的虚拟 MSEE 设备），这使你能够将 Global Reach 连接到“本地”对等互连位置。  这使你能够将一个区域中的多个 Azure VMware 解决方案实例连接到同一对等互连位置。 

>[!NOTE]
>对于未启用 ExpressRoute Global Reach（例如，由于本地法规）的位置，必须使用 Azure IaaS VM 构建路由解决方案。 若要查看一些示例，请参阅 [AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking)。

