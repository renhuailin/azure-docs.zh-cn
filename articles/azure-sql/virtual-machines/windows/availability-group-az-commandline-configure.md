---
title: 配置可用性组 (PowerShell & Az CLI)
description: 使用 PowerShell 或 Azure CLI 在 Azure 中的 SQL Server VM 上创建 Windows 故障转移群集、可用性组侦听程序和内部负载均衡器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 060d09d17bf622af5ca5c062e00d2961a0a2b566
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572460"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>使用 PowerShell 或 Az CLI 在 Azure VM 上为 SQL Server 配置可用性组 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何使用 [PowerShell](/powershell/scripting/install/installing-powershell) 或 [Azure CLI](/cli/azure/sql/vm) 部署 Windows 故障转移群集，将 SQL Server VM 添加到群集，以及为 Always On 可用性组创建内部负载均衡器和侦听程序。 

始终通过 SQL Server Management Studio (SSMS) 或 Transact-SQL (T-SQL) 手动完成可用性组的部署。 

尽管本文使用 PowerShell 和 Az CLI 来配置可用性组环境，但也可使用 [Azure 快速启动模板](availability-group-quickstart-template-configure.md)或以[手动](availability-group-manually-configure-tutorial.md)方式从 [Azure 门户](availability-group-azure-portal-configure.md)进行配置。 

> [!NOTE]
> 现在，可以使用 Azure Migrate 将可用性组解决方案直接迁移到 Azure VM 上的 SQL Server。 有关详细信息，请参阅[迁移可用性组](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)。 

## <a name="prerequisites"></a>先决条件

若要配置 Always On 可用性组，必须满足以下先决条件： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个具有域控制器的资源组。 
- Azure 中的一个或多个已加入域的 VM，它们[运行 SQL Server 2016 Enterprise 版本（或更高版本）](./create-sql-vm-portal.md)，位于[已注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)的同一个可用性集或不同可用性区域中 。  
- 最新版本的 [PowerShell](/powershell/scripting/install/installing-powershell) 或 [Azure CLI](/cli/azure/install-azure-cli)。 
- 两个可用的（未被任何实体使用的）IP 地址。 一个用于内部负载均衡器。 另一个用于与可用性组位于同一子网中的可用性组侦听器。 如果使用现有的负载均衡器，则可用性组侦听程序只需要一个有效的 IP 地址。 

## <a name="permissions"></a>权限

需要具有以下帐户权限才能使用 Azure CLI 配置 Always On 可用性组： 

- 在域中具有“创建计算机对象”权限的现有域用户帐户。 例如，域管理员帐户通常拥有足够的权限（如 account@domain.com）。 该帐户还应该属于每个 VM 上负责创建群集的本地管理员组。
- 可控制 SQL Server 的域用户帐户。 
 
## <a name="create-a-storage-account"></a>创建存储帐户 

群集需要一个存储帐户来充当云见证。 可以使用现有存储帐户，也可以创建新的存储帐户。 如果要使用现有存储帐户，请跳转至下一部分。 

下面的代码片段将创建存储帐户： 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 如果使用的是过时的 Azure CLI 版本，可能会看到错误 `az sql: 'vm' is not in the 'az sql' command group`。 下载[最新版本的 Azure CLI](/cli/azure/install-azure-cli-windows)，以跳过此错误。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>定义群集元数据

