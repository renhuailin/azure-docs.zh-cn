---
title: 为可用性组配置 DNN 侦听器
description: 了解如何 (DNN) 侦听器配置分布式网络名称以将虚拟网络名称替换 (VNN) 侦听器，并将流量路由到 Azure VM 上 Always On 上的 SQL Server 可用性组。
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
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 07ce01304f27ded4e0a566777fcf7027f7a15e4b
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359432"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>为可用性组配置 DNN 侦听器
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure Vm 上 SQL Server，分布式网络名称 (DNN) 会将流量路由到相应的群集资源。 它提供一种更简单的方法来连接到 Always On 可用性组 (AG) ，而不是虚拟网络名称 (VNN) 侦听器，无需使用 Azure 负载均衡器。 

本文介绍如何配置 DNN 侦听器来替换 VNN 侦听器，并使用 Azure Vm 上的 SQL Server 将流量路由到可用性组，以实现高可用性和灾难恢复 (HADR) 。 

DNN 侦听器功能当前仅在 Windows Server 2016 和更高版本上从 SQL Server 2019 CU8 开始可用。 

对于备用连接选项，请考虑改用 [VNN 侦听器和 Azure 负载均衡器](availability-group-vnn-azure-load-balancer-configure.md) 。 

## <a name="overview"></a>概述

当与 Always On 可用性组 [vm 上的 SQL Server](availability-group-overview.md)一起使用时，分布式网络名称 (DNN) 侦听器将替换传统虚拟网络名称 (VNN) 可用性组侦听器。 这无需使用 Azure 负载均衡器来路由流量，从而简化了部署、维护和改进故障转移。 

请使用 DNN 侦听器来替代现有的 VNN 侦听器，或者将它与现有的 VNN 侦听器一起使用，使你的可用性组有两个不同的连接点 - 一个使用 VNN 侦听器名称（如果非默认，则还使用 VNN 侦听器端口），而另一个使用 DNN 侦听器名称和端口。 

## <a name="prerequisites"></a>先决条件

在完成本文中的步骤之前，应已做好以下准备：

- Windows Server 2016 和更高版本上的 CU8 或更高版本上的 SQL Server 2019
- 确定分布式网络名称是适用 [于 HADR 解决方案的连接选项](hadr-cluster-best-practices.md#connectivity)。
- 已配置 [Always On 可用性组](availability-group-overview.md)。 
- 已安装最新版本的 [PowerShell](/powershell/azure/install-az-ps)。 


## <a name="create-script"></a>创建脚本

使用 PowerShell 创建 (DNN) 资源的分布式网络名称，并将其与可用性组相关联。 

为此，请执行下列步骤： 

1. 打开文本编辑器，例如记事本。 
1. 复制并粘贴以下脚本： 

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. 将脚本另存为 `.ps1` 文件，如 `add_dnn_listener.ps1` 。 


## <a name="execute-script"></a>执行脚本

若要创建 DNN 侦听器，请执行传入参数的脚本，以获取可用性组的名称、侦听器名称和端口。 

例如，假设可用性组名称 `ag1` 、侦听器名称 `dnnlsnr` 和侦听器端口为 `6789` ，请执行以下步骤： 

1. 打开命令行界面工具，如命令提示符或 PowerShell。 
1. 导航到保存脚本的位置 `.ps1` ，如 c:\Documents。 
1. 执行脚本： ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` 。 例如： 

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>验证侦听器

使用 SQL Server Management Studio 或 Transact-sql 确认已成功创建 DNN 侦听器。 

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

展开 " **可用性组侦听器** " [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) ，查看 DNN 侦听器： 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="查看 SQL Server Management Studio (SSMS 的可用性组侦听器下的 DNN 侦听器) ":::

### <a name="transact-sql"></a>Transact-SQL

使用 Transact-sql 查看 DNN 侦听器的状态： 

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

的值 `1` `is_distributed_network_name` 指示侦听器是 (DNN) 侦听器的分布式网络名称： 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="使用 sys.availability_group_listeners 标识值为1的 DNN 侦听器 is_distributed_network_name":::


## <a name="update-connection-string"></a>更新连接字符串

更新应用程序的连接字符串，使其连接到 DNN 侦听器。 若要确保在故障转移时快速连接，请在 `MultiSubnetFailover=True` SQL 客户端支持连接字符串时将其添加到连接字符串中。 

## <a name="test-failover"></a>测试故障转移

测试可用性组的故障转移以确保功能。 

若要测试故障转移，请执行以下步骤： 

1. 使用 [SQL Server Management Studio (SSMS) ](/sql/ssms/download-sql-server-management-studio-ssms)连接到 DNN 侦听器或某个副本。 
1. 在 **对象资源管理器** 中展开 **Always On 可用性组**。 
1. 右键单击可用性组，然后选择 " **故障转移** " 以打开 " **故障转移向导**"。 
1. 按照提示选择故障转移目标，将可用性组故障转移到辅助副本。 
1. 确认新的主副本上的数据库处于同步状态。 
1.  (可选) 故障回复到原始主副本或另一个辅助副本。 

## <a name="test-connectivity"></a>测试连接

通过以下步骤测试与 DNN 侦听器的连接：

1. 打开 SQL Server Management Studio  。
1. 连接到你的 DNN 侦听器。 
1. 打开一个新的查询窗口，并通过运行来检查您连接到的副本 `SELECT @@SERVERNAME` 。 
1. 将可用性组故障转移到另一个副本。
1. 在合理的时间内，运行 `SELECT @@SERVERNAME` 确认可用性组现在托管在另一个副本上。 


## <a name="limitations"></a>限制

- 目前，只有 Windows Server 2016 和更高版本的 SQL Server 2019 CU8 和更高版本支持可用性组的 DNN 侦听器。 
- 在处理其他 SQL Server 功能和具有 DNN 的可用性组时，可能会有其他注意事项。 有关详细信息，请参阅 [具有 DNN 互操作性的 AG](availability-group-dnn-interoperability.md)。 

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 中 SQL Server HADR 功能的详细信息，请参阅 [可用性组](availability-group-overview.md) 和 [故障转移群集实例](failover-cluster-instance-overview.md)。 你还可以了解配置环境以实现高可用性和灾难恢复的 [最佳实践](hadr-cluster-best-practices.md) 。 


