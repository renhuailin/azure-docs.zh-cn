---
title: 使用 Azure Migrate 服务器迁移通过 ExpressRoute 复制数据
description: 使用 Azure Migrate 服务器迁移通过 Azure ExpressRoute 进行复制。
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: 3a6afa0fadf5a84ad938b0b0cec321c0e17adeff
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108317516"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>使用 Azure Migrate: 服务器迁移通过 ExpressRoute 复制数据

在本文中，你将了解如何配置 [Azure Migrate：服务器迁移](./migrate-services-overview.md#azure-migrate-server-migration-tool)工具，以便在将服务器迁移到 Azure 时通过 Azure ExpressRoute 线路复制数据。

## <a name="understand-azure-expressroute-circuits"></a>了解 Azure ExpressRoute 线路

ExpressRoute 线路通过连接提供商将本地基础结构连接到 Microsoft。 你可将 ExpressRoute 线路配置为使用专用对等互连和/或 Microsoft 对等互连。 若要了解有关 ExpressRoute 对等互连选项的详细信息，请参阅 [ExpressRoute 线路和对等互连](../expressroute/expressroute-circuit-peerings.md#peeringcompare)。

“Azure Migrate：服务器迁移”工具可帮助你将本地服务器和其他云中的服务器迁移到 Azure 虚拟机。 此工具会建立持续的复制流，将所要迁移的服务器中的数据复制到 Azure 订阅的托管磁盘。 准备好迁移服务器时，就会使用复制到 Azure 中的数据来迁移服务器。

你可配置从本地服务器复制的数据，以通过 Internet 或 ExpressRoute 连接发送到 Azure 订阅。 通过 Internet 发送的数据会使用安全的加密连接。 你有许多服务器要迁移时，使用 ExpressRoute 进行复制可帮助你通过使用 ExpressRoute 线路提供的预配带宽更有效地迁移。

本文介绍如何使用以下线路复制数据：
> [!div class="checklist"]
>
> * 具备专用对等互连的 ExpressRoute 线路。
> * 具备 Microsoft 对等互连的 ExpressRoute 线路。

## <a name="replicate-data-by-using-an-expressroute-circuit-with-private-peering"></a>使用具备专用对等互连的 ExpressRoute 线路复制数据

> [!Note]
> 本文说明如何通过专用对等互连线路进行复制，[以无代理方式将 VMware 虚拟机迁移到 Azure](./tutorial-migrate-vmware.md)。 若要将专用终结点支持用于[其他复制方法](./migrate-services-overview.md#azure-migrate-server-migration-tool)，请参阅[将 Azure Migrate 与专用终结点配合使用](./how-to-use-azure-migrate-with-private-endpoints.md)。
 
在将 VMware 虚拟机迁移到 Azure 的无代理方法中，Azure Migrate 设备首先将复制数据上传到订阅中的存储帐户（缓存存储帐户）。 然后，Azure Migrate 会将复制的数据从缓存存储帐户转移到订阅中的副本托管磁盘。

若要使用专用对等互连线路进行复制，需创建专用终结点，并将其附加到缓存存储帐户。 专用终结点使用虚拟网络中的一个或多个专用 IP 地址，将该存储帐户有效接入 Azure 虚拟网络。 该专用终结点允许 Azure Migrate 设备使用 ExpressRoute 专用对等互连来连接至该缓存存储帐户。 然后，就可以直接在该专用 IP 地址上传输数据。 <br/>

![此屏幕截图显示复制过程。](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
> - 除复制数据以外，Azure Migrate 设备还会与 Azure Migrate 服务通信，以实现其控制平面活动。 这些活动包括协调复制。 Azure Migrate 设备与 Azure Migrate 服务之间的控制平面通信在 Azure Migrate 服务的公共终结点上继续通过 Internet 进行。
> - 该存储帐户的专用终结点应当可从 Azure Migrate 设备所部署在的网络中访问。
> - DNS 必须配置为，通过 Azure Migrate 设备为缓存存储帐户的 blob 服务终结点将 DNS 查询解析到连接到缓存存储帐户的专用终结点的专用 IP 地址。
> - 必须可在缓存存储帐户的公共终结点上访问该帐户。 Azure Migrate 使用缓存存储帐户的公共终结点，将数据从存储帐户转移到副本托管磁盘。

### <a name="prerequisites"></a>先决条件

对于要在其中创建专用终结点的资源组和虚拟网络，你需要下列权限。

使用案例 | 权限
--- | --- 
 创建和管理专用终结点。 | Microsoft.Network/privateEndpoint/write/action<br/>Microsoft.Network/privateEndpoint/read/action 
|将专用终结点附加到虚拟网络或子网。<br/>对于要在其中创建专用终结点的虚拟网络，必须具有此权限。| Microsoft.Network/virtualNetworks/subnet/join/action <br/> Microsoft.Network/virtualNetworks/join/action
|将专用终结点链接到存储帐户。 <br/>| Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnections/read
|创建一个网络接口并将其加入网络安全组。 | Microsoft.Network/networkInterfaces/read <br/> Microsoft.Network/networkInterfaces/subnets/write <br/> Microsoft.Network/networkInterfaces/subnets/read<br/> Microsoft.Network/networkSecurityGroups/join/action（可选）
创建和管理专用 DNS 区域。| 专用 DNS 区域参与者角色 <br/> _Or_ <br/> Microsoft.Network/privateDnsZones/A/* <br/> Microsoft.Network/privateDnsZones/write Microsoft.Network/privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="identify-the-cache-storage-account"></a>识别缓存存储帐户

 当你首次在 Azure Migrate 项目中为虚拟机配置复制（使用 Azure 门户体验）时，Azure Migrate 会自动创建一个缓存存储帐户。 创建该存储帐户所在的订阅和资源组，就是你创建 Azure Migrate 项目所在的订阅和资源组。

若要创建并查找存储帐户，请执行以下操作：

1. 使用 Azure 门户来复制 Azure Migrate 项目中的一个或多个虚拟机。
1. 转到 Azure Migrate 项目的资源组。
1. 通过识别存储帐户名中的前缀 lsa 找到缓存存储帐户。

   ![此屏幕截图显示资源组视图。](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> 如果资源组中有多个前缀为 lsa 的存储帐户，可以通过导航到项目中任何复制 VM 的复制设置和目标配置菜单来验证存储帐户。
>
> ![此屏幕截图显示复制设置概况。](./media/replicate-using-expressroute/storage-account.png)

### <a name="upgrade-the-cache-storage-account-to-general-purpose-v2"></a>将缓存存储帐户升级到常规用途 v2

只能在常规用途 v2 存储帐户上创建专用终结点。 如果缓存存储帐户不是常规用途 v2 存储帐户，请将其升级。

1. 转到存储帐户。
1. 选择“配置”。
1. 在“帐户类型”下选择“升级”。
1. 在“确认升级”下输入帐户名称。
1. 选择页面底部的“升级”。

   ![此屏幕截图显示如何升级存储帐户。](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="create-a-private-endpoint-for-the-storage-account"></a>创建存储帐户的专用终结点

1. 转到存储帐户，从左侧菜单中选择“网络”，然后选择“专用终结点连接”选项卡。
1. 选择 “+ 专用终结点”。

    1. 在“创建专用终结点”窗口中，选择“订阅”和“资源组”。   输入专用终结点名称，并选择存储帐户区域。
     
       ![此屏幕截图显示专用终结点配置窗口。](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    1. 在“资源”选项卡上，输入存储帐户所在的“订阅名称”。  选择“Microsoft.Storage/storageAccounts”作为“资源类型”。  在“资源”中，输入常规用途 v2 类型复制存储帐户的名称。 选择“blob”作为“目标子资源”。 
     
       ![此屏幕截图显示存储帐户专用终结点设置。](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    1. 在“配置”选项卡上，选择存储帐户专用终结点的“虚拟网络”和“子网”。  

       > [!Note]
       > 该虚拟网络必须包含 ExpressRoute 网关终结点，或者通过 ExpressRoute 网关连接到虚拟网络。

       在“专用 DNS 集成”部分，选择“是”，然后与专用 DNS 区域集成。  选择“是”会自动将该 DNS 区域链接到选定的虚拟网络。 另外，此操作还会添加对新 IP 进行 DNS 解析所需的 DNS 记录，以及为该专用终结点创建的完全限定域名 (FQDN)。 详细了解[专用 DNS 区域](../dns/private-dns-overview.md)。

       ![此屏幕截图显示专用 DNS 区域。](./media/replicate-using-expressroute/private-dns-zone.png)

    1. 还可以为专用终结点添加标记。

    1. 输入详细信息完成后，请选择“查看 + 创建”选项卡。验证完成后，请选择“创建”以创建该专用终结点。 

> [!Note]
> 如果创建该专用终结点的用户也是该存储帐户的所有者，则会自动批准该专用终结点。 否则，所有者必须批准该专用终结点才能使用。

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>创建专用 DNS 区域并手动添加 DNS 记录（可选）

如果在创建专用终结点时没有选择与专用 DNS 区域集成的选项，则需要手动创建专用 DNS 区域。

> [!Note]
> 如果已选择“是”与专用 DNS 区域集成，则可以跳过此部分。

若要手动创建专用 DNS 区域：

1. 选择“专用 DNS 区域”。

    ![此屏幕截图显示创建专用 DNS 区域。](./media/replicate-using-expressroute/create-private-dns.png)

    1. 在“专用 DNS 区域”页上，选择“+ 添加”以创建新区域。 
    1. 在“创建专用 DNS 区域”页上，填写所需的详细信息。 输入 privatelink.blob.core.windows.net 作为专用 DNS 区域名称。
    1. 在“查看 + 创建”选项卡上，查看并创建该 DNS 区域。

1. 将专用 DNS 区域链接到虚拟网络。

    创建的专用 DNS 区域必须链接到专用终结点所附加到的虚拟网络。

    1. 转到在上一步创建的专用 DNS 区域，然后转到页面左侧的“虚拟网络链接”。 选择“+ 添加”。
    1. 填写必需的详细信息。 必须使用专用终结点附加到的虚拟网络的相应详细信息填写“订阅”和“虚拟网络”字段。 其他字段可以保留不动。

1. 下一步是将 DNS 记录添加到 DNS 区域。 在专用 DNS 区域中，添加对应于存储帐户 FQDN 的条目。

    1. 转到你的专用 DNS 区域，然后转到页面左侧的“概览”部分。 选择“+记录”开始添加记录。
    1. 在“添加记录集”页上，添加一个对应于 FQDN 和专用 IP 的条目作为 A 类记录。

> [!Important]
> 可能需要其他 DNS 设置，以便解析源环境中存储帐户专用终结点的专用 IP 地址。 若要了解所需的 DNS 配置，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)。

## <a name="replicate-data-by-using-an-expressroute-circuit-with-microsoft-peering"></a>使用具备 Microsoft 对等互连的 ExpressRoute 线路复制数据

你可使用 Microsoft 对等互连或现有公共对等互连域（不建议用于新的 ExpressRoute 连接），通过 ExpressRoute 线路路由复制流量。

![此图显示使用 Microsoft 对等互连进行复制。](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

即使在通过 Microsoft 对等互连线路传输复制数据的情况下，仍需要从本地站点建立 Internet 连接，才能与 Azure Migrate（控制平面）进行其他通信。 某些其他 URL 无法通过 ExpressRoute 访问。 复制设备或 Hyper-V 主机需要访问这些 URL 才能协调复制过程。 请根据迁移方案（[VMware 无代理迁移](./migrate-appliance.md#public-cloud-urls)或[基于代理的迁移](./migrate-replication-appliance.md)）查看 URL 要求。

如果你在本地站点上使用代理，并且要将 ExpressRoute 用于复制流量，请在本地设备上配置针对相关 URL 的代理跳过。

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>在 Azure Migrate 设备上配置代理跳过规则（适用于 VMware 无代理迁移）

1. 通过远程桌面登录 Azure Migrate 设备。
1. 使用记事本打开 C:/ProgramData/MicrosoftAzure/Config/appliance.json 文件。
1. 在该文件中，将内容为 `"EnableProxyBypassList": "false"` 的行更改为 `"EnableProxyBypassList": "true"`。 保存所做的更改，并重启该设备。
1. 重新启动后，当你打开设备配置管理器时，你会在 Web 应用 UI 中看到代理跳过选项。 请将下列 URL 添加到代理跳过列表中：

    - .*.vault.azure.net
    - .*.servicebus.windows.net
    - .*.discoverysrv.windowsazure.com
    - .*.migration.windowsazure.com
    - .*.hypervrecoverymanager.windowsazure.com
    - .*.blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>在复制设备上配置代理绕过规则（适用于基于代理的迁移）

若要在配置服务器和进程服务器上配置代理跳过列表：

1. 下载 [PsExec 工具](/sysinternals/downloads/psexec)来访问系统用户上下文。
1. 运行以下命令行，在系统用户上下文中打开 Internet Explorer：`psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"`。
1. 在 Internet Explorer 中添加代理设置。
1. 在绕过列表中，添加 Azure 存储 URL：*.blob.core.windows.net。

上述跳过规则可确保复制流量可以通过 ExpressRoute，而管理通信可以通过 Internet 的代理。

此外，还必须在路由筛选器中播发下列 BGP 社区的路由，使 Azure Migrate 复制流量遍历 ExpressRoute 线路而不是 Internet：

- 源 Azure 区域的区域 BGP 社区（Azure Migrate 项目区域）
- 目标 Azure 区域的区域 BGP 团体（迁移区域）
- Azure Active Directory 的 BGP (12076:5060)

详细了解[路由筛选器](../expressroute/how-to-routefilter-portal.md)以及 [ExpressRoute 的 BGP 社区](../expressroute/expressroute-routing.md#bgp)列表。

## <a name="next-steps"></a>后续步骤

请参阅下列文章以了解更多信息：

- [ExpressRoute 线路](../expressroute/expressroute-circuit-peerings.md)
- [ExpressRoute 路由域](../expressroute/expressroute-circuit-peerings.md#peeringcompare)
- [专用终结点](../private-link/private-endpoint-overview.md)