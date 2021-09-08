---
title: 自动注册到 SQL IaaS 代理扩展
description: 了解如何使用 Azure 门户启用自动注册功能，以向 SQL IaaS 代理扩展自动注册所有过去和未来的 SQL Server VM。
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 9/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 894d16e1a72243bee9aff1fff2778ec75c2af6ec
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227146"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>自动注册到 SQL IaaS 代理扩展
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 门户中启用自动注册功能，以轻型模式向 [SQL IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)自动注册 Azure 虚拟机 (VM) 中所有当前和未来 SQL Server。 

本文将指导你启用自动注册功能。 或者，也可以向 SQL IaaS 代理扩展[注册单个 VM](sql-agent-extension-manually-register-single-vm.md) 或[批量注册 VM](sql-agent-extension-manually-register-vms-bulk.md)。 

> [!NOTE]
> 从 2021 年 9 月开始，在完全模式下注册 SQL IaaS 扩展不再需要重启 SQL Server 服务。 

## <a name="overview"></a>概述

向 [SQL IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)注册 SQL Server VM，以释放完整功能集权益。 

启用自动注册后，作业将每天运行，检测订阅中所有未注册 VM 上是否安装了 SQL Server。 完成此操作的方法是：将 SQL IaaS 代理扩展二进制文件复制到 VM，然后运行一个用于检查 SQL Server 注册表配置单元的一次性实用程序。 如果检测到 SQL Server 配置单元，则会以轻型模式向扩展注册虚拟机。 如果注册表中不存在任何 SQL Server 配置单元，则将删除二进制文件。 自动注册可能需要多达 4 天的时间来检测新创建的 SQL Server VM。

为订阅启用自动注册后，将以轻型模式向 SQL IaaS 代理扩展注册已安装 SQL Server 的所有当前和未来 VM，且无需停机和重启 SQL Server 服务。 你仍需要[手动升级到完全可管理性模式](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)，才能利用完整功能集。 许可证类型自动默认为 VM 映像的类型。 如果对 VM 使用即用即付映像，则许可证类型将为 `PAYG`，否则许可证类型将默认为 `AHUB`。 

> [!IMPORTANT]
> SQL IaaS 代理扩展会收集数据，目的只是当客户在 Azure 虚拟机中使用 SQL Server 时为他们提供可选权益。 在未经得客户事先同意的情况下，Microsoft 不会将此数据用于许可审核。 有关详细信息，请参阅 [SQL Server 隐私补充条款](/sql/sql-server/sql-server-privacy#non-personal-data)。

## <a name="prerequisites"></a>先决条件

若要向扩展注册 SQL Server VM，需要提供： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)以及至少一个[参与者角色](../../../role-based-access-control/built-in-roles.md#all)权限。
- 一个 Azure 资源模型 [Windows Server 2008 R2（或更高版本）虚拟机](../../../virtual-machines/windows/quick-create-portal.md)，其 [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) 已部署到公有云或 Azure 政府云。 不支持 Windows Server 2008。 


## <a name="enable"></a>启用

若要在 Azure 门户中启用 SQL Server VM 自动注册，请按照以下步骤操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到 [SQL 虚拟机](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines)资源页。 
1. 选择“自动 SQL Server VM 注册”，以打开“自动注册”页。 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="选择“自动 SQL Server VM 注册”以打开“自动注册”页":::

1. 从下拉菜单中选择你的订阅。 
1. 通读条款，如果同意，请选择“我接受”。 
1. 选择“注册”以启用此功能，并向 SQL IaaS 代理扩展自动注册所有当前和未来 SQL Server VM。 这不会重启任何 VM 中的 SQL Server 服务。 

## <a name="disable"></a>禁用

使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps) 禁用自动注册功能。 禁用自动注册功能后，需要手动向 SQL IaaS 代理扩展注册添加到订阅的 SQL Server VM。 这不会注销已注册的现有 SQL Server VM。



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 禁用自动注册，请运行以下命令： 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 禁用自动注册，请运行以下命令： 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>针对多个订阅启用

可以使用 PowerShell 为多个 Azure 订阅启用自动注册功能。 

为此，请执行下列步骤：

1. 保存[此脚本](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/EnableBySubscription.ps1)。
1. 使用管理命令提示符或 PowerShell 窗口导航到保存脚本的位置。 
1. 连接到 Azure (`az login`)。
1. 执行此脚本，并传入 SubscriptionIds 作为参数，例如   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   例如： 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

注册失败的错误存储在 `RegistrationErrors.csv` 中，它与保存和执行 `.ps1` 脚本的位置位于相同的目录中。 

## <a name="next-steps"></a>后续步骤

将可管理性模式升级到[完全](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)，以利用 SQL IaaS 代理扩展所提供的完整功能集。 
