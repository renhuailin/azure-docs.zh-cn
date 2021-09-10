---
title: 向 SQL IaaS 代理扩展注册
description: 向 SQL IaaS 代理扩展注册 Azure SQL Server 虚拟机，以便为在 Azure 市场外部署的 SQL Server 虚拟机启用功能，并提供合规性以及改进的可管理性。
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
ms.date: 09/01/2021
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 3dfcfcab7421b6aa8710310fdffa08f54688c4fd
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219956"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>向 SQL IaaS 代理扩展注册 SQL Server VM

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

向 [SQL IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)注册 SQL Server VM，以便为 Azure VM 上的 SQL Server 解锁丰富的功能权益。

本文介绍如何向 SQL IaaS 代理扩展注册单个 SQL Server VM。 或者，可以在订阅中[自动](sql-agent-extension-automatic-registration-all-vms.md)注册所有 SQL Server VM，也可以[通过脚本批量注册多个 VM](sql-agent-extension-manually-register-vms-bulk.md)。

> [!NOTE]
> 从 2021 年 9 月开始，在完整模式下注册 SQL IaaS 扩展不再需要重启 SQL Server 服务。 

## <a name="overview"></a>概述

注册 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)可在订阅（该资源独立于虚拟机资源）中创建 [SQL 虚拟机资源](manage-sql-vm-portal.md)。  从扩展中取消注册 SQL Server VM 会删除 **SQL 虚拟机** 资源，但不会删除实际虚拟机。

通过 Azure 门户部署 SQL Server VM Azure 市场映像会自动将 SQL Server VM 注册到扩展。 但是，如果选择在 Azure 虚拟机上自行安装 SQL Server，或通过自定义 VHD 预配 Azure 虚拟机，则必须向 SQL IaaS 代理扩展 SQL Server VM 注册才能解锁完整功能权益和可管理性。

