---
title: 教程 - 将本地环境与私有云对等互连
description: 了解如何在 Azure VMware 解决方案中创建到私有云的 ExpressRoute Global Reach 对等互连。
ms.topic: tutorial
ms.date: 1/5/2021
ms.openlocfilehash: 613aece6ed548f70840349e017de4416883d6cf3
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913152"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>教程：将本地环境与私有云对等互连

ExpressRoute Global Reach 将本地环境连接到 Azure VMware 解决方案私有云。 ExpressRoute Global Reach 连接是在私有云 ExpressRoute 线路和本地环境的现有 ExpressRoute 连接之间建立的。 

[配置 Azure 到私有云网络](tutorial-configure-networking.md)时使用的 ExpressRoute 线路要求创建和使用授权密钥。  你已使用了 ExpressRoute 线路的一个授权密钥，在本教程中，你将再创建一个授权密钥，将其与本地 ExpressRoute 线路对等互连。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 为线路 2（私有云 ExpressRoute 线路）创建另一个授权密钥
> * 使用 [Azure 门户](#azure-portal-method)或线路 1 所在订阅的 [Cloud Shell 方法中的 Azure CLI](#azure-cli-in-a-cloud-shell-method)，以启用本地到私有云 ExpressRoute Global Reach 对等互连。


## <a name="before-you-begin"></a>开始之前

通过 ExpressRoute Global Reach 在两个 ExpressRoute 线路之间启用连接之前，请查看有关如何[在不同的 Azure 订阅之间启用连接](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)的文档。  


## <a name="prerequisites"></a>先决条件

- 建立了与 Azure VMware 解决方案私有云的连接，具有与 Azure 虚拟网络 (VNet) 中的 ExpressRoute 网关对等互连的 ExpressRoute 线路，即对等互连过程中的线路 2。  
- 用于将本地环境连接到 Azure 的正常运行的 ExpressRoute 独立线路 – 从对等互连过程的角度而言，这是线路 1。
- 用于 ExpressRoute Global Reach 对等互连的 /29 非重叠[网络地址块](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings)。
- 确保所有路由器（包括 ExpressRoute 提供商的服务）都支持 4 字节自治系统编号 (ASN)。 Azure VMware 解决方案使用 4 字节公共 ASN 来播发路由。

> [!TIP]
> 在这些先决条件的上下文中，本地 ExpressRoute 线路是线路 1，私有云 ExpressRoute 线路位于其他的订阅中并被标记为线路 2。  


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>在私有云中创建 ExpressRoute 授权密钥

1. 从私有云“管理”下的“概述”中，选择“连接 > ExpressRoute > 请求授权密钥” 。

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="选择“连接”>“ExpressRoute”>“请求授权密钥”以启动新请求。":::

2. 输入授权密钥的名称，并选择“创建”。 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="选择“创建”以创建新的授权密钥。":::

   创建后，新密钥会出现在私有云的授权密钥列表中。 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="确认新授权密钥出现在私有云的密钥列表中。":::

3. 记下此授权密钥、ExpressRoute ID 以及 /29 地址块。 在下一步中，你将使用它们来完成对等互连。 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>使用授权密钥将私有云与本地对等互连

现已为 ExpressRoute 线路创建授权密钥，接下来可以将其与本地 ExpressRoute 线路对等互连。  对等互连是通过 [Azure 门户](#azure-portal-method)或 [Cloud Shell 中的 Azure CLI](#azure-cli-in-a-cloud-shell-method) 从本地 ExpressRoute 线路的角度完成的。 通过这两种方法，可以使用私有云 ExpressRoute 线路的资源 ID 和授权密钥来完成对等互连。

### <a name="azure-portal-method"></a>Azure 门户方法

1. 使用与本地 ExpressRoute 线路相同的订阅登录到 [Azure 门户](https://portal.azure.com)。

1. 在私有云“概览”的“管理”下，选择“连接性”>“ExpressRoute Global Reach”>“添加”。 

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="从菜单中依次选择“连接性”、“ExpressRoute Global Reach”选项卡、“添加”。":::

1. 可以通过以下任一选项创建本地云连接：

   - 从列表中选择 ExpressRoute 线路。
   - 如果你有线路 ID，请复制并粘贴该 ID。

1. 选择“连接”  。 新连接显示在“本地云连接”列表中。  

>[!TIP]
>可以通过选择“更多”从列表中删除连接或断开连接。  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="断开或删除本地连接":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Cloud Shell 方法中的 Azure CLI

我们已通过特定详细信息和示例扩展了 [CLI 命令](../expressroute/expressroute-howto-set-global-reach-cli.md)，以便帮助你在本地环境和 Azure VMware 解决方案私有云之间配置 ExpressRoute Global Reach 对等互连。  

> [!TIP]  
> 为保证 Azure CLI 命令输出的简洁性，这些指令可以[使用 `–query` 参数来执行 JMESPath 查询以便仅显示所需的结果](/cli/azure/query-azure-cli)。


1. 使用本地 ExpressRoute 线路所在的同一订阅登录到 Azure 门户，并打开 Cloud Shell。 将 Shell 保留为 Bash。
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="登录到 Azure 门户并打开 Cloud Shell。":::
 
2. 输入 Azure CLI 命令以创建对等互连。 使用特定信息和资源 ID、授权密钥及 /29 CIDR 网络块。 

   该图显示将使用的命令的示例，以及指示成功对等互连的输出。 示例命令基于[“在位于不同 Azure 订阅的 ExpressRoute 线路之间启用连接”中的步骤 3](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions) 所使用的命令。

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="在 Cloud Shell 中使用 Azure CLI 命令创建 ExpressRoute Global Reach 对等互连。":::
 
   现在，可以通过 ExpressRoute Global Reach 对等互连从本地环境连接到私有云。

> [!TIP]
> 可以按照[禁用本地网络之间的连接](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks)说明删除刚刚创建的对等互连。


## <a name="next-steps"></a>后续步骤

通过学习本教程，你了解了如何为私有云 ExpressRoute 线路创建第二个授权密钥。 你还了解了如何启用本地到私有云的 ExpressRoute Global Reach 对等互连。 

请继续学习下一教程，了解如何为 Azure VMware 解决方案私有云部署和配置 VMware HCX 解决方案。

> [!div class="nextstepaction"]
> [部署并配置 VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->