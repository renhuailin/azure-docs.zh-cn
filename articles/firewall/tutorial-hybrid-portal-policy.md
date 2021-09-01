---
title: 教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙和策略
description: 在本教程中，你将学习如何使用 Azure 门户部署和配置 Azure 防火墙和策略。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 25ec41f3c3d191c0ff13eb4301396b78100b3ef7
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123028467"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-and-policy-in-a-hybrid-network-using-the-azure-portal"></a>教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙和策略

将本地网络连接到 Azure 虚拟网络以创建混合网络时，必须能够控制对 Azure 网络资源的访问，这是整体安全计划的重要部分。

可以使用 Azure 防火墙和防火墙策略，通过规则来定义允许的和拒绝的网络流量，以便控制混合网络中的网络访问。

在本教程中，请创建三个虚拟网络：

- **VNet-Hub** - 防火墙在此虚拟网络中。
- **VNet-Spoke** - 分支虚拟网络代表 Azure 中的工作负荷。
- **VNet-Onprem** - 本地虚拟网络代表本地网络。 在实际部署中，可以使用 VPN 或 ExpressRoute 来连接它。 为简单起见，本教程将使用 VPN 网关连接，并使用 Azure 中的某个虚拟网络来代表本地网络。

![混合网络中的防火墙](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建防火墙中心虚拟网络
> * 创建分支虚拟网络
> * 创建本地虚拟网络
> * 配置和部署防火墙和策略
> * 创建并连接 VPN 网关
> * 将中心和分支虚拟网络对等互连
> * 创建路由
> * 创建虚拟机
> * 测试防火墙

如果要使用 Azure PowerShell 完成此过程，请参阅[使用 Azure PowerShell 在混合网络中部署和配置 Azure 防火墙](tutorial-hybrid-ps.md)。

## <a name="prerequisites"></a>先决条件

混合网络使用中心辐射型网络体系结构模型在 Azure VNet 与本地网络之间路由流量。 中心辐射型网络体系结构具有以下要求：

- 将 VNet-Hub 对等互连到 VNet-Spoke 时，应设置“使用此虚拟网络的网关或路由服务器”。 在中心辐射型网络体系结构中，辐射虚拟网络可以通过网关传输共享中心的 VPN 网关，不必在每个辐射虚拟网络中部署 VPN 网关。 

   此外，通往网关连接的虚拟网络或本地网络的路由会通过网关传输自动传播到对等互连的虚拟网络的路由表。 有关详细信息，请参阅[针对虚拟网络对等互连配置 VPN 网关传输](../vpn-gateway/vpn-gateway-peering-gateway-transit.md)。

- 将 VNet-Spoke 对等互连到 VNet-Hub 时，应设置“使用远程虚拟网络的网关或路由服务器”。 如果设置了“使用远程虚拟网络的网关或路由服务器”，同时在远程对等端上设置了“使用此虚拟网络的网关或路由服务器”，则辐射虚拟网络将使用远程虚拟网络的网关进行传输 。
- 若要通过中心防火墙路由分支子网流量，则可使用一个用户定义的路由 (UDR) 指向禁用了“虚拟网关路由传播”选项的防火墙。 禁用了“虚拟网关路由传播”选项可防止将路由分配到分支子网。 这可以防止获知的路由与你的 UDR 冲突。 如果要保持“虚拟网关路由传播”处于启用状态，请确保定义到防火墙的特定路由，以替代通过 BGP 从本地发布的路由。
- 请在中心网关子网上配置一个指向防火墙 IP 地址的 UDR，将其作为通向辐射网络的下一跃点。 无需在 Azure 防火墙子网中创建 UDR，因为它会从 BGP 探测路由。

请参阅本教程的[创建路由](#create-the-routes)部分了解如何创建这些路由。

>[!NOTE]
>Azure 防火墙必须具有直接的 Internet 连接。 如果 AzureFirewallSubnet 知道通过 BGP 的本地网络的默认路由，则必须将其替代为 0.0.0.0/0 UDR，将 NextHopType 值设置为 Internet 以保持 Internet 直接连接 。
>
>可将 Azure 防火墙配置为支持强制隧道。 有关详细信息，请参阅 [Azure 防火墙强制隧道](forced-tunneling.md)。

>[!NOTE]
>即使 UDR 指向作为默认网关的 Azure 防火墙，也会直接路由直接对等互连 VNet 之间的流量。 若要在此方案中将子网到子网流量发送到防火墙，UDR 必须在这两个子网上显式地包含目标子网网络前缀。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-firewall-hub-virtual-network"></a>创建防火墙中心虚拟网络

首先，创建用于存储本教程资源的资源组：

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 在 Azure 门户主页上，选择“资源组” > “添加” 。
3. 对于“订阅”，请选择自己的订阅。 
1. 对于“资源组名称”，请键入 **FW-Hybrid-Test**。
2. 对于“区域”，请选择“(US)美国东部”。 以后创建的所有资源必须位于同一位置。
3. 选择“查看 + 创建”  。
4. 选择“创建”。

现在创建 VNet：

> [!NOTE]
> AzureFirewallSubnet 子网的大小为 /26。 有关子网大小的详细信息，请参阅 [Azure 防火墙常见问题解答](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size)。

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在“网络”下，选择“虚拟网络”。 
1. 选择“创建”。
1. 对于“资源组”，请选择“FW-Hybrid-Test”。 
1. 对于“名称”，请键入 **VNet-hub**。
1. 在完成时选择“下一步:IP 地址”。
1. 对于“IPv4 地址空间”，请删除默认地址然后键入“10.5.0.0/16” 。
1. 在“子网名称”下，选择“添加子网” 。
1. 对于“子网名称”，键入“AzureFirewallSubnet”。  防火墙将位于此子网中，子网名称 **必须** 是 AzureFirewallSubnet。
1. 对于“子网地址范围”，请键入“10.5.0.0/26” 。 
1. 选择 **添加** 。
1. 选择“查看 + 创建”  。
1. 选择“创建”。

## <a name="create-the-spoke-virtual-network"></a>创建分支虚拟网络

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在“网络”中，选择“虚拟网络” 。
7. 对于“资源组”，请选择“FW-Hybrid-Test”。 
1. 对于“名称”，请键入 **VNet-Spoke**。
2. 对于“区域”，请选择“(US)美国东部”。
3. 在完成时选择“下一步:IP 地址”。
4. 对于“IPv4 地址空间”，请删除默认地址然后键入“10.6.0.0/16” 。
6. 在“子网名称”下，选择“添加子网” 。
7. 对于“子网名称”，请键入“SN-Workload” 。
8. 对于“子网地址范围”，请键入“10.6.0.0/24” 。 
9. 选择 **添加** 。
10. 选择“查看 + 创建”  。
11. 选择“创建”。

## <a name="create-the-on-premises-virtual-network"></a>创建本地虚拟网络

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在“网络”中，选择“虚拟网络” 。
7. 对于“资源组”，请选择“FW-Hybrid-Test”。 
1. 对于“名称”，请键入 **VNet-OnPrem**。
2. 对于“区域”，请选择“(US)美国东部”。
3. **选择“下一步:** IP 地址”
4. 对于“IPv4 地址空间”，请删除默认地址然后键入“192.168.0.0/16” 。
5. 在“子网名称”下，选择“添加子网” 。
7. 对于“子网名称”，请键入“SN-Corp” 。
8. 对于“子网地址范围”，请键入“192.168.1.0/24” 。 
9. 选择 **添加** 。
10. 选择“查看 + 创建”  。
11. 选择“创建”。

现在，为网关创建第二个子网。

1. 在“VNet-Onprem”页上，选择“子网”。 
2. 选择“+子网”。
3. 对于“名称”，请键入 **GatewaySubnet**。
4. 对于“子网地址范围”，请键入“192.168.2.0/24” 。
5. 选择“确定”。

## <a name="configure-and-deploy-the-firewall"></a>配置和部署防火墙

现在，将防火墙部署到防火墙中心虚拟网络中。

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在左列中选择“网络”，然后搜索并选择“防火墙” 。
4. 在“创建防火墙”页上，使用下表配置防火墙：

      |设置  |值  |
   |---------|---------|
   |订阅     |\<your subscription\>|
   |资源组     |**FW-Hybrid-Test** |
   |名称     |**AzFW01**|
   |区域     |**美国东部**|
   |防火墙管理|**使用防火墙策略来管理此防火墙**|
   |防火墙策略|添加新项：<br>**hybrid-test-pol**<br>**美国东部** 
   |选择虚拟网络     |使用现有项：<br> **VNet-hub**|
   |公共 IP 地址     |添加新项： <br>fw-pip。 |


5. 选择“查看 + 创建”。
6. 查看摘要，然后选择“创建”以创建防火墙。 

   部署过程需要花费几分钟时间。
7. 部署完成后，转到“FW-Hybrid-Test”资源组，然后选择“AzFW01”防火墙。 
8. 记下专用 IP 地址。 稍后在创建默认路由时需要用到此地址。

### <a name="configure-network-rules"></a>配置网络规则

首先，添加一个网络规则以允许 Web 流量。

1. 从“FW-Hybrid-Test”资源组选择“hybrid-test-pol”防火墙策略 。
2. 选择“网络规则”。
3. 选择“添加规则集合”。
4. 对于“名称”，请键入 **RCNet01**。
5. 对于“优先级”，请键入 **100**。
6. 对于 **规则集合操作**，请选择 **允许**。
6. 在“规则”下，为“名称”键入 **AllowWeb**。 
8. 对于 **源类型**，请选择“IP 地址”。
9. 对于 **源**，请键入 **192.168.1.0/24**。
7. 对于“协议”，请选择“TCP”。 
1. 对于“目标端口”，请键入 **80**。
1. 对于“目标类型”，请选择“IP 地址” 。
1. 对于“目标”，请键入 **10.6.0.0/16**。

现在添加一个规则以允许 RDP 流量。

在第二个规则行中键入以下信息：

1. 对于“名称”，请键入 **AllowRDP**。
3. 对于 **源类型**，请选择“IP 地址”。
4. 对于 **源**，请键入 **192.168.1.0/24**。
2. 对于“协议”，请选择“TCP”。 
1. 对于“目标端口”，请键入 **3389**。
1. 对于“目标类型”，请选择“IP 地址” 。
1. 对于“目标”，键入 10.6.0.0/16 
1. 选择 **添加** 。

## <a name="create-and-connect-the-vpn-gateways"></a>创建并连接 VPN 网关

通过 VPN 网关连接中心和本地虚拟网络。

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>为中心虚拟网络创建 VPN 网关

现在，为中心虚拟网络创建 VPN 网关。 网络到网络配置需要 RouteBased VpnType。 创建 VPN 网关通常需要 45 分钟或更长时间，具体取决于所选 VPN 网关的 SKU。

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在搜索文本框中，键入“虚拟网络网关”。
3. 依次选择“虚拟网络网关”、“创建”。
4. 对于“名称”，请键入 **GW-hub**。
5. 对于“区域”，请选择前面使用的同一区域。
6. 对于“网关类型”，请选择“VPN”。 
7. 对于“VPN 类型”，请选择“基于路由”。
8. 对于“SKU”，请选择“基本”。
9. 对于“虚拟网络”，请选择“VNet-hub”。
10. 对于“公共 IP 地址”，请选择“新建”，然后键入 **VNet-hub-GW-pip** 作为名称。
11. 接受剩余的默认值，然后选择“查看 + 创建”。
12. 检查配置，然后选择“创建”。

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>为本地虚拟网络创建 VPN 网关

现在，为本地虚拟网络创建 VPN 网关。 网络到网络配置需要 RouteBased VpnType。 创建 VPN 网关通常需要 45 分钟或更长时间，具体取决于所选 VPN 网关的 SKU。

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在搜索文本框中，键入“虚拟网络网关”并按 **Enter**。
3. 依次选择“虚拟网络网关”、“创建”。
4. 对于“名称”，请键入 **GW-Onprem**。
5. 对于“区域”，请选择前面使用的同一区域。
6. 对于“网关类型”，请选择“VPN”。 
7. 对于“VPN 类型”，请选择“基于路由”。
8. 对于“SKU”，请选择“基本”。
9. 对于“虚拟网络”，请选择“VNet-Onprem”。 
10. 对于“公共 IP 地址”，请选择“新建”，然后键入 **VNet-Onprem-GW-pip** 作为名称。 
11. 接受剩余的默认值，然后选择“查看 + 创建”。
12. 检查配置，然后选择“创建”。

### <a name="create-the-vpn-connections"></a>创建 VPN 连接

现在，可在中心与本地网关之间创建 VPN 连接。

此步骤创建从中心虚拟网络到本地虚拟网络的连接。 示例中引用了共享密钥。 可以对共享密钥使用自己的值。 共享密钥必须与两个连接匹配，这一点非常重要。 创建连接可能需要简短的一段时间才能完成。

1. 打开“FW-Hybrid-Test”资源组并选择“GW-hub”网关。
2. 在左栏中选择“连接”。
3. 选择 **添加** 。
4. 键入 **Hub-to-Onprem** 作为连接名称。
5. 选择“VNet 到 VNet”作为“连接类型”。
6. 对于“第二个虚拟网络网关”，请选择“GW-Onprem”。
7. 对于“共享密钥(PSK)”，请键入 **AzureA1b2C3**。
8. 选择“确定”。

创建本地到中心虚拟网络连接。 此步骤类似于前一步骤，但这次是创建从 VNet-Onprem 到 VNet-hub 的连接。 确保共享密钥匹配。 几分钟后会建立连接。

1. 打开“FW-Hybrid-Test”资源组并选择“GW-Onprem”网关。
2. 在左栏中选择“连接”。
3. 选择 **添加** 。
4. 对于“连接名称”，请键入“Onprem-to-Hub”。
5. 选择“VNet 到 VNet”作为“连接类型”。
6. 对于“第二个虚拟网络网关”，请选择“GW-hub”。
7. 对于“共享密钥(PSK)”，请键入 **AzureA1b2C3**。
8. 选择“确定”。


#### <a name="verify-the-connection"></a>验证连接

大约五分钟后，这两个连接的状态应显示为“已连接”。

![网关连接](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>将中心和分支虚拟网络对等互连

现在，将中心和分支虚拟网络对等互连。

1. 打开“FW-Hybrid-Test”资源组并选择“VNet-hub”虚拟网络。
2. 在左栏中选择“对等互连”。
3. 选择 **添加** 。
4. 在“此虚拟网络”下：
 
   
   |设置名称  |值  |
   |---------|---------|
   |对等互连链接名称| HubtoSpoke|
   |到远程虚拟网络的流量|   允许（默认）      |
   |从远程虚拟网络转接的流量    |   允许（默认）      |
   |虚拟网络网关     |  使用此虚拟网络的网关       |
    
5. 在“远程虚拟网络”下：

   |设置名称  |值  |
   |---------|---------|
   |对等互连链接名称 | SpoketoHub|
   |虚拟网络部署模型| 资源管理器|
   |订阅|\<your subscription\>|
   |虚拟网络| VNet-Spoke
   |到远程虚拟网络的流量     |   允许（默认）      |
   |从远程虚拟网络转接的流量    |   允许（默认）      |
   |虚拟网络网关     |  使用此远程虚拟网络的网关       |

5. 选择 **添加** 。

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="VNet 对等互连":::

## <a name="create-the-routes"></a>创建路由

接下来创建一对路由：

- 通过防火墙 IP 地址从中心网关子网连接到分支子网的路由
- 通过防火墙 IP 地址从分支子网连接的默认路由

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在搜索文本框中，键入“路由表”并按 **Enter**。
3. 选择“路由表”。
4. 选择“创建”。
6. 选择“FW-Hybrid-Test”作为资源组。
8. 对于“区域”，请选择以前使用的同一位置。 
1. 对于“名称”，请键入 **UDR-Hub-Spoke**。
9. 选择“查看 + 创建”  。
10. 选择“创建”。
11. 创建路由表后，请选择它以打开路由表页。
12. 在左栏中选择“路由”。
13. 选择 **添加** 。
14. 键入 **ToSpoke** 作为路由名称。
15. 键入 **10.6.0.0/16** 作为地址前缀。
16. 选择“虚拟设备”作为下一跃点类型。
17. 键入前面记下的防火墙专用 IP 地址作为下一跃点地址。
18. 选择“确定”。

现在，将路由关联到子网。

1. 在“UDR-Hub-Spoke - 路由”页上，选择“子网”。
2. 选择“关联”。
3. 在“虚拟网络”下，选择“VNet-hub”。
1. 在“子网”下，选择“GatewaySubnet”。
2. 选择“确定”。

现在，创建从辐射子网的默认路由。

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在搜索文本框中，键入“路由表”并按 **Enter**。
3. 选择“路由表”。
5. 选择“创建”。
7. 选择“FW-Hybrid-Test”作为资源组。
8. 对于“区域”，请选择以前使用的同一位置。 
1. 键入 **UDR-DG** 作为名称。
4. 对于“传播网关路由”，请选择“否” 。
5. 选择“查看 + 创建”  。
6. 选择“创建”。
7. 创建路由表后，请选择它以打开路由表页。
8. 在左栏中选择“路由”。
9. 选择 **添加** 。
10. 键入“ToHub”作为路由名称。
11. 键入 **0.0.0.0/0** 作为地址前缀。
12. 选择“虚拟设备”作为下一跃点类型。
13. 键入前面记下的防火墙专用 IP 地址作为下一跃点地址。
14. 选择“确定”。

现在，将路由关联到子网。

1. 在“UDR-DG - 路由”页上，选择“子网”。
2. 选择“关联”。
3. 在“虚拟网络”下，选择“VNet-spoke”。
1. 在“子网”下，选择“SN-Workload”。
2. 选择“确定”。

## <a name="create-virtual-machines"></a>创建虚拟机

现在，创建分支工作负荷与本地虚拟机，并将其放入相应的子网。

### <a name="create-the-workload-virtual-machine"></a>创建工作负荷虚拟机

在分支虚拟网络中，创建运行 IIS 且不使用公共 IP 地址的虚拟机。

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在“常用”下，选择“Windows Server 2016 Datacenter”。  
3. 输入虚拟机的以下值：
    - **资源组** - 选择“FW-Hybrid-Test”。
    - **虚拟机名称**：*VM-Spoke-01*。
    - **区域** - 前面使用的同一区域。
    - **用户名**：\<type a user name\>。
    - **密码**：\<type a password\>
4. 对“公共入站端口”，请选择“允许所选端口”，然后选择“HTTP (80)”和“RDP (3389)”。
4. 选择“下一步:磁盘”  。
5. 接受默认设置，然后选择“下一步:网络”。
6. 选择“VNet-Spoke”作为虚拟网络；子网为“SN-Workload”。
7. 对于“公共 IP”，请选择“无”。 
9. 选择“下一步: 管理”。
10. 对于“启动诊断”，请选择“禁用” 。
11. 选择“查看 + 创建”，检查摘要页上的设置，然后选择“创建”。

### <a name="install-iis"></a>安装 IIS

1. 在 Azure 门户中打开 Cloud Shell，确保它设置为“PowerShell”。
2. 运行以下命令以在虚拟机上安装 IIS 并根据需要更改位置：

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>创建本地虚拟机

你将使用此虚拟机通过远程桌面连接到公共 IP 地址。 然后，请在该虚拟机中通过防火墙连接到本地服务器。

1. 在 Azure 门户主页上，选择“创建资源”。
2. 在“常用”下，选择“Windows Server 2016 Datacenter”。  
3. 输入虚拟机的以下值：
    - **资源组** - 选择现有资源组，然后选择“FW-Hybrid-Test”。
    - **虚拟机名称** - *VM-Onprem*。
    - **区域** - 前面使用的同一区域。
    - **用户名**：\<type a user name\>。
    - **密码**：\<type a user password\>。
7. 对“公共入站端口”，请选择“允许所选端口”，然后选择“RDP (3389)”。  
4. 选择“下一步:磁盘”  。
5. 接受默认值，然后选择“下一步: 网络”。
6. 选择“VNet-Onprem”作为虚拟网络；子网为“SN-Corp”。 
8. 选择“下一步: 管理”。
10. 对于“启动诊断”，请选择“禁用” 。
10. 选择“查看 + 创建”，检查摘要页上的设置，然后选择“创建”。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="test-the-firewall"></a>测试防火墙

1. 首先，记下“VM-spoke-01”虚拟机的专用 IP 地址。

2. 在 Azure 门户中，连接到 **VM-Onprem** 虚拟机。
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. 在 VM-Onprem 上打开 Web 浏览器并浏览到 http://\<VM-spoke-01 private IP\>。

   应会看到“VM-spoke-01”网页：![VM-Spoke-01 网页](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. 在“VM-Onprem”虚拟机中，打开远程桌面并连接到 **VM-spoke-01** 的专用 IP 地址。

   应可成功连接，并且可以登录。

现已验证防火墙规则可正常工作：

<!---- You can ping the server on the spoke VNet.--->
- 可以浏览分支虚拟网络中的 Web 服务器。
- 可以使用 RDP 连接到分支虚拟网络中的服务器。

接下来，将防火墙网络规则集合操作更改为“拒绝”，以验证防火墙规则是否按预期工作。 

1. 选择“hybrid-test-pol”防火墙策略。
2. 选择“规则集合”。
3. 选择“RCNet01”规则集合。
4. 对于“规则集合操作”，请选择“拒绝”。
5. 选择“保存”。

在测试更改的规则之前，请关闭所有现有的远程桌面。 现在再次运行测试。 这一次，这些规则应该全部失败。

## <a name="clean-up-resources"></a>清理资源

可以保留防火墙资源以便在下一篇教程中使用。不再需要时，请删除 **FW-Hybrid-Test** 资源组，以删除与防火墙相关的所有资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署和配置 Azure 防火墙高级版](premium-deploy.md)