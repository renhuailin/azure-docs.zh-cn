---
title: 筛选网络流量 - 教程 - Azure 门户
titlesuffix: Azure Virtual Network
description: 本教程介绍如何在 Azure 门户中使用网络安全组筛选发往子网的网络流量。
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 4af40f7f1caab7e4517c0dd621e400f58e3a29ad
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091362"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>教程：使用 Azure 门户通过网络安全组筛选网络流量

可以使用网络安全组来筛选来自虚拟网络子网的入站和出站网络流量。

网络安全组包含安全规则，这些规则可按 IP 地址、端口和协议筛选网络流量。 安全规则应用到子网中部署的资源。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建网络安全组和安全规则
> * 创建虚拟网络并将网络安全组关联到子网
> * 将虚拟机 (VM) 部署到子网中
> * 测试流量筛选器

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在门户的左上角选择“创建资源”。

2. 在搜索框中，输入“虚拟网络”。 在搜索结果中，选择“虚拟网络”。

3. 在“虚拟网络”页中选择“创建” 。

4. 在“创建虚拟网络”  的“基本信息”选项卡中输入或选择以下信息  ：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“新建”。  </br> 输入“myResourceGroup”。 </br> 选择“确定”。 |
    | **实例详细信息** |   |
    | 名称 | 输入 **myVNet**。 |
    | 区域 | 选择“(US)美国东部”。 |

5. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

6. 选择“创建”。

## <a name="create-application-security-groups"></a>创建应用程序安全组

使用应用程序安全组可以将功能类似的服务器（例如 Web 服务器）组合在一起。

1. 在门户的左上角选择“创建资源”。

2. 在搜索框中，输入“应用程序安全组”。 在搜索结果中选择“应用程序安全组”。

3. 在“应用程序安全组”页中，选择“创建”。 

4. 在“创建应用程序安全组”的“基本信息”选项卡中，输入或选择以下信息 ：

    | 设置 | 值 |
    | ------- | ----- |
    |**项目详细信息** |  |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 |
    | **实例详细信息** |  |
    | 名称 | 输入“myAsgWebServers”。 |
    | 区域 | 选择“(US)美国东部”。 | 

5. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

6. 选择“创建”。

7. 重复步骤 4 并指定以下值：

    | 设置 | 值 |
    | ------- | ----- |
    |**项目详细信息** |  |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 |
    | **实例详细信息** |  |
    | 名称 | 输入“myAsgMgmtServers”。 |
    | 区域 | 选择“(US)美国东部”。 |

8. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

9. 选择“创建”。

## <a name="create-a-network-security-group"></a>创建网络安全组

网络安全组保护虚拟网络中的网络流量。

1. 在门户的左上角选择“创建资源”。

2. 在搜索框中，输入“网络安全组”。 在搜索结果中选择“网络安全组”。

3. 在网络安全组页中，选择“创建” 。

4. 在“创建网络安全组”的“基本信息”选项卡中，输入或选择以下信息 ：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |   |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 |
    | **实例详细信息** |   |
    | 名称 | 输入“myNSG”。 |
    | 位置 | 选择“(US)美国东部”。 | 

5. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

6. 选择“创建”。

## <a name="associate-network-security-group-to-subnet"></a>将网络安全组关联到子网

在本部分中，我们会将网络安全组与之前创建的虚拟网络的子网相关联。

1. 在门户顶部的“搜索资源、服务和文档”框中，开始键入“myNsg”。 当“myNsg”出现在搜索结果中时，将其选中。

2. 在“myNSG”的概述页中，选择“设置”中的“子网”  。

3. 在“设置”页中，选择“关联” ：

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="将 NSG 关联到子网。" border="true":::

3. 在“关联子网”下选择“虚拟网络”，然后选择“myVNet”  。 

4. 依次选择“子网”、“默认”、“确定”  。

## <a name="create-security-rules"></a>创建安全规则

1. 在“myNSG”的“设置”中，选择“入站安全规则”  。

2. 在“入站安全规则”中，单击“+ 添加” ：

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="添加入站安全规则。" border="true":::

