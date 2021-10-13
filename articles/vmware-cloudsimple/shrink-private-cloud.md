---
title: 收缩 Azure VMware Solution by CloudSimple 私有云
description: 了解如何通过删除现有 vSphere 群集中的节点或删除整个群集来在 CloudSimple 中动态收缩私有云。
author: suzizuber
ms.author: v-szuber
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d6608ccc3cbeecc691663a7d10df6535e44c19ac
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615992"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>收缩 CloudSimple 私有云

CloudSimple 可以灵活地对私有云进行动态收缩。  私有云由一个或多个 vSphere 群集组成。 每个群集可以有 3 到 16 个节点。 收缩私有云时，删除现有群集中的节点或删除整个群集。 

## <a name="before-you-begin"></a>准备阶段

若要收缩私有云，必须满足以下条件。  不能删除在创建私有云时创建的管理群集（第一个群集）。

* vSphere 群集必须具有三个节点。  只有三个节点的群集无法收缩。
* 使用的总存储空间不应超过群集收缩后的总容量。
* 检查是否有任何分布式资源计划程序 (DRS) 规则阻止虚拟机的 vMotion。  如果有这样的规则，请禁用或删除这些规则。  DRS 规则包含用于托管关联规则的虚拟机。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="shrink-a-private-cloud"></a>收缩私有云

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开“资源”页。

3. 单击要收缩的私有云

4. 在“摘要”页上，单击“收缩”。

    ![收缩私有云](media/shrink-private-cloud.png)

5. 选择要收缩或删除的群集。 

    ![收缩私有云 - 选择群集](media/shrink-private-cloud-select-cluster.png)

6. 选择“删除一个节点”或“删除整个群集” 。 

7. 验证群集容量

8. 单击“提交”以收缩私有云。

私有云开始收缩。  可在任务中监视进度。  收缩过程可能需要花费几个小时，具体取决于数据（需要在 vSAN 上重新同步）。

> [!NOTE]
> 1. 如果通过删除数据中心中的最后一个或唯一一个群集来收缩私有云，则不会删除该数据中心。
> 2. 如果发生任何 DRS 规则冲突，则不会从群集中删除节点，此外，任务说明显示删除节点将违反群集上的 DRS 规则。    


## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的详细信息
