---
title: 使用 Azure 防火墙检查流向专用终结点的流量
titleSuffix: Azure Private Link
description: 了解如何使用 Azure 防火墙检查流向专用终结点的流量。
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 0e252a1822d36bf1d8aecbac7f38a0d315d38351
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371839"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>使用 Azure 防火墙检查流向专用终结点的流量

> [!NOTE]
> 如果要使用安全虚拟中心保护流向 Azure 虚拟 WAN 中专用终结点的流量，请参阅[保护流向 Azure 虚拟 WAN 中专用终结点的流量](../firewall-manager/private-link-inspection-secure-virtual-hub.md)。

Azure 专用终结点是 Azure 专用链接的构建基块。 通过专用终结点，虚拟网络中部署的 Azure 资源可与专用链接资源进行私密通信。

资源可通过专用终结点访问虚拟网络中部署的专用链接服务。 通过虚拟网络对等互连和本地网络连接对专用终结点进行访问扩展了连接性。

你可能需要检查或阻止从客户端到通过专用终结点公开的服务的流量。 使用 [Azure 防火墙](../firewall/overview.md)或第三方网络虚拟设备完成此检查。

以下限制适用：

* 来自专用终结点的流量将绕过网络安全组 (NSG)
* 来自专用终结点的流量将绕过用户定义路由 (UDR)。 用户定义的路由可用于替代发往专用终结点的流量。
* 单个路由表可附加到一个子网
* 一个路由表最多支持 400 个路由

Azure 防火墙使用以下方法之一筛选流量：

* [网络规则中的 FQDN](../firewall/fqdn-filtering-network-rules.md)，适用于 TCP 和 UDP 协议
* [应用程序规则中的 FQDN](../firewall/features.md#application-fqdn-filtering-rules)，适用于 HTTP、HTTPS 和 MSSQL。 

> [!IMPORTANT] 
> 检查流向专用终结点的流量时，相较于网络规则，更建议使用应用程序规则，以维持流对称性。 如果使用网络规则，或者使用 NVA 而不是 Azure 防火墙，则必须为流向专用终结点的流量配置 SNAT。

> [!NOTE]
> 仅在[代理模式](../azure-sql/database/connectivity-architecture.md#connection-policy)下支持 SQL FQDN 筛选（端口 1433）。 与重定向相比，代理模式可能会导致更高的延迟。 如果你要继续使用重定向模式（这是在 Azure 中进行连接的客户端的默认模式），可以使用防火墙网络规则中的 FQDN 来筛选访问。

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>方案 1：中心辐射型体系结构 - 面向专用终结点的专用虚拟网络

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="面向专用终结点的专用虚拟网络" border="true":::

此方案是扩展性最强的体系结构，可使用专用终结点以私密方式连接到多个 Azure 服务。 会创建一个路由，它指向部署了专用终结点的网络地址空间。 此配置可减少管理开销，并防止超过 400 个路由的限制。

如果虚拟网络已对等互连，则从客户端虚拟网络连接到中心虚拟网络中的 Azure 防火墙会产生费用。 从中心虚拟网络中的 Azure 防火墙连接到对等互连虚拟网络中的专用终结点不收取费用。

有关与对等互连虚拟网络的连接相关的费用的详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/private-link/)页的常见问题解答部分。

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>方案 2：中心辐射型体系结构 - 专用终结点和虚拟机的共享虚拟网络

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="同一虚拟网络中的专用终结点和虚拟机" border="true":::

在下列情况中实现此方案：

* 无法将专用虚拟网络用于专用终结点

* 仅当使用专用终结点在虚拟网络中公开几项服务时

虚拟机将具有指向每个专用终结点的 /32 系统路由。 每个专用终结点的一个路由配置为通过 Azure 防火墙路由流量。 

随着服务在虚拟网络中公开，因维护路由表而产生的管理开销会增加。 达到路由限制的可能性也会增加。

根据你的总体体系结构，可能会达到 400 个路由这一限制。 建议尽可能使用方案 1。

如果虚拟网络已对等互连，则从客户端虚拟网络连接到中心虚拟网络中的 Azure 防火墙会产生费用。 从中心虚拟网络中的 Azure 防火墙连接到对等互连虚拟网络中的专用终结点不收取费用。

有关与对等互连虚拟网络的连接相关的费用的详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/private-link/)页的常见问题解答部分。

