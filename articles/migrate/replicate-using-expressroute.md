---
title: Azure Migrate Server 迁移通过 ExpressRoute 复制数据
description: 如何将 Azure ExpressRoute 用于 Azure Migrate 服务器迁移的复制
author: ms-deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: fe44ff423240cbe24d91cef0c0f4bb803ad8df98
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744439"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>通过 ExpressRoute 复制数据与 Azure Migrate：服务器迁移

在本文中，你将了解如何配置 [Azure Migrate：服务器迁移](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) ，通过 ExpressRoute 线路在将服务器迁移到 Azure 时复制数据。

## <a name="understand-azure-expressroute-circuits"></a>了解 Azure ExpressRoute 线路
ExpressRoute (ER) 线路通过连接提供商将本地基础结构连接到 Microsoft。 ExpressRoute 线路可以配置为使用专用对等互连和/或 Microsoft 对等互连。 查看有关 expressroute [线路和对等互连](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) 的文章，详细了解 expressroute 提供的各种对等互连选项。

Azure Migrate 的服务器迁移工具可帮助你将本地服务器和服务器从其他云迁移到 Azure 虚拟机。 此工具的工作原理是设置正在进行的复制流，将要迁移的服务器中的数据复制到 Azure 订阅中的托管磁盘。 准备好迁移服务器时，将使用 Azure 中的复制数据来迁移服务器。

可以将从本地服务器复制的数据配置为通过 internet 发送到你的 Azure 订阅 (使用安全加密的连接) 或通过 ExpressRoute 连接。 当你有大量要迁移的服务器时，使用 ExpressRoute 进行复制可通过使用 ExpressRoute 线路提供的预配带宽来更有效地迁移服务器。

本文介绍
> [!div class="checklist"]
>
> * 如何使用具有专用对等互连的 ExpressRoute 线路复制数据。
> * 如何使用 ExpressRoute 线路和 Microsoft 对等互连来复制数据。

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>使用具有专用对等互连的 ExpressRoute 线路复制数据

