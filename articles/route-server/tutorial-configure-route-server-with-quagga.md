---
title: 教程：配置 Azure 路由服务器与 Quagga 网络虚拟设备之间的对等互连
description: 本教程介绍如何配置 Azure 路由服务器，并将其与 Quagga 网络虚拟设备进行对等互连。
author: duongau
ms.author: duau
ms.service: route-server
ms.topic: tutorial
ms.date: 08/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbeee212b8ed7284780083166af34285f2d44a5e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823946"
---
# <a name="tutorial-configure-peering-between-azure-route-server-and-quagga-network-virtual-appliance"></a>教程：配置 Azure 路由服务器与 Quagga 网络虚拟设备之间的对等互连

本教程介绍如何将 Azure 路由服务器部署到虚拟网络中，并与 Quagga 网络虚拟设备建立 BGP 对等互连连接。 你将部署包含五个子网的虚拟网络。 一个子网专用于 Azure 路由服务器，另一个子网专用于 Quagga NVA。 将 Quagga NVA 配置为与路由服务器交换路由。 最后，进行测试以确保路由在 Azure 路由服务器和 Quagga NVA 上正确交换。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建包含五个子网的虚拟网络
> * 部署 Azure 路由服务器
> * 部署运行 Quagga 的虚拟机
> * 配置路由服务器对等互连
> * 检查已获知的路由

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-network"></a>创建虚拟网络

需要一个虚拟网络，以将 Azure 路由服务器和 Quagga NVA 都部署到该虚拟网络中。 每个部署都有自己的专用子网。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在屏幕左上方，选择“创建资源”，然后搜索“虚拟网络” 。 然后选择“创建”。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-new-virtual-network.png" alt-text="屏幕截图：创建新的虚拟网络资源。":::

1. 在“创建虚拟网络”的“基本信息”选项卡中，输入或选择以下信息，然后选择“下一步: IP 地址 >” ：

    | 设置 | 值 |
    | -------- | ----- | 
    | 订阅 | 为此部署选择订阅。 |
    | 资源组 | 为该部署选择现有资源组或创建新资源组。 | 
    | 名称 | 为虚拟机输入名称。 本教程将使用“myVirtualNetwork”。
    | 区域 | 选择要在其中部署此虚拟网络的区域。 本教程将使用“美国西部”。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-basics-tab.png" alt-text="屏幕截图：虚拟网络的“基本信息”选项卡设置。":::

1. 在“IP 地址”选项卡上，将“虚拟网络地址空间”配置为“10.1.0.0/16”。 然后配置以下五个子网：

    | 子网名称 | 子网地址范围 |
    | ----------- | -------------------- |
    | RouteServerSubnet | 10.1.1.0/25 |
    | subnet1 | 10.1.2.0/24 |
    | subnet2 | 10.1.3.0/24 |
    | subnet3 | 10.1.4.0/24 |
    | GatewaySubnet | 10.1.5.0/24 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-ip-addresses.png" alt-text="屏幕截图：虚拟网络的 IP 地址设置。":::

1. 选择“查看 + 创建”，然后在通过验证后选择“创建” 。

## <a name="create-the-azure-route-server"></a>创建 Azure 路由服务器

路由服务器用于通过 BGP 对等互连连接与 NVA 通信并交换虚拟网络路由。

1. 转到  https://aka.ms/routeserver 。

1. 选择“+ 新建路由服务器”。

   :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="路由服务器登录页的屏幕截图。":::

1. 在“创建路由服务器”页上，输入或选择以下信息：

    | 设置 | 值 |
    | -------- | ----- |
    | 订阅 | 选择在上一部分中创建虚拟网络的同一订阅。 | 
    | 资源组 | 选择现有的资源组 myRouteServerRG。 |
    | 名称 | 输入路由服务器名称 myRouteServer。 |
    | 区域 | 选择“美国西部”区域。 |
    | 虚拟网络 | 选择“myVirtualNetwork”虚拟网络。 |
    | 子网 | 选择之前创建的“RouteServerSubnet (10.1.0.0/25)”。 |
    | 公共 IP 地址 | 创建新的或选择现有的标准公共 IP 地址与路由服务器一起使用。 此 IP 地址可确保连接到管理路由服务器配置的后端服务。 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/route-server-basics-tab.png" alt-text="屏幕截图：路由服务器创建的“基本信息”选项卡。":::

1. 选择“查看 + 创建”，然后在通过验证后选择“创建” 。 部署路由服务器大约需要 15 分钟。

## <a name="create-quagga-network-virtual-appliance"></a>创建 Quagga 网络虚拟设备

若要配置 Quagga 网络虚拟设备，需要部署 Linux 虚拟机。

1. 在 Azure 门户中，选择“+ 创建资源”>“计算”>“虚拟机”。 然后选择“创建”。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-virtual-machine.png" alt-text="屏幕截图：“创建新的虚拟机”页面。":::

