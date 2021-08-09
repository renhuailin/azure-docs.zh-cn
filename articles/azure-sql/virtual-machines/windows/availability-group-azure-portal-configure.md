---
title: 配置可用性组（Azure 门户）
description: 使用 Azure 门户在 Azure 中的 SQL Server VM 上创建 Windows 故障转移群集、可用性组侦听器和内部负载均衡器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ece52b707418ba9a0c92bffc39f5a8b17b720336
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572485"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>使用 Azure 门户为 Azure VM 上的 SQL Server 配置可用性组（预览） 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何使用 [Azure 门户](https://portal.azure.com) 为 Azure VM 上的 SQL Server 配置可用性组。 

使用 Azure 门户创建新群集或加入现有群集，然后创建可用性组、侦听器和内部负载均衡器。 

此功能目前处于预览状态。 

尽管本文介绍的是使用 Azure 门户配置可用性组环境，但也可使用 [PowerShell 或 Azure CLI](availability-group-az-commandline-configure.md)、[Azure 快速入门模板](availability-group-quickstart-template-configure.md)或[手动](availability-group-manually-configure-tutorial.md)进行配置。 

> [!NOTE]
> 现在，可以使用 Azure Migrate 将可用性组解决方案直接迁移到 Azure VM 上的 SQL Server。 有关详细信息，请参阅[迁移可用性组](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)。 


## <a name="prerequisites"></a>先决条件

要使用 Azure 门户配置 Always On 可用性组，必须满足以下先决条件： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个具有域控制器的资源组。 
- Azure 中[运行 SQL Server 2016 Enterprise 版或更新版本的一个或多个已加入域的 VM](./create-sql-vm-portal.md)，已[在完全可用性模式下向 SQL IaaS 代理扩展注册了](sql-agent-extension-manually-register-single-vm.md)相同的可用性集或不同的可用性区域 ，并为每个 VM 上的 SQL Server 服务使用同一域帐户。
- 两个可用的（未被任何实体使用的）IP 地址。 一个用于内部负载均衡器。 另一个用于与可用性组位于同一子网中的可用性组侦听器。 如果使用现有的负载均衡器，则可用性组侦听程序只需要一个有效的 IP 地址。 

## <a name="permissions"></a>权限

需要具有以下帐户权限才能使用 Azure 门户配置可用性组： 

- 在域中具有“创建计算机对象”权限的现有域用户帐户。 例如，域管理员帐户通常拥有足够的权限（如 account@domain.com）。 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。
- 可控制 SQL Server 的域用户帐户。 用户应使用这同一个帐户，用于将每个 SQL Server VM 添加到可用性组。 

## <a name="configure-cluster"></a>配置群集

使用 Azure 门户配置群集 用户可以创建新群集，或者，如果已拥有一个现有群集，则可以将其加入到 SQL IaaS 代理扩展，以便实现门户可管理性。


### <a name="create-a-new-cluster"></a>创建新群集

