---
author: cherylmc
ms.author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 563de3b6c57f38d63f2759c62fccc5a17d17dae9
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778462"
---
1. 导航到“虚拟 WAN”。

1. 选择“虚拟网络连接”。

1. 在虚拟网络连接页上，选择“+ 添加连接”。

   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/add.png" alt-text="显示添加的屏幕截图。":::

1. 在“添加连接”页上，配置所需的设置。 有关路由设置的详细信息，请参阅[关于路由](../articles/virtual-wan/about-virtual-hub-routing.md)。
 
   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/connection.png" alt-text="显示 VNet 连接页的屏幕截图。":::

   * 连接名称：为连接命名。
   * 中心：选择要与此连接关联的中心。
   * **订阅**：验证订阅。
   * 资源组：包含 VNet 的资源组。
   * 虚拟网络：选择要连接到此中心的虚拟网络。 选择的虚拟网络不能包含现有的虚拟网络网关。
   * **不传播到任何内容**：默认设置为“否”。 将开关更改为“是”会使“传播到路由表”和“传播到标签”的配置选项对配置不可用  。
   * **关联路由表**：可以选择要关联的路由表。
   * **静态路由**：可以使用此设置指定下一个跃点。

1. 完成要配置的设置后，选择“创建”以创建连接。