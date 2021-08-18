---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 0db7058ba6491aae86bbaf45257a7aed9041bc35
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "114665981"
---
确保已[创建中继命名空间][namespace-how-to]。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有资源”  。
3. 选择要在其中创建混合连接的命名空间。 在本示例中，该命名空间为 **mynewns**。  
4. 在“中继命名空间”下选择“混合连接”。

    ![创建混合连接](./media/relay-create-hybrid-connection-portal/create-hc-1.png)

5. 在命名空间概览窗口中，选择“+ 混合连接”
   
    ![选择混合连接](./media/relay-create-hybrid-connection-portal/create-hc-2.png)
6. 在“创建混合连接”下输入一个值，作为混合连接名称。 保留其他默认值。
   
    ![选择“新建”](./media/relay-create-hybrid-connection-portal/create-hc-3.png)
7. 选择“创建”。

[namespace-how-to]: ../relay-create-namespace-portal.md 