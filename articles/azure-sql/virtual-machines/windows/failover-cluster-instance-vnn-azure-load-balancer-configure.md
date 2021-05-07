---
title: 为故障转移群集实例 VNN 配置 Azure 负载均衡器
description: 了解如何将 Azure 负载均衡器配置为使用 Azure VM 上的 SQL Server 将流量路由到故障转移群集实例 (FCI) 的虚拟网络名称 (VNN)，以实现高可用性和灾难恢复 (HADR)。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5670a29e86eb201a707e5ceef28043aafe4839d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357970"
---
# <a name="configure-azure-load-balancer-for-failover-cluster-instance-vnn"></a>为故障转移群集实例 VNN 配置 Azure 负载均衡器
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 虚拟机上，群集使用负载均衡器来保存每次都需要位于一个群集节点上的 IP 地址。 在此解决方案中，负载均衡器保留 Azure 中的群集化资源使用的虚拟网络名称 (VNN) 的 IP 地址。 

本文介绍了如何使用 Azure 负载均衡器服务配置负载均衡器。 负载均衡器会使用 Azure VM 上的 SQL Server 将流量路由到你的[故障转移群集实例 (FCI)](failover-cluster-instance-overview.md)，以实现高可用性和灾难恢复 (HADR)。 

对于 SQL Server 2019 CU2 及更高版本上的替代连接选项，请考虑改用[分布式网络名称](failover-cluster-instance-distributed-network-name-dnn-configure.md)，以便简化配置并改进故障转移。 


## <a name="prerequisites"></a>先决条件

在完成本文中的步骤之前，应已做好以下准备：

