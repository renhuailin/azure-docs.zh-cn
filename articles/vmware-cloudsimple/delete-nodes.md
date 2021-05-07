---
title: 删除 Azure VMware Solution by CloudSimple 的节点
description: 了解如何从部署了 CloudSimple 的 VMWare 中删除节点。 CloudSimple 节点按流量计费。 从 Azure 门户中删除未使用的节点。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 569bc6350b1bfa01228d49d28a1d12e2ab62f6f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88142258"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>从 Azure VMware Solution by CloudSimple 删除节点

创建 CloudSimple 节点后，将对其按流量计费。  必须删除节点以停止对节点的按流量计费。  从 Azure 门户中删除未使用的节点。

## <a name="before-you-begin"></a>开始之前

仅在以下条件下才能删除节点：

* 使用节点创建的私有云被删除。  若要删除私有云，请参阅[删除 Azure VMware Solution by CloudSimple 私有云](delete-private-cloud.md)。
* 已通过收缩私有云从私有云中删除了节点。  若要收缩私有云，请参阅[收缩 Azure VMware Solution by CloudSimple 私有云](shrink-private-cloud.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="delete-cloudsimple-node"></a>删除 CloudSimple 节点

1. 选择“所有服务”  。

2. 搜索“CloudSimple 节点”。

   ![搜索 CloudSimple 节点](media/create-cloudsimple-node-search.png)

3. 选择“CloudSimple 节点”。

4. 选择不属于私有云的节点以进行删除。  “私有云名称”列显示节点所属的私有云名称。  如果私有云未使用节点，则该值将为空。 

    ![选择 CloudSimple 节点](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 只能删除不属于私有云的节点。

## <a name="next-steps"></a>后续步骤

* 了解[私有云](cloudsimple-private-cloud.md)
