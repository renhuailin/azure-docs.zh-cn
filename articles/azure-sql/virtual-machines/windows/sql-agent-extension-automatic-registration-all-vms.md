---
title: 自动注册 SQL IaaS 代理扩展
description: 了解如何启用自动注册功能，以使用 Azure 门户自动向 SQL IaaS 代理扩展注册过去和以后 SQL Server Vm。
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: 139852949a3744fd603cb197b2e27fa32679aae0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042413"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>自动注册 SQL IaaS 代理扩展
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 门户中启用自动注册功能，以在轻型模式下自动注册 Azure 虚拟机 (Vm [) 中的](sql-server-iaas-agent-extension-automate-management.md) 所有当前和将来 SQL Server。 

本文介绍如何启用自动注册功能。 或者，可以 [注册单个 VM](sql-agent-extension-manually-register-single-vm.md)，也可以使用 SQL IaaS 代理扩展 [批量注册 vm](sql-agent-extension-manually-register-vms-bulk.md) 。 

## <a name="overview"></a>概述

向 [SQL IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md) 注册您的 SQL Server VM，以释放一组完整的功能权益。 

启用自动注册后，作业将每天运行，检测是否在订阅中的所有未注册 Vm 上安装了 SQL Server。 完成此操作的方法是：将 SQL IaaS 代理扩展二进制文件复制到 VM，然后运行一个用于检查 SQL Server 注册表配置单元的一次性实用程序。 如果检测到 SQL Server 配置单元，则虚拟机将以轻型模式注册到扩展。 如果注册表中不存在任何 SQL Server 配置单元，则将删除二进制文件。

为订阅启用了自动注册后，所有当前和未来安装了 SQL Server 的 Vm 将在轻型模式下以轻型模式注册 SQL IaaS 代理扩展 **，且不会重新启动 SQL Server 服务**。 你仍需要 [手动升级到完整的可管理性模式](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) ，才能利用完整的功能集。 

> [!IMPORTANT]
> SQL IaaS 代理扩展收集数据，以便在 Azure 虚拟机中使用 SQL Server 时，为客户提供可选的权益。 在未经客户的事先同意的情况下，Microsoft 不会将此数据用于许可审核。 有关详细信息，请参阅 [SQL Server 隐私补充](/sql/sql-server/sql-server-privacy#non-personal-data) 。

## <a name="prerequisites"></a>先决条件

若要向扩展注册 SQL Server VM，需要： 

- 至少为[参与者角色](../../../role-based-access-control/built-in-roles.md#all)权限的[Azure 订阅](https://azure.microsoft.com/free/)。
- Azure 资源模型 [Windows Server 2008 R2 (或更高版本) 虚拟机](../../../virtual-machines/windows/quick-create-portal.md) ， [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) 部署到公共或 Azure 政府云。 不支持 Windows Server 2008。 


## <a name="enable"></a>启用

若要在 Azure 门户中启用 SQL Server Vm 的自动注册，请按照以下步骤操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到 [**SQL 虚拟机**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 资源页。 
1. 选择 " **自动 SQL Server VM 注册** " 打开 **自动注册** 页面。 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="选择 &quot;自动 SQL Server VM 注册&quot; 打开自动注册页面":::

1. 从下拉项中选择你的订阅。 
1. 通读条款，如果同意，请选择 " **我接受**"。 
1. 选择 " **注册** " 以启用此功能，并使用 SQL IaaS 代理扩展自动注册所有当前和未来 SQL Server vm。 这不会重新启动任何 Vm 上的 SQL Server 服务。 

## <a name="disable"></a>禁用

使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps) 来禁用自动注册功能。 当禁用自动注册功能时，需要在 SQL IaaS 代理扩展中手动注册添加到订阅 SQL Server Vm。 这不会注销已注册的现有 SQL Server Vm。



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

## <a name="enable-for-multiple-subscriptions"></a>启用多个订阅

可以使用 PowerShell 为多个 Azure 订阅启用自动注册功能。 

为此，请执行下列步骤：

1. 将 [此脚本](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) 保存到 `.ps1` 文件，如 `EnableBySubscription.ps1` 。 
1. 使用管理命令提示符或 PowerShell 窗口导航到保存脚本的位置。 
1. 连接到 Azure (`az login`) 。
1. 执行脚本，并传入 Subscriptionid 作为参数，如   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   例如： 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

失败的注册错误存储在与 `RegistrationErrors.csv` 从中保存和执行脚本的相同目录中 `.ps1` 。 

## <a name="next-steps"></a>后续步骤

将可管理性模式升级到 " [完全](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) "，以利用 SQL IaaS 代理扩展所提供的完整功能集。 
