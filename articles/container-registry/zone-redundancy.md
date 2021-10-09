---
title: 用于实现高可用性的区域冗余注册表
description: 了解如何在 Azure 容器注册表中启用区域冗余。 在 Azure 可用性区域中创建容器注册表或副本。 区域冗余是高级服务层级的一项功能。
ms.topic: article
ms.date: 09/13/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: ca475f61c3164f54682a22f459e1f8f768073f1f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586810"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>启用 Azure 容器注册表中的区域冗余以实现复原能力和高可用性

除[异地复制](container-registry-geo-replication.md)外，Azure 容器注册表支持可选“区域冗余”，异地复制可跨一个或多个 Azure 区域复制注册表数据，从而为区域操作提供可用性并降低延迟。 [区域冗余](../availability-zones/az-overview.md#availability-zones)为特定区域中的注册表或复制资源（如本）提供了复原能力和高可用性。

本文介绍如何使用 Azure CLI、Azure 门户或 Azure 资源管理器模板来设置区域冗余容器注册表或副本。 

区域冗余是高级容器注册表服务层的“预览”功能。 若要了解注册表服务层和限制，请参阅 [Azure 容器注册表服务层](container-registry-skus.md)。

## <a name="preview-limitations"></a>预览版限制

* 目前在以下地区受支持： 
  
    |美洲  |欧洲  |非洲  |亚太区  |
    |---------|---------|---------|---------|
    |巴西南部<br/>加拿大中部<br/>美国中部<br/>美国东部<br/>美国东部 2<br/>美国中南部<br/>美国弗吉尼亚州政府<br/>美国西部 2<br/>美国西部 3     |法国中部<br/>德国中西部<br/>北欧<br/>挪威东部<br/>西欧<br/>英国南部      |南非北部<br/>        |澳大利亚东部<br/>印度中部<br/>Japan East<br/>韩国中部<br/>  |

* 当前不支持将区域转换到可用性区域。 若要在区域中启用可用性区域支持，必须在所需区域中创建注册表并启用可用性区域支持，或者必须添加已复制区域并启用可用性区域支持。
* 无法在区域中禁用区域冗余。
* [Azure 容器注册表任务](container-registry-tasks-overview.md)尚不支持可用性区域。

## <a name="about-zone-redundancy"></a>关于区域冗余

使用 Azure [可用性区域](../availability-zones/az-overview.md)在 Azure 区域中创建可复原和高度可用的 Azure 容器注册表。 例如，组织可以使用其他[受支持的 Azure 资源](../availability-zones/az-region.md)来设置区域冗余 Azure 容器注册表，以满足数据驻留或其他合规性要求，同时在某个区域内提供高可用性。

Azure 容器注册表还支持[异地复制](container-registry-geo-replication.md)，这种复制可跨多个区域复制服务，从而使冗余和区域能够计算其他位置的资源。 某个区域内用于冗余的可用性区域和跨多个区域的异地复制合并在一起，可增强注册表的可靠性和性能。

可用性区域是 Azure 区域中独特的物理位置。 为确保能够进行复原，所有已启用的地区中都必须至少有三个单独的区域。 每个区域有一个或多个数据中心，这些数据中心配置了独立电源以及散热和网络设备。 为区域冗余进行配置时，区域中的所有可用性区域都会复制注册表 （或注册表副本），使其在发生数据中心故障时可以使用。

## <a name="create-a-zone-redundant-registry---cli"></a>创建区域冗余注册表 - CLI

若要使用 Azure CLI 来启用区域冗余，需要安装 Azure CLI 版本 2.17.0 或更高版本，或 Azure Cloud Shell。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-a-resource-group"></a>创建资源组

如有需要，请使用 [az group create](/cli/azure/group#az_group_create) 命令为注册表创建资源组。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>创建启用区域的注册表

运行 [az acr create](/cli/azure/acr#az_acr_create) 命令，以在“高级”服务层级创建区域冗余注册表。 选择支持 Azure 容器注册表 [可用性区域](../availability-zones/az-region.md) 的地区。 在以下示例中，*eastus* 区域中已启用区域冗余。 请参阅 `az acr create` 命令帮助查看更多注册表选项。

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

在命令输出中，记下注册表的 `zoneRedundancy` 属性。 启用后，注册表为区域冗余：

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>创建区域冗余复制

运行 [az acr replication create](/cli/azure/acr/replication#az_acr_replication_create) 命令，以在支持 Azure 容器注册表（如 *westus2*）的 [可用性区域](../availability-zones/az-region.md)的区域中创建区域冗余的注册表副本。 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
在命令输出中，记下副本的 `zoneRedundancy` 属性。 启用后，副本为区域冗余：

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>创建区域冗余注册表 - 门户

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
1. 选择“创建资源” > “容器” > “容器注册表”。  
1. 在“基本信息”选项卡中选择或创建一个资源组，然后输入唯一注册表名称。 
1. 在“位置”中，选择支持 Azure 容器注册表的区域冗余的区域，如“美国东部”。
1. 在“SKU”中选择“高级”。
1. 在“可用性区域”""中，选择“已启用”。
1. （可选）配置其他注册表设置，然后选择“查看 + 创建“。
1. 选择“创建”以部署注册表实例。

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="在 Azure 门户中启用区域冗余":::

创建区域冗余复制：

1. 导航到高基层容器注册表，然后选择“复制”。
1. 在出现的地图上，在支持 Azure 容器注册表区域冗余的地区中选择一个绿色六边形（如“美国西部 2”）。 或选择“添加”。
1. 在“创建复制”窗口中，确认“位置”。  在“可用性区域”中，选择“已启用“，然后选择“创建”。  

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="在 Azure 门户中启用区域冗余复制":::

## <a name="create-a-zone-redundant-registry---template"></a>创建区域冗余注册表 - 模板

### <a name="create-a-resource-group"></a>创建资源组

如果需要，请运行 [az group create](/cli/azure/group#az_group_create) 命令，以在支持 Azure 容器注册表[可用性区域](../availability-zones/az-region.md)的地区中为注册表创建资源组（如“美国东部”）。 模板使用此区域设置注册表位置。

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>部署模板 

可以使用以下资源管理器模板创建区域冗余的、异地复制的注册表。 默认情况下，该模板启用注册表中的区域冗余和区域副本。 

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

运行以下 [az deployment group create](/cli/azure/group/deployment#az_group_deployment_create) 命令，以使用前面的模板文件创建注册表。 如有指示，请提供：

* 唯一的注册表名称，或者不使用参数部署模板，这会创建一个唯一名称
* 支持可用性区域的副本的位置，例如“美国西部 2”

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

在命令输出中，记下注册表和副本的 `zoneRedundancy` 属性。 启用后，每个资源都是区域冗余的：

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>后续步骤

* 详细了解[支持可用性区域的地区](../availability-zones/az-region.md)。
* 详细了解在 Azure 中构建[可靠性](/azure/architecture/framework/resiliency/app-design)。