## <a name="scenario-3-single-virtual-network"></a>方案 3：单个虚拟网络

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="单个虚拟网络" border="true":::

如果无法迁移到中心辐射型体系结构，请使用此模式。 方案 2 中的注意事项同样适用。 在此方案中，虚拟网络对等互连费用并不适用。

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>方案 4：流向专用终结点的本地流量

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="本地流量流向专用终结点" border="true":::

如果已通过以下任一方式配置与本地网络的连接，则可实现此体系结构： 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [站点到站点 VPN](../vpn-gateway/tutorial-site-to-site-portal.md) 

如果安全要求规定流向通过专用终结点公开的服务的客户端流量通过安全设备进行路由，请部署此方案。

需遵守上述方案 2 中的注意事项。 在此方案中，不存在虚拟网络对等互连费用。 有关如何将 DNS 服务器配置为允许本地工作负载访问专用终结点的详细信息，请参阅[使用 DNS 转发器的本地工作负载](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。
* Log Analytics 工作区。  

如果订阅中没有工作区，请查看[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)来创建一个。


## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-vm"></a>创建 VM

在本部分，你将创建一个虚拟网络和子网来托管用于访问专用链接资源的虚拟机 (VM)。 将 Azure SQL 数据库用作示例服务。

## <a name="virtual-networks-and-parameters"></a>虚拟网络和参数

创建 3 个虚拟网络及其相应的子网，以便：

* 包含用于限制 VM 与专用终结点之间的通信的 Azure 防火墙。
* 托管用于访问专用链接资源的 VM。
* 托管专用终结点。

将步骤中的以下参数替换为以下信息：

### <a name="azure-firewall-network"></a>Azure 防火墙网络

| 参数                   | 值                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | 美国中南部      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>虚拟机网络

| 参数                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | 美国中南部      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>专用终结点网络

| 参数                   | 值                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | 美国中南部      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. 重复步骤 1 到 9，创建虚拟网络来托管虚拟机和专用终结点资源。

### <a name="create-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “计算” > “虚拟机”。  

2. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 我们在上一部分创建了此资源组。  |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入“myVM”。 |
    | 区域 | 选择“(美国)美国中南部”。 |
    | 可用性选项 | 保留默认值“不需要基础结构冗余”。 |
    | 映像 | 选择“Ubuntu Server 18.04 LTS - Gen1”。 |
    | 大小 | 选择“Standard_B2s”。 |
    | **管理员帐户** |  |
    | 身份验证类型 | 选择“密码”。 |
    | 用户名 | 输入所选用户名。 |
    | 密码 | 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/linux/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)。|
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |  |
    | 公共入站端口 | 选择“无”。 |
    |||

3. 在完成时选择“下一步:**磁盘”** 。

4. 在“创建虚拟机 - 磁盘”中保留默认值，然后选择“下一步: **网络”** 。

5. 在“创建虚拟机 - 基本信息”中，选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 虚拟网络 | 选择“myVMVNet”。  |
    | 子网 | 选择“VMSubnet (10.1.0.0/24)”。|
    | 公共 IP | 保留默认值“(new) myVm-ip”。 |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 选择“SSH”。|
    ||

6. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

7. 看到“验证通过”消息时，选择“创建” 。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="deploy-the-firewall"></a>部署防火墙

1. 在 Azure 门户菜单或“主页”页上，选择“创建资源” 。

