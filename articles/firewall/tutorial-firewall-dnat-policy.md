---
title: 教程：在门户中通过 Azure 防火墙 DNAT 策略筛选入站 Internet 流量
description: 在本教程中，你将学习如何使用 Azure 门户部署和配置 Azure 防火墙策略 DNAT。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2f863c9fc5bad369813ee37c51ded73e96980a54
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123034199"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-policy-dnat-using-the-azure-portal"></a>教程：在 Azure 门户中通过 Azure 防火墙策略 DNAT 筛选入站 Internet 流量

可配置 Azure 防火墙策略目标网络地址转换 (DNAT)，以转换和筛选到子网的入站 Internet 流量。 配置 DNAT 时，规则收集操作设置为“DNAT”。 然后，可以使用 NAT 规则集合中的每个规则将防火墙公共 IP 地址和端口转换为专用 IP 地址和端口。 DNAT 规则会隐式添加一个对应的网络规则来允许转换后的流量。 出于安全原因，推荐的方法是添加特定的 Internet 源以允许 DNAT 访问网络，并避免使用通配符。 若要详细了解 Azure 防火墙规则处理逻辑，请参阅 [Azure 防火墙规则处理逻辑](rule-processing.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置测试网络环境
> * 部署防火墙和策略
> * 创建默认路由
> * 配置 DNAT 规则
> * 测试防火墙

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。



## <a name="create-a-resource-group"></a>创建资源组

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 在 Azure 门户主页上，依次选择“资源组”、“添加”。  
4. 对于“订阅”，请选择自己的订阅。 
1. 对于“资源组名称”，请键入 **RG-DNAT-Test**。 
5. 对于“区域”，选择一个区域。 你创建的所有其他资源必须位于同一区域中。
6. 选择“查看 + 创建”。
1. 选择“创建”  。

## <a name="set-up-the-network-environment"></a>设置网络环境

请为本教程创建两个对等互连的 VNet：

- **VN-Hub** - 防火墙在此 VNet 中。
- **VN-Spoke** - 工作负荷服务器在此 VNet 中。

首先创建 VNet，然后将其对等互连。

### <a name="create-the-hub-vnet"></a>创建中心 VNet

1. 在 Azure 门户主页上，选择“所有服务”。 
2. 在“网络”下，选择“虚拟网络”。  
3. 选择“添加”   。
7. 对于“资源组”，请选择“RG-DNAT-Test” 。
1. 对于“名称”，请键入“VN-Hub”。  
1. 对于“区域”，请选择之前使用的同一区域。
1. 在完成时选择“下一步:IP 地址”。
1. 对于“IPv4 地址空间”，请接受默认值“10.0.0.0/16”。
1. 在“子网名称”下，选择“默认值”。
1. 编辑“子网名称”，然后键入“AzureFirewallSubnet”。

     防火墙将位于此子网中，子网名称 **必须** 是 AzureFirewallSubnet。
     > [!NOTE]
     > AzureFirewallSubnet 子网的大小为 /26。 有关子网大小的详细信息，请参阅 [Azure 防火墙常见问题解答](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size)。

10. 对于“子网地址范围”，请键入“10.0.1.0/26” 。
11. 选择“保存”。
1. 选择“查看 + 创建”。
1. 选择“创建”。

### <a name="create-a-spoke-vnet"></a>创建辐射 VNet

1. 在 Azure 门户主页上，选择“所有服务”。 
2. 在“网络”下，选择“虚拟网络”。  
3. 选择“添加”   。
1. 对于“资源组”，请选择“RG-DNAT-Test” 。
1. 对于“名称”  ，请键入 **VN-Spoke**。
1. 对于“区域”，请选择之前使用的同一区域。
1. 在完成时选择“下一步:IP 地址”。
1. 对于“IPv4 地址空间”，请编辑默认值并键入“192.168.0.0/16”。
1. 选择“添加子网”。
1. 对于“子网名称”，请键入“SN-Workload” 。
10. 对于“子网地址范围”，请键入“192.168.1.0/24” 。
11. 选择 **添加** 。
1. 选择“查看 + 创建”  。
1. 选择“创建”。

### <a name="peer-the-vnets"></a>在 VNet 之间建立对等互连

现在，请将这两个 VNet 对等互连。

1. 选择“VN-Hub”虚拟网络。 
2. 在“设置”下选择“对等互连”。  
3. 选择“添加”   。
4. 在“此虚拟网络”下的“对等互连链接名称”中，键入“Peer-HubSpoke”。
5. 在“远程虚拟网络”下的“对等互连链接名称”中，键入“Peer-SpokeHub”。 
1. 选择 **VN-Spoke** 作为虚拟网络。
1. 接受所有其他默认值，然后选择“添加”。

## <a name="create-a-virtual-machine"></a>创建虚拟机

创建一个工作负荷虚拟机，将其置于 **SN-Workload** 子网中。

1. 在 Azure 门户菜单中，选择“创建资源”  。
2. 在“常用”下，选择“Windows Server 2016 Datacenter”。  

**基础知识**

1. 对于“订阅”，请选择自己的订阅。 
1. 对于“资源组”，请选择“RG-DNAT-Test” 。
1. 对于“虚拟机名称”，请键入 **Srv-Workload**。 
1. 对于“区域”，请选择以前使用的同一位置。 
1. 键入用户名和密码。
1. 在完成时选择“下一步:**磁盘”** 。

**磁盘**
1. 在完成时选择“下一步:  网络”。

**联网**

1. 对于“虚拟网络”，请选择“VN-Spoke”。  
2. 对于“子网”，请选择“SN-Workload”。  
3. 对于“公共 IP”，请选择“无”。
4. 对于“公共入站端口”，请选择“无”。   
2. 保留其他默认设置，然后选择“下一步: 管理”。

**Management**

1. 对于“启动诊断”，请选择“禁用” 。
1. 选择“查看 + 创建”  。

**查看 + 创建**

查看摘要，然后选择“创建”。  完成此设置可能需要几分钟时间。

部署完成后，请记下虚拟机的专用 IP 地址。 该地址会在稍后配置防火墙时使用。 选择虚拟机名称，然后在“设置”下选择“网络”，找到专用 IP 地址。  

## <a name="deploy-the-firewall-and-policy"></a>部署防火墙和策略

1. 在门户主页上，选择“创建资源”。 
1. 搜索“防火墙”，然后选择“防火墙” 。
1. 选择“创建”。 
1. 在“创建防火墙”页上，使用下表配置防火墙：

   |设置  |值  |
   |---------|---------|
   |订阅     |\<your subscription\>|
   |资源组     |选择“RG-DNAT-Test” |
   |名称     |**FW-DNAT-test**|
   |区域     |选择前面使用的同一位置|
   |防火墙管理|**使用防火墙策略来管理此防火墙**|
   |防火墙策略|**添加新项**：<br>**fw-dnat-pol**<br>你选择的区域 
   |选择虚拟网络     |**使用现有项**：VN-Hub|
   |公共 IP 地址     |新建，名称：fw-pip。|

5. 接受其他默认值，然后选择“查看 + 创建”。
6. 查看摘要，然后选择“创建”以创建防火墙。 

   部署过程需要花费几分钟时间。
7. 部署完成后，转到“RG-DNAT-Test”资源组，然后选择“FW-DNAT-test”防火墙。  
8. 记下防火墙专用 IP 地址和公共 IP 地址。 稍后在创建默认路由和 NAT 规则时，将用到它们。

## <a name="create-a-default-route"></a>创建默认路由

对于“SN-Workload”子网，请配置要通过防火墙的出站默认路由。 

1. 在 Azure 门户主页上，选择“所有服务”。 
2. 在“网络”下，选择“路由表”。  
3. 选择 **添加** 。
5. 对于“订阅”，请选择自己的订阅。 
1. 对于“资源组”，请选择“RG-DNAT-Test” 。
1. 对于“区域”，请选择前面使用的同一区域。
1. 对于“名称”，请键入“RT-FW-route” 。
1. 选择“查看 + 创建”  。
1. 选择“创建”。
1. 选择“转到资源”。
1. 依次选择“子网”、“关联”。  
1. 对于“虚拟网络”，请选择“VN-Spoke”。  
1. 对于“子网”，请选择“SN-Workload”。  
1. 选择“确定”  。
1. 依次选择“路由”、“添加”。  
1. 对于“路由名称”，请键入 **fw-dg**。
1. 对于“地址前缀”，请键入 **0.0.0.0/0**。 
1. 对于“下一跃点类型”，请选择“虚拟设备”。  

    Azure 防火墙实际上是一个托管服务，但虚拟设备可在此场合下正常工作。
18. 对于“下一跃点地址”，请键入前面记下的防火墙专用 IP 地址。 
19. 选择“确定”  。

## <a name="configure-a-nat-rule"></a>配置 NAT 规则

此规则允许通过防火墙将远程桌面连接到 Srv-Workload 虚拟机。

1. 打开 RG-DNAT-Test 资源组，然后选择 fw-dnat-pol 防火墙策略 。 
1. 在“设置”下，选择“DNAT 规则” 。
2. 选择“添加规则集合”。
3. 对于“名称”，请键入“rdp”。 
1. 对于“优先级”，请键入 **200**。
1. 对于“规则收集组”，选择“DefaultDnatRuleCollectionGroup” 。
1. 在“规则”下，对于“名称”，键入“rdp-nat”  。
1. 对于 **源类型**，请选择“IP 地址”。
1. 对于“源”，请键入 \* 。
1. 对于“协议”，请选择“TCP”。 
1. 对于“目标端口”，请键入 **3389**。
1. 对于“目标类型”，请选择“IP 地址”。 
1. 对于“目标”，请键入防火墙公共 IP 地址。
1. 对于“已转换的地址”，键入 Srv-Workload 的专用 IP 地址 。
1. 对于“已翻译的端口”  ，键入 **3389**。
1. 选择 **添加** 。


## <a name="test-the-firewall"></a>测试防火墙

1. 将远程桌面连接到防火墙公共 IP 地址。 此时会连接到 **Srv-Workload** 虚拟机。
2. 关闭远程桌面。

## <a name="clean-up-resources"></a>清理资源

可以将防火墙资源保留到下一教程使用。不再需要时，请删除 **RG-DNAT-Test** 资源组，以删除与防火墙相关的所有资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署和配置 Azure 防火墙高级版](premium-deploy.md)