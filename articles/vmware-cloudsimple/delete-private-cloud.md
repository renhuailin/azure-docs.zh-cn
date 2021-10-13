---
title: 删除 Azure VMware Solution by CloudSimple 私有云
description: 了解如何删除 CloudSimple 私有云。 删除私有云时，所有群集都将被删除。
author: suzizuber
ms.author: v-szuber
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b8afadea5c1f0236166f9d2cd5b7c7fa7d6c3d6e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620386"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>删除 CloudSimple 私有云

CloudSimple 可以灵活地删除私有云。  私有云由一个或多个 vSphere 群集组成。 每个群集可以有 3 到 16 个节点。 删除私有云时，所有群集都将被删除。

## <a name="before-you-begin"></a>准备阶段

删除私有云会删除整个私有云。  私有云的所有组件都将被删除。  如果要保留任何数据，请确保已将数据备份到本地存储或 Azure 存储。

私有云的组件包括：

* CloudSimple 节点
* 虚拟机
* VLAN/子网
* 私有云上存储的所有用户数据
* VLAN/子网的所有防火墙规则附件

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="delete-a-private-cloud"></a>删除私有云

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开“资源”。

3. 单击要删除的私有云

4. 在“摘要”页上，单击“删除”。

    ![删除私有云](media/delete-private-cloud.png)

5. 在“确认”页上，输入私有云的名称，然后单击“删除”。 

    ![删除私有云 - 确认](media/delete-private-cloud-confirm.png)

私有云已标记删除。  删除进程将在三个小时后开始，并将删除私有云。

> [!CAUTION]
> 删除私有云后，必须删除节点。  从订阅中删除节点之前，会继续执行节点计数。

## <a name="next-steps"></a>后续步骤

* [删除节点](delete-nodes.md)