1. 在“基本信息”选项卡上，输入或选择以下信息：

    | 设置 | 值 |
    | -------- | ----- |
    | 订阅 | 选择与之前部署的虚拟网络相同的订阅。 |
    | 资源组 | 选择现有的资源组 myRouteServerRG。 |
    | 虚拟机名称 | 输入名称 Quagga。 |
    | 区域 | 选择“美国西部”区域。 |
    | 映像 | 选择“Ubuntu 18.04 LTS - Gen 1”。 |
    | 大小 | 选择“Standard_B2s - 2vcpus，4GiB 内存”。 |
    | 身份验证类型 | 选择“密码”  |
    | 用户名 | 输入“azureuser”。 不要使用“quagga”作为用户名，否则在后续步骤中安装脚本将失败。 |
    | Password | 输入并确认你选择的密码。 |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 选择“SSH (22)”。 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab.png" alt-text="屏幕截图：创建新虚拟机的“基本信息”选项卡。" lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab-expanded.png":::
    
1. 在“网络”选项卡上，选择以下网络设置：

    | 设置 | 值 |
    | -------- | ----- |
    | 虚拟网络 | 选择“myVirtualNetwork”。 |
    | 子网 | 选择“subnet3 (10.1.4.0/24)”。 |
    | 公共 IP | 保留为默认值。 |
    | NIC 网络安全组 | 保留为默认值。 **基本**。 |
    | 公共入站端口 | 保留为默认值。 “允许选定的端口”。 |
    | 选择入站端口 | 保留为默认值。 “22 (SSH)”。 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab.png" alt-text="屏幕截图：创建新虚拟机的“网络”选项卡。" lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab-expanded.png":::

1. 选择“查看 + 创建”，然后在通过验证后选择“创建” 。 部署 VM 大约需要 10 分钟。

1. 部署 VM 后，转到 Quagga VM 的网络设置，并选择该网络接口。

     :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-network-settings.png" alt-text="屏幕截图：Quagga VM 的“网络”页。":::

1. 在“设置”下选择“IP 配置”，然后选择“ipconfig1”。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-ip-configuration.png" alt-text="屏幕截图：Quagga VM 的“IP 配置”页。":::

1. 将分配从“动态”更改为“静态”，然后将 IP 地址从“10.1.4.4”更改为“10.1.4.10”。 在该[脚本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh)中使用这个 IP，将在后续步骤中运行该脚本。 如果要使用不同的 IP 地址，请确保更新脚本中的 IP。 选择“保存”，以更新 VM 的 IP 配置。

1. 使用 [Putty](https://www.putty.org/)，通过用于创建 Quagga VM 的公共 IP 地址和证书连接到该 VM。

1. 登录后，输入 `sudo su` 以切换到超级用户，避免运行脚本时出现错误。 复制此[脚本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh)并将其粘贴到 Putty 会话中。 此脚本将通过 Quagga 以及其他网络设置来配置虚拟机。 先更新脚本，使其适合你的网络环境，然后在虚拟机上运行脚本。 脚本完成安装需要几分钟时间。

## <a name="configure-route-server-peering"></a>配置路由服务器对等互连

1. 转到上一步中创建的路由服务器。

1. 在“设置”下选择“对等”。 然后选择“+ 添加”以添加新的对等。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peers.png" alt-text="屏幕截图：路由服务器的“对等”页。":::

1. 在“添加对等”页输入如下信息，然后选择“添加”以保存配置：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入名称以标识该对等。 “Quagga”。 |
    | ASN | 输入 Quagga NVA 的 ASN 编号。 “65001”是在脚本中定义的 ASN。 |
    | IPv4 地址 | 输入 Quagga NVA 虚拟机的专用 IP。 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/add-peer.png" alt-text="“添加对等方”页的屏幕截图。":::

1. 添加对等后，“对等”页应如下所示：

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peer-configured.png" alt-text="屏幕截图：已配置的对等。":::
    
## <a name="check-learned-routes"></a>检查已获知的路由
1. 若要检查路由服务器已获知的路由，请使用以下命令：

    ```azurepowershell-interactive
    $routes = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        PeerName = 'Quagga'
    }
    Get-AzRouteServerPeerLearnedRoute @routes | ft
    ```

    输出应如下所示： 

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/routes-learned.png" alt-text="屏幕截图：路由服务器已获知的路由。":::

1. 若要检查 Quagga NVA 已获知的路由，请输入 `vtysh`，然后输入 `show ip bgp`。 输出应如下所示：

    ```
    root@Quagga:/home/azureuser# vtysh

    Hello, this is Quagga (version 1.2.4).
    Copyright 1996-2005 Kunihiro Ishiguro, et al.

    Quagga# show ip bgp
    BGP table version is 0, local router ID is 10.1.4.10
    Status codes: s suppressed, d damped, h history, * valid, > best, = multipath,
                  i internal, r RIB-failure, S Stale, R Removed
    Origin codes: i - IGP, e - EGP, ? - incomplete

        Network          Next Hop            Metric LocPrf Weight Path
        10.1.0.0/16      10.1.1.4                               0 65515 i
                         10.1.1.5                               0 65515 i
    *> 10.100.1.0/24    0.0.0.0                  0         32768 i
    *> 10.100.2.0/24    0.0.0.0                  0         32768 i
    *> 10.100.3.0/24    0.0.0.0                  0         32768 i
    ```

## <a name="clean-up-resources"></a>清理资源

当你不再需要路由服务器和所有关联资源时，可以删除“myRouteServerRG”资源组。

:::image type="content" source="./media/tutorial-configure-route-server-with-quagga/delete-resource-group.png" alt-text="“删除资源组”按钮的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

继续学习下一篇文章，了解排查路由服务器故障的方法。
> [!div class="nextstepaction"]
> [排查路由服务器故障](troubleshoot-route-server.md)
