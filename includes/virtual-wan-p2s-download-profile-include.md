---
ms.author: cherylmc
author: cherylmc
ms.date: 07/29/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: d13072a03dce5aa3c7fd84ccf458b4f4fad112f1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778465"
---
1. 在“虚拟 WAN”页上，选择“用户 VPN 配置” 。
1. 在“用户 VPN 配置”页面上，选择一个配置，然后选择“下载虚拟 WAN 用户 VPN 配置文件” 。

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/download.png" alt-text="“下载虚拟 WAN 用户 VPN 配置文件”的屏幕截图。":::

   * 下载 WAN 级配置时，你将获得内置的基于流量管理器的用户 VPN 配置文件。 
   
   * 有关全局配置文件和基于中心的配置文件的信息，请参阅[中心配置文件](../articles/virtual-wan/global-hub-profile.md)。 使用全局配置文件可简化故障转移方案。

   * 如果中心由于某种原因而不可用，则该服务提供的内置流量管理可确保（通过不同的中心）连接到点到站点用户的 Azure 资源。 始终可以通过导航到中心来下载特定于中心的 VPN 配置。 在“用户 VPN (点到站点)”下，下载虚拟中心用户 VPN 配置文件 。
1. 在“下载虚拟 WAN 用户 VPN 配置文件”页面上，选择所需“身份验证类型”，然后单击“生成和下载配置文件”  。 

   系统将生成包含客户端配置设置的配置文件包（zip 文件），并将其下载到计算机。

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/generate.png" alt-text="生成和下载配置文件的屏幕截图。":::

