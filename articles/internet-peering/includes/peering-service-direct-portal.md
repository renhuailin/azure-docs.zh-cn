---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "81687046"
---
1. 选择要为 Azure 对等互连服务启用的对等互连连接。 然后选择“…” > “编辑连接” 。
    > [!div class="mx-imgBorder"]
    > ![对等互连连接“编辑连接”](../media/setup-direct-modify-editconnection.png)
1. 在“用于对等互连服务”中，选择“启用”，然后选择“保存”  。
    > [!div class="mx-imgBorder"]
    > ![对等互连连接启用对等互连服务](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. 在“概述”屏幕上，可以看到部署详细信息。 部署完成后，选择“转到资源”。
    > [!div class="mx-imgBorder"]
    > ![部署已完成](../media/setup-direct-modify-overview-deployment-complete.png)

1. 在“已注册的前缀”窗格上，选择“添加已注册的前缀” 。
    > [!div class="mx-imgBorder"]
    > ![添加已注册的前缀](../media/setup-direct-modify-add-registered-prefix.png)
1. 选择“名称”和“前缀”并选择“保存”来注册前缀  。
    > [!div class="mx-imgBorder"]
    >  ![注册前缀](../media/setup-direct-modify-register-a-prefix.png) 

1. 创建前缀后，你会在“已注册的前缀”列表中看到它。 选择前缀的“名称”以查看更多详细信息。
    > [!div class="mx-imgBorder"]
    > ![已注册的前缀和连接](../media/setup-direct-modify-registered-prefixes.png)
1. 在已注册的前缀页上，你会看到完整的详细信息，其中包括每个前缀的“前缀键”。 必须将此键提供给从其提供商 ISP 分配此前缀的客户。 然后，客户可以使用此键在其订阅中注册其前缀。
    > [!div class="mx-imgBorder"]
    > ![具有前缀键的前缀](../media/setup-direct-modify-registered-prefix-detail.png)