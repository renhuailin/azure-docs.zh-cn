---
title: 什么是 SQL Server IaaS 代理扩展？
description: 本文介绍 SQL Server IaaS 代理扩展如何帮助在 Azure Vm 上自动执行 SQL Server 管理特定管理任务。 其中包括自动备份、自动修补、Azure Key Vault 集成、授权管理、存储配置和所有 SQL Server VM 实例的集中管理等功能。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: cab5ac5e6a8fd900a41ff3690763746033b6200e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034806"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>通过 SQL Server IaaS 代理扩展自动进行管理
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 代理扩展 (SqlIaasExtension) 在 Azure 虚拟机上的 SQL Server 上运行 (Vm) 来自动执行管理任务和管理任务。 

本文提供扩展的概述。 若要在 Azure Vm 上安装 SQL Server IaaS 扩展以 SQL Server，请批量查看 [自动安装](sql-agent-extension-automatic-registration-all-vms.md)、 [单个 vm](sql-agent-extension-manually-register-single-vm.md)或 [vm](sql-agent-extension-manually-register-vms-bulk.md)的文章。 

## <a name="overview"></a>概述

SQL Server IaaS 代理扩展允许与 Azure 门户集成，并且根据管理模式，可为 Azure Vm 上的 SQL Server 解锁多种功能权益： 

