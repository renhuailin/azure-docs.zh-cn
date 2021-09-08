---
title: 将 Azure 容器注册表移到另一区域
description: 手动将 Azure 容器注册表设置和数据移到另一个 Azure 区域。
ms.topic: article
ms.date: 06/08/2021
ms.openlocfilehash: e2bc00287923a95e2e4d3698b22c4c2ca65bebc6
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835887"
---
# <a name="manually-move-a-container-registry-to-another-region"></a>手动将容器注册表移到另一区域

你可能需要将 Azure 容器注册表从一个 Azure 区域移到另一个 Azure 区域。 例如，你在一个不同的区域中运行开发管道或托管新的部署目标，并希望提供附近区域的注册表。

尽管 [Azure 资源转移器](../resource-mover/overview.md)无法自动对 Azure 容器注册表执行移动操作，但你可以手动将容器注册表移到另一个区域：

* 将注册表设置导出到资源管理器模板
* 使用模板在不同的 Azure 区域中部署注册表
* 将源注册表中的注册表内容导入到目标注册表

[!INCLUDE [container-registry-geo-replication-include](../../includes/container-registry-geo-replication-include.md)]

## <a name="prerequisites"></a>先决条件

Azure CLI

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="considerations"></a>注意事项

* 使用本文中的步骤将注册表移到同一订阅中的不同区域。 可能需要进行其他配置才能将注册表移动到同一 Active Directory 租户中的其他 Azure 订阅。
* 导出和使用资源管理器模板可帮助重新创建许多注册表设置。 可以编辑模板以配置更多设置，或者在创建后更新目标注册表。
* 目前，Azure 容器注册表不支持将注册表移动到其他 Active Directory 租户。 此限制适用于使用[客户管理的密钥](container-registry-customer-managed-keys.md)加密的注册表和未加密的注册表。
* 如果无法移动注册表（如本文所述），可以创建新注册表，手动重新创建设置，然后[在目标注册表中导入注册表内容](#import-registry-content-in-target-registry)。

## <a name="export-template-from-source-registry"></a>从源注册表导出模板 

使用 Azure 门户、Azure CLI、Azure PowerShell 或其他 Azure 工具导出资源管理器模板。 若要使用 Azure 门户：

1. 在 [Azure 门户](https://portal.azure.com)中，导航到你的源注册表。
1. 在菜单中的“自动化”下，选择“导出模板” > “下载”  。

    :::image type="content" source="media/manual-regional-move/export-template.png" alt-text="导出容器注册表的模板":::

## <a name="redeploy-target-registry-in-new-region"></a>在新区域中重新部署目标注册表

### <a name="modify-template"></a>修改模板

检查下载的模板 JSON 文件中的注册表属性，并做出必要的更改。 至少：

* 将注册表名称的 `defaultValue` 更改为目标注册表的所需名称
* 将 `location` 更新为目标注册表的所需 Azure 区域

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "registries_myregistry_name": {
            "defaultValue": "myregistry",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('myregistry_name')]",
            "location": "centralus",
[...]
```

有关详细信息，请参阅[从 Azure 门户使用导出的模板](../azure-resource-manager/templates/template-tutorial-export-template.md)和[模板参考](/azure/templates/microsoft.containerregistry/registries)。

> [!IMPORTANT]
> 如果你要使用客户管理的密钥来加密目标注册表，请确保使用所需托管标识、密钥保管库和密钥的设置更新模板。 只能在部署注册表时启用客户管理的密钥。
> 
> 有关详细信息，请参阅[使用客户管理的密钥加密注册表](./container-registry-customer-managed-keys.md#enable-customer-managed-key---template)。

### <a name="create-resource-group"></a>创建资源组 

使用 [az group create](/cli/azure/group#az_group_create) 为目标注册表创建资源组。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。 

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="deploy-target-registry-in-new-region"></a>在新区域中部署目标注册表

使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 命令通过模板部署目标注册表：

```azurecli
az deployment group --resource-group myResourceGroup \
   --template-file template.json --name mydeployment
```

> [!NOTE]
> 如果在部署过程中看到错误，可能需要更新模板文件中的特定配置，然后重试该命令。

## <a name="import-registry-content-in-target-registry"></a>在目标注册表中导入注册表内容

在目标区域中创建注册表后，使用 [az acr import](/cli/azure/acr#az_acr_import) 命令或等效的 PowerShell 命令 `Import-AzContainerImage`，将源注册表中的映像以及你要保留的其他项目导入到目标注册表。 有关命令示例，请参阅[将容器映像导入到容器注册表](container-registry-import-images.md)。

* 使用 Azure CLI 命令 [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list) 和 [az acr repository show-tags](/cli/azure/acr/repository#az_acr_repository_show_tags) 或等效的 Azure PowerShell 命令来帮助枚举源注册表的内容。
* 针对单个项目运行 import 命令，或编写脚本以针对一系列项目运行该命令。

以下示例 Azure CLI 脚本枚举源存储库和标记，然后将项目导入到同一 Azure 订阅中的目标注册表。 根据需要进行修改，以导入特定的存储库或标记。 若要从其他订阅或租户中的注册表导入，请参阅[将容器映像导入容器注册表](container-registry-import-images.md)中的示例。

```azurecli
#!/bin/bash
# Modify registry names for your environment
SOURCE_REG=myregistry
TARGET_REG=targetregistry

# Get list of source repositories
REPO_LIST=$(az acr repository list \
    --name $SOURCE_REG --output tsv)

# Enumerate tags and import to target registry
for repo in $REPO_LIST; do
    TAGS_LIST=$(az acr repository show-tags --name $SOURCE_REG --repository $repo --output tsv);
    for tag in $TAGS_LIST; do
        echo "Importing $repo:$tag";
        az acr import --name $TARGET_REG --source $SOURCE_REG.azurecr.io/$repo":"$tag;
    done
done
```



## <a name="verify-target-registry"></a>验证目标注册表

确认目标注册表中包含以下信息：

* 注册表设置，例如注册表名称、服务层级、公共访问权限和复制内容
* 要保留的内容的存储库和标记。


### <a name="additional-configuration"></a>其他配置

* 如果需要，请手动配置目标注册表中的设置，例如专用终结点、IP 访问规则和托管标识。

* 将开发和部署系统更新为使用目标注册表而不是源注册表。

* 更新任何客户端防火墙规则以允许访问目标注册表。

## <a name="delete-original-registry"></a>删除原始注册表

成功部署了目标注册表、迁移了内容并验证了注册表设置后，可以删除源注册表。

## <a name="next-steps"></a>后续步骤

* 详细了解如何从公共注册表或其他专用注册表[将容器映像导入到](container-registry-import-images.md) Azure 容器注册表。 
* 请参阅 Azure 容器注册表的[资源管理器的模板参考](/azure/templates/microsoft.containerregistry/registries)。