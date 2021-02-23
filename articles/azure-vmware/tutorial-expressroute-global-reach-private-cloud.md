---
title: 教程 - 将本地环境与私有云对等互连
description: 了解如何在 Azure VMware 解决方案中创建到私有云的 ExpressRoute Global Reach 对等互连。
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558820"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>教程：将本地环境与私有云对等互连

ExpressRoute Global Reach 将本地环境连接到 Azure VMware 解决方案私有云。 ExpressRoute Global Reach 连接是在私有云 ExpressRoute 线路和本地环境的现有 ExpressRoute 连接之间建立的。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Azure 门户启用本地到私有云的 ExpressRoute Global Reach 对等互连。


## <a name="before-you-begin"></a>开始之前

通过 ExpressRoute Global Reach 在两个 ExpressRoute 线路之间启用连接之前，请查看有关如何[在不同的 Azure 订阅之间启用连接](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)的文档。  


## <a name="prerequisites"></a>先决条件

- 用于将本地环境连接到 Azure 的正常运行的 ExpressRoute 独立线路。
- 确保所有网关（包括 ExpressRoute 提供商的服务）都支持 4 字节自治系统编号 (ASN)。 Azure VMware 解决方案使用 4 字节公共 ASN 来播发路由。


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>在本地 ExpressRoute 线路中创建 ExpressRoute 授权密钥

1. 在“ExpressRoute 线路”边栏选项卡的“设置”下，选择“授权” 。

2. 输入授权密钥的名称，并选择“保存”。

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="选择“授权”，并输入授权密钥的名称。":::
  
     创建后，新密钥会出现在线路的授权密钥列表中。
 
 4. 记下此授权密钥和 ExpressRoute ID。 在下一步中，你将使用它们来完成对等互连。
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>从私有云对等连接到本地

1. 在私有云“概览”的“管理”下，选择“连接性”>“ExpressRoute Global Reach”>“添加”。 

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="从菜单中依次选择“连接性”、“ExpressRoute Global Reach”选项卡、“添加”。":::

2. 输入在上一部分中创建的 ExpressRoute ID 和授权密钥。

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="输入 ExpressRoute ID 和授权密钥，然后选择“创建”。":::

3. 选择“创建”。 新连接显示在“本地云连接”列表中。  

>[!TIP]
>可以通过选择“更多”从列表中删除连接或断开连接。  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="断开或删除本地连接":::


## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何启用本地到私有云的 ExpressRoute Global Reach 对等互连。 

请继续学习下一教程，了解如何为 Azure VMware 解决方案私有云部署和配置 VMware HCX 解决方案。

> [!div class="nextstepaction"]
> [部署并配置 VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
