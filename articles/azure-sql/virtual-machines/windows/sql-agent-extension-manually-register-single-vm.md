---
title: 注册到 SQL IaaS 代理扩展
description: 使用 SQL IaaS 代理扩展注册 Azure SQL Server 虚拟机，为在 Azure Marketplace 外部部署的 SQL Server 虚拟机启用功能，并提供相容性和改进的可管理性。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 983cdab0c5f5b856537c661c7427a83099f30ed4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181425"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>向 SQL IaaS 代理扩展注册 SQL Server VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

向 [SQL IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md) 注册 SQL Server VM，以便为你的 SQL Server 在 Azure VM 上提供丰富的功能优势。 

本文介绍如何使用 SQL IaaS 代理扩展注册单个 SQL Server VM。 或者，你可以 [自动](sql-agent-extension-automatic-registration-all-vms.md) 注册所有 SQL Server vm，也可以 [批量注册多个虚拟机](sql-agent-extension-manually-register-vms-bulk.md)。


## <a name="overview"></a>概述

注册到 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)会在你的订阅中创建 **SQL 虚拟机**_资源_，该资源与虚拟机资源是 _单独_ 的资源。 从扩展中取消注册 SQL Server VM 将删除 **SQL 虚拟机**_资源_，但不会删除实际的虚拟机。

通过 Azure 门户部署 SQL Server VM Azure Marketplace 映像会自动向该扩展注册 SQL Server VM。 但是，如果选择在 Azure 虚拟机上自行安装 SQL Server，或通过自定义 VHD 来预配 Azure 虚拟机，则必须将 SQL Server VM 与 SQL IaaS 代理扩展一起注册，以解锁全部功能权益和可管理性。 