2. 在搜索框中键入“防火墙”，然后按 **Enter**。

3. 选择“防火墙”，然后选择“创建” 。

4. 在“创建防火墙”页上，使用下表配置防火墙：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。  |
    | **实例详细信息** |  |
    | 名称 | 输入“myAzureFirewall”。 |
    | 区域 | 选择“美国中南部”。 |
    | 可用性区域 | 保留默认值“无”。 |
    | 选择虚拟网络    |    选择“使用现有项”。    |
    | 虚拟网络    |    选择“myAzFwVNet”。    |
    | 公共 IP 地址    |    选择“新增”，然后在“名称”中输入“myFirewall-ip” 。    |
    | 强制隧道    | 保留默认值“禁用”。    |
    |||
5. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

6. 看到“验证通过”消息时，选择“创建” 。

## <a name="enable-firewall-logs"></a>启用防火墙日志

在本部分中，请在防火墙上启用日志。

1. 在 Azure 门户的左侧菜单中，选择“所有资源”。

2. 在资源列表中选择防火墙“myAzureFirewall”。

3. 在防火墙设置中的“监视”下，选择“诊断设置” 

4. 在“诊断设置”中，选择“+添加诊断设置”。

5. 在“诊断设置”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 诊断设置名称 | 输入“myDiagSetting”。 |
    | 类别详细信息 | |
    | log | 选择“AzureFirewallApplicationRule”和“AzureFirewallNetworkRule” 。 |
    | 目标详细信息 | 选择“发送到 Log Analytics”。 |
    | 订阅 | 选择订阅。 |
    | Log Analytics 工作区 | 选择 Log Analytics 工作区。 |

6. 选择“保存”。

## <a name="create-azure-sql-database"></a>创建 Azure SQL 数据库

在本部分，请创建一个专用 SQL 数据库。

1. 在 Azure 门户屏幕的左上方，选择“创建资源” > “数据库” > “SQL 数据库”  。

2. 在“创建 SQL 数据库 - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 我们在上一部分创建了此资源组。|
    | **数据库详细信息** |  |
    | 数据库名称  | 输入 **mydatabase**。  |
    | 服务器 | 选择“新建”，然后输入以下信息。    |
    | 服务器名称 | 输入“mydbserver”。 如果此名称已被使用，请输入唯一的名称。   |
    | 服务器管理员登录名 | 输入所选名称。 |
    | 密码    |    输入所选密码。    |
    | 确认密码 | 重新输入密码    |
    | 位置    | 选择“(美国)美国中南部”。    |
    | 想要使用 SQL 弹性池    | 保留默认值“否”。 |
    | 计算 + 存储 | 保留“常规用途 Gen5、2 个 vCore、32 GB 存储”这一默认设置。 |
    |||

3. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

4. 看到“验证通过”消息时，选择“创建” 。

## <a name="create-private-endpoint"></a>创建专用终结点

在本部分，请创建上一部分中的 Azure SQL 数据库的专用终结点。

1. 在 Azure 门户的左侧菜单中，选择“所有资源”。

2. 在服务列表中，选择 Azure SQL 服务器“mydbserver”。  如果使用其他服务器名称，请选择该名称。

3. 在服务器设置的“安全”下，选择“专用终结点连接” 。

4. 选择 “+ 专用终结点”。

5. 在“创建专用终结点”的“基本信息”选项卡中，输入或选择以下信息 ：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 |
    | **实例详细信息** | |
    | 名称 | 输入“SQLPrivateEndpoint”。 |
    | 区域 | 选择“(美国)美国中南部”。 |

6. 选择“资源”选项卡，或者选择页面底部的“下一步: 资源” 。

7. 在“资源”选项卡中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 连接方法 | 选择“连接到我的目录中的 Azure 资源”。 |
    | 订阅 | 选择订阅。 |
    | 资源类型 | 选择“Microsoft.Sql/servers”。 |
    | 资源 | 选择“mydbserver”，或者选择在上一步中创建的服务器的名称。
    | 目标子资源 | 选择“sqlServer”。 |

