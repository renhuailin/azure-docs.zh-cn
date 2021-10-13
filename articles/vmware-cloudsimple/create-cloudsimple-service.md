---
title: Azure VMware Solution by CloudSimple - 创建 CloudSimple 服务
description: 了解如何在 Azure 门户中创建 CloudSimple 服务。 在开始之前，请评价所需的配置。
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: be62455293975a046524ea81271428ebede7c354
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612097"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>创建 Azure VMware Solution by CloudSimple 服务

若要开始使用 Azure VMware Solution by CloudSimple，请在 Azure 门户中创建 Azure VMware Solution by CloudSimple 服务。

## <a name="before-you-begin"></a>准备阶段

为网关子网分配 /28 CIDR 块。 网关子网是每个 CloudSimple 服务所必需的，并且对其创建区域也是唯一的。 网关子网可用于边缘网络服务，需要 /28 CIDR 块。 网关子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。 与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-the-service"></a>创建服务

1. 选择“所有服务”  。
2. 搜索 **CloudSimple 服务**。
    ![搜索 CloudSimple 服务](media/create-cloudsimple-service-search.png)
3. 选择 **CloudSimple 服务**。
4. 单击“**添加**”创建一个新服务。
    ![添加 CloudSimple 服务](media/create-cloudsimple-service-add.png)
5. 选择想在其中创建 CloudSimple 服务的订阅。
6. 为服务选择资源组。 若要添加新的资源组，请单击“新建”。
7. 输入名称以标识服务。
8. 输入服务网关的 CIDR。 指定不与任何本地子网、Azure 子网或计划 CloudSimple 子网重叠的 /28 子网。 创建服务后无法更改 CIDR。

    ![创建 CloudSimple 服务](media/create-cloudsimple-service.png)
9. 单击 **“确定”** 。

创建服务并将其添加到服务列表中。

## <a name="next-steps"></a>后续步骤

* 了解如何[预配节点](create-nodes.md)
* 了解如何[创建私有云](create-private-cloud.md)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
