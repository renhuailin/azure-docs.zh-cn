---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e207866b61d21334bc9923d0d784b900906b0045
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539727"
---
1. 登录到 [Azure 门户](https://portal.azure.com/)并导航到要拍摄快照的磁盘。
1. 在磁盘上，选择“创建快照”

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="屏幕截图。磁盘的边栏选项卡，其中突出显示了“+创建快照”****，因为这是必选的。":::

1. 选择要使用的资源组并输入名称。
1. 选择“增量”，然后选择“查看 + 创建” 

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="屏幕截图。创建快照边栏选项卡，填写名称并选择增量，然后创建快照。":::

1. 选择“创建”

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="屏幕截图。验证快照的验证页，确认所做的选择，然后创建快照。":::