8. 选择“配置”选项卡，或者选择页面底部的“下一步: 配置” 。

9. 在“配置”选项卡中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **联网** | |
    | 虚拟网络 | 选择“myPEVnet”。 |
    | 子网 | 选择“PrivateEndpointSubnet”。 |
    | **专用 DNS 集成** | |
    | 与专用 DNS 区域集成 | 请选择“是”。 |
    | 订阅 | 选择订阅。 |
    | 专用 DNS 区域 | 保留默认值“privatelink.database.windows.net”。 |

10. 选择“查看 + 创建”选项卡，或者选择页面底部的“查看 + 创建” 。

11. 选择“创建”。

12. 创建终结点后，选择“安全”下的“防火墙和虚拟网络” 。

13. 在“防火墙和虚拟网络”中，选择“允许 Azure 服务和资源访问此服务器”旁边的“是”  。

14. 选择“保存”。

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>使用虚拟网络对等互连连接虚拟网络

在本部分中，我们将使用对等互连将虚拟网络 myVMVNet 和 myPEVNet 连接到 myAzFwVNet  。 myVMVNet 和 myPEVNet 之间没有直接连接 。

1. 在门户的搜索栏中，输入“myAzFwVNet”。

2. 选择“设置”菜单下的“对等互连”，然后选择“+添加”  。

3. 在“添加对等互连”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 从 myAzFwVNet 到远程虚拟网络的对等互连的名称 | 输入“myAzFwVNet-to-myVMVNet”。 |
    | **对等详细信息** |  |
    | 虚拟网络部署模型  | 保留默认值“资源管理器”。  |
    | 我知道我的资源 ID | 保持未选中状态。    |
    | 订阅 | 选择订阅。    |
    | 虚拟网络 | 选择“myVMVNet”。 |
    | 从远程虚拟网络到 myAzFwVNet 的对等互连的名称    |    输入“myVMVNet-to-myAzFwVNet”。    |
    | **配置** | |
    | **配置虚拟网络访问设置** | |
    | 允许从 myAzFwVNet 到远程虚拟网络的虚拟网络访问 | 保留默认值“已启用”。    |
    | 允许从远程虚拟网络到 myAzFwVNet 的虚拟网络访问    | 保留默认值“已启用”。    |
    | **配置转发流量设置** | |
    | 允许从远程虚拟网络到 myAzFwVNet 的转发流量    | 选择“启用”。  |
    | 允许从 myAzFwVNet 到远程虚拟网络的转发流量 | 选择“启用”。  |
    | **配置网关传输设置** | |
    | 允许网关传输 | 保持未选中状态 |
    |||

4. 选择“确定”。

5. 选择“+ 添加”。

6. 在“添加对等互连”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 从 myAzFwVNet 到远程虚拟网络的对等互连的名称 | 输入“myAzFwVNet-to-myPEVNet”。 |
    | **对等详细信息** |  |
    | 虚拟网络部署模型  | 保留默认值“资源管理器”。  |
    | 我知道我的资源 ID | 保持未选中状态。    |
    | 订阅 | 选择订阅。    |
    | 虚拟网络 | 选择“myPEVNet”。 |
    | 从远程虚拟网络到 myAzFwVNet 的对等互连的名称    |    输入“myPEVNet-to-myAzFwVNet”。    |
    | **配置** | |
    | **配置虚拟网络访问设置** | |
    | 允许从 myAzFwVNet 到远程虚拟网络的虚拟网络访问 | 保留默认值“已启用”。    |
    | 允许从远程虚拟网络到 myAzFwVNet 的虚拟网络访问    | 保留默认值“已启用”。    |
    | **配置转发流量设置** | |
    | 允许从远程虚拟网络到 myAzFwVNet 的转发流量    | 选择“启用”。  |
    | 允许从 myAzFwVNet 到远程虚拟网络的转发流量 | 选择“启用”。  |
    | **配置网关传输设置** | |
    | 允许网关传输 | 保持未选中状态 |

