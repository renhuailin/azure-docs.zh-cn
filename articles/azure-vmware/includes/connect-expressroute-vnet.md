---
title: 将 ExpressRoute 连接到虚拟网络网关
description: 将 ExpressRoute 连接到虚拟网络网关的步骤。
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: a3e759c12496613848f6f0bc8de596a5099b904b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438183"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->


1. 请求 ExpressRoute 授权密钥：

   [!INCLUDE [request-authorization-key](request-authorization-key.md)]

1. 导航到打算使用的虚拟网络网关，然后选择“连接” > + 添加 。

1. 在“添加连接”页上为字段提供值，然后选择“确定”。 

   | 字段 | 值 |
   | --- | --- |
   | **名称**  | 输入连接的名称。  |
   | **连接类型**  | 选择“ExpressRoute”。  |
   | **兑换授权**  | 确保选中此框。  |
   | **虚拟网络网关** | 打算使用的虚拟网络网关。  |
   | **授权密钥**  | 粘贴之前复制的授权密钥。 |
   | **对等线路 URI**  | 粘贴之前复制的 ExpressRoute ID。  |

   :::image type="content" source="../media/tutorial-configure-networking/add-connection.png" alt-text="屏幕截图，显示了用于将 ExpressRoute 连接到虚拟网络网关的“添加连接”页。":::

这会在 ExpressRoute 线路与虚拟网络之间创建连接。

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="屏幕截图，显示了成功的虚拟网络网关连接。":::