- 确定了 Azure 负载均衡器是适合[用于你的 HADR 解决方案的连接选项](hadr-cluster-best-practices.md#connectivity)。
- 配置你的[可用性组侦听程序](availability-group-overview.md)或[故障转移群集实例](failover-cluster-instance-overview.md)。 
- 安装了最新版本的 [PowerShell](/powershell/azure/install-az-ps)。 


## <a name="create-load-balancer"></a>创建负载均衡器

使用 [Azure 门户](https://portal.azure.com)创建负载均衡器：

1. 在 Azure 门户中，转到包含虚拟机的资源组。

1. 选择 **添加** 。 在 Azure 市场中搜索“负载均衡器”。 选择“负载均衡器”。

1. 选择“创建”。

1. 使用以下值设置负载均衡器：

   - **订阅**：Azure 订阅。
   - **资源组**：包含你的虚拟机的资源组。
   - **名称**：标识负载均衡器的名称。
   - **区域**：包含你的虚拟机的 Azure 位置。
   - **类型**：公共或专用。 可从虚拟网络内部访问专用负载均衡器。 大多数 Azure 应用程序可以使用专用负载均衡器。 如果应用程序需要通过 Internet 直接访问 SQL Server，请使用公共负载均衡器。
   - **SKU**：标准。
   - **虚拟网络**：虚拟机所在的网络。
   - **IP 地址分配**：静态。 
   - **专用 IP 地址**：你分配给群集化网络资源的 IP 地址。

   下图显示“创建负载均衡器”用户界面：

   ![设置负载均衡器](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>配置后端池

1. 返回到包含虚拟机的 Azure 资源组，找到新的负载均衡器。 可能需要在资源组中刷新视图。 选择负载均衡器。

1. 依次选择“后端池”、“添加”。 

1. 将该后端池与包含 VM 的可用性集进行关联。

1. 在“目标网络 IP 配置”下选择“虚拟机”，并选择要作为群集节点参与操作的虚拟机。  请务必包括将承载 FCI 或可用性组的所有虚拟机。

1. 选择“确定”以创建后端池。

## <a name="configure-health-probe"></a>配置运行状况探测

1. 在负载均衡器窗格上，选择“运行状况探测”。

1. 选择 **添加** 。

1. 在“添加运行状况探测”窗格上，<span id="probe"></span>设置以下运行状况探测参数：

   - 名称：运行状况探测的名称。
   - **协议**：TCP。
   - **端口**：[准备 VM 时](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1)你在防火墙中为运行状况探测创建的端口。 在本文中，示例使用了 TCP 端口 `59999`。
   - **时间间隔**：5 秒。
   - **不正常阈值**：2 次连续失败。

1. 选择“确定”  。

## <a name="set-load-balancing-rules"></a>设置负载均衡规则

1. 在负载均衡器窗格中，选择“负载均衡规则”。

1. 选择 **添加** 。

1. 设置负载均衡规则参数：

   - **名称**：负载均衡规则的名称。
   - **前端 IP 地址**：SQL Server FCI 或 AG 侦听程序的群集化网络资源的 IP 地址。
   - **端口**：SQL Server TCP 端口。 默认实例端口为 1433。
   - **后端端口**：与启用“浮动 IP (直接服务器返回)”时使用的“端口”值相同的端口。 
   - **后端池**：前面配置的后端池名称。
   - **运行状况探测**：前面配置的运行状况探测。
   - **会话持久性**：无。
   - **空闲超时(分钟)** ：4.
   - **浮动 IP (直接服务器返回)** ：已启用。

1. 选择“确定”  。

## <a name="configure-cluster-probe"></a>配置群集探测

在 PowerShell 中设置群集探测端口参数。

若要设置群集探测端口参数，请使用环境中的值更新以下脚本中的变量。 从脚本中删除尖括号（`<` 和 `>`）。

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

下表介绍了需要更新的值：


|**值**|**说明**|
|---------|---------|
|`Cluster Network Name`| Windows Server 故障转移群集的网络名称。 在“故障转移群集管理器” > “网络”中，右键单击该网络并选择“属性”。   正确的值位于“常规”选项卡的“名称”下。|
|`SQL Server FCI/AG listener IP Address Resource Name`|SQL Server FCI 的或 AG 侦听程序的 IP 地址的资源名称。 在“故障转移群集管理器” > “角色”中“SQL Server FCI”角色的“服务器名称”下，右键单击 IP 地址资源并选择单击“属性”。    正确的值位于“常规”选项卡的“名称”下。|
|`ILBIP`|内部负载均衡器 (ILB) 的 IP 地址。 在 Azure 门户中将此地址配置为 ILB 的前端地址。 这也是 SQL Server FCI 的 IP 地址。 可在“故障转移群集管理器”中找到该地址，它与 `<SQL Server FCI/AG listener IP Address Resource Name>` 位于同一属性页。|
|`nnnnn`|在负载均衡器的运行状况探测中配置的探测端口。 任何未使用的 TCP 端口都有效。|
|"SubnetMask"| 群集参数的子网掩码。 它必须是 TCP IP 广播地址：`255.255.255.255`。| 


设置群集探测后，可在 PowerShell 中看到所有群集参数。 运行此脚本：

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>测试故障转移


测试群集化资源的故障转移以验证群集功能。 

执行以下步骤：

1. 使用 RDP 连接到 SQL Server 群集节点之一。
1. 打开“故障转移群集管理器”。 选择“角色”。 观察哪个节点拥有 SQL Server FCI 角色。
1. 右键单击“SQL Server FCI”角色。 
1. 选择“移动”，再选择“最佳节点” 。

“故障转移群集管理器”显示该角色，其资源脱机。 然后资源会移动，并在另一个节点上恢复联机。


## <a name="test-connectivity"></a>测试连接

若要测试连接，请登录到同一虚拟网络中的另一个虚拟机。 打开“SQL Server Management Studio”并连接到 SQL Server FCI 名称。 

>[!NOTE]
>如果需要，可以[下载 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。



## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 中的 SQL Server HADR 功能，请参阅[可用性组](availability-group-overview.md)和[故障转移群集实例](failover-cluster-instance-overview.md)。 你还可以了解对环境进行配置以实现高可用性和灾难恢复的[最佳做法](hadr-cluster-best-practices.md)。 



