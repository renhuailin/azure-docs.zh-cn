---
title: 教程 - 将本地环境与私有云对等互连
description: 了解如何在 Azure VMware 解决方案中创建到私有云的 ExpressRoute Global Reach 对等互连。
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 59ddc2dd4aed850328807a4543d2fabd6f5a99f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564494"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>教程：将本地环境与私有云对等互连

ExpressRoute Global Reach 将本地环境连接到 Azure VMware 解决方案私有云。 ExpressRoute Global Reach 连接是在私有云 ExpressRoute 线路和本地环境的现有 ExpressRoute 连接之间建立的。 

[为 Azure 中的 VMware 私有云配置网络](tutorial-configure-networking.md)时使用的 ExpressRoute 线路要求创建并使用授权密钥。  你已使用了 ExpressRoute 线路中的一个授权密钥，在本教程中，你将创建另一个授权密钥来与本地 ExpressRoute 线路建立对等互连。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 为线路 2（私有云 ExpressRoute 线路）创建另一个授权密钥。
> * 在线路 1 所在的订阅中，使用 Azure 门户或 Azure CLI（如果使用的是 Cloud Shell 方法）来启用本地到私有云的 ExpressRoute Global Reach 对等互连。


## <a name="before-you-begin"></a>开始之前

通过 ExpressRoute Global Reach 在两个 ExpressRoute 线路之间启用连接之前，请查看有关如何[在不同的 Azure 订阅之间启用连接](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)的文档。  

## <a name="prerequisites"></a>先决条件

- 建立了与 Azure VMware 解决方案私有云的连接，具有与 Azure 虚拟网络 (VNet) 中的 ExpressRoute 网关对等互连的 ExpressRoute 线路，即对等互连过程中的线路 2。
- 用于将本地环境连接到 Azure 的正常运行的 ExpressRoute 独立线路 – 从对等互连过程的角度而言，这是线路 1。
- 用于 ExpressRoute Global Reach 对等互连的 /29 非重叠[网络地址块](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings)。
- 确保所有网关（包括 ExpressRoute 提供商的服务）都支持 4 字节自治系统编号 (ASN)。 Azure VMware 解决方案使用 4 字节公共 ASN 来播发路由。

>[!IMPORTANT]
>在这些先决条件的上下文中，本地 ExpressRoute 线路是线路 1，私有云 ExpressRoute 线路位于其他的订阅中并被标记为线路 2。 

## <a name="create-an-expressroute-authorization-key-in-the-private-cloud-expressroute-circuit"></a>在私有云 ExpressRoute 线路中创建 ExpressRoute 授权密钥

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>使用授权密钥将私有云对等互连到本地
现已为 ExpressRoute 线路创建授权密钥，接下来可以将其与本地 ExpressRoute 线路对等互连。 对等互连是通过 **Azure 门户** 或 **Cloud Shell 中的 Azure CLI** 从本地 ExpressRoute 线路的角度完成的。 通过这两种方法，可以使用私有云 ExpressRoute 线路的资源 ID 和授权密钥来完成对等互连。

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. 使用与本地 ExpressRoute 线路相同的订阅登录到 [Azure 门户](https://portal.azure.com)。

1. 在私有云“概述”的“管理”下，选择“连接” > “ExpressRoute Global Reach” > “添加”。   

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="显示 Azure VMware 解决方案私有云中“ExpressRoute Global Reach”选项卡的屏幕截图。":::

1. 创建本地云连接。 执行以下操作之一，然后选择“创建”：

   - 从列表中选择“ExpressRoute 线路”，或
   - 如果你有线路 ID，请将其粘贴到字段中，并提供授权密钥。

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="输入 ExpressRoute ID 和授权密钥，然后选择“创建”。":::   
   
   新连接显示在“本地云连接”列表中。

>[!TIP]
>可以通过选择“更多”从列表中删除连接或断开连接。  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="断开或删除本地连接":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

我们已通过特定详细信息和示例扩展了 [CLI 命令](../expressroute/expressroute-howto-set-global-reach-cli.md)，以便帮助你在本地环境和 Azure VMware 解决方案私有云之间配置 ExpressRoute Global Reach 对等互连。

>[!TIP]
>为保持 Azure CLI 命令输出的简洁性，这些指令可以使用 [`–query` 参数](/cli/azure/query-azure-cli)来执行 JMESPath 查询，以便仅显示所需的结果。

1. 使用与本地 ExpressRoute 线路相同的订阅登录到 [Azure 门户](https://portal.azure.com)。 

1. 打开 Cloud Shell 并将 Shell 保留为 bash。

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="显示 Azure 门户 Cloud Shell 的屏幕截图。":::

1. 针对线路 1 创建对等互连，传入线路 2 的资源 ID 和授权密钥： 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="显示命令以及在线路 1 与线路 2 之间成功建立对等互连的结果的屏幕截图。":::

现在，可以通过 ExpressRoute Global Reach 对等互连从本地环境连接到私有云。

>[!TIP]
>可以按照[禁用本地网络之间的连接](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks)说明删除对等互连。


---

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何启用本地到私有云的 ExpressRoute Global Reach 对等互连。 

请继续学习下一教程，了解如何为 Azure VMware 解决方案私有云部署和配置 VMware HCX 解决方案。

> [!div class="nextstepaction"]
> [部署并配置 VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->