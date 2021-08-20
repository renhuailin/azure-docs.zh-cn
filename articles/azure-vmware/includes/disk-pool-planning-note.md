---
title: 关于 vNet 的磁盘池规划说明
description: 重要说明：关于将 vNet 部署到更接近 Azure VMware 解决方案主机的重要性。
ms.topic: include
ms.date: 07/14/2021
ms.openlocfilehash: 39bcf823a71852396c1c05db4902abf000433499
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640558"
---
如果计划使用[Azure 磁盘池](../../virtual-machines/disks-pools.md)缩放 Azure VMware 解决方案主机，请务必将 vNet 部署到接近具有 ExpressRoute 虚拟网络网关的主机。  存储空间越接近于主机，性能越好。