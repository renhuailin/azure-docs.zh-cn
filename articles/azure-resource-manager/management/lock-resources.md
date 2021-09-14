---
title: 锁定资源以防止更改
description: 通过对所有用户和角色应用锁，来防止用户更新或删除 Azure 资源。
ms.topic: conceptual
ms.date: 07/01/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: fe644c740f5c96f9a7864850fe4760151e1c6f87
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449884"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>锁定资源，以防止意外更改

作为管理员，你可以锁定订阅、资源组或资源，以防止组织中的其他用户意外删除或修改关键资源。 该锁优先于用户可能拥有的任何权限。

可以将锁定级别设置为 **CanNotDelete** 或 **ReadOnly**。 在门户中，锁定分别称为 **删除** 和 **只读**。

- **CanNotDelete** 味着经授权的用户仍可读取和修改资源，但不能删除资源。
- **ReadOnly** 意味着经授权的用户可以读取资源，但不能删除或更新资源。 应用此锁类似于将所有经授权的用户限制于“读者”角色授予的权限。

与基于角色的访问控制不同，可以使用管理锁来对所有用户和角色应用限制。 若要了解如何为用户和角色设置权限，请参阅 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)。

## <a name="lock-inheritance"></a>锁继承

在父范围应用锁时，该范围内所有资源都会继承相同的锁。 即使是之后添加的资源也会从父作用域继承该锁。 继承中限制性最强的锁优先执行。

## <a name="understand-scope-of-locks"></a>了解锁的作用域

> [!NOTE]
> 需要了解的是，锁并不适用于所有类型的操作。 Azure 操作可以分为两个类别 - 控制平面和数据平面。 锁仅适用于控制平面操作。

控制平面操作是发送到 `https://management.azure.com` 的操作。 数据平面操作是发送到服务实例的操作，例如 `https://myaccount.blob.core.windows.net/`。 有关详细信息，请参阅 [Azure 控制平面和数据平面](control-plane-and-data-plane.md)。 要了解哪些操作使用控制平面 URL，请参阅 [Azure REST API](/rest/api/azure/)。

这种区别意味着锁可以防止对资源的更改，但不会限制资源执行自身功能的方式。  例如，SQL 数据库逻辑服务器上的 ReadOnly 锁会阻止你删除或修改该服务器。 它不会阻止你在该服务器上的数据库中创建、更新或删除数据。 会允许数据事务，因为这些操作不会发送到 `https://management.azure.com`。

下一部分将介绍有关控制平面操作和数据平面操作之间的差异的更多示例。

## <a name="considerations-before-applying-locks"></a>应用锁之前的注意事项

应用锁可能会导致意外结果，因为某些操作看似不会修改资源，但实际上需要执行被锁阻止的操作。 锁会阻止需要向 Azure 资源管理器 API 发出 POST 请求的任何操作。 被锁阻止的一些常见操作的示例包括：

- 存储帐户上的只读锁阻止用户列出帐户密钥。 Azure 存储[列出密钥](/rest/api/storagerp/storageaccounts/listkeys)操作通过 POST 请求进行处理，以保护对帐户密钥的访问，从而提供对存储帐户中数据的完全访问。 如果为存储帐户配置了只读锁，没有帐户密钥的用户就必须使用 Azure AD 凭据来访问 blob 或队列数据。 只读锁还阻止分配存储帐户或数据容器（blob 容器或队列）范围内的 Azure RBAC 角色。

