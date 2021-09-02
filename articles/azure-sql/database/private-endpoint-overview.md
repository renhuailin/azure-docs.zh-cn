---
title: Azure 专用链接
description: 专用终结点功能概述。
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.topic: overview
ms.custom: sqldbrb=1, fasttrack-edit
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: dd9fe79249fe5e02ad8a4adaf8dda02e72c954c6
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039151"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL 数据库和 Azure Synapse Analytics 的 Azure 专用链接
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

使用专用链接可以通过 **专用终结点** 连接到 Azure 中的各种 PaaS 服务。 若要查看支持专用链接功能的 PaaS 服务列表，请转到[专用链接文档](../../private-link/index.yml)页。 专用终结点是特定[ VNet ](../../virtual-network/virtual-networks-overview.md)和子网中的专用 IP 地址。

> [!IMPORTANT]
> 本文同时适用于 Azure SQL 数据库和 Azure Synapse Analytics。 为简单起见，术语“数据库”是指 Azure SQL 数据库中的数据库和 Azure Synapse Analytic 中的数据库。 同样，无论何时提及“服务器”，都是指托管 Azure SQL 数据库和 Azure Synapse Analytics[ 的逻辑 SQL Server](logical-servers.md)。 本文不适用于 **Azure SQL 托管实例**。

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>如何设置 Azure SQL 数据库的专用链接 