7. 选择“确定”。

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>将虚拟网络链接到专用 DNS 区域

在本部分中，我们将虚拟网络 myVMVNet 和 myAzFwVNet 链接到专用 DNS 区域 privatelink.database.windows.net  。 此区域是在创建专用终结点时创建的。 

VM 和防火墙需要此链接来将数据库的 FQDN 解析为其专用终结点地址。 虚拟网络 myPEVNet 是在创建专用终结点时自动链接的。

>[!NOTE]
>如果没有将 VM 和防火墙虚拟网络链接到专用 DNS 区域，VM 和防火墙仍将能够解析 SQL Server FQDN。 它们将解析为其公共 IP 地址。

1. 在门户的搜索栏中，输入“privatelink.database”。

2. 在搜索结果中选择“privatelink.database.windows.net”。

3. 选择“设置”下的“虚拟网络链接” 。

4. 选择“+ 添加”。

5. 在“添加虚拟网络链接”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 链接名称 | 输入“Link-to-myVMVNet”。 |
    | **虚拟网络详细信息** |  |
    | 我知道虚拟网络的资源 ID  | 保持未选中状态。  |
    | 订阅 | 选择订阅。    |
    | 虚拟网络 | 选择“myVMVNet”。 |
    | **CONFIGURATION** | |
    | 启用自动注册 | 保持未选中状态。    |


6. 选择“确定”。

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>使用 Azure 防火墙中的 SQL FQDN 配置应用程序规则

在本部分，配置一个应用程序规则来允许 myVM 和 SQL Server 专用终结点 mydbserver.database.windows.net 之间的通信 。 

此规则允许通过我们在之前步骤中创建的防火墙进行通信。

1. 在门户的搜索栏中，输入“myAzureFirewall”。

2. 在搜索结果中选择“myAzureFirewall”。

3. 在“myAzureFirewall”概述中的“设置”下选择“规则”  。

4. 选择“应用程序规则集合”选项卡。

5. 选择“+添加应用程序规则集合”。

6. 在“添加应用程序规则集合”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入“SQLPrivateEndpoint”。 |
    | 优先级 | 输入 **100**。 |
    | 操作 | 输入“允许”。 |
    | **规则** |  |
    | **FQDN 标记** | |
    | 名称  | 留空。  |
    | 源类型 | 保留默认值“IP 地址”。    |
    | 源 | 留空。 |
    | FQDN 标记 | 保留默认值“已选择 0 个”。 |
    | **目标 FQDN** | |
    | 名称 | 输入“SQLPrivateEndpoint”。    |
    | 源类型 | 保留默认值“IP 地址”。 |
    | 源 | 输入 10.1.0.0/16。 |
    | 协议：端口 | 输入“mssql:1433”。 |
    | 目标 FQDN | 输入“mydbserver.database.windows.net”。 |
    |||

7. 选择“添加”。

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>通过 Azure 防火墙在虚拟机和专用终结点之间路由流量

我们未直接在虚拟网络 myVMVNet 和 myPEVNet 之间创建虚拟网络对等互连 。 虚拟机 myVM 没有路由指向我们创建的专用终结点。 

在本部分中，我们将创建一个包含自定义路由的路由表。 

该路由通过 Azure 防火墙将流量从 myVM 子网发送到虚拟网络 myPEVNet 的地址空间 。

1. 在 Azure 门户菜单上或在“主页”中，选择“创建资源”。

2. 在搜索框中键入“路由表”，然后按 Enter 。

3. 选择“路由表”，然后选择“创建” 。

