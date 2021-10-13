---
title: 扩展 Azure VMware Solution by CloudSimple 私有云
description: 介绍如何扩展现有的 CloudSimple 私有云，以增加现有群集或新群集的容量
author: suzizuber
ms.author: v-szuber
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a1770473b1c86524c77cc487e472872e792bded0
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612857"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>扩展 CloudSimple 私有云

CloudSimple 可以灵活地对私有云进行动态扩展。 可以从较小的配置开始，根据需要进行更大容量的扩展。 或者，也可以根据当前需求创建一个私有云，然后随使用量的增加而扩展。

私有云由一个或多个 vSphere 群集组成。 每个群集可以有 3 到 16 个节点。  扩展私有云时，可以向现有群集添加节点，也可以创建新群集。 若要扩展现有的群集，添加的节点必须与现有节点的类型 (SKU) 相同。 若要创建新群集，则节点可以是不同的类型。 有关私有云限制的详细信息，请参阅 [CloudSimple 私有云概述](cloudsimple-private-cloud.md)一文中的限制部分。

在 vCenter 上创建具有默认“数据中心”的私有云。  每个数据中心都充当顶层管理实体。  对于新群集，CloudSimple 可以选择添加到现有数据中心，也可以创建新数据中心。

新群集配置期间，CloudSimple 会配置 VMware 基础结构。  设置项包括 vSAN 磁盘组的存储设置、VMware 高可用性和分布式资源计划程序 (DRS)。

私有云可以多次扩展。 只有保持在整体节点限制内时，才可以进行扩展。 每次扩展私有云时，可以添加到现有群集，也可以创建新群集。

## <a name="before-you-begin"></a>准备阶段

必须先预配节点，才能扩展私有云。  有关预配节点的详细信息，请参阅[为 VMware Solution by CloudSimple 预配节点 - Azure](create-nodes.md) 一文。  若要创建新群集，必须至少有三个相同 SKU 的可用节点。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="expand-a-private-cloud"></a>扩展私有云

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开“资源”页，选择要扩展的私有云。

3. 在“摘要”部分中，单击“扩展”。

    ![扩展私有云](media/resources-expand-private-cloud.png)

4. 选择是扩展现有群集还是创建新的 vSphere 群集。 进行更改时，会更新页面中的摘要信息。

    * 若要扩展现有群集，请单击“扩展现有群集”。 选择要扩展的群集，然后输入要添加的节点数。 每个群集最多可以包含 16 个节点。
    * 若要添加新群集，请单击“创建新群集”。 输入群集的名称。 选择现有数据中心，或输入名称以创建新数据中心。 选择节点类型。 创建新的 vSphere 群集时，可以选择不同的节点类型，但扩展现有的 vSphere 群集时只能选择相同的节点类型。 选择节点数。 每个新群集必须至少有三个节点。

    ![扩展私有云 - 添加节点](media/resources-expand-private-cloud-add-nodes.png)

5. 单击“提交”以扩展私有云。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的详细信息