若要利用 SQL IaaS 代理扩展，必须首先[向 Microsoft.SqlVirtualMachine 提供程序注册订阅](#register-subscription-with-rp)，这使 SQL IaaS 扩展能够在该特定订阅中创建资源。

> [!IMPORTANT]
> SQL IaaS 代理扩展会收集数据，目的只是当客户在 Azure 虚拟机中使用 SQL Server 时为他们提供可选权益。 在未经得客户事先同意的情况下，Microsoft 不会将此数据用于许可审核。 有关详细信息，请参阅 [SQL Server 隐私补充条款](/sql/sql-server/sql-server-privacy#non-personal-data)。

## <a name="prerequisites"></a>先决条件

若要向扩展注册 SQL Server VM，需要提供：

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 一个 Azure 资源模型 [Windows Server 2008（或更高版本）虚拟机](../../../virtual-machines/windows/quick-create-portal.md)，其 [SQL Server 2008（或更高版本）](https://www.microsoft.com/sql-server/sql-server-downloads)已部署到公有云或 Azure 政府云。
- 最新版本的 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell（最低 5.0）](/powershell/azure/install-az-ps)。

## <a name="register-subscription-with-rp"></a>将订阅注册到 RP

若要向 SQL Server VM 注册 SQL IaaS 代理扩展，必须首先向订阅注册 Microsoft.SqlVirtualMachine 资源提供程序 (RP)。 这使 SQL IaaS 代理扩展能够在订阅中创建资源。 可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 执行此操作。

### <a name="azure-portal"></a>Azure 门户

1. 打开 Azure 门户，转到“所有服务”。
1. 转到“订阅”，选择感兴趣的订阅。
1. 在“订阅”页上，选择“设置”下的“资源提供程序”。
1. 在筛选器中输入“sql”，以便显示与 SQL 相关的资源提供程序。
1. 根据所需操作为“Microsoft.SqlVirtualMachine”提供程序选择“注册”、“重新注册”或“取消注册”   。

   ![修改提供程序](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)

### <a name="command-line"></a>命令行

使用 Azure CLI 或 Azure PowerShell 向 Microsoft.SqlVirtualMachine 提供程序注册 Azure 订阅。

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

## <a name="full-mode"></a>完整模式

若要直接在完整模式下注册 SQL Server VM，请使用以下 Azure PowerShell 命令：

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  
  ```

若要详细了解完整模式，请参阅 [管理模式](sql-server-iaas-agent-extension-automate-management.md#management-modes)。

### <a name="upgrade-to-full"></a>升级到完整模式

在轻型模式下注册了扩展的 SQL Server VM 可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 升级到完整模式。 在 OS 升级到 Windows 2008 R2 及更高版本之后，无代理模式下的 SQL Server VM 可以升级到完整模式 。 无法进行降级 - 若要执行此操作，需要 从 SQL IaaS 代理扩展[取消注册](#unregister-from-extension) SQL Server VM。 这样做会删除 SQL 虚拟机资源，但不会删除实际虚拟机。

#### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户将扩展升级到完整模式，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到 [SQL 虚拟机](manage-sql-vm-portal.md#access-the-resource)资源。
1. 选择 SQL Server VM，然后导航到“概述”页。
1. 对于具有无代理或轻型 IaaS 扩展模式的 SQL Server VM，请选择“当前 SQL IaaS 扩展模式仅提供许可证类型和版本更新...”消息。

   ![用于从门户更改模式的选项](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. 选择"确认"，将 SQL Server IaaS 扩展模式升级到完整模式。

    ![选择“确认”，将 SQL Server IaaS 扩展模式升级到完整模式。](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

#### <a name="command-line"></a>命令行

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
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -Location $vm.Location
  ```

---

## <a name="lightweight-mode"></a>轻型模式

使用 Azure CLI 或 Azure PowerShell 在轻型模式下注册 SQL Server VM 扩展，以获得有限的功能。 

提供 SQL Server 许可证类型，形式为即用即付（`PAYG`，用于按使用情况付费）、Azure 混合权益（`AHUB`，用于使用自己的许可证）或灾难恢复（`DR`，用于激活[免费 DR 副本许可证](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)）。

故障转移群集实例和多实例部署只能在轻型模式下向 SQL IaaS 代理扩展注册。

若要详细了解轻型模式，请参阅[管理模式](sql-server-iaas-agent-extension-automate-management.md#management-modes)。

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

## <a name="noagent-management-mode"></a>无代理管理模式

在 Windows Server 2008（非 R2）上安装的 SQL Server 2008 和 2008 R2 只能在[无代理模式](sql-server-iaas-agent-extension-automate-management.md#management-modes)下注册 SQL IaaS 代理扩展。 此选项可确保符合性，并允许使用有限功能在 Azure 门户中监视 SQL Server VM。

对于许可证类型，请指定：`AHUB`、`PAYG` 或 `DR`。 对于映像套餐，请指定 `SQL2008-WS2008` 或 `SQL2008R2-WS2008`

若要在 Windows Server 2008 实例上注册 SQL Server 2008 (`SQL2008-WS2008`) 或 2008 R2 (`SQL2008R2-WS2008`)，请使用以下 Azure CLI 或 Azure PowerShell 代码片段：

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

使用 Azure CLI 在无代理模式下注册 SQL Server 虚拟机：

```azurecli-interactive
az sql vm create -n sqlvm -g myresourcegroup -l eastus |
--license-type <license type>  --sql-mgmt-type NoAgent 
--image-sku Enterprise --image-offer <image offer> 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

使用 Azure PowerShell 在无代理模式下注册 SQL Server 虚拟机：

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
```

---


## <a name="check-extension-mode"></a>检查扩展模式

使用 Azure PowerShell 检查 SQL Server IaaS 代理扩展所处的管理模式。 

若要检查扩展模式，请使用此 Azure PowerShell cmdlet： 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>升级到完整模式

在轻型模式下注册了扩展的 SQL Server VM 可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 升级到完整模式。 在 OS 升级到 Windows 2008 R2 及更高版本之后，无代理模式下的 SQL Server VM 可以升级到完整模式 。 无法进行降级 - 若要执行此操作，需要 从 SQL IaaS 代理扩展[取消注册](#unregister-from-extension) SQL Server VM。 这样做会删除 SQL 虚拟机资源，但不会删除实际虚拟机。

### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户将扩展升级到完整模式，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到 [SQL 虚拟机](manage-sql-vm-portal.md#access-the-resource)资源。
1. 选择 SQL Server VM，然后选择“概述”。
1. 对于具有无代理或轻型 IaaS 模式的 SQL Server VM，请选择“当前 SQL IaaS 扩展仅提供许可证类型和版本更新...”消息。

   ![用于从门户更改模式的选项](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. 选择“确认”，将 SQL Server 扩展 IaaS 模式升级到完整模式。

    ![选择“确认”，将 SQL Server 扩展 IaaS 模式升级到完整模式](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

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
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full -Location $vm.Location
  ```

---

## <a name="verify-registration-status"></a>验证注册状态

可以使用 Azure 门户、Azure CLI 或 Azure PowerShell 验证是否已向 SQL IaaS 代理扩展注册 SQL Server VM。

### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户验证注册状态，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 转到 [SQL Server VM](manage-sql-vm-portal.md)。
1. 从列表中选择 SQL Server VM。 如果 SQL Server VM 未在此处列出，则可能尚未向 SQL IaaS 代理扩展注册。
1. 查看“状态”下的值。 如果“状态”为“成功”，则 SQL Server VM 已成功向 SQL IaaS 代理扩展注册 。

   ![通过 SQL RP 注册验证状态](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

也可通过在“SQL 虚拟机”资源的“支持 + 故障排除”窗格下选择“修复”来检查状态。 SQL IaaS 代理扩展的预配状态可以是“成功”，也可以是“失败”。 

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

错误表明 SQL Server VM 尚未向扩展注册。

## <a name="repair-extension"></a>修复扩展

SQL IaaS 代理扩展可能处于失败状态。 使用 Azure 门户修复 SQL IaaS 代理扩展。 为此，请执行下列步骤： 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到 [SQL Server VM](manage-sql-vm-portal.md)。
1. 从列表中选择 SQL Server VM。 如果 SQL Server VM 未在此处列出，则可能尚未向 SQL IaaS 代理扩展注册。
1. 在“SQL 虚拟机”资源页的“支持 + 故障排除”下选择“修复”。 

   :::image type="content" source="media/sql-agent-extension-manually-register-single-vm/repair-extension.png" alt-text="在“SQL 虚拟机”资源页的“支持 + 故障排除”下选择“修复”":::   

1. 如果预配状态显示为“失败”，请选择“修复”来修复扩展。 如果状态为“成功”，则可以选中“强制修复”旁边的框来修复扩展，而不考虑状态。 

   ![如果预配状态显示为“失败”，请选择“修复”来修复扩展。 如果状态为“成功”，则可选中“强制修复”旁边的框来修复扩展，而不考虑状态。](./media/sql-agent-extension-manually-register-single-vm/force-repair-extension.png)


## <a name="unregister-from-extension"></a>从扩展取消注册

若要从 SQL IaaS 代理扩展取消注册 SQL Server VM，请使用 Azure 门户或 Azure CLI 删除 SQL 虚拟机资源。 删除 SQL 虚拟机资源不会删除 SQL Server VM。 但是，请小心谨慎并仔细执行以下步骤，因为在尝试删除资源时，可能会意外删除虚拟机。

若要将管理模式从完整模式降级，需要从 SQL IaaS 代理扩展取消注册 SQL 虚拟机。

### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户从扩展取消注册 SQL Server VM，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到 SQL VM 资源。

   ![SQL 虚拟机资源](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. 选择“删除”。

   ![在顶部导航中选择删除](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. 键入 SQL 虚拟机的名称，并清除虚拟机旁的复选框。

   ![取消选中 VM 以阻止删除实际虚拟机，然后选择“删除”以继续删除 SQL VM 资源](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   > [!WARNING]
   > 如果未能清除虚拟机名称的复选框，会导致彻底删除虚拟机。 清除复选框可从扩展取消注册 SQL Server VM，但不会删除实际虚拟机。

1. 选择“删除”以确认 SQL 虚拟机资源的删除，而不是 SQL Server VM。

### <a name="command-line"></a>命令行

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 从扩展取消注册 SQL Server VM，请使用 [az sql vm delete](/cli/azure/sql/vm#az_sql_vm_delete) 命令。 这会删除 SQL Server VM 资源，但不会删除虚拟机。

```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 从扩展取消注册 SQL Server VM，请使用 [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm) 命令。 这会删除 SQL Server VM 资源，但不会删除虚拟机。

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.yml)
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](../../database/doc-changes-updates-release-notes.md)
