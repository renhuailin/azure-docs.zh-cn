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
ms.openlocfilehash: e8268630b2c108dc95ded059ce41866a14fadd0e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359245"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>通过 SQL Server IaaS 代理扩展自动进行管理
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 代理扩展 (SqlIaasExtension) 在 Azure 虚拟机上的 SQL Server 上运行 (Vm) 来自动执行管理任务和管理任务。 

本文提供扩展的概述。 若要在 Azure Vm 上安装 SQL Server IaaS 扩展以 SQL Server，请批量查看 [自动安装](sql-agent-extension-automatic-registration-all-vms.md)、 [单个 vm](sql-agent-extension-manually-register-single-vm.md)或 [vm](sql-agent-extension-manually-register-vms-bulk.md)的文章。 

## <a name="overview"></a>概述

SQL Server IaaS 代理扩展为 Azure Vm 上的 SQL Server 提供了很多好处： 

- **功能优势**：扩展功能使你能够更好地利用一些自动化功能权益，例如门户管理、许可证灵活性、自动备份、自动修补等。 有关详细信息，请参阅本文后面的 [功能权益](#feature-benefits) 。 

- **符合性**：该扩展提供了一种简化的方法，使您能够在产品条款中指定 Azure 混合权益已启用。 此过程无需为每个资源管理许可注册表单。  

- **免费**：所有三个可管理性模式中的扩展都是完全免费的。 扩展或更改管理模式不会产生额外的费用。 

- **简化的许可证管理**：扩展可简化 SQL Server 许可证管理，并使你能够使用 [Azure 门户](manage-sql-vm-portal.md)、Azure CLI 或 PowerShell，使用 Azure 混合权益来快速确定 SQL Server vm： 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---


> [!IMPORTANT]
> SQL IaaS 代理扩展收集数据，以便在 Azure 虚拟机中使用 SQL Server 时，为客户提供可选的权益。 在未经客户的事先同意的情况下，Microsoft 不会将此数据用于许可审核。 有关详细信息，请参阅 [SQL Server 隐私补充](/sql/sql-server/sql-server-privacy#non-personal-data) 。


## <a name="feature-benefits"></a>功能优势 

SQL Server IaaS 代理扩展将为管理 SQL Server VM 提供多种功能优势。 

下表详细说明了这些优点： 


| 功能 | 描述 |
| --- | --- |
| **门户管理** | 在 [门户](manage-sql-vm-portal.md)中取消锁定管理，以便你可以在一个位置查看所有 SQL Server vm，以便可以直接从门户中启用和禁用 SQL 特定功能。 
| **自动备份** |对 VM 中的 SQL Server 默认实例或已[正确安装](frequently-asked-questions-faq.md#administration)的命名实例自动执行所有数据库的备份计划。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动备份 (Resource Manager)](automated-backup-sql-2014.md)。 |
| **自动修补** |配置维护时段，在这种情况下，可能会发生重要的 Windows 和 SQL Server VM 的安全更新，因此可以避免在工作负荷高峰时间进行更新。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动修补 (Resource Manager)](automated-patching.md)。 |
| **Azure 密钥保管库集成** |可让你在 SQL Server VM 上自动安装和配置 Azure 密钥保管库。 有关详细信息，请参阅 [为 Azure 虚拟机上的 SQL Server 配置 Azure 密钥保管库集成 (Resource Manager)](azure-key-vault-integration-configure.md)。 |
| **灵活的许可** | [无缝地](licensing-model-azure-hybrid-benefit-ahb-change.md)从自带许可证 (（也称为 Azure 混合权益) ）过渡到 "即用即付" 许可模式，并再次返回。 | 
| **灵活版本/版本** | 如果决定更改[SQL Server 的](change-sql-server-edition.md)[版本](change-sql-server-version.md)，则可以更新 Azure 门户中的元数据，而无需重新部署整个 SQL Server VM。  | 


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

如果是虚拟机上唯一可用的 SQL Server 实例，则 SQL Server IaaS 代理扩展将与 SQL Server 的命名实例一起工作。 无法在具有多个 SQL Server 实例的 Vm 上安装扩展。 

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


## <a name="frequently-asked-questions"></a>常见问题 

是否应从 Azure 市场中的 SQL Server 映像预配的 SQL Server VM？

不是。 Microsoft 会自动注册从 Azure 市场中的 SQL Server 映像预配的 VM。 仅当 *未* 从 Azure Marketplace 中的 SQL Server 映像预配 VM，并且 SQL Server 自安装时，才需要向扩展注册。

**SQL IaaS 代理扩展是否适用于所有客户？** 

是。 如果客户不使用 Azure Marketplace 中的 SQL Server 映像，而是自行安装的 SQL Server，或者如果他们提供自定义 VHD，则他们应该向其注册其 SQL Server Vm。 所有订阅类型所拥有的 Vm (直接、企业协议和云解决方案提供商) 可以注册 SQL IaaS 代理扩展。

**在 SQL IaaS 代理扩展中注册时，默认的管理模式是什么？**

在 SQL IaaS 代理扩展中注册时，默认管理模式为 *轻型*。 如果在向扩展注册时未设置 "SQL Server 管理" 属性，则该模式将设置为轻型，你的 SQL Server 服务将不会重新启动。 建议先向轻型模式下的 SQL IaaS 代理扩展注册，然后在维护时段内升级到完整。 同样，使用 [自动注册功能](sql-agent-extension-automatic-registration-all-vms.md)时，默认管理也是轻型。

**向 SQL IaaS 代理扩展注册需要满足哪些先决条件？**

除了在虚拟机上安装 SQL Server 之外，不需要向 SQL IaaS 代理扩展注册。 请注意，如果在完整模式下安装 SQL IaaS 代理扩展，则 SQL Server 服务将重新启动，因此建议在维护时段执行此操作。

**注册到 SQL IaaS 代理扩展会在我的 VM 上安装代理吗？**

是的，使用完全可管理性模式下的 SQL IaaS 代理扩展注册会将代理安装到 VM。 以轻型或 NoAgent 模式注册不会。 

向轻型模式下的 SQL IaaS 代理扩展注册只会将 SQL IaaS 代理扩展 *二进制文件* 复制到 VM，而不会安装代理。 然后，将使用这些二进制文件在管理模式升级到完整版时安装代理。


**是否向 VM 上的 SQL IaaS 代理扩展重新启动 SQL Server？**

这取决于在注册期间指定的模式。 如果指定了轻型模式或 NoAgent 模式，则 SQL Server 服务将不会重新启动。 但是，将管理模式指定为 full 将导致 SQL Server 服务重新启动。 自动注册功能将以轻型模式注册 SQL Server Vm，除非 Windows Server 版本为2008，在这种情况下，将在 NoAgent 模式下注册 SQL Server VM。 

**向 SQL IaaS 代理扩展注册时，轻型模式和 NoAgent 管理模式之间有何区别？** 

在 Windows Server 2008 上，NoAgent 管理模式是 SQL Server 2008 和 SQL Server 2008 R2 的唯一可用管理模式。 对于所有更高版本的 Windows Server，两种可用的可管理性模式为轻型和 full。 

NoAgent 模式需要客户设置 SQL Server 版本和版本属性。 轻型模式会查询 VM 以查找 SQL Server 实例的版本。

**能否在不指定 SQL Server 许可证类型的情况下注册到 SQL IaaS 代理扩展？**

错误。 当你使用 SQL IaaS 代理扩展注册时，SQL Server 许可证类型不是可选属性。 在所有可管理性模式下注册 SQL IaaS 代理扩展时，必须将 SQL Server 许可证类型设置为即用即付或 Azure 混合权益 (NoAgent、轻型和完整) 。 如果安装了任何免费版本的 SQL Server，例如开发人员或评估版，则必须注册 "即用即付" 许可。 Azure 混合权益仅适用于付费版本的 SQL Server 例如 Enterprise edition 和 Standard edition。

**是否可以将 SQL Server IaaS 扩展从 NoAgent 模式升级到完整模式？**

错误。 在 NoAgent 模式下，不能将可管理性模式升级为 full 或轻型。 这是 Windows Server 2008 的技术限制。 需要首先将 OS 升级到 Windows Server 2008 R2 或更高版本，然后才能升级到完整管理模式。 

是否可以将 SQL Server IaaS 扩展从轻型模式升级到完整模式？

是的。 通过 Azure PowerShell 或 Azure 门户，支持从轻型升级到完整的可管理性模式。 这会触发 SQL Server 服务的重新启动。

**是否可以将 SQL Server IaaS 扩展从完整模式降级到 NoAgent 或轻型管理模式？**

不是。 不支持降级 SQL Server IaaS 扩展可管理性模式。 可管理性模式无法从完整模式降级为轻型模式或 NoAgent 模式，并且无法从轻型模式降级到 NoAgent 模式。 

若要从完全可管理性更改可管理性 [模式，请](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) 删除 Sql iaas 代理扩展中的 SQL Server VM，方法是删除 sql 虚拟机 _资源_ ，然后在不同的管理模式下重新向 sql iaas 代理扩展注册 SQL Server VM。

**是否可以从 Azure 门户中的 SQL IaaS 代理扩展注册？**

错误。 使用 SQL IaaS 代理扩展注册在 Azure 门户中不可用。 仅 Azure CLI 或 Azure PowerShell 支持注册到 SQL IaaS 代理扩展。 

**在安装 SQL Server 之前，是否可以使用 SQL IaaS 代理扩展注册 VM？**

错误。 VM 必须至少有一个 SQL Server (数据库引擎) 实例才能成功注册到 SQL IaaS 代理扩展。 如果 VM 上没有 SQL Server 实例，则新的 Microsoft.SqlVirtualMachine 资源会处于失败状态。

**如果有多个 SQL Server 实例，能否使用 SQL IaaS 代理扩展注册 VM？**

是。 SQL IaaS 代理扩展将只注册一个 SQL Server (数据库引擎) 实例。 SQL IaaS 代理扩展将在多个实例的情况下注册默认 SQL Server 实例。 如果没有默认实例，则仅支持在轻型模式下注册。 若要从轻型升级到完整可管理性模式，应存在默认 SQL Server 实例，或者 VM 应只有一个命名 SQL Server 实例。

**是否可以使用 SQL IaaS 代理扩展注册 SQL Server 故障转移群集实例？**

是。 在 Azure VM 上 SQL Server 故障转移群集实例可以使用轻型模式下的 SQL IaaS 代理扩展进行注册。 但是，SQL Server 故障转移群集实例无法升级到完整可管理性模式。

**如果配置了 Always On 可用性组，能否使用 SQL IaaS 代理扩展注册 VM？**

是。 如果正在加入 Always On 可用性组配置，则不会限制使用 SQL IaaS 代理扩展在 Azure VM 上注册 SQL Server 实例。

**在 SQL IaaS 代理扩展或升级到完全可管理性模式时，注册的成本是多少？**

无。 向 SQL IaaS 代理扩展注册或使用三个可管理性模式中的任何一种，都不会产生任何费用。 用扩展管理 SQL Server VM 完全免费。 

使用不同的可管理性模式会产生哪些性能影响？

使用无代理和轻型可管理性模式时，不会产生任何影响 。 从安装到 OS 的两个服务使用完整可管理性模式时，影响最小。 可以通过任务管理器监视这些服务，并可以在内置 Windows 服务控制台中查看。 

这两个服务名称为：
- `SqlIaaSExtensionQuery`（显示名称 - `Microsoft SQL Server IaaS Query Service`）
- `SQLIaaSExtension`（显示名称 - `Microsoft SQL Server IaaS Agent`）

**删除扩展如何实现？**

通过从 SQL IaaS 代理扩展中 [注销](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server VM 来删除扩展。 

## <a name="next-steps"></a>后续步骤

若要在 Azure Vm 上安装 SQL Server IaaS 扩展以 SQL Server，请批量查看 [自动安装](sql-agent-extension-automatic-registration-all-vms.md)、 [单个 vm](sql-agent-extension-manually-register-single-vm.md)或 [vm](sql-agent-extension-manually-register-vms-bulk.md)的文章。

有关在 Azure 虚拟机中运行 SQL Server 的详细信息，请参阅[什么是 Azure 虚拟机中的 SQL Server？](sql-server-on-azure-vm-iaas-what-is-overview.md)。
