---
title: 路由网络流量 - 教程 - Azure 门户
titlesuffix: Azure Virtual Network
description: 本教程介绍如何使用 Azure 门户通过路由表路由网络流量。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 7da59e996ec37d3653dbde68c5f56caa9e8261ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061904"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>教程：使用 Azure 门户通过路由表路由网络流量

默认情况下，Azure 会在虚拟网络中的所有子网之间路由流量。 可以创建自己的路由来覆盖 Azure 的默认路由。 自定义路由非常有用，例如，它可以让你通过网络虚拟设备 (NVA) 在子网之间路由流量。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建用于流量路由的 NVA
> * 创建路由表
> * 创建路由
> * 将路由表关联到子网
> * 将虚拟机 (VM) 部署到不同子网
> * 通过 NVA 将从一个子网的流量路由到另一个子网

本教程使用 [Azure 门户](https://portal.azure.com)。 你也可使用 [Azure CLI](tutorial-create-route-table-cli.md) 或 [Azure PowerShell](tutorial-create-route-table-powershell.md)。

## <a name="prerequisites"></a>先决条件

在开始之前，需要一个包含有效订阅的 Azure 帐户。 如果你没有帐户，可以[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在 Azure 门户菜单中，选择“创建资源”。 在 Azure 市场中，选择“网络” > “虚拟网络”，或者在搜索框中搜索“虚拟网络”  。

2. 选择“创建”。

2. 在“创建虚拟网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”，输入 **myResourceGroup** 。 </br> 选择“确定”。 |
    | 名称 | 输入 myVirtualNetwork。 |
    | 位置 | 选择“(US)美国东部”。|

3. 选择“IP 地址”选项卡，或选择页面底部的“下一步: IP 地址”按钮。

4. 在“IPv4 地址空间”中，选择现有地址空间，并将其更改为 **10.0.0.0/16**。

4. 选择“+ 添加子网”，然后输入“公共”作为“子网名称”，输入 **10.0.0.0/24** 作为“子网地址范围”。

5. 选择 **添加** 。

6. 选择“+ 添加子网”，然后输入“专用”作为“子网名称”，输入 **10.0.1.0/24** 作为“子网地址范围”。

7. 选择 **添加** 。

8. 选择“+ 添加子网”，然后输入“外围网络”作为“子网名称”，输入 **10.0.2.0/24** 作为“子网地址范围”。

9. 选择 **添加** 。

10. 选择“安全性”选项卡，或者选择“下一步: 安全性”按钮（位于页面底部）。

11. 在“BastionHost”下，选择“启用” 。 输入此信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名称 | 输入“myBastionHost” |
    | AzureBastionSubnet 地址空间 | 输入 **10.0.3.0/24** |
    | 公共 IP 地址 | 选择“新建”。 </br> 对于“名称”，请输入“myBastionIP” 。 </br> 选择“确定”。 |

12. 选择“查看 + 创建”选项卡，或选择“查看 + 创建”按钮。

13. 选择“创建”。

## <a name="create-an-nva"></a>创建 NVA

网络虚拟设备 (NVA) 是帮助提供网络功能（例如路由和防火墙优化）的虚拟机。 本教程假设使用 **Windows Server 2019 Datacenter**。 如果需要，可以选择不同的操作系统。

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”  。 
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 **myVMNVA** |
    | 区域 | 选择“(US)美国东部” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |    |
    | 公共入站端口 | 选择“无”。 |
    |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | 选择“myVirtualNetwork”。 |
    | 子网 | 选择“外围网络” |
    | 公共 IP | 选择“无” |
    | NIC 网络安全组 | 选择“基本”|
    | 公共入站端口网络 | 选择“无”。 |
   
5. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。
  
6. 检查设置，然后选择“创建”。

## <a name="create-a-route-table"></a>创建路由表

1. 在 [Azure 门户](https://portal.azure.com)菜单或“主页”中，选择“创建资源”。 

2. 在搜索框中，输入“路由表”。 当“路由表”出现在搜索结果中时，请选择它。

3. 在“路由表”页中，选择“创建”。

4. 在“基本信息”选项卡上的“创建路由表”中，输入或选择以下信息 ：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“myResourceGroup”。 |
    | **实例详细信息** |    |
    | 区域 | 选择“美国东部”。 |
    | 名称 | 输入 **myRouteTablePublic**。 |
    | 传播网关路由 | 请选择“是”。 |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="创建路由表，Azure 门户" border="true":::

5. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

## <a name="create-a-route"></a>创建路由

1. 转到 [Azure 门户](https://portal.azure.com)来管理路由表。 搜索并选择“路由表”。

2. 选择路由表的名称“myRouteTablePublic”。

3. 在“myRouteTablePublic”页中的“设置”部分，选择“路由”  。

4. 在“路由”页中，选择“+ 添加”按钮。

5. 在“添加路由”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 路由名称 | 输入 **ToPrivateSubnet** |
    | 地址前缀 | 输入 **10.0.1.0/24**（前面创建的“专用”子网的地址范围） |
    | 下一跃点类型 | 选择“虚拟设备”。 |
    | 下一跃点地址 | 输入 **10.0.2.4**（“外围网络”子网地址范围内的某个地址） |

6. 选择“确定”。

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

1. 转到 [Azure 门户](https://portal.azure.com)来管理虚拟网络。 搜索并选择“虚拟网络”。

2. 选择虚拟网络的名称“myVirtualNetwork”。

3. 在“myVirtualNetwork”页中的“设置”部分，选择“子网”  。

4. 在虚拟网络的子网列表中，选择“公共”。

5. 在“路由表”中，选择创建的路由表“myRouteTablePublic” 。 

6. 选择“保存”，将路由表关联到“公共”子网 。

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="关联路由表，子网列表，虚拟网络，Azure 门户。" border="true":::

## <a name="turn-on-ip-forwarding"></a>启用 IP 转发

接下来，为新的 NVA 虚拟机 **myVMNVA** 启用 IP 转发。 当 Azure 向 **myVMNVA** 发送网络流量时，如果流量发往不同的 IP 地址，则 IP 转发会将流量发送到正确的位置。

1. 转到 [Azure 门户](https://portal.azure.com)来管理 VM。 搜索并选择“虚拟机”。

2. 选择虚拟机的名称“myVMNVA”。

3. 在“myVMNVA”概述页上的“设置”中，选择“网络”  。

4. 在 **myVMNVA** 的“网络”页中，选择“网络接口”旁边的网络接口 。  该接口的名称以 **myvmnva** 开头。

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="网络，网络虚拟设备 (NVA) 虚拟机 (VM)，Azure 门户" border="true":::

5. 在网络接口概述页上的“设置”中，选择“IP 配置” 。

6. 在“IP 配置”页中，将“IP 转发”设置为“已启用”，然后选择“保存”   。

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="启用 IP 转发" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>创建公共和专用虚拟机

在虚拟网络中创建公共 VM 和专用 VM。 稍后，我们将使用这些 VM 来查看 Azure 如何通过 NVA 将“公共”子网流量路由到“专用”子网。 


### <a name="public-vm"></a>公共 VM

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”  。 
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 **myVMPublic** |
    | 区域 | 选择“(US)美国东部” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |    |
    | 公共入站端口 | 选择“无”。 |
    |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | 选择“myVirtualNetwork”。 |
    | 子网 | 选择“公共” |
    | 公共 IP | 选择“无” |
    | NIC 网络安全组 | 选择“基本”|
    | 公共入站端口网络 | 选择“无”。 |
   
5. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。
  
6. 检查设置，然后选择“创建”。

### <a name="private-vm"></a>专用 VM

1. 在门户的左上方，选择“创建资源” > “计算” > “虚拟机”  。 
   
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    | 设置 | 值                                          |
    |-----------------------|----------------------------------|
    | **项目详细信息** |  |
    | 订阅 | 选择 Azure 订阅 |
    | 资源组 | 选择“myResourceGroup” |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 **myVMPrivate** |
    | 区域 | 选择“(US)美国东部” |
    | 可用性选项 | 选择“无需基础结构冗余” |
    | 映像 | 选择“Windows Server 2019 Datacenter” |
    | Azure Spot 实例 | 请选择“否” |
    | 大小 | 选择 VM 大小或采用默认设置 |
    | **管理员帐户** |  |
    | 用户名 | 输入用户名 |
    | 密码 | 输入密码 |
    | 确认密码 | 重新输入密码 |
    | **入站端口规则** |    |
    | 公共入站端口 | 选择“无”。 |
    |

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
  
4. 在“网络”选项卡中，选择或输入：

    | 设置 | 值 |
    |-|-|
    | **网络接口** |  |
    | 虚拟网络 | 选择“myVirtualNetwork”。 |
    | 子网 | 选择“专用” |
    | 公共 IP | 选择“无” |
    | NIC 网络安全组 | 选择“基本”|
    | 公共入站端口网络 | 选择“无”。 |
   
5. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。
  
6. 检查设置，然后选择“创建”。

## <a name="route-traffic-through-an-nva"></a>通过 NVA 路由流量

### <a name="sign-in-to-private-vm"></a>登录到专用 VM

1. 转到 [Azure 门户](https://portal.azure.com)来管理专用 VM。 搜索并选择“虚拟机”。

2. 选择专用虚拟机的名称“myVmPrivate”。

3. 在 VM 菜单栏中，选择“连接”，然后选择“堡垒”。

4. 在“连接”页中，选择蓝色的“使用堡垒”按钮。

5. 在“堡垒”页上，输入以前为虚拟机创建的用户名和密码。

6. 选择“连接”  。

### <a name="configure-firewall"></a>配置防火墙

在稍后的步骤中，我们将使用跟踪路由工具来测试路由。 跟踪路由使用 Internet 控制消息协议 (ICMP)，而 Windows 防火墙默认拒绝该协议。 

启用通过 Windows 防火墙的 ICMP。

1. 在 **myVMPrivate** 的堡垒连接中，使用管理权限打开 PowerShell。

2. 输入以下命令：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    本教程将使用跟踪路由来测试路由。 对于生产环境，我们不建议允许通过 Windows 防火墙的 ICMP。

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>在 myVMNVA 中启用 IP 转发

我们已使用 Azure 为 VM 的网络接口[启用了 IP 转发](#turn-on-ip-forwarding)。 虚拟机的操作系统也必须转发网络流量。 

使用这些命令为 **myVMNVA** 启用 IP 转发。

1. 在 **myVMPrivate** VM 上的 PowerShell 中，打开远程桌面并连接到 **myVMNVA** VM：

    ```powershell
    mstsc /v:myvmnva
    ```

2. 在 **myVMNVA** VM 上的 PowerShell 中，输入以下命令以启用 IP 转发：

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. 重启 **myVMNVA**。

    ```powershell
    Restart-Computer
    ```

4. 重启 **myVMNVA** 后，创建与 **myVMPublic** 的远程桌面会话。 
    
    在仍与 **myVMPrivate** 保持连接的情况下，打开 PowerShell 并运行以下命令：

    ```powershell
    mstsc /v:myvmpublic
    ```
5. 在 **myVMPublic** 的远程桌面中打开 PowerShell。

6. 输入以下命令，启用通过 Windows 防火墙的 ICMP：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>测试网络流量的路由

首先，让我们测试从 **myVMPublic** 到 **myVMPrivate** 的网络流量路由。

1. 在 **myVMPublic** 上的 PowerShell 中，输入以下命令：

    ```powershell
    tracert myvmprivate
    ```

    响应类似于以下示例：

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * 可以看到，第一个跃点连接到 10.0.2.4，即 **myVMNVA** 的专用 IP 地址。 

    * 第二个跃点连接到 **myVMPrivate** 的专用 IP 地址：10.0.1.4。 

    前面我们已将路由添加到 **myRouteTablePublic** 路由表，并已将它关联到“公共”子网。 Azure 通过 NVA 发送了流量，而不是直接将流量发送到“专用”子网。

2. 关闭与 **myVMPublic** 建立的远程桌面会话，这样，就仍会与 **myVMPrivate** 保持连接。

3. 在 **myVMPrivate** 上打开 PowerShell，输入以下命令：

    ```powershell
    tracert myvmpublic
    ```

    此命令测试从 *myVmPrivate* VM 到 *myVmPublic* VM 的网络流量路由。 响应类似于以下示例：

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    可以看到，Azure 直接将流量从 *myVMPrivate* 路由到了 *myVMPublic*。 默认情况下，Azure 直接在子网之间路由流量。

4. 关闭与 **myVMPrivate** 建立的堡垒会话。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，请删除 **myResourceGroup** 及其包含的所有资源：

1. 转到 [Azure 门户](https://portal.azure.com)来管理资源组。 搜索并选择“资源组”。

2. 选择资源组的名称“myResourceGroup”。

3. 选择“删除资源组”。

4. 在确认对话框中，在“键入资源组名称”中输入 **myResourceGroup**，然后选择“删除”。  


## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

* 创建了一个路由表并将其关联到了某个子网。
* 创建了一个简单的 NVA，用于将流量从公共子网路由到专用子网。 

可以从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)部署不同的预配置 NVA，其中提供了许多有用的网络功能。 

若要了解有关路由的详细信息，请参阅[路由概述](virtual-networks-udr-overview.md)和[管理路由表](manage-route-table.md)。

若要筛选虚拟网络中的网络流量，请参阅：
> [!div class="nextstepaction"]
> [教程：使用 Azure 门户通过网络安全组筛选网络流量](tutorial-filter-network-traffic.md)