如果已有群集，请跳过此部分，转到[加入现有群集](#onboard-existing-cluster)。 

如果还没有现有群集，请使用 Azure 门户执行以下步骤来创建群集：

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 转到 [SQL 虚拟机](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines)资源。 
1. 在“设置”中选择“高可用性” 。 
1. 选择“+ 新建 Window Server 故障转移群集”，打开“配置 Window 故障转移群集”页面。  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="通过在门户中选择“+ 新建群集”来创建新群集":::

1. 为群集命名并提供要用作云见证的存储帐户。 选择一个现有存储帐户或者选择“新建”创建新的存储帐户。 存储帐户名称长度必须为 3 到 24 个字符，并且只能使用数字和小写字母。

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="提供群集的名称、存储帐户和凭据":::

1. 展开“Windows Server 故障转移群集凭据”提供 SQL Server 服务帐户的[凭据](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile)，以及群集操作员和启动帐户（如果该帐户与用于 SQL Server 服务的帐户不同）。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="提供 SQL 服务帐户、群集操作员帐户和群集启动帐户的凭据":::

1. 选择要添加到群集的 SQL Server VM。 请注意是否需要重新启动，并谨慎操作。 VM 只有在满足下列条件时才可见：在完全可管理性模式下注册了 SQL IaaS 代理扩展，并且它们位于同一位置、同一域以及位于与主 SQL Server VM 相同的虚拟网络上。 
1. 选择“应用”创建群集。 可通过顶部导航栏中的电铃图标访问“活动日志”，检查部署的状态。 
1. 故障转移群集必须通过群集验证才能得到 Microsoft 的支持。 使用首选方法（例如远程桌面协议 (RDP)）连接到 VM，并验证群集是否通过验证，然后再进行下一步。 否则，群集将处于不受支持状态。 用户可使用故障转移群集管理器 (FCM) 或以下 PowerShell 命令来验证群集：

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>加入现有群集

如果已在 SQL Server VM 环境中配置了群集，则可以将其从 Azure 门户上加入。

为此，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 转到 [SQL 虚拟机](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines)资源。 
1. 在“设置”中选择“高可用性” 。 
1. 选择“加入现有 Windows Server 故障转移群集”，打开“加入 Windows Server 故障转移群集”页面。 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="从 SQL 虚拟机资源上的“高可用性”页面中加入现有群集":::

1. 查看群集的设置。 
1. 选择“应用”加入群集，然后在提示时选择“是”继续下一步骤。

## <a name="create-availability-group"></a>创建可用性组

创建或加入群集后，使用 Azure 门户创建可用性组。 为此，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 转到 [SQL 虚拟机](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines)资源。 
1. 在“设置”中选择“高可用性” 。 
1. 选择“+ 新建 Always On 可用性组”，打开“创建可用性组”页。

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="选择“新建 Always On 可用性组”，打开“创建可用性组”页。":::

1. 输入可用性组的名称。 
1. 选择“配置侦听器”，打开“配置可用性组侦听器”页面。 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="提供可用性组的名称并配置侦听器":::

1. 填写值，并使用现有的负载均衡器，或选择“新建”创建新的负载均衡器。  选择“应用”保存设置，并创建侦听器和负载均衡器。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="在表格中填写值以创建新的侦听器和负载均衡器":::

1. 选择“选择副本”，打开“配置可用性组副本”页面。
1. 选择要添加到可用性组的虚拟机，并选择最适合用户业务需求的可用性组设置。 选择“应用”保存所做的更改。 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="选择要添加到可用性组的 VM，并配置适合用户业务需求的设置":::

1. 验证可用性组设置，然后选择“应用”创建可用性组。 

可通过顶部导航栏中的电铃图标访问“活动日志”，检查部署的状态。 

  > [!NOTE]
  > 在将数据库添加到可用性组之前，Azure 门户中“高可用性”页面上的“同步运行状况”将显示为“不正常”。 


## <a name="add-database-to-availability-group"></a>将数据库添加到可用性组

部署完成后，将数据库添加到可用性组。 以下步骤将使用 SQL Server Management Studio (SSMS)，但也可以使用 [Transact-SQL 或 PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database)。 

如要使用 SQL Server Management Studio 将数据库添加到可用性组，请执行以下步骤：

1. 使用例如远程桌面连接 (RDP) 的首选方法连接到其中一个 SQL Server VM。 
1. 打开 SQL Server Management Studio (SSMS)。
1. 连接到 SQL Server 实例。 
1. 在“对象资源管理器”中展开“Always On 高可用性”。
1. 展开“可用性组”，右键单击可用性组，然后选择“添加数据库...”。

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="在对象资源管理器中右键单击可用性组，然后选择“添加数据库”":::

1. 按照提示选择要添加到可用性组的数据库。 
1. 选择“确定”保存设置并将数据库添加到可用性组。 
1. 添加数据库后，刷新“对象资源管理器”确认数据库的状态为 `synchronized`。 

添加数据库后，可以在 Azure 门户中查看可用性组的状态： 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="在数据库同步后，从 Azure 门户中的“高可用性”页面查看可用性组的状态":::

## <a name="add-more-vms"></a>添加更多 VM

如要将更多 SQL Server VM 添加到群集，请执行以下步骤： 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 转到 [SQL 虚拟机](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines)资源。 
1. 在“设置”中选择“高可用性” 。 
1. 选择“配置 Window Server 故障转移群集”，打开“配置 Window Server 故障转移群集”页面。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="选择“配置 Windows Server 故障转移群集”，将 VM 添加到群集。":::

1. 展开“Windows Server 故障转移群集凭据”，并输入用于 SQL Server 服务、群集操作员和群集启动帐户的帐户。 
1. 选择要添加到群集的 SQL Server VM。 
1. 选择“应用”。 

可通过顶部导航栏中的电铃图标访问“活动日志”，检查部署的状态。 

## <a name="configure-quorum"></a>配置仲裁

虽然磁盘见证是最具复原能力的仲裁选项，但它需要 Azure 共享磁盘，这对可用性组施加了一些限制。 因此，对于在 Azure VM 上的 SQL Server 中托管可用性组的群集，云见证是建议的仲裁解决方案。 

如果群集中的投票数为偶数，请配置最适合你的业务需求的[仲裁解决方案](hadr-cluster-quorum-configure-how-to.md)。 有关详细信息，请参阅 [SQL Server VM 上的仲裁](hadr-windows-server-failover-cluster-overview.md#quorum)。 


## <a name="modify-availability-group"></a>修改可用性组 


在 Azure 门户中的“高可用性”页面，通过选择可用性组旁的省略号 (...)，将更多副本添加到可用性组，配置侦听器，或删除侦听器： 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="选择可用性组旁边的省略号，然后选择“添加副本”，将更多副本添加到可用性组。":::

## <a name="remove-cluster"></a>删除群集

从群集中删除所有 SQL Server VM 以消除该群集，然后从 SQL IaaS 代理扩展中删除该群集元数据。 可以使用最新版本的 [Azure CLI](/cli/azure/install-azure-cli) 或 PowerShell 完成上述操作。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，从群集中删除所有 SQL Server VM。 该操作将删除群集中的节点并销毁群集：  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

如果这些 VM 是群集中仅有的 VM，则该群集将被销毁。 如果群集中除了删除的 SQL Server VM 之外，还有其他 VM，则不会删除其他的 VM，并且群集不会销毁。 

接下来，从 SQL IaaS 代理扩展中删除群集元数据： 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，从群集中删除所有 SQL Server VM。 该操作将删除群集中的节点并销毁群集： 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

如果这些 VM 是群集中仅有的 VM，则该群集将被销毁。 如果群集中除了删除的 SQL Server VM 之外，还有其他 VM，则不会删除其他的 VM，并且群集不会销毁。 


接下来，从 SQL IaaS 代理扩展中删除群集元数据： 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>疑难解答

如果遇到问题，可以检查部署历史记录，并查看常见错误及其解决方法。 

### <a name="check-deployment-history"></a>查看部署历史记录

通过门户对群集和可用性组进行的更改是通过部署来完成的。 如果在创建或加入群集、或创建可用性组时出现问题，部署历史记录可以提供详细信息进行参考。

如要查看部署的日志，并检查部署历史记录，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到资源组。
1. 选择“设置”下面的“部署”。
1. 选择所需的部署，了解有关部署的详细信息。 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="选择想要了解的相关部署。" :::

### <a name="common-errors"></a>常见错误

下面介绍了常见错误及其解决方法。 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>用于启动 sql 服务的帐户不是域帐户

这表示资源提供程序无法使用提供的凭据访问 SQL Server 服务。 一些常见解决方法：
- 确保域控制器正在运行。
- 验证门户中提供的凭据与 SQL Server 服务的凭据匹配。 


## <a name="next-steps"></a>后续步骤

部署可用性组后，请考虑优化 [Azure VM 上的 SQL Server 的 HADR 设置](hadr-cluster-best-practices.md)。 


若要了解更多信息，请参阅以下文章：

- [Azure VM 上的 SQL Server 的 Windows Server 故障转移群集](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM 上的 SQL Server 的 Always On 可用性组](availability-group-overview.md)
- [Always On 可用性组概述](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
