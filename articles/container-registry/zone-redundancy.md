---
title: 用于实现高可用性的区域冗余注册表
description: 了解如何通过在 Azure 可用性区域中创建容器注册表或复制来启用 Azure 容器注册表中的区域冗余。 区域冗余是高级服务层的一项功能。
ms.topic: article
ms.date: 12/11/2020
ms.openlocfilehash: 1553beef47a3d493f066e47cd39751093d83fc24
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803504"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>启用 Azure 容器注册表中的区域冗余以实现复原和高可用性

除 [异地复制](container-registry-geo-replication.md)以外，它还可跨一个或多个 Azure 区域复制注册表数据，从而为区域操作提供可用性和降低延迟，Azure 容器注册表支持可选 *区域冗余*。 [区域冗余](../availability-zones/az-overview.md#availability-zones) 为注册表或复制资源提供了复原能力和高可用性， (副本) 在特定区域中。

本文介绍如何使用 Azure 门户或 Azure 资源管理器模板来设置区域冗余容器注册表或区域冗余副本。 

区域冗余是高级容器注册表服务层的 **预览** 功能。 若要了解注册表服务层和限制，请参阅 [Azure 容器注册表服务层](container-registry-skus.md)。

## <a name="preview-limitations"></a>预览版限制

* 目前在以下区域中受支持：美国东部、美国东部2、美国西部2。
* 当前不支持区域转换到可用性区域。 若要在区域中启用可用性区域支持，必须在所需的区域中创建注册表，启用了可用性区域支持，或者必须添加已复制区域，并且启用了可用性区域支持。
* 无法在区域中禁用区域冗余。
* [ACR 任务](container-registry-tasks-overview.md) 尚不支持可用性区域。
* 目前通过 Azure 资源管理器模板或 Azure 门户支持。 未来版本中将启用 Azure CLI 支持。

## <a name="about-zone-redundancy"></a>关于区域冗余

使用 Azure [可用性区域](../availability-zones/az-overview.md) 在 azure 区域中创建弹性且高可用性 azure 容器注册表。 例如，组织可以使用其他 [受支持的 azure 资源](../availability-zones/az-region.md) 来设置区域冗余 azure 容器注册表，以满足数据驻留或其他符合性要求，同时在某个区域内提供高可用性。

Azure 容器注册表还支持 [异地复制](container-registry-geo-replication.md)，这种复制可跨多个区域复制服务，从而使冗余和位置能够计算其他位置的资源。 在某个区域内冗余的可用性区域和跨多个区域的异地复制的组合，增强了注册表的可靠性和性能。

可用性区域是 Azure 区域中独特的物理位置。 为确保能够进行复原，所有已启用的地区中都必须至少有三个单独的区域。 每个区域都有一个或多个数据中心，其配备了独立的电源、冷却和网络。 为区域冗余配置时，在区域中的所有可用性区域之间复制注册表 (或注册表副本) ，并使其在发生数据中心故障时可用。

## <a name="create-a-zone-redundant-registry---portal"></a>创建区域冗余注册表-门户

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
1. 选择“创建资源” > “容器” > “容器注册表”。  
1. 在 " **基本** 信息" 选项卡中，选择或创建资源组，并输入唯一的注册表名称。 
1. 在 " **位置**" 中，选择支持 Azure 容器注册表的区域冗余的区域，如 " *美国东部*"。
1. 在“SKU”中选择“高级”。
1. 在 " **可用性区域**" 中，选择 " **已启用**"。
1. （可选）配置其他注册表设置，然后选择 " **查看 + 创建**"。
1. 选择“创建”以部署注册表实例。

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="在 Azure 门户中启用区域冗余":::

若要创建区域冗余复制，请执行以下操作：

1. 导航到 "高级层" 容器注册表，然后选择 " **复制**"。
1. 在出现的地图上，为 Azure 容器注册表（如 " **美国西部 2**"）的区域中的一个绿色六边形选择支持区域冗余。 然后选择“创建”。
1. 在 " **创建复制** " 窗口的 " **可用性区域**" 中，选择 " **已启用**"，然后选择 " **创建**"。

## <a name="create-a-zone-redundant-registry---template"></a>创建区域冗余的注册表-模板

### <a name="create-a-resource-group"></a>创建资源组

如果需要，请运行 [az group create](/cli/azure/group)命令，以在支持 Azure 容器注册表（如 *eastus*）的 [可用性区域](../availability-zones/az-region.md)的区域中为注册表创建资源组。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="deploy-the-template"></a>部署模板 

你可以使用以下资源管理器模板创建区域冗余的、异地复制的注册表。 默认情况下，该模板启用注册表中的区域冗余和其他区域副本。 

将以下内容复制到新文件，并使用类似于 `registryZone.json` 的文件名保存该文件。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

运行以下 [az deployment group create](/cli/azure/deployment?view=azure-cli-latest) 命令，以使用前面的模板文件创建注册表。 如果指示，请提供：

* 唯一的注册表名称，或者不使用参数部署模板，它将为你创建一个唯一的名称
* 支持可用性区域的副本的位置，例如 *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

在命令输出中，记下 `zoneRedundancy` 注册表和副本的属性。 启用后，每个资源都是区域冗余：

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>后续步骤

* 详细了解 [支持可用性区域的区域](../availability-zones/az-region.md)。
* 详细了解如何在 Azure 中构建 [可靠性](/azure/architecture/framework/resiliency/overview) 。