3. 创建一项允许端口 80 和 443 与 **myAsgWebServers** 应用程序安全组通信的安全规则。 在“添加入站安全规则”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 源 | 保留默认值“任意”。 |
    | 源端口范围 | 保留默认值“(*)” |
    | 目标 | 选择“应用程序安全组”。 |
    | 目标应用程序安全组 | 选择“myAsgWebServers”。 |
    | 服务 | 保留默认值“自定义”。 |
    | 目标端口范围 | 输入“80,443”。 |
    | 协议 | 选择“TCP”。 |
    | 操作 | 保留默认值“允许”。 |
    | 优先级 | 保留默认值“100”。 |
    | 名称 | 输入“Allow-Web-All”。 |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="入站安全规则。" border="true":::

3. 使用以下值再次完成步骤 2：

    | 设置 | 值 |
    | ------- | ----- |
    | 源 | 保留默认值“任意”。 |
    | 源端口范围 | 保留默认值“(*)” |
    | 目标 | 选择“应用程序安全组”。 |
    | 目标应用程序安全组 | 选择“myAsgMgmtServers”。 |
    | 服务 | 保留默认值“自定义”。 |
    | 目标端口范围 | 输入“3389”。 |
    | 协议 | 选择 **“任何”**。 |
    | 操作 | 保留默认值“允许”。 |
    | 优先级 | 保留默认值“110”。 |
    | 名称 | 输入“Allow-RDP-All”。 |

    > [!CAUTION]
    > 在本文中，将在 Internet 上为分配给“myAsgMgmtServers”应用程序安全组的 VM 公开 RDP（端口 3389）。 
    >
    > 在生产环境中，建议使用 VPN、专用网络连接或 Azure Bastion 来连接到要管理的 Azure 资源，而不要向 Internet 公开端口 3389。
    >
    > 有关 Azure Bastion 的详细信息，请参阅[什么是 Azure Bastion？](../bastion/bastion-overview.md)。

完成步骤 1-3 以后，请复查所创建的规则。 你的列表应如以下示例中的列表所示：

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="安全规则。" border="true":::

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM。

### <a name="create-the-first-vm"></a>创建第一个 VM

1. 在门户的左上角选择“创建资源”。

2. 选择“计算”，然后选择“虚拟机角色” 。

3. 在“创建虚拟机”的“基本信息”选项卡中，输入或选择以下信息 ：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** |  |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 |
    | **实例详细信息** |   |
    | 虚拟机名称 | 输入“myVMWeb”。 |
    | 区域 | 选择“(US)美国东部”。 |
    | 可用性选项 | 保留默认值“无需冗余”。 |
    | 映像 | 选择“Windows Server 2019 Datacenter - Gen1”。 |
    | Azure Spot 实例 | 保留默认值“未选中”。 |
    | 大小 | 选择“Standard_D2s_V3”。 |
    | **管理员帐户** |   |
    | 用户名 | 输入用户名。 |
    | Password | 输入密码。 |
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |   |
    | 公共入站端口 | 选择“无”。 |

4. 选择“网络”选项卡。

5. 在“网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **网络接口** |   |
    | 虚拟网络 | 选择“myVNet”。 |
    | 子网 | 选择“默认值 (10.0.0.0/24)”。 |
    | 公共 IP | 保留默认值“新建公共 IP”。 |
    | NIC 网络安全组 | 选择“无”。 | 

6. 选择“查看 + 创建”选项卡，或选择页面底部的“查看 + 创建”按钮 。

7. 选择“创建”。

### <a name="create-the-second-vm"></a>创建第二个 VM

再次完成步骤 1-7，但在步骤 3 中，将 VM 命名为“myVMMgmt”。 部署 VM 需要几分钟时间。 

在 VM 部署完以前，请勿转到下一步。

## <a name="associate-network-interfaces-to-an-asg"></a>将网络接口关联到 ASG

门户在创建 VM 时，已为每个 VM 创建一个网络接口，并将该网络接口附加到 VM。 

请将每个 VM 的网络接口添加到以前创建的应用程序安全组：

