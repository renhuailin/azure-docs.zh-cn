---
title: 主机的磁盘功能
description: 用于生成或缩放群集的主机来自独立的主机池。
ms.topic: include
ms.date: 04/23/2021
ms.openlocfilehash: 7ee15fbfd668d5db24282a6e4de8e4dfc6639c3e
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945734"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-cloud-clusters.md -->


Azure VMware 解决方案群集基于超融合裸机基础结构。 下表显示了主机的 RAM、CPU 和磁盘容量。

| 主机类型 | CPU   | RAM (GB)  | vSAN NVMe 缓存层（TB，原始）  | vSAN SSD 容量层（TB，原始）  |
| :---      | :---: | :---:     | :---:                           | :---:                             |
| AV36     |  双 Intel 18 核心 2.3 GHz  |     576      |                3.2               |                15.20               |

用于生成或缩放群集的主机来自独立的主机池。 这些主机已通过硬件测试，并已安全删除所有数据。 