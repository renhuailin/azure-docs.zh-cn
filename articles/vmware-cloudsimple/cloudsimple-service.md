---
title: Azure VMware 解决方案（按 CloudSimple）
description: 了解 CloudSimple 服务的概述。 通过创建服务，可以购买节点、预留节点并创建私有云。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8f32197eda4fc7632e883fd21dd6e1caa0dbd24b
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898736"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服务概述

CloudSimple 服务允许 CloudSimple 使用 Azure VMware 解决方案。  通过创建服务，可以购买节点、预留节点并创建私有云。  在 CloudSimple 服务可用的每个 Azure 区域中创建 CloudSimple 服务。 该服务通过 CloudSimple 定义 Azure VMware 解决方案的边缘网络。 边缘网络支持包含 VPN、ExpressRoute 和 internet 连接到私有云的服务。

## <a name="gateway-subnet"></a>网关子网

网关子网是每个 CloudSimple 服务所必需的，并且对其创建的区域是唯一的。 网关子网是在创建边缘网络时使用的，需要一个/28 个 CIDR 块。  网关子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。 与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。  创建网关子网后，无法将其删除。  删除服务后，会删除网关子网。

## <a name="next-steps"></a>后续步骤

* 了解如何 [在 Azure 上创建 CloudSimple 服务](quickstart-create-cloudsimple-service.md)。
