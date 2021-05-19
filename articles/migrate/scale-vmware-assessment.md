---
title: 使用 Azure Migrate 评估 VMware 环境中的大量服务器是否适合迁移到 Azure
description: 介绍如何使用 Azure Migrate 服务评估 VMware 环境中的大量服务器是否适合迁移到 Azure。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778247"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>评估 VMware 环境中的大量服务器是否适合迁移到 Azure


本文介绍如何使用 Azure Migrate 发现和评估工具来评估 VMware 环境中的大量本地服务器（1000-35,000 个）是否适合迁移到 Azure。

[Azure Migrate](migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 

在本文中，学习如何：
> [!div class="checklist"]
> * 制定大规模评估计划。
> * 配置 Azure 权限，并准备要进行评估的 VMware。
> * 创建 Azure Migrate 项目，并创建评估。
> * 在计划迁移时查看评估。


> [!NOTE]
> 如果要在进行大规模评估之前先尝试使用概念证明来评估几台服务器，请按[教程系列](./tutorial-discover-vmware.md)的要求进行操作

## <a name="plan-for-assessment"></a>制定评估计划

计划在 VMware 环境中评估大量服务器时，需要考虑几个问题：

- **计划 Azure Migrate 项目**：了解如何部署 Azure Migrate 项目。 例如，如果数据中心位于不同的地理位置，或者需要在不同的地理位置存储发现、评估或与迁移相关的元数据，则可能需要多个项目。 
- **计划设备**：Azure Migrate 使用部署为 VMware VM 的本地 Azure Migrate 设备来持续发现服务器。 该设备会监视环境的变化，例如，添加了服务器、磁盘或网络适配器。 它还会将元数据和性能数据发送到 Azure。 需要计算出要部署多少台设备。
- **计划用于发现的帐户**：Azure Migrate 设备使用有权访问 vCenter Server 的帐户来发现用于评估和迁移的服务器。 如果发现超过 10,000 台服务器，请设置多个帐户，因为要求从一个项目中的任何两台设备发现的服务器之间没有重叠。 

> [!NOTE]
> 如果要设置多台设备，请确保提供的 vCenter 帐户上的服务器之间没有重叠。 具有此类重叠的发现是不受支持的方案。 如果一个服务器被多台设备发现，这将导致发现结果中有重复项，并且在服务器迁移中使用 Azure 门户为服务器启用复制时出现问题。

## <a name="planning-limits"></a>计划限制
 
请使用此表中汇总的限制来进行计划。

**规划** | **限制**
--- | --- 
**Azure Migrate 项目** | 在一个项目中最多评估 35,000 个服务器。
**Azure Migrate 设备** | 一台设备最多可在一个 vCenter Server 上发现 10,000 个服务器。<br/> 一台设备仅可连接到一个 vCenter Server。<br/> 一台设备只能与一个 Azure Migrate 项目关联。<br/>  一个 Azure Migrate 项目可以关联任意数量的设备。 <br/><br/> 
**组** | 在一个组中最多可添加 35,000 个服务器。
**Azure Migrate 评估** | 在一次评估中最多可评估 35,000 个服务器。

记住这些限制，下面是一些示例部署：


**vCenter 服务器** | **服务器上的服务器数量** | 建议 | **操作**
---|---|---|---
一个 | < 10,000 | 一个 Azure Migrate 项目。<br/> 一台设备。<br/> 一个用于发现的 vCenter 帐户。 | 设置设备，使用帐户连接到 vCenter Server。
一个 | > 10,000 | 一个 Azure Migrate 项目。<br/> 多台设备。<br/> 多个 vCenter 帐户。 | 为每 10,000 个服务器设置设备。<br/><br/> 设置 vCenter 帐户并划分清单，将帐户的访问权限限制为小于 10,000 个服务器。<br/> 使用帐户将每个设备连接到 vCenter Server。<br/> 可分析使用不同设备发现的服务器之间的依赖关系。 <br/> <br/> 请确保提供的 vCenter 帐户上的服务器之间没有重叠。 具有此类重叠的发现是不受支持的方案。 如果一个服务器被多台设备发现，这将导致发现结果中有重复项，并且在服务器迁移中使用 Azure 门户为服务器启用复制时出现问题。
多个 | < 10,000 |  一个 Azure Migrate 项目。<br/> 多台设备。<br/> 一个用于发现的 vCenter 帐户。 | 设置设备，使用帐户连接到 vCenter Server。<br/> 可分析使用不同设备发现的服务器之间的依赖关系。
多个 | > 10,000 | 一个 Azure Migrate 项目。<br/> 多台设备。<br/> 多个 vCenter 帐户。 | 如果 vCenter Server 发现的服务器少于 10,000 个，则为每个vCenter Server 设置一台设备。<br/><br/> 如果 vCenter Server 发现的服务器多于 10,000 个，则为每 10,000 个服务器设置一台设备。<br/> 设置 vCenter 帐户并划分清单，将帐户的访问权限限制为小于 10,000 个服务器。<br/> 使用帐户将每个设备连接到 vCenter Server。<br/> 可分析使用不同设备发现的服务器之间的依赖关系。 <br/><br/> 请确保提供的 vCenter 帐户上的服务器之间没有重叠。 具有此类重叠的发现是不受支持的方案。 如果一个服务器被多台设备发现，这将导致发现结果中有重复项，并且在服务器迁移中使用 Azure 门户为服务器启用复制时出现问题。



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>在多租户环境中计划发现

如果计划使用多租户环境，可 vCenter Server 上指定发现范围。

- 可将设备发现范围设置为 vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个服务器。
- 如果环境在租户之间共享，并且你想要单独发现每个租户，可将访问范围指定为设备用于发现的 vCenter 帐户。 
    - 如果租户共享主机，则可能需要按 VM 文件夹来确定范围。 如果 vCenter 帐户在 vCenter VM 文件夹级别具有访问权限，则 Azure Migrate 无法发现服务器。 若要按 VM 文件夹指定发现范围，确保在服务器级别为 vCenter 帐户分配只读访问权限即可实现此目的。 [了解详细信息](set-discovery-scope.md)。

## <a name="prepare-for-assessment"></a>准备进行评估

为发现和评估工具准备 Azure 和 VMware：

1. 验证 [VMware 支持要求和限制](migrate-support-matrix-vmware.md)。
2. 为 Azure 帐户设置用来与 Azure Migrate 交互的权限。
3. 准备 VMware 以进行评估。

请按照[此教程](./tutorial-discover-vmware.md)中的说明来配置这些设置。


## <a name="create-a-project"></a>创建一个项目

按照计划要求，执行以下操作：

1. 创建 Azure Migrate 项目。
2. 将 Azure Migrate 发现和评估工具添加到项目。

[了解详细信息](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>创建和查看评估

1. 为 VMware 环境中的服务器创建评估。
1. 在准备迁移计划时查看评估。


请按照[此教程](./tutorial-assess-vmware-azure-vm.md)中的说明来配置这些设置。
    

## <a name="next-steps"></a>后续步骤

本文内容：
 
> [!div class="checklist"] 
> * 已计划对使用 Azure Migrate 评估 VMware 环境中的服务器的操作进行缩放
> * 已准备好 Azure 和 VMware 来进行评估
> * 已创建 Azure Migrate 项目并运行了评估
> * 在准备迁移时查看了评估。

现在，请[了解如何](concepts-assessment-calculation.md)计算评估，以及如何[修改评估](how-to-modify-assessment.md)。