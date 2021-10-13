---
title: 关于 vNet 的磁盘池规划说明
description: 重要说明：关于将 vNet 部署到更接近 Azure VMware 解决方案主机的重要性。
ms.topic: include
ms.date: 07/14/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 04bd1b8b4faf697084dad49fa927394deaad3572
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638357"
---
如果计划使用 [Azure 磁盘池](../../virtual-machines/disks-pools.md)缩放 Azure VMware 解决方案主机，请务必将 vNet 部署到接近具有 ExpressRoute 虚拟网络网关的主机。 存储空间越接近于主机，性能越好。
