---
title: 部署和配置 Azure VMware 解决方案
description: 了解如何使用规划阶段中收集的信息部署和配置 Azure VMware 解决方案私有云。
ms.topic: tutorial
ms.custom: contperf-fy22q1, devx-track-azurecli
ms.date: 07/09/2021
ms.openlocfilehash: 30c6360e49da2574edd87c639811aac4b66d5e9e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438182"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>部署和配置 Azure VMware 解决方案

本文介绍如何使用[规划部分](production-ready-deployment-steps.md)的信息部署和配置 Azure VMware 解决方案。 

此图显示了 Azure VMware 解决方案的部署工作流。 

:::image type="content" source="media/deploy-azure-vmware-solution-workflow.png" alt-text="Azure VMware 解决方案的部署工作流关系图。" lightbox="media/deploy-azure-vmware-solution-workflow.png" border="false":::

## <a name="step-1-register-the-microsoftavs-resource-provider"></a>步骤 1。 注册 Microsoft.AVS 资源提供程序

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]

## <a name="step-2-create-an-azure-vmware-solution-private-cloud"></a>步骤 2. 创建 Azure VMware 解决方案私有云

[!INCLUDE [create-private-cloud-azure-portal-steps](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>有关此步骤的端到端概述，请观看 [Azure VMware 解决方案：部署](https://www.youtube.com/embed/gng7JjxgayI)视频。


## <a name="step-3-connect-to-azure-virtual-network-with-expressroute"></a>步骤 3. 通过 ExpressRoute 连接到 Azure 虚拟网络

在计划阶段，你定义了是要使用现有的 ExpressRoute 虚拟网络网关，还是要使用新的 ExpressRoute 虚拟网络网关 。  

:::image type="content" source="media/connect-expressroute-vnet-workflow.png" alt-text="关系图，显示了在 Azure VMware 解决方案中将 Azure 虚拟网络连接到 ExpressRoute 的工作流。" border="false":::

>[!IMPORTANT]
>[!INCLUDE [disk-pool-planning-note](includes/disk-pool-planning-note.md)] 

### <a name="use-a-new-expressroute-virtual-network-gateway"></a>使用新的 ExpressRoute 虚拟网络网关

>[!IMPORTANT]
>必须有一个虚拟网络，该虚拟网络的 GatewaySubnet 尚不具备虚拟网络网关。

| 如果 | Then  |
| --- | --- |
| 如果还没有虚拟网络…     |  创建以下各项：<ol><li><a href="tutorial-configure-networking.md#create-a-virtual-network">虚拟网络</a></li><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">虚拟网络网关</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">将 ExpressRoute 连接到网关</a></li></ol>        |
| 你已经有一个不具备 GatewaySubnet 的虚拟网络…   | 创建以下各项： <ol><li><a href="../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet">GatewaySubnet</a></li><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">虚拟网络网关</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">将 ExpressRoute 连接到网关</a></li></ol>          |
| 你已经有一个带有 GatewaySubnet 的虚拟网络… | 创建以下各项： <ol><li><a href="tutorial-configure-networking.md#create-a-virtual-network-gateway">虚拟网络网关</a></li><li><a href="tutorial-configure-networking.md#connect-expressroute-to-the-virtual-network-gateway">将 ExpressRoute 连接到网关</a></li></ol>    |


### <a name="use-an-existing-virtual-network-gateway"></a>使用现有虚拟网络网关

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="step-4-validate-the-connection"></a>步骤 4. 验证连接

应在 ExpressRoute 在其中终止的 Azure 虚拟网络与 Azure VMware 解决方案私有云之间建立连接。 

1. 使用 Azure VMware 解决方案 ExpressRoute 在其中终止的 Azure 虚拟网络中的[虚拟机](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)（请参阅[步骤 3. 通过 ExpressRoute 连接到 Azure 虚拟网络](#step-3-connect-to-azure-virtual-network-with-expressroute)）。  

   1. 登录到 [Azure 门户](https://portal.azure.com)。

   1. 导航到处于“正在运行”状态的 VM，在“设置”下选择“网络”，然后选择网络接口资源 。

      :::image type="content" source="../virtual-network/media/diagnose-network-routing-problem/view-nics.png" alt-text="显示虚拟网络接口设置的屏幕截图。":::

   1. 在左侧选择“有效路由”。 可看到在部署阶段输入的 `/22` CIDR 块中包含的地址前缀列表。

1. 如果要同时登录到 vCenter 和 NSX-T 管理器，请打开 Web 浏览器并登录到用于网络路由验证的同一虚拟机。  

   你可以在 Azure 门户中标识 vCenter 以及 NSX-T Manager 控制台的 IP 地址和凭据。  选择私有云，然后选择“管理” > “标识”。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="显示私有云 vCenter 以及 NSX 管理器 URL 和凭据的屏幕截图。" border="true":::


## <a name="next-steps"></a>后续步骤

在下一部分，需通过 ExpressRoute 将 Azure VMware 解决方案连接到本地网络。

> [!div class="nextstepaction"]
> [连接到本地环境](tutorial-expressroute-global-reach-private-cloud.md)
