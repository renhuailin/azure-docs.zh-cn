---
title: 使用 Azure NetApp 文件删除快照 | Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件删除快照。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 19ae00a47dfee9fc86a3c5c42b81821934ad5999
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699009"
---
# <a name="delete-snapshots-using-azure-netapp-files"></a>使用 Azure NetApp 文件删除快照 

可以删除不再需要保留的快照。 

> [!IMPORTANT]
> 快照删除操作无法撤消。 无法恢复已删除的快照。 

## <a name="steps"></a>步骤

1. 转到卷的“快照”菜单。 右键单击要删除的快照。 选择“删除”。 

    ![描述右键单击快照菜单的屏幕截图](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 在“删除快照”窗口中，单击“是”确认要删除快照。 

    ![确认删除快照的屏幕截图](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>后续步骤

* [详细了解快照](snapshots-introduction.md)
* [Azure NetApp 文件快照概述](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)