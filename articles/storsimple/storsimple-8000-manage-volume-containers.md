---
title: 管理 StorSimple 8000 系列设备的卷容器
description: 介绍如何使用“StorSimple Device Manager 服务卷容器”页添加、修改或删除卷容器。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: d48f1cf99b22c091f069e7e5f572941a234eaebe
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449827"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>使用 StorSimple Device Manager 服务管理 StorSimple 卷容器

## <a name="overview"></a>概述
本教程介绍如何使用 StorSimple Device Manager 服务创建和管理 StorSimple 卷容器。

Microsoft Azure StorSimple 设备中的卷容器包含一个或多个卷，这些卷共享存储帐户、加密和带宽消耗设置。 设备可以为它的所有卷使用多个卷容器。 

卷容器具有以下属性：

* **卷** – 卷容器中包含的分层的或本地固定 StorSimple 卷。 
* **加密** – 加密密钥，每个卷容器均可定义一个。 此密钥用于对从 StorSimple 设备发送到云的数据进行加密。 军用级 AES 256 位密钥与用户输入的密钥配合使用。 为保护数据安全，建议始终启用云存储加密。
* **存储帐户** - 用于存储数据的 Azure 存储帐户。 同一卷容器中的所有卷都共享此存储帐户。 可以从现有列表中选择一个存储帐户，也可以在创建卷容器时创建一个新帐户，并为该帐户指定访问凭据。
* **云带宽** – 将数据从设备发送到云中时设备所使用的带宽。 如果你希望设备使用所有可用带宽，请将此字段设置为 **“无限制”**。 还可以创建并应用一个带宽模板以基于计划分配带宽。

以下过程介绍了如何使用 StorSimple“卷容器”边栏选项卡完成以下常见操作：

* 添加卷容器
* 修改卷容器
* 删除卷容器

## <a name="add-a-volume-container"></a>添加卷容器
可以执行以下步骤来添加卷容器。

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>修改卷容器
可以执行以下步骤来修改卷容器。

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>删除卷容器
卷容器中具有卷。 只有首先删除卷容器中包含的所有卷后才能删除卷容器。 可以执行以下步骤来删除卷容器。

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>后续步骤
* 详细了解[管理 StorSimple 卷](storsimple-8000-manage-volumes-u2.md)。 
* 了解有关如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)的详细信息。

