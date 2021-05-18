---
title: 为 VMware Solution by CloudSimple 预配节点 - Azure
description: 了解如何在 Azure 门户中通过 CloudSimple 部署向 VMWare 添加节点。 可以为私有云环境设置即用即付容量。
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccff5b4dc6ed5a571e25ab0569b9fa92674e1340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140728"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>为 Azure VMware Solution by CloudSimple 预配节点

在 Azure 门户中预配节点。 然后，可以为 CloudSimple 私有云环境设置即用即付容量。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>向 CloudSimple 私有云添加节点

1. 选择“所有服务”  。
2. 搜索“CloudSimple 节点”。

   ![搜索 CloudSimple 节点](media/create-cloudsimple-node-search.png)

3. 选择“CloudSimple 节点”。
4. 单击“添加”以创建节点。

    ![添加 CloudSimple 节点](media/create-cloudsimple-node-add.png)

5. 选择要在其中预配 CloudSimple 节点的订阅。
6. 为节点选择资源组。 若要添加新的资源组，请单击“新建”。
7. 输入前缀以标识节点。
8. 选择节点资源的位置。
9. 选择用于托管节点资源的专用位置。
10. 选择[节点类型](cloudsimple-node.md)。
11. 选择要预配的节点数。
12. 选择“查看 + 创建”。
13. 查看设置。 若要修改任何设置，请单击“上一步”。
14. 选择“创建”。

## <a name="next-steps"></a>后续步骤

* [创建私有云](create-private-cloud.md)
