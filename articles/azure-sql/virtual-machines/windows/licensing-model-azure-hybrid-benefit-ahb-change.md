---
title: 在 Azure 中更改 SQL VM 的许可模式
description: 了解如何使用 Azure 混合权益在 Azure 中将 SQL Server VM 的许可从即用即付许可切换为自带许可。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6d840893de2c516137e54d015123fa9249b93d54
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434602"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>在 Azure 中更改 SQL 虚拟机的许可模式
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


本文介绍如何使用 [SQL IaaS 代理扩展](./sql-server-iaas-agent-extension-automate-management.md)在 Azure 中更改 SQL Server 虚拟机 (VM) 的许可证模型。

## <a name="overview"></a>概述

托管 SQL Server 的 Azure VM 有三种许可证模式：即用即付、Azure 混合权益 (AHB) 和高可用性/灾难恢复 (HA/DR)。 可以使用 Azure 门户、Azure CLI 或 PowerShell 来修改 SQL Server VM 的许可模式。 

- “即用即付”模型意味着 Azure VM 的每秒运行成本包括 SQL Server 许可的费用。
- 借助 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)，你可以将自己的 SQL Server 许可与运行 SQL Server 的 VM 一起使用。 
- “HA/DR”许可证类型用于 Azure 中的[免费 HA/DR 副本](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)。 

Azure 混合权益允许在 Azure 虚拟机上使用带有软件保障（“合格许可”）的 SQL Server 许可。 使用 Azure 混合权益，客户无需支付在 VM 上使用 SQL Server 许可的费用， 但仍须支付基础云计算（即，基准费率）、存储和备份的费用， 还必须支付与使用服务（如果适用）相关的 I/O 的费用。

根据 Microsoft [产品条款](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)：“在 Azure 上配置工作负载时，客户必须表明他们是在适用于 SQL Server 的 Azure 混合权益下使用 Azure SQL 数据库（托管实例、弹性池和单一数据库）、Azure 数据工厂、SQL Server Integration Services 或 SQL Server 虚拟机。”

要表明是在 Azure VM 上使用适用于 SQL Server 的 Azure 混合权益并符合标准，有以下三个选项可供选择：

- 使用 Azure 市场中的自带许可 SQL Server 映像预配虚拟机。 此选项仅适用于拥有企业协议的客户。
- 使用 Azure 市场中的即用即付 SQL Server 映像预配虚拟机，并激活 Azure 混合权益。
- 在 Azure VM 上自行安装 SQL Server，手动[注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)，并激活 Azure 混合权益。

可以在预配 VM 时或之后的任何时间配置 SQL Server 的许可类型。 在许可模式之间进行切换不会导致停机，不会重启 VM 或 SQL Server 服务，不会增加费用，并且会立即生效。 事实上，激活 Azure 混合权益可降低成本。

## <a name="prerequisites"></a>先决条件

更改 SQL Server VM 的许可模式具有以下要求： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 已注册 [SQL IaaS 代理扩展](./sql-server-iaas-agent-extension-automate-management.md)的 [SQL Server VM](./create-sql-vm-portal.md)。
- [软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)是利用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)的一项要求。 


## <a name="change-license-model"></a>更改许可模型

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

可以直接从门户修改许可模式： 

1. 打开 [Azure 门户](https://portal.azure.com)并从 SQL Server VM 打开 [SQL 虚拟机资源](manage-sql-vm-portal.md#access-the-resource)。 
1. 选择“设置”下的“配置”。 
1. 选择“Azure 混合权益”选项，然后选中该复选框以确认你拥有具有软件保障的 SQL Server 许可。 
1. 选择“配置”页底部的“应用”。 

![门户中的 Azure 混合权益](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以通过 Azure CLI 更改许可模式。  

为“许可证模型”指定以下值：
- `AHUB` 表示 Azure 混合权益
- `PAYG` 表示即用即付
- `DR` 表示激活免费的 HA/DR 副本


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

可以使用 PowerShell 更改许可模式。

为“许可证模型”指定以下值：
- `AHUB` 表示 Azure 混合权益
- `PAYG` 表示即用即付
- `DR` 表示激活免费的 HA/DR 副本


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>备注

- Azure 云解决方案提供商 (CSP) 客户可以通过先部署即用即付 VM，然后将其转换为自带许可（如果他们具有有效的软件保障）来使用 Azure 混合权益。
- 如果删除 SQL 虚拟机资源，则会回退到映像的硬编码许可证设置。 
- 更改许可证模型是 SQL IaaS 代理扩展的一项功能。 通过 Azure 门户部署 Azure 市场映像会自动将 SQL Server VM 注册到代理扩展中。 但自行安装 SQL Server 的客户需要手动[注册其 SQL Server VM](sql-agent-extension-manually-register-single-vm.md)。 
- 若要将 SQL Server VM 添加到可用性集，则需要重新创建 VM。 因此，添加到可用性集的任何 VM 都将恢复为默认的即用即付许可类型。 需要重新启用 Azure 混合权益。 


## <a name="limitations"></a>限制

更改许可模式的步骤如下：
   - 仅适用于具有[软件保障](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)的客户。
   - 仅支持 SQL Server 的 Standard 版和 Enterprise 版。 不支持更改 Express、Web 和 Developer 版本的许可。 
   - 仅支持通过 Azure 资源管理器模型部署的虚拟机。 不支持通过经典模型部署的虚拟机。 
   - 仅适用于公共云或 Azure 政府云。 

> [!Note]
> 只有具有软件保障或订阅许可证的 SQL Server 基于内核的许可才有资格使用 Azure 混合权益。 如果对 SQL Server 使用服务器 + CAL 许可且具有软件保障，可以使用自带许可访问 Azure SQL Server 虚拟机映像以利用这些服务器的许可移动性，但不能利用 Azure 混合权益的其他功能。 

## <a name="known-errors"></a>已知错误

请查看常见错误及其解决方法。 

找不到资源组“\<resource-group>”下的资源“Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>”。

尝试在尚未注册到 SQL Server IaaS 代理扩展的 SQL Server VM 上更改许可模式时，将发生此错误：

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

需要先将订阅注册到资源提供程序，然后[将 SQL Server VM 注册到 SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)。 



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.yml)
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](../../database/doc-changes-updates-release-notes.md)
* [SQL IaaS 代理扩展概述](./sql-server-iaas-agent-extension-automate-management.md)
