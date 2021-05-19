---
title: 渲染概述
description: 介绍如何使用 Azure 进行渲染，并提供 Azure Batch 渲染功能的概述
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98232108"
---
# <a name="rendering-using-azure"></a>使用 Azure 进行渲染

渲染是创建 3D 模型并将其转换为 2D 图像的过程。 在 Autodesk 3ds Max、Autodesk Maya 和 Blender 等应用程序中创作 3D 场景文件。  Autodesk Maya、Autodesk Arnold、Chaos Group V-Ray 和 Blender Cycles 等渲染应用程序可生成 2D 图像。  有时，可以从场景文件创建单一的图像。 但是，常见的操作是建模并渲染多个图像，然后将其组合成动画。

传媒娱乐行业往往使用渲染工作负荷来生成特效 (VFX)。 广告、零售、石油和天然气及制造等其他众多行业也会使用渲染。

渲染过程属于计算密集型工作；要生成的帧/图像数可能很多，而渲染每个图像可能需要大量的时间。  因此，渲染是一个完美的批处理工作负荷，可以利用 Azure 并行运行许多渲染，并利用包括 GPU 在内的各种硬件。

## <a name="why-use-azure-for-rendering"></a>为何使用 Azure 进行渲染？

由于多种原因，渲染是非常适合 Azure 的工作负荷：

* 可将渲染作业拆分为多个片段，然后使用多个 VM 并行运行这些片段：
  * 动画由许多帧组成，每个帧可以并行渲染。  可用于处理每个帧的 VM 越多，生成所有帧和动画的速度就越快。
  * 某些渲染软件允许将单个帧分解为多个片段，例如平铺图像或切片。  每个片段可以单独渲染，并在完成所有片段后合并成最终的图像。  可用的 VM 越多，渲染帧的速度就越快。
* 渲染项目可能需要庞大的规模：
  * 单个帧可能很复杂，即使在高端硬件上，也需要花费多个小时才能完成渲染；而动画可能由几十万个帧组成。  优质动画需要占用大量的计算资源，才能在合理的时间内完成渲染。  在某些情况下，并行渲染数千个帧需要使用 100,000 多个核心。
* 渲染项目基于项目，需要不同数量的计算资源：
  * 根据需要分配计算和存储容量，根据项目期间的负载扩展或缩减容量，并在完成项目后删除容量。
  * 为分配的容量付费，没有负载时不需要付费（例如，在项目切换时）。
  * 为意外变化导致的需求喷发做好准备；如果以后项目发生意外的变化，并且需要在短时间内处理好这些变化，则可以进行扩展。
* 根据应用程序、工作负荷和时间范围，从各种硬件中进行选择：
  * Azure 中提供了可以使用 Batch 进行分配和管理的各种硬件。
  * 根据具体的项目，要求可能体现在最佳性价比或者最佳总体性能方面。  不同的场景和/或渲染应用程序具有不同的内存要求。  某些渲染应用程序可以利用 GPU 来获得最佳性能或某些功能。 
* 低优先级或[现成 VM](https://azure.microsoft.com/pricing/spot/) 可降低成本：
  * 与标准 VM 相比，低优先级和现成 VM 享有较高的折扣，适用于某些作业类型。
  
## <a name="existing-on-premises-rendering-environment"></a>现有的本地渲染环境

最常见的案例是，通过渲染管理应用程序（例如 PipelineFX Qube、Royal Render、Thinkbox Deadline 或自定义应用程序）管理某个现有的本地渲染场。  要求是使用 Azure VM 扩展本地渲染场的容量。

Azure 基础结构和服务用来创建混合环境，在此环境中，Azure 用来补充本地容量。 例如：

* 使用[虚拟网络](../virtual-network/virtual-networks-overview.md)将 Azure 资源置于与本地渲染场相同的网络上。
* 使用 [Avere vFXT for Azure](../avere-vfxt/avere-vfxt-overview.md) 或 [Azure HPC 缓存](../hpc-cache/hpc-cache-overview.md)来缓存 Azure 中的源文件，以减少带宽使用和延迟，从而最大程度地提高性能。
* 请确保现有许可证服务器位于虚拟网络上，并购买额外的许可证，以满足额外的基于 Azure 的容量需求。

## <a name="no-existing-render-farm"></a>没有现有的渲染场

客户端工作站可能正在执行渲染，但渲染负载不断增大，并长时间独占了工作站容量。

可用的两个主要选项是：

* 部署一个本地渲染管理器（例如 Royal Render），并配置混合环境以在需要更高的容量或性能时使用 Azure。 渲染管理器是为了渲染工作负荷而专门定制的，它会包括用于常用客户端应用程序的插件，因此用户可以轻松提交渲染作业。

* 一个自定义解决方案，它使用 Azure Batch 来分配和管理计算容量，并提供作业计划来运行渲染作业。

## <a name="next-steps"></a>后续步骤

 了解如何[使用 Azure 基础结构和服务来扩展现有的本地渲染场](https://azure.microsoft.com/solutions/high-performance-computing/rendering/)。

详细了解 [Azure Batch 渲染功能](batch-rendering-functionality.md)。