若要利用 SQL IaaS 代理扩展，必须首先将 [你的订阅注册到 **SqlVirtualMachine** 提供程序，该提供程序](#register-subscription-with-rp)提供了在特定订阅中创建资源的能力。

> [!IMPORTANT]
> SQL IaaS 代理扩展收集数据，以便在 Azure 虚拟机中使用 SQL Server 时，为客户提供可选的权益。 在未经客户的事先同意的情况下，Microsoft 不会将此数据用于许可审核。 有关详细信息，请参阅 [SQL Server 隐私补充](/sql/sql-server/sql-server-privacy#non-personal-data) 。

## <a name="prerequisites"></a>先决条件

若要向扩展注册 SQL Server VM，需要： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- Azure 资源模型 [Windows Server 2008 (或更高版本) ](../../../virtual-machines/windows/quick-create-portal.md) 具有 [SQL Server 2008 (或更高) 版本 ](https://www.microsoft.com/sql-server/sql-server-downloads) 的虚拟机部署到公共或 Azure 政府云。 
- 最新版本的 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell)  (5.0 ](/powershell/azure/install-az-ps)。 


## <a name="register-subscription-with-rp"></a>将订阅注册到 RP

若要向 SQL IaaS 代理扩展注册 SQL Server VM，必须先将订阅注册到 **SqlVirtualMachine** 提供程序。 这为 SQL IaaS 代理扩展提供了在订阅中创建资源的能力。  为此，可以使用 Azure 门户、Azure CLI 或 Azure PowerShell。

### <a name="azure-portal"></a>Azure 门户

1. 打开 Azure 门户，转到“所有服务”。 
1. 转到“订阅”，选择感兴趣的订阅。  
1. 在 " **订阅** " 页上，中转到 " **扩展**"。 
1. 在筛选器中输入 **sql** 以打开与 sql 相关的扩展。 
1. 根据所需操作为“Microsoft.SqlVirtualMachine”提供程序选择“注册”、“重新注册”或“取消注册”   。 

   ![修改提供程序](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>命令行

使用 Azure CLI 或 Azure PowerShell 将你的 Azure 订阅注册到 **SqlVirtualMachine** 提供程序。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>注册扩展

对于 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md#management-modes)，有三种管理模式。 

在完全管理模式下注册扩展将重新启动 SQL Server 服务，因此建议首先在轻型模式下注册扩展，然后在维护时段内 [升级到完整](#upgrade-to-full) 扩展。 

### <a name="lightweight-management-mode"></a>轻型管理模式

使用 Azure CLI 或 Azure PowerShell 在轻型模式下将 SQL Server VM 注册为扩展。 这不会重新启动 SQL Server 服务。 随后可以随时升级到完整模式，但这样做会重启 SQL Server 服务，因此建议等到计划性维护时段。 

提供 SQL Server 许可证类型作为即用即付 (`PAYG`) 为按使用情况付费，Azure 混合权益 (`AHUB`) 使用自己的许可证，或使用灾难恢复 (`DR`) 激活 [免费的 DR 副本许可证](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)。

故障转移群集实例和多实例部署只能在轻型模式下以 SQL IaaS 代理扩展注册。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

使用 Azure CLI 在轻型模式下注册 SQL Server VM： 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

使用 Azure PowerShell 在轻型模式下注册 SQL Server VM：  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>完整管理模式

以完整模式注册 SQL Server VM 将重新启动 SQL Server 服务。 请谨慎操作。 

若要以完全模式直接注册 SQL Server VM (并可能重新启动 SQL Server 服务) ，请使用以下 Azure PowerShell 命令： 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>无代理管理模式 

Windows Server 上安装的 SQL Server 2008 和 2008 R2 2008 (不能在 [NoAgent 模式下](sql-server-iaas-agent-extension-automate-management.md#management-modes)向 SQL IaaS 代理扩展注册 _r2_) 。 此选项可确保符合性，并允许使用有限功能在 Azure 门户中监视 SQL Server VM。


对于 **许可证类型**，请指定： `AHUB` 、 `PAYG` 或 `DR` 。 对于 **映像产品/服务**，请指定 `SQL2008-WS2008` 或 `SQL2008R2-WS2008`

若要 `SQL2008-WS2008` 在 Windows Server 2008 实例上注册 SQL Server 2008 () 或 2008 R2 (`SQL2008R2-WS2008`) ，请使用以下 Azure CLI 或 Azure PowerShell 代码片段： 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

在 NoAgent 模式下向 Azure CLI 注册 SQL Server 虚拟机： 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


在 NoAgent 模式下向 Azure PowerShell 注册 SQL Server 虚拟机： 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>验证模式

可以通过使用 Azure PowerShell 查看 SQL Server IaaS 代理的当前模式： 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>升级到完整  

在 *轻型* 模式下注册扩展的 SQL Server vm 可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 升级为 _完整_ 。 在 OS 升级到 Windows 2008 R2 及更高版本之后，无代理模式下的 SQL Server VM 可以升级到完整模式 。 不可能进行降级-若要执行此操作，你将需要从 SQL IaaS 代理扩展中 [注销](#unregister-from-extension) SQL Server VM。 这样做会删除 SQL 虚拟机资源，但不会删除实际虚拟机。 

> [!NOTE]
> 将 SQL IaaS 扩展的管理模式升级到 full 后，它将重新启动 SQL Server 服务。 在某些情况下，重新启动可能会导致服务主体名称 (Spn) 与 SQL Server 服务相关联才能更改为错误的用户帐户。 如果在将管理模式升级到 full 后出现连接问题，请 [注销并重新注册 spn](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections)。


### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户将扩展升级到完整模式，请执行以下步骤： 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到 [SQL 虚拟机](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)资源。 
1. 选择 SQL Server VM，然后选择 " **概述**"。 
1. 对于具有无代理或轻型 IaaS 模式的 SQL Server VM，请选择“SQL IaaS 扩展仅提供许可证类型和版本更新”消息。

   ![用于从门户更改模式的选项](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. 选中“我同意重启虚拟机上的 SQL Server 服务”复选框，然后选择“确认”以将 IaaS 模式升级到完整模式 。 

    ![用于同意重启虚拟机上的 SQL Server 服务的复选框](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>命令行

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

若要将扩展升级到完整模式，请运行以下 Azure CLI 代码片段：

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

若要将扩展升级到完整模式，请运行以下 Azure PowerShell 代码片段：

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>验证注册状态
你可以通过使用 Azure 门户、Azure CLI 或 Azure PowerShell 来验证是否已将你的 SQL Server VM 注册到 SQL IaaS 代理扩展。 

### <a name="azure-portal"></a>Azure 门户 

若要使用 Azure 门户验证注册状态，请执行以下步骤： 

1. 登录 [Azure 门户](https://portal.azure.com)。 
1. 中转到 [SQL Server vm](manage-sql-vm-portal.md)。
1. 从列表中选择 SQL Server VM。 如果你的 SQL Server VM 未在此处列出，则它可能尚未注册到 SQL IaaS 代理扩展。 
1. 查看“状态”下的值。 如果 **状态** 为 " **成功**"，则 SQL Server VM 已成功注册到 SQL IaaS 代理扩展。 

   ![通过 SQL RP 注册验证状态](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>命令行

使用 Azure CLI 或 Azure PowerShell 验证当前 SQL Server VM 注册状态。 如果注册成功，`ProvisioningState` 将显示 `Succeeded`。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

若要使用 Azure CLI 验证注册状态，请运行以下代码片段：  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

若要使用 Azure PowerShell 验证注册状态，请运行以下代码片段：

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

一个错误，指示未向扩展注册 SQL Server VM。 


## <a name="unregister-from-extension"></a>从扩展中注销

若要使用 SQL IaaS 代理扩展取消注册 SQL Server VM，请使用 Azure 门户或 Azure CLI 删除 SQL 虚拟机 *资源* 。 删除 SQL 虚拟机 *资源* 不会删除 SQL Server VM。 但是，请小心谨慎并仔细执行以下步骤，因为在尝试删除资源时，可能会意外删除虚拟机。 

若要将管理模式从完全降级，必须在 SQL IaaS 代理扩展中注销 SQL 虚拟机。 

### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户从扩展中注销 SQL Server VM，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到 SQL VM 资源。 
  
   ![SQL 虚拟机资源](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. 选择“删除”。 

   ![在顶部导航栏中选择 "删除"](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. 键入 SQL 虚拟机的名称，并 **清除该虚拟机旁边的复选框**。

   ![取消选中 VM 以阻止删除实际虚拟机，然后选择 "删除" 以继续删除 SQL VM 资源](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 如果未能清除虚拟机名称的复选框，会导致彻底删除虚拟机。 清除该复选框可从扩展中注销 SQL Server VM，但 *不会删除实际的虚拟机*。 

1. 选择 " **删除** " 以确认删除 SQL 虚拟机 *资源*，而不是 SQL Server VM。 

### <a name="command-line"></a>命令行

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 从扩展中注销 SQL Server VM，请使用 [az SQL VM delete](/cli/azure/sql/vm#az-sql-vm-delete) 命令。 这会删除 SQL Server VM *资源* ，但不会删除虚拟机。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 从扩展中注销 SQL Server VM，请使用 [AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)命令。 这会删除 SQL Server VM *资源* ，但不会删除虚拟机。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.md)  
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](../../database/doc-changes-updates-release-notes.md)
