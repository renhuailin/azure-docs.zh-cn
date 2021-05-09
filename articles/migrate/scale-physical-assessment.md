---
title: 使用 Azure Migrate 为迁移到 Azure 评估大量物理服务器 | Microsoft Docs
description: 介绍如何使用 Azure Migrate 服务为迁移到 Azure 评估大量物理服务器。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/19/2020
ms.openlocfilehash: 232475c50ab56fe6fb7a39a3497a8de3947fe851
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780304"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>评估大量物理服务器以便迁移到 Azure

本文介绍如何使用 Azure Migrate 发现和评估工具为迁移到 Azure 评估大量本地物理服务器。

[Azure Migrate](migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 


在本文中，学习如何：
> [!div class="checklist"]
> * 制定大规模评估计划。
> * 配置 Azure 权限，并准备要进行评估的物理服务器。
> * 创建 Azure Migrate 项目，并创建评估。
> * 在计划迁移时查看评估。


> [!NOTE]
> 如果要在进行大规模评估之前先尝试使用概念证明来评估几台服务器，请按[教程系列](./tutorial-discover-physical.md)的要求进行操作。

## <a name="plan-for-assessment"></a>制定评估计划

在计划大量物理服务器的评估时，需要考虑几个问题：

- 计划 Azure Migrate 项目：了解如何部署 Azure Migrate 项目。 例如，如果数据中心位于不同的地理位置，或者需要在不同的地理位置存储发现、评估或与迁移相关的元数据，则可能需要多个项目。
- 计划设备：Azure Migrate 使用部署在 Windows 服务器上的本地 Azure Migrate 设备来持续发现服务器以进行评估和迁移。 该设备会监视环境的变化，例如，添加了服务器、磁盘或网络适配器。 它还会将元数据和性能数据发送到 Azure。 需要了解要部署多少设备。


## <a name="planning-limits"></a>计划限制
 
请使用此表中汇总的限制来进行计划。

**规划** | **限制**
--- | --- 
**Azure Migrate 项目** | 在一个项目中最多评估 35,000 台服务器。
**Azure Migrate 设备** | 一个设备最多可发现 1000 台服务器。<br/> 一个设备只能与一个 Azure Migrate 项目关联。<br/> 一个 Azure Migrate 项目可以关联任意数量的设备。 <br/><br/> 
**组** | 在一个组中最多可以添加 35,000 台服务器。
Azure Migrate 评估 | 在一次评估中最多可以评估 35,000 台服务器。


## <a name="other-planning-considerations"></a>其他计划注意事项

- 若要从设备启动发现，必须选择每一台物理服务器。 

## <a name="prepare-for-assessment"></a>准备进行评估

为发现和评估工具准备 Azure 和物理服务器：  

1. 验证[物理服务器支持要求和限制](migrate-support-matrix-physical.md)。
2. 为 Azure 帐户设置用来与 Azure Migrate 交互的权限。
3. 准备物理服务器。

请按照[此教程](./tutorial-discover-physical.md)中的说明来配置这些设置。

## <a name="create-a-project"></a>创建一个项目

按照计划要求，执行以下操作：

1. 创建 Azure Migrate 项目。
2. 将 Azure Migrate 发现和评估工具添加到项目。

[了解详细信息](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>创建和查看评估

1. 为物理服务器创建评估。
1. 在准备迁移计划时查看评估。

[详细了解](tutorial-assess-physical.md)如何创建和查看评估。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 计划了对物理服务器的 Azure Migrate 评估进行缩放。
> * 准备了 Azure 和物理服务器以进行评估。
> * 创建了 Azure Migrate 项目并运行了评估。
> * 在准备迁移时查看了评估。

现在，请[了解如何](concepts-assessment-calculation.md)计算评估，以及如何[修改评估](how-to-modify-assessment.md)。