> [!NOTE]
> 目前仅支持通过专用对等环路复制 [到 Azure 的 VMware 虚拟机的无代理迁移](./tutorial-migrate-vmware.md)。 即将推出其他 [复制方法](./migrate-services-overview.md#azure-migrate-server-migration-tool) 的专用终结点支持。

在将 VMware 虚拟机迁移到 Azure 的无代理方法中，Azure Migrate 设备首先将复制数据上传到存储帐户， (缓存存储帐户) 订阅。 然后，Azure Migrate 服务会将缓存存储帐户中的复制数据移动到订阅中的副本托管磁盘。 若要使用专用对等电路进行复制，需创建专用终结点并将其附加到缓存存储帐户使用。 专用终结点使用虚拟网络中的一个或多个专用 IP 地址 (VNet) ，将存储帐户有效地引入 Azure VNet。 专用终结点允许 Azure Migrate 设备使用 ExpressRoute 专用对等互连连接到缓存存储帐户，并直接在专用 IP 地址上传输数据。 <br/>  

![复制过程](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - 除了复制数据以外，Azure Migrate 设备还会与 Azure Migrate 服务通信，以实现其控制平面活动，包括协调复制。 Azure Migrate 设备与 Azure Migrate 服务之间的控制平面通信在 Azure Migrate 服务的公共终结点上继续通过 internet 进行。
> - 应可从部署 Azure Migrate 设备的网络访问存储帐户的专用终结点。
> - DNS 必须配置为将缓存存储帐户的 blob 服务终结点的 Azure Migrate 设备解析 DNS 查询，以将其附加到缓存存储帐户的专用终结点的专用 IP 地址。
> - 必须可在其公共终结点上访问缓存存储帐户。  (Azure Migrate 服务使用缓存存储帐户的公共终结点将数据从存储帐户移到副本托管磁盘。 )  


### <a name="1-pre-requisites"></a>1. 先决条件

创建专用终结点的 Azure 用户应该对要在其中创建专用终结点的资源组和虚拟网络具有以下权限。

**用例** | **权限** 
--- | --- 
 创建和管理专用终结点。 | PrivateEndpoint/写入/操作<br/>PrivateEndpoint/读取/操作  
|将专用终结点附加到 VNet/子网。<br/>这是将在其中创建专用终结点的虚拟网络所必需的。| VirtualNetworks//子网/加入/操作 virtualNetworks/联接/操作
|将专用终结点链接到存储帐户。 <br/>| StorageAccounts/privateEndpointConnectionApproval/action <br/> StorageAccounts/privateEndpointConnections/read
|创建一个网络接口并将其加入网络安全组。 | Microsoft.Network/networkInterfaces/read <br/> NetworkInterfaces/子网/写入 <br/> NetworkInterfaces/子网/读取<br/> NetworkSecurityGroups/join/action (可选) 
创建和管理专用 DNS 区域。| 专用 DNS 区域参与者角色 <br/> _Or_ <br/> PrivateDnsZones//// <br/>  PrivateDnsZones//write privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/>  Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="2-identify-the-cache-storage-account"></a>2. 标识缓存存储帐户 
 
在 Azure Migrate 项目中首次使用虚拟机的 Azure 门户体验)  (时，Azure Migrate 会自动创建一个缓存存储帐户。 存储帐户是在中创建 Azure Migrate 项目的同一订阅和资源组中创建的。

若要创建并查找存储帐户，请执行以下操作：

1. 使用 Azure Migrate 的 Azure 门户体验来复制项目中的一个或多个虚拟机。
2. 导航到 Azure Migrate 项目的资源组。
3. 通过在存储帐户名称中标识前缀 **"lsa"** 定位缓存存储帐户。

![资源组视图](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> 如果资源组中有多个前缀为 **"lsa"** 的存储帐户，可以通过导航到项目中任何复制 vm 的 "复制设置" 和 "目标配置" 菜单来验证存储帐户。 <br/> 
> ![复制设置概述](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. 将缓存存储帐户升级到常规用途 v2 

只能在常规用途 v2 (GPv2) 存储帐户上创建专用终结点。 如果缓存存储帐户不是 GPv2 存储帐户，请使用以下步骤将其升级到 GPv2：

1. 导航到自己的存储帐户。
2. 选择“配置”。
3. 在 " **帐户类型**" 下，选择 " **升级**"。
4. 在“确认升级”下键入帐户名称。
5. 选择页面底部的 " **升级** "。

![升级存储帐户](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. 为存储帐户创建专用终结点

1. 请在存储帐户中，从左侧菜单中选择 " **网络** "，然后选择 " **专用终结点连接** " 选项卡。  
2. 选择 “+ 专用终结点”。

    a. 在 " **创建专用终结点** " 窗口中，选择 " **订阅** " 和 " **资源组**"。 提供专用终结点的名称，并选择存储帐户区域。  
    ![PE 配置窗口](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. 在 " **资源** " 选项卡中，提供存储帐户所在的 **订阅名称** 。 选择 " **storageAccounts** " 作为 **资源类型**。 在 " **资源**" 中，提供 GPv2 类型的复制存储帐户的名称。 选择 " **Blob** " 作为 " **目标子资源**"。  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. 在 " **配置** " 选项卡中，选择用于存储帐户的专用终结点的 **虚拟网络** 和 **子网** 。  

    > [!Note]
    > 虚拟网络必须包含 ExpressRoute 网关终结点，或者必须通过 ExpressRoute 网关连接到虚拟网络。 

    在 **专用 DNS 集成** "部分中，选择 **" 是 "** ，并将与专用 DNS 区域集成。 选择 **"是"** 会自动将 dns 区域链接到所选虚拟网络，并添加 dns 解析新 ip 和为专用终结点创建的完全限定域名所需的 dns 记录。 了解有关[专用 DNS 区域的](https://docs.microsoft.com/azure/dns/private-dns-overview)详细信息。

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. 你还可以为专用终结点添加 **标记** 。  

    e. 输入详细信息后，继续“查看 + 创建”。 完成验证后，选择“创建”以创建专用终结点。

    > [!Note]
    > 如果创建专用终结点的用户也是存储帐户的所有者，则将自动批准专用终结点。 否则，所有者必须批准专用终结点才能使用。 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>创建专用 DNS 区域，并手动添加 DNS 记录 (可选) 

如果未选择在创建专用终结点时与专用 DNS 区域集成的选项，请按照本部分中的步骤手动创建专用 DNS 区域。 

> [!Note]
> 如果选择 **"是"** 以与专用 DNS 区域集成，则可以跳过此部分。 

1. 创建专用 DNS 区域。 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a.  在 " **专用 DNS 区域** " 页上，选择 " **+ 添加** " 按钮开始创建新区域。  
    b.  在 " **创建专用 DNS 区域** " 页上，填写所需的详细信息。 输入专用 DNS 区域的名称 _privatelink_. blob.core.windows.net。 c. 继续查看 " **查看 + 创建** " 选项卡，查看并创建 DNS 区域。

2. 将专用 DNS 区域链接到虚拟网络。  

    上面创建的专用 DNS 区域必须链接到专用终结点所附加到的虚拟网络。

    a. 转到上一步中创建的专用 DNS 区域，然后导航到页面左侧的 "虚拟网络" 链接。 选择 " **+ 添加** " 按钮。   
    b. 填写必需的详细信息。 必须用附加了专用终结点的虚拟网络的相应详细信息填充 " **订阅** " 和 " **虚拟网络** " 字段。 其他字段可以保持原样。

3. 下一步是将 DNS 记录添加到 DNS 区域。 将存储帐户的完全限定域名的条目添加到专用 DNS 区域。

    a. 转到你的专用 DNS 区域，导航到页面左侧的“概览”部分。 选择 " **+ 记录** 集" 以开始添加记录。  

    b. 在 " **添加记录集** " 页中，添加完全限定的域名和专用 IP 的条目作为 A 类型记录。

> [!Important]
> 你可能需要其他 DNS 设置，以便从源环境解析存储帐户专用终结点的专用 IP 地址。 [查看本文](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) ，了解所需的 DNS 配置。

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>使用 ExpressRoute 线路和 Microsoft 对等互连复制数据

你可以使用 Microsoft 对等互连或现有的公共对等域 (不推荐用于新的 ExpressRoute 连接) 通过 ExpressRoute 线路路由复制流量，如下图所示。
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

即使在通过 Microsoft 对等互连线路传输复制数据的情况下，仍需要从本地站点建立 internet 连接，才能与 Azure Migrate 服务)  (控制平面。 有些附加 Url 无法通过 ExpressRoute 访问，复制设备/Hyper-v 主机需要访问来协调复制过程。 你可以根据迁移方案、 [VMware 无代理迁移](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) 或 [基于代理](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance)的迁移来查看 URL 要求。  

如果你在本地站点上使用代理并想要将 ExpressRoute 用于复制流量，则需要在本地设备上为相关 Url 配置 "跳过代理"。 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>在 Azure Migrate 设备上配置代理绕过规则 (用于 VMware 无代理迁移) 

1. 登录 (远程桌面) 到 Azure Migrate 设备。   
2. 使用记事本打开 file C：/ProgramData/Microsoftazure.mobileengagement/Config/appliance.js。
3. 在文件中，将显示为 "EnableProxyBypassList" 的行更改为 "EnableProxyBypassList"： "true"。 保存更改并重新启动设备。
4. 重新启动后，当你打开设备配置管理器时，你将能够在 web 应用 UI 中看到 "代理跳过" 选项。 将以下 Url 添加到代理跳过列表。   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>在复制设备上配置代理绕过规则 (用于基于代理的迁移) 

按照以下步骤配置配置服务器和进程服务器上的代理跳过列表：

1. [下载 PsExec 工具](https://docs.microsoft.com/sysinternals/downloads/psexec) 来访问系统用户上下文。
2. 通过运行以下命令行 psexec-s-i "%Programfiles%\internet explorer\ Explorer\iexplore.exe" 在系统用户上下文中打开 Internet Explorer
3. 在 IE 中添加代理设置。
4. 在跳过列表中，添加 Azure 存储 URL. *. .net。  

上述旁路规则将确保在管理通信可以通过 ExpressRoute 的情况下，复制流量可以流过 ExpressRoute。  

此外，还必须在路由筛选器中播发以下 BGP 团体的路由，使 Azure Migrate 复制流量遍历 ExpressRoute 线路而不是 internet。  

- 用于源 Azure 区域 (Azure Migrate 项目区域的区域 BGP 社区) 
- 用于迁移的目标 Azure 区域 (区域的区域 BGP 社区) 
- 用于 Azure Active Directory (12076:5060 的 BGP 社区) 

了解有关 [路由筛选器](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) 的详细信息以及 [适用于 ExpressRoute 的 BGP 社区](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp)列表。 

## <a name="next-steps"></a>后续步骤

- 详细了解 [ExpressRoute 线路](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings)。
- 详细了解 [ExpressRoute 路由域](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare)。
- 了解有关 [专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)的详细信息。