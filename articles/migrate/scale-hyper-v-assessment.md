---
title: 使用 Azure Migrate 评估 Hyper-V 环境中的大量服务器是否适合迁移到 Azure | Microsoft Docs
description: 介绍如何使用 Azure Migrate 服务评估 Hyper-V 环境中的大量服务器是否适合迁移到 Azure。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 495e1bf415146471fcccad34e2879398e12e1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780287"
---
# <a name="assess-large-numbers-of-servers-in-hyper-v-environment-for-migration-to-azure"></a>评估 Hyper-V 环境中的大量服务器是否适合迁移到 Azure

本文介绍如何使用 Azure Migrate 发现和评估工具评估 Hyper-V 环境中的大量本地服务器是否适合迁移到 Azure。

[Azure Migrate](migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 


在本文中，学习如何：
> [!div class="checklist"]
> * 制定大规模评估计划。
> * 配置 Azure 权限，并准备要进行评估的 Hyper-V。
> * 创建 Azure Migrate 项目，并创建评估。
> * 在计划迁移时查看评估。


> [!NOTE]
> 如果要在进行大规模评估之前先尝试使用概念证明来评估几台服务器，请按[教程系列](./tutorial-discover-hyper-v.md)操作

## <a name="plan-for-assessment"></a>制定评估计划

计划评估 Hyper-V 环境中的大量服务器时，需要考虑几个问题：

- 计划 Azure Migrate 项目：了解如何部署 Azure Migrate 项目。 例如，如果数据中心位于不同的地理位置，或者需要在不同的地理位置存储发现、评估或与迁移相关的元数据，则可能需要多个项目。
- 计划设备：Azure Migrate 使用部署为 Hyper-V VM 的本地 Azure Migrate 设备来持续发现服务器以进行评估和迁移。 该设备会监视环境的变化，例如，添加服务器、磁盘或网络适配器。 它还会将相关元数据和性能数据发送到 Azure。 需要了解要部署多少设备。


## <a name="planning-limits"></a>计划限制
 
请使用此表中汇总的限制来进行计划。

**规划** | **限制**
--- | --- 
**Azure Migrate 项目** | 在一个项目中评估最多 35,000 台服务器。
**Azure Migrate 设备** | 一个设备可发现最多 5000 台服务器。<br/> 一个设备最多可连接到 300 个 Hyper-V 主机。<br/> 一个设备只能与一个 Azure Migrate 项目关联。<br/> 一个 Azure Migrate 项目可以关联任意数量的设备。 <br/><br/> 
**组** | 在一个组中可以添加最多 35,000 台服务器。
**Azure Migrate 评估** | 在一次评估中可以评估最多 35,000 台服务器。



## <a name="other-planning-considerations"></a>其他计划注意事项

- 若要从设备启动发现，必须选择每一台 Hyper-V 主机。 
- 如果运行的是多租户环境，则当前无法仅发现属于特定租户的服务器。 

## <a name="prepare-for-assessment"></a>准备进行评估

为发现和评估工具准备 Azure 和 Hyper-V： 

1. 验证 [Hyper-V 支持要求和限制](migrate-support-matrix-hyper-v.md)。
2. 为 Azure 帐户设置与 Azure Migrate 交互所需的权限
3. 准备 Hyper-V 主机和服务器

请按照[此教程](./tutorial-discover-hyper-v.md)中的说明来配置这些设置。

## <a name="create-a-project"></a>创建一个项目

按照计划要求，执行以下操作：

1. 创建 Azure Migrate 项目。
2. 将 Azure Migrate 发现和评估工具添加到项目。

[了解详细信息](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>创建和查看评估

1. 为 Hyper-V 环境中的服务器创建评估。
1. 在准备迁移计划时查看评估。

[详细了解](tutorial-assess-hyper-v.md)如何创建和查看评估。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 已计划对使用 Azure Migrate 评估 Hyper-V 环境中的服务器的操作进行缩放
> * 已准备好 Azure 和 Hyper-V 来进行评估
> * 创建了 Azure Migrate 项目并运行了评估
> * 在准备迁移时查看了评估。

现在，请[了解如何](concepts-assessment-calculation.md)计算评估，以及如何[修改评估](how-to-modify-assessment.md)