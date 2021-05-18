---
title: 快速入门：创建 VMware CloudSimple 服务
titleSuffix: Azure VMware Solution by CloudSimple
description: 了解如何创建 CloudSimple 服务、购买节点和预留节点
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 14df0f131aaef8a4c24e2d1eb242a9b440e7c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86507584"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>快速入门 - 创建 Azure VMware Solution by CloudSimple 服务

开始前，请在 Azure 门户中创建 Azure VMware Solution by CloudSimple。

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware Solution by CloudSimple - 服务概述

通过 CloudSimple 服务可以使用 Azure VMware Solution by CloudSimple。  通过创建服务，可以预配节点、预留节点并创建私有云。  在 CloudSimple 服务可用的每个 Azure 区域中添加 CloudSimple 服务。  该服务定义 Azure VMware Solution by CloudSimple 的边缘网络。  此边缘网络适用的服务包括 VPN、ExpressRoute 以及私有云的 Internet 连接。

若要添加 CloudSimple 服务，必须创建一个网关子网。 网关子网在创建边缘网络时使用，需要 /28 CIDR 块。 网关子网地址空间必须唯一。 不能与任何本地网络地址空间或 Azure 虚拟网络地址空间重叠。

## <a name="before-you-begin"></a>开始之前

为网关子网分配 /28 CIDR 块。  网关子网是每个 CloudSimple 服务所必需的，且对其创建区域是唯一的。 网关子网用于 Azure VMware Solution by CloudSimple 边缘网络服务，需要 /28 CIDR 块。 网关子网地址空间必须唯一。 它不得与任何与 CloudSimple 环境通信的网络重叠。  与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。

查看[网络先决条件](cloudsimple-network-checklist.md)。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-the-service"></a>创建服务

1. 选择“所有服务”  。
2. 搜索“CloudSimple 服务”。

    ![搜索 CloudSimple 服务](media/create-cloudsimple-service-search.png)

3. 选择“CloudSimple 服务”。
4. 单击“添加”以创建新服务。

    ![添加 CloudSimple 服务](media/create-cloudsimple-service-add.png)

5. 选择想在其中创建 CloudSimple 服务的订阅。
6. 为服务选择资源组。 若要添加新的资源组，请单击“新建”。
7. 输入名称以标识服务。
8. 输入服务网关的 CIDR。 指定不与任何本地子网、Azure 子网或计划的 CloudSimple 子网重叠的 /28 子网。 创建服务后无法更改 CIDR。

    ![创建 CloudSimple 服务](media/create-cloudsimple-service.png)

9. 单击 **“确定”** 。

创建服务并将其添加到服务列表中。

## <a name="provision-nodes"></a>预配节点

若要为 CloudSimple 私有云环境设置即付即用容量，请首先在 Azure 门户中预配节点。

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

* [创建私有云并配置环境](quickstart-create-private-cloud.md)
* 了解关于 [CloudSimple 服务](./cloudsimple-service.md)的详细信息