4. 在“创建路由表”页面上，使用以下表配置路由表：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。  |
    | **实例详细信息** |  |
    | 区域 | 选择“美国中南部”。 |
    | 名称 | 输入“VMsubnet-to-AzureFirewall”。 |
    | 传播网关路由 | 请选择“否”。 |

5. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

6. 看到“验证通过”消息时，选择“创建” 。

7. 部署完成后，请选择“转到资源”。

8. 在“设置”下选择“路由”。 

9. 选择“+ 添加”。

10. 在“添加路由”页面上，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 路由名称 | 输入“myVMsubnet-to-privateendpoint”。 |
    | 地址前缀 | 输入“10.2.0.0/16”。  |
    | 下一跃点类型 | 选择“虚拟设备”。 |
    | 下一跃点地址 | 输入“10.0.0.4”。 |

11. 选择“确定”。

12. 在“设置”下选择“子网” 。

13. 选择“+ 关联”。

14. 在“关联子网”页面上，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 虚拟网络 | 选择“myVMVNet”。 |
    | 子网 | 选择“VMSubnet”。  |

15. 选择“确定”。

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>从客户端计算机连接到虚拟机

从 Internet 连接到 VM **myVm**，如下所示：

1. 在门户的搜索栏中，输入“myVm-ip”。

2. 在搜索结果中选择“myVM-ip”。

3. 复制或记下“IP 地址”下的值。

4. 如果使用的是 Windows 10，请使用 PowerShell 运行以下命令。 对于其他 Windows 客户端版本，请使用 SSH 客户端（如 [Putty](https://www.putty.org/)）：

* 将用户名替换为在 VM 创建期间输入的管理员用户名。

* 将 IP 地址替换为上一步骤中的 IP 地址。

    ```bash
    ssh username@IPaddress
    ```

5. 输入在创建 myVm 时定义的密码

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>以私密方式从虚拟机访问 SQL Server

在本部分中，你将使用专用终结点以私密方式连接到 SQL 数据库。

1. 输入 `nslookup mydbserver.database.windows.net`
    
    你将收到如下所示的消息：

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. 安装 [SQL Server 命令行工具](/sql/linux/quickstart-install-connect-ubuntu#tools)。

3. 运行以下命令连接到 SQL Server。 使用在前面的步骤中创建 SQL Server 时定义的服务器管理员和密码。

* 将 \<ServerAdmin> 替换为在 SQL Server 创建期间输入的管理员用户名。

* 将 \<YourPassword> 替换为在 SQL Server 创建期间输入的管理员密码。

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. 成功登录时，将显示 SQL 命令提示符。 输入 exit 可退出 sqlcmd 工具 。

5. 输入 exit 来关闭与 myVM 的连接 。

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>验证 Azure 防火墙日志中的流量

1. 在 Azure 门户中，选择“所有资源”，然后选择你的 Log Analytics 工作区。

2. 在 Log Analytics 工作区页中，选择“常规”下的“日志” 。

3. 选择蓝色的“开始使用”按钮。

4. 在“示例查询”窗口，选择“所有查询”下的“防火墙”  。

5. 选择“应用程序规则日志数据”下的“运行”按钮 。

6. 在日志查询输出中，验证 mydbserver.database.windows.net 是否在 FQDN 下列出，SQLPrivateEndpoint 是否在 RuleCollection 下列出   。

## <a name="clean-up-resources"></a>清理资源

使用完资源后，请删除资源组和其包含的所有资源：

1. 在门户顶部的“搜索”框中输入“myResourceGroup”，并从搜索结果中选择“myResourceGroup”。

1. 选择“删除资源组”  。

1. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本文中，你了解了可用来通过 Azure 防火墙限制虚拟机与专用终结点之间的流量的不同方案。 

你连接到了 VM，还使用专用链接通过 Azure 防火墙与数据库进行了安全通信。

若要详细了解专用终结点，请查看[什么是 Azure 专用终结点？](private-endpoint-overview.md)。