### <a name="creation-process"></a>创建过程
可以使用 Azure 门户、PowerShell 或 Azure CLI 创建专用终结点：
- [门户](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>审批过程
网络管理员创建专用终结点 (PE) 后，SQL 管理员可以管理与 SQL 数据库建立的专用终结点连接 (PEC)。

1. 按照下面的屏幕截图中所示的步骤，导航到 Azure 门户中的服务器资源

    - (1) 在左窗格中选择“专用终结点连接”
    - (2) 显示所有专用终结点连接 (PEC) 的列表
    - (3) 创建的相应专用终结点 (PE) ![所有 PEC 的屏幕截图][3]

1. 在列表中选择单个 PEC。
![选定 PEC 的屏幕截图][6]

1. SQL 管理员可以选择批准或拒绝 PEC，并可以选择性地添加简短的文本回复。
![PEC 审批屏幕截图][4]

1. 批准或拒绝后，该列表将反映相应的状态以及回复文本。
![审批后的所有 PEC 的屏幕截图][5]

1. 最后单击专用终结点名称 ![PEC 详细信息的屏幕截图][7]   

   指向网络接口详细信息 ![NIC 详细信息的屏幕截图][8]

   最后指向专用终结点的 IP 地址![专用 IP 的屏幕截图][9]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>测试从同一虚拟网络中的 Azure VM 到 SQL 数据库的连接
对于此场景，假设你已在与专用终结点相同的虚拟网络中创建了一个运行最新版 Windows 的 Azure虚拟机 (VM)。

1. [启动远程桌面 (RDP) 会话并连接到虚拟机](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)。 

1. 然后，可以使用以下工具执行一些基本的连接检查，以确保 VM 通过专用终结点连接到 SQL 数据库：
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>使用 Telnet 检查连接

[Telnet 客户端](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29)是可用于测试连接的 Windows 功能。 根据 Windows OS 的版本，可能需要显式启用此功能。 

安装 Telnet 后，打开命令提示符窗口。 运行 Telnet 命令并指定 SQL 数据库中的数据库的 IP 地址和专用终结点。

```
>telnet 10.9.0.4 1433
```

当 Telnet 连接成功时，命令窗口中会显示下图所示的空白屏幕：

 ![Telnet 示意图][2]

### <a name="check-connectivity-using-psping"></a>使用 Psping 检查连接

可按如下所示使用 [Psping](/sysinternals/downloads/psping) 检查专用终结点是否正在侦听端口 1433 上的连接。

按如下所示运行 Psping，并提供逻辑 SQL Server 的 FQDN 和端口 1433：

```
>psping.exe mysqldbsrvr.database.windows.net:1433
...
TCP connect to 10.9.0.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.9.0.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.9.0.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.9.0.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.9.0.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.9.0.4:1433: from 10.6.0.4:49958: 2.28ms
```

输出显示 Psping 可以 ping 通与专用终结点关联的专用 IP 地址。

### <a name="check-connectivity-using-nmap"></a>使用 Nmap 检查连接

Nmap（网络映射器）是一个用于网络发现和安全审核的免费开源工具。 有关详细信息和下载链接，请访问 https://nmap.org 。可以使用此工具来确保专用终结点侦听端口 1433 上的连接。

按如下所示运行 Nmap，并提供托管专用终结点的子网的地址范围。

```
>nmap -n -sP 10.9.0.0/24
...
Nmap scan report for 10.9.0.4
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```
结果显示，一个对应于专用终结点 IP 地址的 IP 地址已启动。

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 检查连接
> [!NOTE]
> 在客户端的连接字符串中 (`<server>.database.windows.net`) 使用服务器的完全限定域名 (FQDN)。 直接登录 IP 地址的任何尝试或使用专用链接 FQDN (`<server>.privatelink.database.windows.net`) 都将失败。 此行为是设计使然，因为专用终结点会将流量路由到该区域中的 SQL 网关，并且需要指定正确的 FQDN 才能成功登录。

请按照此处的步骤使用 [SSMS 连接到 SQL 数据库](connect-query-ssms.md)。 使用 SSMS 连接到 SQL 数据库后，以下查询将反映与你从中连接的 Azure VM 的专用 IP 地址匹配的 client_net_address：

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>限制 
与专用终结点的连接仅支持使用“代理”作为[连接策略](connectivity-architecture.md#connection-policy)


## <a name="on-premises-connectivity-over-private-peering"></a>通过专用对等互连建立本地连接

当客户从本地计算机连接到公共终结点时，需要使用[服务器级防火墙规则](firewall-create-server-level-portal-quickstart.md)将其 IP 地址添加到基于 IP 的防火墙。 尽管此模型非常适合用于允许对开发或测试工作负荷的单个计算机进行访问，但在生产环境中却难以管理。

借助专用链接，客户可以使用 [ExpressRoute](../../expressroute/expressroute-introduction.md)、专用对等互连或 VPN 隧道实现对专用终结点的跨界访问。 然后，客户可以通过公共终结点禁用所有访问，而无需使用基于 IP 的防火墙来允许任何 IP 地址。

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Azure SQL 数据库专用链接的用例 

客户端可以从同一虚拟网络、同一区域中的对等互联虚拟网络或通过跨区域的虚拟网络到虚拟网络连接连接到专用终结点。 此外，客户端可以使用 ExpressRoute、专用对等互连或 VPN 隧道从本地进行连接。 以下简化示意图显示了常见用例。

 ![连接选项示意图][1]

此外，不直接在虚拟网络中运行但与之集成的服务（例如，应用服务 Web 应用或 Functions）也可以实现与数据库的专用连接。 有关此特定用例的详细信息，请参阅[与 Azure SQL 数据库建立专用连接的 Web 应用](/azure/architecture/example-scenario/private-web-app/private-web-app)体系结构方案。

## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>从对等互联虚拟网络中的 Azure VM 进行连接 

配置[虚拟网络对等互联](../../virtual-network/tutorial-connect-virtual-networks-powershell.md)，以便从对等互联虚拟网络中的 Azure VM 建立与 SQL 数据库的连接。

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>从虚拟网络中的 Azure VM 连接到虚拟网络环境

配置[虚拟网络到虚拟网络 VPN 网关连接](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)，以便从另一区域或订阅中的 Azure VM 建立与 SQL 数据库中的数据库的连接。

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>通过 VPN 从本地环境进行连接

若要建立从本地环境到 SQL 数据库中的数据库的连接，请选择并实施以下选项之一：
- [点到站点连接](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [站点到站点 VPN 连接](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute 线路](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>使用 Polybase 和 COPY 语句从 Azure Synapse Analytics 连接到 Azure 存储

PolyBase 和 COPY 语句通常用于将数据从 Azure 存储帐户加载到 Azure Synapse Analytics 中。 如果要从中加载数据的 Azure 存储帐户仅允许通过专用终结点、服务终结点或基于 IP 的防火墙访问一组虚拟网络子网，则通过 PolyBase 和 COPY 语句与该帐户建立的连接将会断开。 对于连接到 Azure 存储（已通过安全方式连接到虚拟网络）的 Azure Synapse Analytics，若要启用导入和导出方案，请执行[此处](vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)提供的步骤。 

## <a name="data-exfiltration-prevention"></a>数据渗透防护

Azure SQL 数据库中的数据渗透是指用户（例如数据库管理员）能够从一个系统提取数据，并将其移到组织外部的其他位置或系统。 例如，该用户将数据移到第三方拥有的存储帐户。

假设某个用户在连接到 SQL 数据库中的数据库的 Azure 虚拟机中运行 SQL Server Management Studio (SSMS)。 此数据库位于“美国西部”数据中心。 以下示例演示如何使用网络访问控制来限制通过公共终结点对 SQL 数据库进行访问。

1. 通过将“允许 azure 服务”设置为“关闭”，禁止所有 Azure 服务流量通过公共终结点进入 SQL 数据库。 确保不要在服务器和数据库级防火墙规则中允许任何 IP 地址。 有关详细信息，请参阅 [Azure SQL 数据库和 Azure Synapse Analytics 网络访问控制](network-access-controls-overview.md)。
1. 仅允许流量使用 VM 的专用 IP 地址进入 SQL 数据库中的数据库。 有关详细信息，请参阅有关[服务终结点](vnet-service-endpoint-rule-overview.md)和[虚拟网络防火墙规则](firewall-configure.md)的文章。
1. 在 Azure VM 上，按如下所示使用[网络安全组 (NSG)](../../virtual-network/manage-network-security-group.md) 和服务标记缩小传出连接的范围
    - 指定一个 NSG 规则以允许服务标记 SQL.WestUs 的流量 - 仅允许连接到“美国西部”的 SQL 数据库
    - 指定一个 NSG 规则（具有 **较高的优先级**），以拒绝服务标记 SQL 的流量 - 拒绝连接到所有区域中的 SQL 数据库

完成此设置后，Azure VM 只能连接到“美国西部”区域的 SQL 数据库中的数据库。 不过，连接并不局限于 SQL 数据库中的单个数据库。 VM 仍可连接到“美国西部”区域中的任何数据库，包括不在订阅中的数据库。 尽管我们在上述场景中已将数据渗透范围缩小到了特定的区域，但我们并未完全消除这种渗透。

借助专用链接，客户现在可以设置 NSG 等网络访问控制来限制对专用终结点的访问。 然后，将单个 Azure PaaS 资源映射到特定的专用终结点。 恶意的预览体验成员只能访问映射的 PaaS 资源（例如 SQL 数据库中的数据库），而不能访问其他资源。 

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库安全概述，请参阅[保护数据库](security-overview.md)
- 有关 Azure SQL 数据库连接的概述，请参阅 [Azure SQL 连接体系结构](connectivity-architecture.md)
- 你可能还会对[与 Azure SQL 数据库建立专用连接的 Web 应用](/azure/architecture/example-scenario/private-web-app/private-web-app)体系结构方案感兴趣，该方案将虚拟网络以外的 Web 应用程序连接到数据库的专用终结点。

<!--Image references-->
[1]: media/private-endpoint/pe-connect-overview.png
[2]: media/private-endpoint/telnet-result.png
[3]: media/private-endpoint/pec-list-before.png
[4]: media/private-endpoint/pec-approve.png
[5]: media/private-endpoint/pec-list-after.png
[6]: media/private-endpoint/pec-select.png
[7]: media/private-endpoint/pec-click.png
[8]: media/private-endpoint/pec-nic-click.png
[9]: media/private-endpoint/pec-ip-display.png