- 存储帐户上的无法删除锁不会阻止删除或修改该帐户中的数据。 该锁类型仅能保护存储账号本身不被删除。 如果请求使用[数据平面操作](control-plane-and-data-plane.md#data-plane)，那么存储帐户上的锁不会保护该存储帐户中的 Blob、队列、表或文件数据。 但是，如果请求使用[控制平面操作](control-plane-and-data-plane.md#control-plane)，那么锁会保护这些功能。

  例如，如果请求使用[“文件共享 - 删除”](/rest/api/storagerp/file-shares/delete)（控制平面操作），删除将被拒绝。 如果请求使用[“删除共享”](/rest/api/storageservices/delete-share)（数据平面操作），删除将成功。 我们建议使用控制平面操作。

- 存储帐户上的只读锁不会阻止删除或修改该帐户中的数据。 此类型的锁仅保护存储帐户本身不被删除或修改，而不会保护存储帐户中的 blob、队列、表或文件数据。

- **应用服务** 资源上的只读锁将阻止 Visual Studio 服务器资源管理器显示资源的文件，因为该交互需要写入访问权限。

- 包含应用服务计划的资源组上的只读锁会阻止你[纵向或横向扩展计划](../../app-service/manage-scale-up.md) 。

- 包含 **虚拟机** 的 **资源组** 上的只读锁将阻止所有用户启动或重启该虚拟机。 这些操作需要 POST 请求。

- 资源组上的不可删除锁可防止 Azure 资源管理器[自动删除历史记录中的部署](../templates/deployment-history-deletions.md)。 如果历史记录中达到 800 个部署，则部署会失败。

- **Azure 备份服务** 创建的 **资源组** 上的“不能删除”锁会导致备份失败。 该服务最多支持 18 个还原点。 锁定后，备份服务无法清理还原点。 有关详细信息，请参阅[常见问题解答 - 备份 Azure VM](../../backup/backup-azure-vm-backup-faq.yml)。

- 资源组上的“无法删除”锁定可防止 Azure 机器学习自动缩放 [Azure 机器学习计算群集](../../machine-learning/concept-compute-target.md#azure-machine-learning-compute-managed)来删除未使用的节点 。

- **订阅** 上的只读锁会导致 **Azure 顾问** 无法正常运行。 顾问将无法存储其查询的结果。

- 应用程序网关上的只读锁将阻止获取应用程序网关的后端运行状况。 [该操作使用 POST](/rest/api/application-gateway/application-gateways/backend-health)（被只读锁阻止）。

- AKS 群集上的只读锁会阻止所有用户从 Azure 门户上的 AKS 群集左侧边栏选项卡的“Kubernetes 资源”部分访问任意群集资源。 这些操作需要 POST 请求进行身份验证。

## <a name="who-can-create-or-delete-locks"></a>谁可以创建或删除锁

若要创建或删除管理锁，必须有权执行 `Microsoft.Authorization/*` 或 `Microsoft.Authorization/locks/*` 操作。 在内置角色中，只有“所有者”和“用户访问管理员”有权执行这些操作。 

## <a name="managed-applications-and-locks"></a>托管应用程序和锁

某些 Azure 服务（如 Azure Databricks）使用[托管应用程序](../managed-applications/overview.md)来实现该服务。 在这种情况下，该服务将创建两个资源组。 一个资源组包含服务的概述，且未锁定。 另一个资源组包含服务的基础结构，且已锁定。

如果尝试删除基础结构资源组，将会收到一条错误消息，指出资源组已锁定。 如果尝试删除基础结构资源组的锁，将会收到一条错误消息，指出无法删除该锁，因为它由系统应用程序所拥有。

应该删除服务，这样也会删除基础结构资源组。

对于托管应用程序，请选择你部署的服务。

![选择服务](./media/lock-resources/select-service.png)

请注意，服务包含 **托管资源组** 的链接。 该资源组包含基础结构且已锁定。 无法直接将其删除。

![显示托管组](./media/lock-resources/show-managed-group.png)

若要删除服务的所有内容（包括锁定的基础结构资源组），请选择该服务对应的“删除”。

![删除服务](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>配置锁定

### <a name="portal"></a>门户

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>ARM 模板

使用 Azure 资源管理器模板（ARM 模板）部署锁定时，需要了解锁定的范围以及部署的范围。 若要在部署范围内应用锁定（例如锁定资源组或订阅），请不要设置 scope 属性。 锁定部署范围内的资源时，请设置 scope 属性。

以下模板会将锁应用于它部署到的资源组。 请注意，锁资源没有 scope 属性，因为锁定范围会与部署范围匹配。 此模板部署在资源组级别。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "rgLock",
      "properties": {
        "level": "CanNotDelete",
        "notes": "Resource group should not be deleted."
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

---

若要创建资源组并对其进行锁定，请在订阅级别部署以下模板。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "lockDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Authorization/locks",
              "apiVersion": "2016-09-01",
              "name": "rgLock",
              "properties": {
                "level": "CanNotDelete",
                "notes": "Resource group and its resources should not be deleted."
              }
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

主 Bicep 文件创建资源组，并使用[模块](../bicep/modules.md)创建锁。

```Bicep
targetScope = 'subscription'

param rgName string
param rgLocation string

resource createRg 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  name: rgName
  location: rgLocation
}

module deployRgLock './lockRg.bicep' = {
  name: 'lockDeployment'
  scope: resourceGroup(createRg.name)
}
```

该模块使用一个添加资源组锁的 Bicep 文件，该文件名为 lockRg.bicep。

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group and its resources should not be deleted.'
  }
}
```

---

对资源组中的资源应用锁时，请添加 scope 属性。 将 scope 设置为要锁定的资源的名称。

以下[示例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/lock.json)演示可创建应用服务计划、网站和网站上的锁的模板。 锁的范围设置为该网站。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-12-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2020-12-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```Bicep
param hostingPlanName string
param location string = resourceGroup().location

var siteName = concat('ExampleSite', uniqueString(resourceGroup().id))

resource serverFarm 'Microsoft.Web/serverfarms@2020-12-01' = {
  name: hostingPlanName
  location: location
  sku: {
    tier: 'Free'
    name: 'f1'
    capacity: 0
  }
  properties: {
    targetWorkerCount: 1
  }
}

resource webSite 'Microsoft.Web/sites@2020-12-01' = {
  name: siteName
  location: location
  properties: {
    serverFarmId: serverFarm.name
  }
}

resource siteLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'siteLock'
  scope: webSite
  properties:{
    level: 'CanNotDelete'
    notes: 'Site should not be deleted.'
  }
}
```

---

### <a name="azure-powershell"></a>Azure PowerShell

可以通过 Azure PowerShell 使用 [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 命令锁定已部署的资源。

若要锁定某个资源，请提供该资源的名称、其资源类型及其资源组名称。

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

若要锁定某个资源组，请提供该资源组的名称。

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

若要获取有关某个锁的信息，请使用 [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock)。 若要获取订阅中的所有锁，请使用：

```azurepowershell-interactive
Get-AzResourceLock
```

若要获取某个资源的所有锁，请使用：

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

若要获取某个资源组的所有锁，请使用：

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

若要删除某个资源的锁，请使用：

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

若要删除某个资源组的锁，请使用：

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

可以通过 Azure CLI 使用 [az lock create](/cli/azure/lock#az_lock_create) 命令锁定已部署的资源。

若要锁定某个资源，请提供该资源的名称、其资源类型及其资源组名称。

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

若要锁定某个资源组，请提供该资源组的名称。

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

若要获取有关某个锁的信息，请使用 [az lock list](/cli/azure/lock#az_lock_list)。 若要获取订阅中的所有锁，请使用：

```azurecli
az lock list
```

若要获取某个资源的所有锁，请使用：

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

若要获取某个资源组的所有锁，请使用：

```azurecli
az lock list --resource-group exampleresourcegroup
```

若要删除某个资源的锁，请使用：

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

若要删除某个资源组的锁，请使用：

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST API

可以使用 [管理锁的 REST API](/rest/api/resources/managementlocks)锁定已部署的资源。 REST API 可用于创建和删除锁，并且检索有关现有锁的信息。

若要创建一个锁，请运行：

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

作用域可能是订阅、资源组或资源。 锁名称可以是要对该锁使用的任何称谓。 对于 api 版本，请使用 **2016-09-01**。

在请求中，包括指定锁属性的 JSON 对象。

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用逻辑方式组织资源，请参阅[使用标记来组织资源](tag-resources.md)。
- 可以使用自定义策略对订阅应用限制和约定。 有关详细信息，请参阅[什么是 Azure Policy？](../../governance/policy/overview.md)。
- 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](/azure/architecture/cloud-adoption-guide/subscription-governance)。
