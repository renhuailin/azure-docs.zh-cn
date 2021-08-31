---
title: 禁止项目导出
description: 设置注册表属性，以防高级 Azure 容器注册表发生数据外泄。
ms.topic: how-to
ms.date: 07/27/2021
ms.openlocfilehash: 8fa32197069376c71c035df0285852f4041efc64
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777814"
---
# <a name="disable-export-of-artifacts-from-an-azure-container-registry"></a>禁止从 Azure 容器注册表导出项目 

若要防止组织中的注册表用户恶意或意外地将项目泄露到虚拟网络之外，你可以配置注册表的导出策略以禁止导出。

导出策略是 API 版本 2021-06-01-preview 中为高级容器注册表引入的一个属性。 如果将 `exportPolicy` 属性的状态设置为 `disabled`，则当用户尝试执行以下操作时，将阻止从受网络限制的注册表导出项目：

* 将注册表的项目[导入](container-registry-import-images.md)另一个 Azure 容器注册表
* 创建注册表[导出管道](container-registry-transfer-images.md)，用于将项目传输到另一个容器注册表

> [!NOTE]
> 禁止导出项目不会阻止授权用户访问虚拟网络内的注册表以拉取项目或执行其他数据平面操作。 若要审核这种使用，建议配置诊断设置以[监视](monitor-service.md)注册表操作。 

## <a name="prerequisites"></a>先决条件

* 使用[专用终结点](container-registry-private-link.md)配置的高级容器注册表。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="other-requirements-to-disable-exports"></a>禁止导出的其他要求

* 禁用公共网络访问 - 若要禁止项目导出，还必须禁用对注册表的公共访问（必须将注册表的 `publicNetworkAccess` 属性设置为 `disabled`）。 可以先禁用对注册表的公共网络访问，然后再禁止导出，或者同时禁用。

    通过禁用对注册表的公共终结点的访问，你可以确保仅在虚拟网络内允许执行注册表操作。 禁止对注册表的公共访问以拉取项目和执行其他操作。 

*  删除导出管道 - 在将注册表的 `exportPolicy` 状态设置为 `disabled` 之前，请删除注册表中配置的任何现有导出管道。 如果配置了管道，则无法更改 `exportPolicy` 状态。

## <a name="disable-exportpolicy-for-an-existing-registry"></a>为现有注册表禁用 exportPolicy

创建注册表时，默认情况下会将 `exportPolicy` 状态设置为 `enabled`，这将允许导出项目。 可以使用 ARM 模板或 `az resource update` 命令将状态更新为 `disabled`。

### <a name="arm-template"></a>ARM 模板 

添加以下 JSON 以更新 `exportPolicy` 状态，并将 `publicNetworkAccess` 属性设置为 `disabled`。 详细了解如何[使用 ARM 模板部署资源](../azure-resource-manager/templates/deploy-cli.md)。

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
      "publicNetworkAccess": "disabled",
      "policies": {
        "exportPolicy": {
          "status": "disabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

运行 [az resource update](/cli/azure/resource/#az_resource_update)，将现有注册表中的 `exportPolicy` 状态设置为 `disabled`。 替换注册表和资源组的名称。

如本示例中所示，禁用 `exportPolicy` 属性时，还应将 `publicNetworkAccess` 属性设置为 `disabled`。

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=disabled" \
    --set "properties.publicNetworkAccess=disabled"  
```

输出显示导出策略状态为“已禁用”。

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "identity": null,
  "kind": null,
  "location": "centralus",
  "managedBy": null,
  "name": "myregistry",
  "plan": null,
  "properties": {
    [...]
    "policies": {
      "exportPolicy": {
        "status": "disabled"
      },
      "quarantinePolicy": {
        "status": "disabled"
      },
      "retentionPolicy": {
        "days": 7,
        "lastUpdatedTime": "2021-07-20T23:20:30.9985256+00:00",
        "status": "disabled"
      },
      "trustPolicy": {
        "status": "disabled",
        "type": "Notary"
      },
    "privateEndpointConnections": [],
    "provisioningState": "Succeeded",
    "publicNetworkAccess": "Disabled",
    "zoneRedundancy": "Disabled"
[...]
}
```

## <a name="enable-exportpolicy"></a>启用 exportPolicy 

在注册表中禁用 `exportPolicy` 状态之后，可以随时使用 ARM 模板或 `az resource update` 命令重新启用它。

### <a name="arm-template"></a>ARM 模板 

添加以下 JSON，以将 `exportPolicy` 状态更新为 `enabled`。 详细了解如何[使用 ARM 模板部署资源](../azure-resource-manager/templates/deploy-cli.md)

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
     "policies": {
        "exportPolicy": {
          "status": "enabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

运行 [az resource update](/cli/azure/resource/#az_resource_update)，将 `exportPolicy` 状态设置为 `enabled`。 替换注册表和资源组的名称。

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=enabled"
```
 
## <a name="next-steps"></a>后续步骤

* 了解 [Azure 容器注册表角色和权限](container-registry-roles.md)。
* 如果你想防止意外删除注册表项目，请参阅[锁定容器映像](container-registry-image-lock.md)。
* 了解内置 [Azure 策略](container-registry-azure-policy.md)如何保护 Azure 容器注册表
