---
title: 使用专用终结点从数据工厂托管 VNET 访问 Microsoft Azure SQL 托管实例
description: 本教程提供使用 Azure 门户设置专用链接服务，并使用专用终结点从托管 VNET 访问 SQL 托管实例的步骤。
author: lrtoyou1223
ms.author: lle
ms.service: data-factory
ms.topic: tutorial
ms.date: 05/06/2021
ms.openlocfilehash: faa08121bde07f82a24fad24ca49b2fe2d0ea618
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731459"
---
# <a name="tutorial-how-to-access-sql-managed-instance-from-data-factory-managed-vnet-using-private-endpoint"></a>教程：如何使用专用终结点从数据工厂托管 VNET 访问 SQL 托管实例

本教程提供使用 Azure 门户设置专用链接服务，并使用专用终结点从托管 VNET 访问 SQL 托管实例的步骤。

:::image type="content" source="./media/tutorial-managed-virtual-network/sql-mi-access-model.png" alt-text="显示 SQL MI 访问模型的屏幕截图。" lightbox="./media/tutorial-managed-virtual-network/sql-mi-access-model-expanded.png":::

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* **虚拟网络**。 如果你没有虚拟网络，请按照[创建虚拟网络](../virtual-network/quick-create-portal.md)中所述创建一个。
* **虚拟网络到本地网络**。 使用 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [VPN](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 在虚拟网络与本地网络之间创建连接。
* **启用了托管 VNET 的数据工厂**。 如果你没有数据工厂或者未启用托管 VNET，请按照[创建具有托管 VNET 的数据工厂](./tutorial-copy-data-portal-private.md)中所述创建一个。

## <a name="create-subnets-for-resources"></a>创建资源的子网

**使用门户在虚拟网络中创建子网**。

| 子网 | 说明 |
|:--- |:--- |
|be-subnet |后端服务器的子网|
|fe-subnet |标准内部负载均衡器的子网|
|pls-subnet|专用链接服务的子网|

:::image type="content" source="./media/tutorial-managed-virtual-network/subnets.png" alt-text="显示子网的屏幕截图。" lightbox="./media/tutorial-managed-virtual-network/subnets-expanded.png":::

## <a name="create-a-standard-load-balancer"></a>创建标准负载均衡器

使用门户创建标准内部负载均衡器。

1. 在屏幕的左上方，选择“创建资源”>“网络”>“负载均衡器”。
2. 在“创建负载均衡器”页的“基本信息”选项卡中，输入或选择以下信息：

    | 设置 | 值 |
    |:--- |:--- |
    |订阅|选择订阅。|
    |资源组|选择你的资源组。|
    |名称|输入 **myLoadBalancer**。|
    |区域|选择“美国东部”。|
    |类型|选择“内部”。|
    |SKU|选择“标准”。|
    |虚拟网络|选择你的虚拟网络。|
    |子网|选择在上一步骤中创建的“fe-subnet”。|
    |IP 地址分配|选择“动态”。|
    |可用性区域|选择“区域冗余”。|

3. 接受剩余设置的默认值，然后选择“查看 + 创建”。
4. 在“查看 + 创建”选项卡中，选择“创建”。
    
    :::image type="content" source="./media/tutorial-managed-virtual-network/create-load-balancer.png" alt-text="显示创建标准负载均衡器的步骤的屏幕截图。":::

## <a name="create-load-balancer-resources"></a>创建负载均衡器资源

### <a name="create-a-backend-pool"></a>创建后端池

后端地址池包含连接到负载均衡器的虚拟 (NIC) 的 IP 地址。

创建后端地址池 **myBackendPool** 以包含用于对 Internet 流量进行负载均衡的虚拟机。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。
2. 在“设置”下，依次选择“后端池”、“添加”。
3. 在“添加后端池”页上，键入 **myBackendPool** 作为后端池的名称，然后选择“添加”。

### <a name="create-a-health-probe"></a>创建运行状况探测器

负载均衡器使用运行状况探测器监视应用的状态。

运行状况探测器基于 VM 对运行状况检查的响应，在负载均衡器中添加或删除 VM。

创建名为 **myHealthProbe** 的运行状况探测来监视 VM 的运行状况。

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。
2. 在“设置”下，依次选择“运行状况探测”、“添加”。

    | 设置 | 值 |
    |:--- |:--- |
    |名称|输入 **myHealthProbe**。|
    |协议|选择“TCP”。|
    |端口|输入 22。|
    |时间间隔|输入 **15** 作为两次探测尝试之间的 **时间间隔**（以秒为单位）。|
    |不正常阈值|选择“2”，作为将 VM 视为不正常所要达到的 **不正常阈值** 或连续探测失败次数。|

3. 将其余字段保留默认值，然后选择“确定”。

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则用于定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量。 源端口和目标端口在规则中定义。

在本部分中，你将创建创建负载均衡器规则：

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择“myLoadBalancer”。
2. 在“设置”下，依次选择“负载均衡规则”、“添加”  。
3. 使用以下值配置负载均衡规则：

    |设置 |值 |
    |:--- |:--- |
    |名称|输入 myRule。|
    |IP 版本|选择“IPv4”。|
    |前端 IP 地址|选择“LoadBalancerFrontEnd”。|
    |协议|选择“TCP”。|
    |端口|输入 1433。|
    |后端端口|输入 1433。|
    |后端池|选择“myBackendPool”。|
    |运行状况探测|选择“myHealthProbe”。|
    |空闲超时（分钟）|将滑块移动到 15 分钟。|
    |TCP 重置|选择“已禁用”。 |

4. 将剩余的字段保留默认设置，然后选择“确定”。

## <a name="create-a-private-link-service"></a>创建专用链接服务

在本部分，在标准负载均衡器后创建专用链接服务。

1. 在 Azure 门户页面的左上部分，选择“创建资源”。
2. 在“在市场中搜索”框中搜索“专用链接” 。
3. 选择“创建”。
4. 在“专用链接中心”下的“概述”中，选择蓝色的“创建专用链接服务”按钮  。
5. 在“创建专用链接服务”的“基本信息”选项卡中，输入或选择以下信息 ：

    |设置 |值|
    |---------|--------|
    |**项目详细信息**||
    |订阅 |选择订阅。|
    |资源组 |选择你的资源组。|
    |**实例详细信息**||
    |名称  |输入 **myPrivateLinkService**。|
    |区域  |选择“美国东部”。|

6. 选择“出站设置”选项卡或选择页面底部的“下一步: 出站设置”。
7. 在“出站设置”选项卡中，输入或选择以下信息：

    | 设置 | Value |
    |:--- |:--- |
    |负载均衡器|选择“myLoadBalancer”。|
    |负载均衡器前端 IP 地址|选择“LoadBalancerFrontEnd”。|
    |源 NAT 子网|选择“pls-subnet”。|
    |启用 TCP 代理 V2|保留默认值“否”。|
    |**专用 IP 地址设置**||
    |保留默认设置。||   

8. 选择“访问安全”选项卡或选择页面底部的“下一步: 访问安全”。
9. 在“访问安全”选项卡中保留默认值“仅限基于角色的访问控制” 。
10. 选择“标记”选项卡或选择页面底部的“下一步: 标记”。
11. 选择“查看 + 创建”选项卡或选择页面底部的“下一步: 查看 + 创建”。
12. 在“查看 + 创建”选项卡中，选择“创建” 。


## <a name="create-backend-servers"></a>创建后端服务器

1. 在门户的左上方，选择“创建资源”>“计算”>“虚拟机”。
2. 在“创建虚拟机”中，在“基本信息”选项卡中键入或选择值：

    |设置 |值|
    |---------|--------|
    |**项目详细信息**||
    |订阅 |选择 Azure 订阅。|
    |资源组 |选择你的资源组。|
    |**实例详细信息**||
    |虚拟机名称  |输入“myVM1”。|
    |区域  |选择“美国东部”。|
    |可用性选项  |选择“可用性区域”。|
    |可用性区域  |选择“1”。 | 
    |映像  |选择“Ubuntu Server 18.04LTS - Gen1”。| 
    |Azure Spot 实例  |请选择“否”。| 
    |大小   |选择 VM 大小或采用默认设置。| 
    |**管理员帐户**||
    |用户名 |输入用户名。|
    |SSH 公钥源  |生成新的密钥对。|
    |密钥对名称  |mySSHKey。|    
    |**入站端口规则**||
    |公共入站端口 |无。|   

3. 选择“网络”选项卡，或选择“下一步: **磁盘”，然后选择“下一步:** 网络”。
4. 在“网络”选项卡中，选择或输入：

    | 设置 |值|
    |---------|--------|
    |**网络接口**||
    |虚拟网络 |选择你的虚拟网络。|
    |子网 |be-subnet。|
    |公共 IP |选择“无”。|
    |NIC 网络安全组 |选择“无”。|
    |**负载均衡**||
    |是否将此虚拟机置于现有负载均衡解决方案之后？|请选择“是”。|
    |**负载均衡设置**||
    |负载均衡选项 |选择“Azure 负载均衡”。|
    |选择负载均衡器 |选择“myLoadBalancer”。|
    |选择后端池 |选择“myBackendPool”。|    

5. 选择“查看 + 创建”。
6. 检查设置，然后选择“创建”。
7. 可重复步骤 1 至 6，创建多个后端服务器 VM 来实现高可用性。

## <a name="creating-forwarding-rule-to-endpoint"></a>创建到终结点的转发规则

1. 登录并将脚本 [ip_fwd.sh](https://github.com/sajitsasi/az-ip-fwd/blob/main/ip_fwd.sh) 复制到后端服务器 VM。 
2. 使用以下选项运行该脚本：<br/>
    sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433<br/>
    <FQDN/IP> 是 SQL 托管实例的主机。
    
    :::image type="content" source="./media/tutorial-managed-virtual-network/sql-mi-host.png" alt-text="显示 SQL MI 主机的屏幕截图。" lightbox="./media/tutorial-managed-virtual-network/sql-mi-host-expanded.png":::

3. 运行以下命令并检查后端服务器 VM 中的 iptables。 在 iptables 中，可以看到一条包含目标 IP 的记录。 <br/>
    sudo iptables -t nat -v -L PREROUTING -n --line-number
    
    :::image type="content" source="./media/tutorial-managed-virtual-network/command-record-2.png" alt-text="显示命令记录的屏幕截图。":::

    >[!Note]
    > 注意：如果有多个 SQL MI 或其他数据源，则需要使用不同的端口定义多个负载均衡器规则和 IP 表记录。 否则会发生某种冲突。 例如，<br/>
    >
    >|                  |负载均衡器规则中的端口|负载均衡器规则中的后端端口|在后端服务器 VM 中运行的命令|
    >|------------------|---------|--------|---------|
    >|SQL MI 1|1433 |1433 |sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433|
    >|SQL MI 2|1434 |1434 |sudo ./ip_fwd.sh -i eth0 -f 1434 -a <FQDN/IP> -b 1433|
    
## <a name="create-a-private-endpoint-to-private-link-service"></a>创建用于连接专用链接服务的专用终结点

1. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中选择你的数据工厂。
2. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。
3. 转到“管理”选项卡，然后转到“托管专用终结点”部分 。
4. 在“托管专用终结点”下选择“+新建” 。
5. 在列表中选择“专用链接服务”磁贴，然后选择“继续” 。
6. 输入专用终结点的名称，然后在专用链接服务列表中选择“myPrivateLinkService”。
7. 添加目标 SQL 托管实例的 FQDN。
    
    :::image type="content" source="./media/tutorial-managed-virtual-network/sql-mi-host.png" alt-text="显示 SQL MI 主机的屏幕截图。" lightbox="./media/tutorial-managed-virtual-network/sql-mi-host-expanded.png":::


    :::image type="content" source="./media/tutorial-managed-virtual-network/private-endpoint-5.png" alt-text="显示专用终结点设置的屏幕截图。":::

8. 创建专用终结点。

## <a name="create-a-linked-service-and-test-the-connection"></a>创建链接服务并测试连接

1. 转到“管理”选项卡，然后转到“托管专用终结点”部分 。
2. 选择“链接服务”下的“+ 新建” 。
3. 在列表中选择“Azure SQL 数据库托管实例”磁贴，然后选择“继续” 。    

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-mi-1.png" alt-text="显示链接服务创建页的屏幕截图。":::        

4. 启用“交互式创作”。

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-mi-2.png" alt-text="显示如何启用“交互式创作”的屏幕截图。":::
  
5. 输入 SQL 托管实例的主机，以及用户名和密码  。

    >[!Note]
    >请手动输入 SQL 托管实例主机。 否则，选择列表中不会显示完全限定的域名。

6. 然后单击“测试连接”。

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-mi-3.png" alt-text="显示 SQL MI 链接服务创建页的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

请继续学习以下教程，了解如何使用专用终结点从数据工厂托管 VNET 访问本地 SQL Server：

> [!div class="nextstepaction"]
> [从数据工厂托管 VNET 访问本地 SQL Server](tutorial-managed-virtual-network-on-premise-sql-server.md)