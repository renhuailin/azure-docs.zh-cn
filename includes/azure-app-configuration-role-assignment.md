---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 05/03/2021
ms.openlocfilehash: 7f5f062e35aad6b7623f1a2f97ab3b9133266ac6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748240"
---
向任务中使用的凭据分配正确的应用配置角色分配，以便任务可以访问应用配置存储。

1. 转到目标应用程序配置存储。 
1. 在左侧菜单中，选择“访问控制(IAM)”。
1. 在右侧窗格中，选择“添加角色分配”。
    
    :::image type="content" source="./media/azure-app-configuration-role-assignment/add-role-assignment-button.png" alt-text="屏幕截图显示“添加角色分配”按钮。":::

1. 在“角色”下，选择“应用程序配置数据所有者”。 此角色允许任务在应用配置存储中进行读取和写入。 
1. 选择与在上一部分创建的服务连接关联的服务主体。

    :::image type="content" source="./media/azure-app-configuration-role-assignment/add-role-assignment.png" alt-text="屏幕截图显示“添加角色分配”对话。":::