Azure CLI [az sql vm group](/cli/azure/sql/vm/group) 命令组管理托管可用性组的 Windows Server 故障转移群集 (WSFC) 服务的元数据。 群集元数据包括 Active Directory 域、群集帐户、要用作云见证的存储帐户和 SQL Server 版本。 使用 [az sql vm group create](/cli/azure/sql/vm/group#az_sql_vm_group_create) 定义 WSFC 的元数据，以便在添加第一个 SQL Server VM 时，按定义创建群集。 

下面的代码片段定义群集的元数据：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>向群集添加 VM

将第一个 SQL Server VM 添加到群集将创建群集。 [az sql vm add-to-group](/cli/azure/sql/vm#az_sql-vm_add_to_group) 命令使用先前指定的名称创建群集，在 SQL Server VM 上安装群集角色，然后将其添加到群集中。 继续使用 `az sql vm add-to-group` 命令将更多 SQL Server VM 添加到新创建的群集中。 

以下代码片段会创建群集，并将第一个 SQL Server VM 添加到其中： 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
使用此命令可将任何其他 SQL Server VM 添加到群集。 仅修改 SQL Server VM 名称的 `-n` 参数。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---

## <a name="configure-quorum"></a>配置仲裁

虽然磁盘见证是最具复原能力的仲裁选项，但它需要 Azure 共享磁盘，这对可用性组施加了一些限制。 因此，对于在 Azure VM 上的 SQL Server 中托管可用性组的群集，推荐使用云见证作为仲裁解决方案。 

如果群集中的投票数为偶数，请配置最适合业务需求的[仲裁解决方案](hadr-cluster-quorum-configure-how-to.md)。 有关详细信息，请参阅 [SQL Server VM 上的仲裁](hadr-windows-server-failover-cluster-overview.md#quorum)。 


## <a name="validate-cluster"></a>验证群集 

故障转移群集必须通过群集验证才能得到 Microsoft 的支持。 使用首选方法（例如远程桌面协议 (RDP)）连接到 VM，并验证群集是否通过验证，然后再进行下一步。 否则，群集将处于不受支持状态。 

用户可使用故障转移群集管理器 (FCM) 或以下 PowerShell 命令来验证群集：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>创建可用性组

像平时一样使用 [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、[PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) 或 [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 手动创建可用性组。 

>[!IMPORTANT]
> 此时不要创建侦听器，因为下面部分将通过 Azure CLI 完成创建。  

## <a name="create-internal-load-balancer"></a>创建内部负载均衡器

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Always On 可用性组侦听器需要 Azure 负载均衡器的内部实例。 内部负载均衡器为可用性组侦听器提供“浮动”IP 地址，可以加快故障转移和重新连接的速度。 如果可用性组中的 SQL Server VM 属于同一个可用性集，则可以使用基本负载均衡器。 否则，需要使用标准负载均衡器。  

> [!NOTE]
> 内部负载均衡器应与 SQL Server VM 实例位于同一虚拟网络中。 

以下代码片段会创建内部负载均衡器：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> 每个 SQL Server VM 的公共 IP 资源应有一个与标准负载均衡器兼容的标准 SKU。 要确定 VM 公共 IP 资源的 SKU，请转到“资源组”，选择所需 SQL Server VM 的“公共 IP 地址”资源，并在“概述”窗格的“SKU”下面找到该值   。  

## <a name="create-listener"></a>创建侦听器

手动创建可用性组后，可以使用 [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_create) 命令创建侦听器。 

子网资源 ID 是 `/subnets/<subnetname>` 附加到虚拟网络资源的资源 ID 的值。 标识子网资源 ID：
   1. 转到 [Azure 门户](https://portal.azure.com)中的资源组。 
   1. 选择虚拟网络资源。 
   1. 在“设置”窗格中选择“属性” 。 
   1. 标识虚拟网络的资源 ID，并在其末尾附加 `/subnets/<subnetname>`，以创建子网资源 ID。 例如：
      - 虚拟网络资源 ID 为 `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - 子网名称为 `default`
      - 因此，子网资源 ID 为 `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


以下代码片段将创建可用性组侦听器：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>修改副本数 
将可用性组部署到 Azure 中托管的 SQL Server VM，这会增加复杂性。 现在可使用资源提供程序和虚拟机组管理资源。 因此，将副本添加到可用性组或从中删除副本时，还有一个附加步骤，即使用有关 SQL Server VM 的信息来更新侦听器元数据。 修改可用性组中的副本数时，还必须使用 [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_update) 命令，以使用 SQL Server VM 的元数据更新侦听器。 


### <a name="add-a-replica"></a>添加副本

将新副本添加到可用性组：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 将 SQL Server VM 添加到群集组中：
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. 使用 SQL Server Management Studio 将 SQL Server 实例作为副本添加到可用性组中。
1. 将 SQL Server VM 元数据添加到侦听器：

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 将 SQL Server VM 添加到群集组中：

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. 使用 SQL Server Management Studio 将 SQL Server 实例作为副本添加到可用性组中。
1. 将 SQL Server VM 元数据添加到侦听器：

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>删除副本

从可用性组中删除副本：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 使用 SQL Server Management Studio 从可用性组中删除副本。 
1. 从侦听器中删除 SQL Server VM 元数据：
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 从群集中删除 SQL Server VM：
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 使用 SQL Server Management Studio 从可用性组中删除副本。 
1. 从侦听器中删除 SQL Server VM 元数据：

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. 从群集中删除 SQL Server VM：

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>删除侦听器
如果以后需要删除使用 Azure CLI 配置的可用性组侦听程序，则必须通过 SQL IaaS 代理扩展执行整个操作。 由于该侦听程序是通过 SQL IaaS 代理扩展注册的，因此仅仅通过 SQL Server Management Studio 删除它是不够的。 

最佳方法是在 Azure CLI 中使用以下代码片段，通过 SQL IaaS 代理扩展将其删除。 这样就会从 SQL IaaS 代理扩展中删除可用性组侦听程序元数据。 同时还会从根本上删除可用性组中的侦听器。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>删除群集

从群集中删除所有节点以销毁该群集，然后从 SQL IaaS 代理扩展中删除该群集元数据。 可以使用 Azure CLI 或 PowerShell 完成上述操作。 


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，从群集中删除所有 SQL Server VM： 

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

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
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

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>后续步骤

部署可用性组后，请考虑优化 [Azure VM 上 SQL Server 的 HADR 设置](hadr-cluster-best-practices.md)。 


若要了解更多信息，请参阅以下文章：

- [Windows Server 故障转移群集与 Azure VM 上的 SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Always On 可用性组与 Azure VM 上的 SQL Server](availability-group-overview.md)
- [Always On 可用性组概述](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
