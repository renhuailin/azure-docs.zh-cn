---
title: 什么是 SQL Server IaaS 代理扩展？
description: 本文介绍 SQL Server IaaS 代理扩展如何帮助自动完成 Azure VM 上的 SQL Server 的特定管理任务。 此扩展的功能包括自动备份、自动修补、Azure 密钥保管库集成、许可管理、存储配置、所有 SQL Server VM 实例的集中管理，等等。
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
ms.openlocfilehash: 619c29b2c28c04e1cbf4d4dcda8fe3048234e7dd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752178"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>使用 SQL Server IaaS 代理扩展实现自动管理
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 代理扩展 (SqlIaasExtension) 在 Azure 虚拟机 (VM) 上的 SQL Server 中运行，可以自动执行管理任务。 

本文将提供该扩展的概述。 若要将 SQL Server IaaS 扩展安装到 Azure VM 上的 SQL Server，请参阅有关[自动安装](sql-agent-extension-automatic-registration-all-vms.md)、[单一 VM](sql-agent-extension-manually-register-single-vm.md) 或[批量 VM](sql-agent-extension-manually-register-vms-bulk.md) 的文章。 

## <a name="overview"></a>概述

SQL Server IaaS 代理扩展允许与 Azure 门户集成，并可以根据管理模式为 Azure VM 上的 SQL Server 解锁多种功能权益： 