- **功能优势**：扩展功能使你能够更好地利用一些自动化功能权益，例如门户管理、许可证灵活性、自动备份、自动修补等。 有关详细信息，请参阅本文后面的 [功能权益](#feature-benefits) 。 

- **符合性**：该扩展提供了一种简化的方法，使您能够在产品条款中指定 Azure 混合权益已启用。 此过程无需为每个资源管理许可注册表单。  

- **免费**：所有三个可管理性模式中的扩展都是完全免费的。 扩展或更改管理模式不会产生额外的费用。 

- **简化的许可证管理**：扩展可简化 SQL Server 许可证管理，并使你能够使用 [Azure 门户](manage-sql-vm-portal.md)、PowerShell 或 Azure CLI 快速标识 Azure 混合权益启用 SQL Server vm： 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> SQL IaaS 代理扩展收集数据，以便在 Azure 虚拟机中使用 SQL Server 时，为客户提供可选的权益。 在未经客户的事先同意的情况下，Microsoft 不会将此数据用于许可审核。 有关详细信息，请参阅 [SQL Server 隐私补充](/sql/sql-server/sql-server-privacy#non-personal-data) 。


## <a name="feature-benefits"></a>功能优势 

SQL Server IaaS 代理扩展将为管理 SQL Server VM 提供多种功能优势。 

下表详细说明了这些优点： 


| 功能 | 说明 |
| --- | --- |
| **门户管理** | 在 [门户](manage-sql-vm-portal.md)中取消锁定管理，以便你可以在一个位置查看所有 SQL Server vm，以便可以直接从门户中启用和禁用 SQL 特定功能。 <br/> 管理模式：轻型 & full|  
| **自动备份** |对 VM 中的 SQL Server 默认实例或已[正确安装](frequently-asked-questions-faq.md#administration)的命名实例自动执行所有数据库的备份计划。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动备份 (Resource Manager)](automated-backup-sql-2014.md)。 <br/> 管理模式：完整|
| **自动修补** |配置维护时段，在这种情况下，可能会发生重要的 Windows 和 SQL Server VM 的安全更新，因此可以避免在工作负荷高峰时间进行更新。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动修补 (Resource Manager)](automated-patching.md)。 <br/> 管理模式：完整|
| **Azure 密钥保管库集成** |可让你在 SQL Server VM 上自动安装和配置 Azure 密钥保管库。 有关详细信息，请参阅 [为 Azure 虚拟机上的 SQL Server 配置 Azure 密钥保管库集成 (Resource Manager)](azure-key-vault-integration-configure.md)。 <br/> 管理模式：完整|
| **在门户中查看磁盘使用率** | 允许您查看 Azure 门户中 SQL 数据文件的磁盘使用率的图形表示形式。  <br/> 管理模式：完整 | 
| **灵活的许可** | [无缝地](licensing-model-azure-hybrid-benefit-ahb-change.md)从自带许可证 (（也称为 Azure 混合权益) ）过渡到 "即用即付" 许可模式，并再次返回。 <br/> 管理模式：轻型 & full| 
| **灵活版本/版本** | 如果决定更改[SQL Server 的](change-sql-server-edition.md)[版本](change-sql-server-version.md)，则可以更新 Azure 门户中的元数据，而无需重新部署整个 SQL Server VM。  <br/> 管理模式：轻型 & full| 


## <a name="management-modes"></a>管理模式

你可以选择在三种管理模式下注册 SQL IaaS 扩展： 

- **轻型** 模式将扩展二进制文件复制到 VM，但不会安装代理，也不会重新启动 SQL Server 服务。 轻型模式仅支持更改 SQL Server 的许可证类型和版本，并提供有限的门户管理。 SQL Server 对于包含多个实例的 Vm 或 (FCI) 的故障转移群集实例的 Vm，请使用此选项。 当使用 [自动注册](sql-agent-extension-automatic-registration-all-vms.md) 功能时，或者当手动注册期间未指定管理类型时，轻型模式是默认的管理模式。 使用轻型模式时，不会对内存或 CPU 产生任何影响，并且没有关联成本。 建议先在轻型模式下注册 SQL Server VM，然后在计划性维护时段内升级到完整模式。 

- **完整** 模式将 SQL IaaS 代理安装到 VM 以提供所有功能，但需要重新启动 SQL Server 服务和系统管理员权限。 使用它管理具有单个实例的 SQL Server VM。 完整模式安装两个 Windows 服务，它们对内存和 CPU 的影响最小 - 可通过任务管理器进行监视。 使用完整可管理性模式时没有关联成本。 

- 无代理模式专用于在 Windows Server 2008 上安装的 SQL Server 2008 和 SQL Server 2008 R2。 使用无代理模式时，不会对内存或 CPU 产生任何影响。 使用 NoAgent 可管理性模式不会产生任何费用，SQL Server 不会重新启动，也不会在 VM 上安装代理。 

可以通过使用 Azure PowerShell 查看 SQL Server IaaS 代理的当前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>安装

向 SQL Server IaaS 代理扩展注册 SQL Server VM，以在订阅中创建 **SQL 虚拟机**_资源_，该资源与虚拟机资源是 _单独_ 的资源。 从扩展中取消注册 SQL Server VM 将删除 **SQL 虚拟机**_资源_，但不会删除实际的虚拟机。

通过 Azure 门户部署 SQL Server VM Azure Marketplace 映像会自动向该扩展注册 SQL Server VM。 但是，如果选择在 Azure 虚拟机上自行安装 SQL Server，或通过自定义 VHD 预配 Azure 虚拟机，则必须使用 SQL IaaS 扩展注册 SQL Server VM，以解锁功能权益。 

在轻型模式下注册扩展将复制二进制文件，但不会将代理安装到 VM。 当扩展升级到完全管理模式时，代理将安装到 VM。 

可以通过三种方式向扩展注册： 
- [对于订阅中的所有当前和未来 Vm 自动执行](sql-agent-extension-automatic-registration-all-vms.md)
- [对于单个 VM 为手动](sql-agent-extension-manually-register-single-vm.md)
- [批量为多个 Vm 手动](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>命名实例支持

如果 SQL Server IaaS 代理扩展是虚拟机上唯一可用的 SQL Server 实例，则与 SQL Server 的命名实例一起工作。 如果 VM 上没有默认实例，则无法在具有多个命名 SQL Server 实例的 Vm 上安装扩展。 

若要使用 SQL Server 的命名实例，请部署 Azure 虚拟机，将名 SQL Server 为的单个实例安装到该虚拟机，然后将其注册到 [SQL IaaS 扩展](sql-agent-extension-manually-register-single-vm.md)。

或者，若要将命名实例用于 Azure Marketplace SQL Server 映像，请执行以下步骤： 

   1. 从 Azure 市场部署 SQL Server VM。 
   1. 从 SQL IaaS 代理扩展中[注销](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)SQL Server VM。 
   1. 完全卸载 SQL Server VM 中的 SQL Server。
   1. 使用 SQL Server VM 中的命名实例安装 SQL Server。 
   1. 将[VM 注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md#register-with-extension)。 

## <a name="verify-status-of-extension"></a>验证扩展状态

使用 Azure 门户或 Azure PowerShell 检查扩展的状态。 

### <a name="azure-portal"></a>Azure 门户

验证是否已在 Azure 门户中安装了该扩展。 

在 "虚拟机" 窗格中选择 " **所有设置** "，然后选择 " **扩展**"。 应看到列出“SqlIaasExtension”扩展。

![Azure 门户中 SQL Server IaaS 代理扩展的状态](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

也可以使用 **Get-AzVMSqlServerExtension** Azure PowerShell cmdlet：

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

上一个命令确认已安装代理并提供常规状态信息。 还可使用以下命令获取有关自动备份和修补的特定状态信息：

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>限制

SQL IaaS 代理扩展仅支持： 

- 通过 Azure 资源管理器部署的 SQL Server VM。 不支持通过经典模型部署的 SQL Server VM。 
- 部署到公有云或 Azure 政府云的 SQL Server VM。 不支持部署到其他私有云或政府云。 



## <a name="next-steps"></a>后续步骤

若要在 Azure Vm 上安装 SQL Server IaaS 扩展以 SQL Server，请批量查看 [自动安装](sql-agent-extension-automatic-registration-all-vms.md)、 [单个 vm](sql-agent-extension-manually-register-single-vm.md)或 [vm](sql-agent-extension-manually-register-vms-bulk.md)的文章。

有关在 Azure 虚拟机中运行 SQL Server 的详细信息，请参阅[什么是 Azure 虚拟机中的 SQL Server？](sql-server-on-azure-vm-iaas-what-is-overview.md)。

若要了解详细信息，请参阅 [常见问题解答](frequently-asked-questions-faq.md)。 
