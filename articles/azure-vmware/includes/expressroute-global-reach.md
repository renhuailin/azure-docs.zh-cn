---
title: Azure VMware 解决方案网络和连接
description: Azure VMware 解决方案网络和连接说明。
ms.topic: include
ms.date: 08/10/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 569beec244e2ccf80aae3e4bb3f7a3e2cf65980a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070712"
---
<!-- Used in articles\azure-vmware\introduction.md and articles\azure-vmware\concepts-networking.md 

articles\azure-vmware\includes\azure-vmware-solution-networking-description.md

-->

[ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md) 用于将私有云连接到本地环境。 它直接在 Microsoft Enterprise Edge (MSEE) 级别连接线路，因此要实现该连接，你的订阅中必须存在一个使用 ExpressRoute 线路（连接到本地）的虚拟网络。  原因是 vNet 网关（ExpressRoute 网关）无法传输流量，这意味着可以将两条线路附加到同一个网关，但网关不会将流量从一条线路发送到另一条线路。

>[!NOTE]
>对于未启用 ExpressRoute Global Reach（例如，由于本地法规）的位置，必须使用 Azure IaaS VM 构建路由解决方案。 若要查看一些示例，请参阅 [AzureCAT-AVS/networking](https://github.com/Azure/AzureCAT-AVS/tree/main/networking)。