- **功能权益**：该扩展可以解锁许多自动化功能权益，例如门户管理、许可证灵活性、自动备份、自动修补等。 有关详细信息，请参阅本文稍后的[功能权益](#feature-benefits)。 

- **合规性**：该扩展提供了一种简化的方法来满足以下要求：根据产品条款中的规定向 Microsoft 通知 Azure 混合权益已启用。 此过程无需为每个资源管理许可注册表单。  

- **免费**：该扩展的所有三种可管理性模式都是完全免费的。 使用该扩展或者更改管理模式不会产生相关的额外费用。 

- **简化的许可证管理**：该扩展简化了 SQL Server 许可证管理，可让你使用 [Azure 门户](manage-sql-vm-portal.md)、PowerShell 或 Azure CLI 快速识别启用了 Azure 混合权益的 SQL Server VM： 

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
> SQL IaaS 代理扩展会收集数据，目的只是当客户在 Azure 虚拟机中使用 SQL Server 时为他们提供可选权益。 在未经得客户事先同意的情况下，Microsoft 不会将此数据用于许可审核。 有关详细信息，请参阅 [SQL Server 隐私补充条款](/sql/sql-server/sql-server-privacy#non-personal-data)。


## <a name="feature-benefits"></a>功能权益 

SQL Server IaaS 代理扩展可以解锁许多功能权益用于管理 SQL Server VM。 

下表详细介绍了这些权益： 


| 功能 | 说明 |
| --- | --- |
| **门户管理** | 解锁[门户中的管理](manage-sql-vm-portal.md)，使你可以在一个位置查看所有 SQL Server VM，并可直接在门户中启用和禁用 SQL 特定的功能。 <br/> 管理模式：轻型和完全|  
| **自动备份** |对 VM 中的 SQL Server 默认实例或已[正确安装](/azure/azure-sql/virtual-machines/windows/frequently-asked-questions-faq#administration)的命名实例自动执行所有数据库的备份计划。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动备份 (Resource Manager)](automated-backup-sql-2014.md)。 <br/> 管理模式：完全|
| **自动修补** |配置维护时段，可在此时段对 VM 进行重要的 Windows 和 SQL Server 安全更新，从而可以避免在工作负载的高峰期进行更新。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动修补 (Resource Manager)](automated-patching.md)。 <br/> 管理模式：完全|
| **Azure 密钥保管库集成** |可让你在 SQL Server VM 上自动安装和配置 Azure 密钥保管库。 有关详细信息，请参阅 [为 Azure 虚拟机上的 SQL Server 配置 Azure 密钥保管库集成 (Resource Manager)](azure-key-vault-integration-configure.md)。 <br/> 管理模式：完全|
| **在门户中查看磁盘使用率** | 可让你在 Azure 门户中查看 SQL 数据文件的磁盘使用率的图形表示形式。  <br/> 管理模式：完全 | 
| **灵活许可** | 在自带许可（也称为 Azure 混合权益）与即用即付许可模型之间来回[无缝转换](licensing-model-azure-hybrid-benefit-ahb-change.md)，从而节省成本。 <br/> 管理模式：轻型和完全| 
| **灵活的发行版本/产品版本** | 如果你决定更改 SQL Server 的[发行版本](change-sql-server-version.md)或[产品版本](change-sql-server-edition.md)，可以在 Azure 门户中更新元数据，而无需重新部署整个 SQL Server VM。  <br/> 管理模式：轻型和完全| 
| **安全中心门户集成** | 如果已启用 [Azure Defender for SQL](../../../security-center/defender-for-sql-usage.md)，则可直接在 Azure 门户的 [SQL 虚拟机](manage-sql-vm-portal.md)资源中查看安全中心建议。 有关详细信息，请参阅[安全最佳做法](security-considerations-best-practices.md)。  <br/> 管理模式：轻型和完全| 


## <a name="management-modes"></a>管理模式

可以选择以三种管理模式注册 SQL IaaS 扩展： 

- **轻型** 模式将扩展二进制文件复制到 VM，但不安装代理，也不重启 SQL Server 服务。 轻型模式仅支持更改 SQL Server 的许可证类型和产品版本，提供有限的门户管理功能。 对于具有多个实例的 SQL Server VM 或参与故障转移群集实例 (FCI) 的 SQL Server VM，请使用此选项。 使用[自动注册](sql-agent-extension-automatic-registration-all-vms.md)功能时，或者在手动注册期间未指定管理类型时，轻型模式是默认的管理模式。 使用轻型模式时，不会对内存或 CPU 产生任何影响，并且没有关联成本。 建议先在轻型模式下注册 SQL Server VM，然后在计划性维护时段内升级到完整模式。 

- **完全** 模式将 SQL IaaS 代理安装到 VM 以提供所有功能，但需要重启 SQL Server 服务并拥有系统管理员权限。 使用它管理具有单个实例的 SQL Server VM。 完整模式安装两个 Windows 服务，它们对内存和 CPU 的影响最小 - 可通过任务管理器进行监视。 使用完整可管理性模式时没有关联成本。 

- 无代理模式专用于在 Windows Server 2008 上安装的 SQL Server 2008 和 SQL Server 2008 R2。 使用无代理模式时，不会对内存或 CPU 产生任何影响。 使用无代理可管理性模式不会产生任何相关费用，SQL Server 不会重启，也不会在 VM 上安装代理。 

可以使用 Azure PowerShell 查看 SQL Server IaaS 代理的当前模式： 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>安装

将 SQL Server VM 注册到 SQL Server IaaS 代理扩展，以在订阅（该资源独立于虚拟机资源）中创建 **SQL 虚拟机** 资源。  从扩展中取消注册 SQL Server VM 会删除 **SQL 虚拟机** 资源，但不会删除实际虚拟机。

通过 Azure 门户部署 SQL Server VM Azure 市场映像会自动将 SQL Server VM 注册到扩展。 但是，如果选择在 Azure 虚拟机上自行安装 SQL Server，或通过自定义 VHD 预配 Azure 虚拟机，则必须将 SQL Server VM 注册到 SQL IaaS 扩展才能解锁功能权益。 

在轻型模式下注册扩展会复制二进制文件，但不会将代理安装到 VM。 将扩展升级为完全管理模式时，会将代理安装到 VM。 

可通过三种方式注册到扩展： 
- [对订阅中的所有当前和未来 VM 自动注册](sql-agent-extension-automatic-registration-all-vms.md)
- [对单个 VM 手动注册](sql-agent-extension-manually-register-single-vm.md)
- [对多个 VM 手动批量注册](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>命名实例支持

如果 SQL Server 的某个命名实例是虚拟机上唯一可用的 SQL Server 实例，则 SQL Server IaaS 代理扩展可与该实例配合工作。 如果具有多个命名 SQL Server 实例的 VM 上没有默认实例，则该扩展无法安装到此 VM 上。 

若要使用 SQL Server 的命名实例，请部署一个 Azure 虚拟机，在其上安装单个命名 SQL Server 实例，然后将其注册到 [SQL IaaS 扩展](sql-agent-extension-manually-register-single-vm.md)。

或者，若要将命名实例与 Azure 市场 SQL Server 映像配合使用，请执行以下步骤： 

   1. 从 Azure 市场部署 SQL Server VM。 
   1. 从 SQL IaaS 代理扩展中[取消注册](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server VM。 
   1. 完全卸载 SQL Server VM 中的 SQL Server。
   1. 使用 SQL Server VM 中的命名实例安装 SQL Server。 
   1. [将 VM 注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md#register-with-extension)。 

## <a name="verify-status-of-extension"></a>验证扩展的状态

使用 Azure 门户或 Azure PowerShell 来检查扩展的状态。 

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中验证扩展是否已安装。 

在虚拟机窗格中选择“所有设置”，然后选择“扩展”。  应看到列出“SqlIaasExtension”扩展。

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


## <a name="in-region-data-residency"></a>区域内数据驻留
Azure SQL 虚拟机和 SQL IaaS 代理扩展不会将客户数据移到或存储到它们已部署到的区域之外。

## <a name="next-steps"></a>后续步骤

若要将 SQL Server IaaS 扩展安装到 Azure VM 上的 SQL Server，请参阅有关[自动安装](sql-agent-extension-automatic-registration-all-vms.md)、[单一 VM](sql-agent-extension-manually-register-single-vm.md) 或[批量 VM](sql-agent-extension-manually-register-vms-bulk.md) 的文章。

有关在 Azure 虚拟机中运行 SQL Server 的详细信息，请参阅[什么是 Azure 虚拟机中的 SQL Server？](sql-server-on-azure-vm-iaas-what-is-overview.md)。

有关详细信息，请参阅[常见问题解答](frequently-asked-questions-faq.yml)。