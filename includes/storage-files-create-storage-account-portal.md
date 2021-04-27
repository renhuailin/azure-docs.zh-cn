---
title: storage-files-create-storage-account-portal
description: 为 Azure 文件创建存储帐户。
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717816"
---
存储帐户是一个共享的存储池，可以在其中部署 Azure 文件共享或其他存储资源，例如 Blob 或队列。 一个存储帐户可以包含无数个共享。 一个共享可以存储无数个文件，直至达到存储帐户的容量限制为止。

创建存储帐户：

1. 在左侧菜单中，选择 **+** 来创建资源。
1. 选中“存储帐户”，以创建存储帐户。

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="“创建资源”边栏选项卡中的“存储帐户”选项的屏幕截图。" lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. 在“名称”中，输入 *mystorageacct* 并后接一些随机数字，直到出现绿色对号，指明该名称是唯一的。 存储帐户名称必须全部为小写，且全局唯一。 记下存储帐户名称。 稍后将使用它。 
1. 在“性能”中，保留默认值“标准”。 
1. 在“复制”中，选择“本地冗余存储(LRS)”。 
1. 在“订阅”中，选择创建存储帐户时所使用的订阅。 如果只有一个订阅，则默认值应是该订阅。
1. 在“资源组”中，选择“新建”。 对于名称，输入 *myResourceGroup*。
1. 在“位置”中，选择“美国东部”。 
1. 完成后，选择“创建”以开始部署。