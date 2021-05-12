---
title: Avere vFXT for Azure 演示项目
description: 这些示例展示了 Avere vFXT for Azure 的主要功能和用例：视频渲染、高性能计算、vFXT 性能和客户端设置。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 31e32bfc0a2c3279375148bdf3da7d4a4829af1c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "88271067"
---
# <a name="avere-vfxt-demo-projects"></a>Avere vFXT 演示项目

[GitHub](https://github.com/Azure/Avere) 上提供了示例教程。 这些小型项目演示了 Avere vFXT for Azure 的主要功能和用例。

## <a name="video-rendering"></a>视频渲染

* [使用 Azure Batch 和 Avere vFXT 进行渲染](https://github.com/Azure/Avere/blob/master/docs/maya_azure_batch_avere_vfxt_demo.md) - 60 分钟的项目，演示如何在 Azure Batch 和 Avere vFXT 群集中使用 Autodesk Maya 生成动画电影

* [为何要使用 Avere vFXT 来进行渲染？](https://github.com/Azure/Avere/blob/master/docs/why_avere_for_rendering.md) - 这个演示比较了使用 Avere vFXT 群集和不使用 Avere vFXT 群集时网络附加存储的渲染时间

## <a name="high-performance-computing"></a>高性能计算

* [缩短 Azure 虚拟机 (VM) 启动时间的最佳做法](https://github.com/Azure/Avere/blob/master/docs/azure_vm_provision_best_practices.md) - 一种可重现的测试，该测试使用 Avere vFXT for Azure，可以在快速启动数千个计算客户端时缩短启动时间

## <a name="vfxt-performance"></a>vFXT 性能

* [使用 vdbench 测量 vFXT 性能](https://github.com/Azure/Avere/blob/master/docs/vdbench.md) - 用于生成中小型工作负载以测试 vFXT 内存和磁盘子系统的基本测试设置

## <a name="client-setup"></a>客户端设置

* [适用于 Avere vFXT 的 Windows 10 工作站](https://github.com/Azure/Avere/blob/master/docs/windows_10_avere_vfxt_mounted_workstation.md) - 演示如何设置 Windows 工作站并将其安装到 Avere vFXT 群集
