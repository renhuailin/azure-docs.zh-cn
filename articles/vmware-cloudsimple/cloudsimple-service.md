---
title: Azure VMware Solution by CloudSimple - 服务
description: 通过此概述了解 CloudSimple 服务。 通过创建服务，可以购买节点、预留节点并创建私有云。
author: shortpatti
ms.author: v-patsho
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: acf5180e438d244f2eab791f4a2ebff77df6eaa4
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108182275"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 服务概述

通过 CloudSimple 服务可以使用 Azure VMware Solution by CloudSimple。  通过创建服务，可以购买节点、预留节点并创建私有云。  在 CloudSimple 服务可用的每个 Azure 区域中创建 CloudSimple 服务。 该服务定义 Azure VMware Solution by CloudSimple 的边缘网络。 边缘网络支持的服务包括 VPN、ExpressRoute 和互联网连接到私有云。

## <a name="gateway-subnet"></a>网关子网

网关子网是每个 CloudSimple 服务所必需的，且对其创建区域是唯一的。 网关子网在创建边缘网络时使用，需要 /28 CIDR 块。  网关子网地址空间必须唯一。 它不得与任何与 CloudSimple 环境通信的网络重叠。 与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。  创建网关子网后无法将其删除。  删除服务后，网关子网也会删除。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 上创建 CloudSimple 服务](quickstart-create-cloudsimple-service.md)。
