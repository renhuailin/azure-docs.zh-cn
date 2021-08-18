---
title: 监视管理操作
titleSuffix: Azure SQL Managed Instance
description: 了解监视 Azure SQL 托管实例管理操作的各种方法。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 32b27eefb3cf8fe46b932f6e0e39274e67cadb7f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751227"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>监视 Azure SQL 托管实例管理操作
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例提供对[管理操作](management-operations-overview.md)的监视，你可以使用这些操作来部署新的托管实例、更新实例属性，或删除不再需要的实例。 

## <a name="overview"></a>概述

所有管理操作可分类为：

- 部署实例（创建新实例）。
- 实例更新（更改实例属性，例如 vCore 数或预留存储）。
- 删除实例。

大多数管理操作都是[长时间运行的操作](management-operations-overview.md#duration)。 因此，需要监视其状态或跟踪操作步骤的进度。 

可以通过多种方式来监视托管实例管理操作：

- [资源组部署](../../azure-resource-manager/templates/deployment-history.md)
- [活动日志](../../azure-monitor/essentials/activity-log.md)
- [托管实例操作 API](#managed-instance-operations-api)


下表比较了管理操作监视选项： 

| 选项 | 保留 | 支持取消 | 创建 | 更新 | 删除 | 取消 | 步骤 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 资源组部署 | 无限<sup>1</sup> | 否<sup>2</sup> | 可见 | 可见 | 不可见 | 可见 | 不可见 |
| 活动日志 | 90 天 | 否 | 可见 | 可见 | 可见 | 可见 |  不可见 |
| 托管实例操作 API | 24 小时 | [是](management-operations-cancel.md) | 可见 | 可见 | 可见 | 可见 | 可见 |
|  |  |  |  |  |  |  | |

<sup>1</sup> 一个资源组的部署历史记录限含 800 个部署。

<sup>2</sup> 资源组部署支持取消操作。 但出于取消逻辑，只有计划在执行取消操作后部署的操作才会被取消。 取消资源组部署时，不会取消正在进行的部署。 由于托管实例部署包含一个长期步骤（从 Azure 资源管理器的角度来看），因此取消资源组部署不会取消托管实例部署，并且该操作将完成。 

## <a name="managed-instance-operations-api"></a>托管实例操作 API

管理操作 API 是为监视操作而专门设计的。 监视托管实例操作可以提供对操作参数和操作步骤的见解，以及[取消特定操作](management-operations-cancel.md)。 除操作详细信息和 cancel 命令之外，此 API 还可在具有多资源部署的自动化脚本中使用；根据进度步骤，你可以启动一些相关的资源部署。

这些 API 如下： 

| 命令 | 描述 |
| --- | --- |
|[托管实例操作 - 获取](/rest/api/sql/managedinstanceoperations/get)|获取托管实例上的管理操作。|
|[托管实例操作 - 取消](/rest/api/sql/managedinstanceoperations/cancel)|取消托管实例上的异步操作。|
|[托管实例操作 - 按托管实例列出](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|获取在托管实例上执行的操作的列表。|

> [!NOTE]
> 使用 API 版本 2020-02-02 查看操作列表中的托管实例创建操作。 这是 Azure 门户以及最新的 PowerShell 和 Azure CLI 包中使用的默认版本。

## <a name="monitor-operations"></a>监视操作

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户中，使用托管实例“概述”页监视托管实例操作。 

例如，“创建操作”在“概述”页上的创建过程开始时可见 ： 

![托管实例创建过程](./media/management-operations-monitor/monitoring-create-operation.png)

选择“正在执行的操作”以打开“正在执行的操作”页，然后查看“创建”或“更新”操作   。 你也可以在此页中[取消](management-operations-cancel.md)操作。  

![托管实例操作详细信息](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> [可以取消](management-operations-cancel.md)使用 REST API 版本 2020-02-02 通过 Azure 门户、PowerShell、Azure CLI 或其他工具提交的创建操作。 用于提交创建操作且低于 2020-02-02 版本的 REST API 将启动实例部署，但该部署不会在操作 API 中列出，且无法取消。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Get-AzSqlInstanceOperation cmdlet 获取有关托管实例上的操作的信息。 你可以查看托管实例上的所有操作，也可以通过提供操作名称来查看特定操作。

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

有关详细的命令说明，请参阅 [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

az sql mi op list 获取在托管实例上执行的操作的列表。 如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

有关详细的命令说明，请参阅 [az sql mi op](/cli/azure/sql/mi/op)。

---

## <a name="next-steps"></a>后续步骤

- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](instance-create-quickstart.md)。
- 有关功能和比较列表，请参阅[常用 SQL 功能](../database/features-comparison.md)。
- 有关 VNet 配置的详细信息，请参阅 [SQL 托管实例 VNet 配置](connectivity-architecture-overview.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关使用 Azure 数据库迁移服务进行迁移的教程，请参阅[使用数据库迁移服务进行 SQL 托管实例迁移](../../dms/tutorial-sql-server-to-managed-instance.md)。