1. 在门户顶部的“搜索资源、服务和文档”框中，开始键入“myVMWeb” 。 当“myVMWeb”虚拟机出现在搜索结果中时，请将其选中。

2. 在“设置”中，选择“网络”。  

3. 选择“应用程序安全组”选项卡，然后选择“配置应用程序安全组” 。

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="配置应用程序安全组。" border="true":::

4. 在“配置应用程序安全组”中，选择“myAsgWebServers” 。 选择“保存”。

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="选择“应用程序安全组”。" border="true":::

5. 再次完成步骤 1 和 2，搜索 myVMMgmt 虚拟机并选择 myAsgMgmtServers ASG 。

## <a name="test-traffic-filters"></a>测试流量筛选器

1. 连接到“myVMMgmt”VM。 在门户顶部的“搜索”框中输入“myVMMgmt”。 当“myVMMgmt”出现在搜索结果中时，将其选中。 选择“连接”按钮。

2. 选择“下载 RDP 文件”。

3. 打开下载的 rdp 文件，然后选择“连接”。 输入在创建 VM 时指定的用户名和密码。

4. 选择“确定”。

5. 在连接过程中可能会收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接 。

    连接将会成功，因为允许通过端口 3389 将入站流量从 Internet 发往“myAsgMgmtServers”应用程序安全组。 
    
    “myVMMgmt”的网络接口与“myAsgMgmtServers”应用程序安全组相关联，并允许连接 。

6. 在“myVMMgmt”上打开 PowerShell 会话。 使用以下示例连接到“myVMWeb”： 

    ```powershell
    mstsc /v:myVmWeb
    ```

    从“myVMMgmt”到“myVMWeb”的 RDP 连接成功，因为同一网络中的虚拟机默认情况下可以通过任何端口与每个虚拟机进行通信 。
    
    不能从 Internet 创建到“myVMWeb”虚拟机的 RDP 连接。 “myAsgWebServers”的安全规则阻止从 Internet 连接到端口 3389 入站。 默认情况下，拒绝来自 Internet 的入站流量到所有资源。

7. 若要在“myVMWeb”虚拟机上安装 Microsoft IIS，请在“myVMWeb”虚拟机上通过 PowerShell 会话输入以下命令 ：

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. 完成 IIS 安装后，从“myVMWeb”虚拟机断开连接，从而保留“myVMMgmt”虚拟机远程桌面连接 。

9. 从“myVMMgmt”VM 断开连接。

10. 在 Azure 门户顶部的“搜索资源、服务和文档”框中，开始在计算机中键入“myVMWeb” 。 当“myVMWeb”出现在搜索结果中时，请选择它。 记下 VM 的 **公共 IP 地址**。 以下示例所示地址为 23.96.39.113，但你的地址与此不同：

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="公共 IP 地址。" border="true":::
    
11. 若要确认能否从 Internet 访问“myVMWeb”Web 服务器，请在计算机上打开 Internet 浏览器并浏览到 `http://<public-ip-address-from-previous-step>`。 

此时会看到 IIS 欢迎屏幕，因为允许通过端口 80 将入站流量从 Internet 发往“myAsgWebServers”应用程序安全组。 

附加到“myVMWeb”的网络接口与“myAsgWebServers”应用程序安全组相关联，并允许连接 。 

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除：

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。
2. 选择“删除资源组”。
3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

* 已创建一个网络安全组并将其关联到虚拟网络子网。 
* 已为 Web 和管理创建应用程序安全组。
* 已创建两个虚拟机。
* 已测试应用程序安全组网络筛选。

若要详细了解网络安全组，请参阅[网络安全组概述](./network-security-groups-overview.md)和[管理网络安全组](manage-network-security-group.md)。

默认情况下，Azure 在子网之间路由流量。 你也可以选择通过某个 VM（例如，充当防火墙的 VM）在子网之间路由流量。 

若要了解如何创建路由表，请继续学习下一教程。
> [!div class="nextstepaction"]
> [创建路由表](./tutorial-create-route-table-portal.md)
