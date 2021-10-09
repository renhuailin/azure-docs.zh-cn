---
title: 管理资源组 - Azure CLI
description: 使用 Azure CLI 通过 Azure 资源管理器管理资源组。 说明如何创建、列出和删除资源组。
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2088f1b4f201f149fc4d51bf608cb5868802d4d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731131"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>使用 Azure CLI 管理 Azure 资源管理器资源组

了解如何将 Azure CLI 与 [Azure 资源管理器](overview.md)配合使用来管理 Azure 资源组。 若要管理 Azure 资源，请参阅[使用 Azure CLI 管理 Azure 资源](manage-resources-cli.md)。

有关资源组管理的其他文章：

- [使用 Azure 门户管理 Azure 资源组](manage-resources-portal.md)
- [使用 Azure PowerShell 管理 Azure 资源组](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>什么是资源组

资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含想要作为组来管理的资源。 在决定如何将资源添加到资源组时，要根据对组织最为有利的原则。 通常可将共享相同生命周期的资源添加到同一资源组，以便将其作为一个组轻松部署、更新和删除。

” 资源组存储有关资源的元数据。 当指定资源组的位置时，也就指定了元数据的存储位置。 出于合规性原因，可能需要确保数据存储在某一特定区域。

## <a name="create-resource-groups"></a>创建资源组

若要创建资源组，请使用 [az group create](/cli/azure/group#az_group_create)。

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>列出资源组

列出订阅中的资源组，请使用 [az group list](/cli/azure/group#az_group_list)。

```azurecli-interactive
az group list
```

若要获取一个资源组，请使用 [az group show](/cli/azure/group#az_group_show)。

```azurecli-interactive
az group show --name exampleGroup
```

## <a name="delete-resource-groups"></a>删除资源组

若要删除资源组，请使用 [az group delete](/cli/azure/group#az_group_delete)。

```azurecli-interactive
az group delete --name exampleGroup
```

若要详细了解 Azure 资源管理器如何控制资源的删除，请参阅 [Azure 资源管理器资源组的删除](delete-resource-group.md)。

## <a name="deploy-resources"></a>部署资源

可以通过使用 Azure CLI 或者通过部署 Azure 资源管理器 (ARM) 模板或 Bicep 文件来部署 Azure 资源。

以下示例创建存储帐户。 为存储帐户提供的名称在 Azure 中必须是唯一的。

```azurecli-interactive
az storage account create --resource-group exampleGroup --name examplestore --location westus --sku Standard_LRS --kind StorageV2
```

若要部署 ARM 模板或 Bicep 文件，请使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)。

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-file storage.bicep
```

若要详细了解如何部署 ARM 模板，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../templates/deploy-cli.md)。

若要详细了解如何部署 Bicep 文件，请参阅[使用 Bicep 和 Azure CLI 部署资源](../bicep/deploy-cli.md)。

## <a name="lock-resource-groups"></a>锁定资源组

锁定可以防止组织中的其他用户意外删除或修改重要资源。

若要防止删除资源组及其资源，请使用 [az lock create](/cli/azure/lock#az_lock_create)。

```azurecli-interactive
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleGroup
```

若要获取某个资源组的锁，请使用 [az lock list](/cli/azure/lock#az_lock_list)。

```azurecli-interactive
az lock list --resource-group exampleGroup
```

若要删除锁，请使用 [az lock delete](/cli/azure/lock#az_lock_delete)

```azurecli-interactive
az lock delete --name exampleLock --resource-group exampleGroup
```

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](lock-resources.md)。

## <a name="tag-resource-groups"></a>标记资源组

可以将标记应用到资源组和资源，以按照逻辑组织资产。 有关信息，请参阅[使用标记组织 Azure 资源](tag-resources.md#azure-cli)。

## <a name="export-resource-groups-to-templates"></a>将资源组导出到模板

若要协助创建 ARM 模板，可以从现有资源导出模板。 有关详细信息，请参阅[使用 Azure CLI 导出模板](../templates/export-template-cli.md)。 

## <a name="manage-access-to-resource-groups"></a>管理对资源组的访问

若要管理资源组的访问权限，请使用 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。 有关详细信息，请参阅[使用 Azure CLI 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-cli.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅 [Azure 资源管理器概述](overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](../templates/syntax.md)。