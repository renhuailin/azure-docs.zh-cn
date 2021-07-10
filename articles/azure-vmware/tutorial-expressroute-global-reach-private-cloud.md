---
title: 将本地环境与 Azure VMware 解决方案对等互连
description: 了解如何在 Azure VMware 解决方案中创建到私有云的 ExpressRoute Global Reach 对等互连。
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 05/14/2021
ms.openlocfilehash: 2b2fdf739b8e690b3210e1ba975ef14125e51c11
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110086853"
---
# <a name="peer-on-premises-environments-to-azure-vmware-solution"></a>将本地环境与 Azure VMware 解决方案对等互连

在快速入门的此步骤中，将 Azure VMware 解决方案连接到本地环境。 ExpressRoute Global Reach 将本地环境连接到 Azure VMware 解决方案私有云。 ExpressRoute Global Reach 连接是在私有云 ExpressRoute 线路和本地环境的现有 ExpressRoute 连接之间建立的。 


>[!NOTE]
>你可以通过 VPN 进行连接，但这超出了本快速入门文档的介绍范围。

本教程将生成如图所示的连接。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="显示 ExpressRoute Global Reach 本地网络连接的示意图。" lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::


## <a name="before-you-begin"></a>开始之前

通过 ExpressRoute Global Reach 在两个 ExpressRoute 线路之间启用连接之前，请查看有关如何[在不同的 Azure 订阅之间启用连接](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)的文档。  

## <a name="prerequisites"></a>先决条件
- 用于将本地环境连接到 Azure 的正常运行的 ExpressRoute 独立线路，即用于对等互连的线路 1。
- 确保所有网关（包括 ExpressRoute 提供商的服务）都支持 4 字节自治系统编号 (ASN)。 Azure VMware 解决方案使用 4 字节公共 ASN 来播发路由。


## <a name="create-an-expressroute-auth-key-in-the-on-premises-expressroute-circuit"></a>在本地 ExpressRoute 线路中创建 ExpressRoute 授权密钥

1. 在“ExpressRoute 线路”边栏选项卡的“设置”下，选择“授权” 。

1. 输入授权密钥的名称，并选择“保存”。

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="选择“授权”，并输入授权密钥的名称。":::

   创建后，新密钥会出现在线路的授权密钥列表中。

1. 记下此授权密钥和 ExpressRoute ID。 在下一步中，你将使用它们来完成对等互连。

## <a name="peer-private-cloud-to-on-premises"></a>从私有云对等连接到本地 
现已为 ExpressRoute 线路创建授权密钥，接下来可以将其与本地 ExpressRoute 线路对等互连。 对等互连是通过 Azure 门户中的本地 ExpressRoute 线路完成的。 你将使用私有云 ExpressRoute 线路的资源 ID（ExpressRoute 线路 ID）和授权密钥来完成对等互连。

1. 在私有云的“管理”下，选择“连接” > “ExpressRoute Global Reach” > “添加”  。

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="显示 Azure VMware 解决方案私有云中“ExpressRoute Global Reach”选项卡的屏幕截图。":::

1. 输入在上一部分中创建的 ExpressRoute ID 和授权密钥。

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="屏幕截图显示用于输入连接信息的对话框。":::   

1. 选择“创建”。 新连接显示在“本地云连接”列表中。

>[!TIP]
>可以通过选择“更多”从列表中删除连接或断开连接。  
>
>:::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="断开或删除本地连接":::


## <a name="verify-on-premises-network-connectivity"></a>验证本地网络连接性

现在，应该会在本地边缘路由器中看到 ExpressRoute 连接 NSX-T 网段和 Azure VMware 解决方案管理段。

>[!IMPORTANT]
>每个人都有不同的环境，有些人需要允许这些路由传播回本地网络。  

## <a name="next-steps"></a>后续步骤
请继续学习下一教程，了解如何为 Azure VMware 解决方案私有云部署和配置 VMware HCX 解决方案。

> [!div class="nextstepaction"]
> [部署并配置 VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
