---
title: 主机的磁盘功能
description: 用于生成或缩放群集的主机来自独立的主机池。
ms.topic: include
ms.date: 04/23/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 847f69950a47208b27976972da8dc3427727e774
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747611"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-cloud-clusters.md -->


Azure VMware 解决方案群集基于超融合裸机基础结构。 下表显示了主机的 RAM、CPU 和磁盘容量。

| 主机类型 | CPU   | RAM (GB)  | vSAN NVMe 缓存层（TB，原始）  | vSAN SSD 容量层（TB，原始）  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  双 Intel 18 核心 2.3 GHz  |     576      |                3.2               |                15.20               |

用于生成或缩放群集的主机来自独立的主机池。 这些主机已通过硬件测试，并已安全删除所有数据。 