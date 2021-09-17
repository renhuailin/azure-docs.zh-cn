---
title: 关于 vNet 的磁盘池规划说明
description: 重要说明：关于将 vNet 部署到更接近 Azure VMware 解决方案主机的重要性。
ms.topic: include
ms.date: 07/14/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: ddee39189fd93d5651e8e879375b977a4222e9fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729049"
---
如果计划使用 [Azure 磁盘池](../../virtual-machines/disks-pools.md)缩放 Azure VMware 解决方案主机，请务必将 vNet 部署到接近具有 ExpressRoute 虚拟网络网关的主机。 存储空间越接近于主机，性能越好。
