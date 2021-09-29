---
title: 使用 Azure CLI 配置 Azure 映像生成器服务权限
description: 使用 Azure CLI 配置 Azure VM 映像生成器服务的要求，包括权限和特权
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 04/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 9a248a5ad65e05db84a0d20f13225d3085b419af
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766310"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>使用 Azure CLI 配置 Azure 映像生成器服务权限

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

注册 (AIB) 时，这会授予 AIB 服务创建、管理和删除暂存资源组 (IT_*) 以及向其添加资源的权限，映像生成需要这些权限。 这是通过在成功注册期间在订阅中提供的 AIB 服务主体名称 (SPN) 来实现的。

若要允许 Azure VM 映像生成器将映像分发到托管映像或共享映像库，则需要创建一个拥有读取和写入映像权限的 Azure 用户分配的标识。 如果要访问 Azure 存储，则需要具有读取专用或公共容器的权限。

必须先设置权限和特权才能生成映像。 以下各部分详细介绍如何使用 Azure CLI 配置可能的方案。


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-azure-user-assigned-managed-identity"></a>创建 Azure 用户分配的托管标识

Azure 映像生成器要求创建 [Azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)。 Azure 映像生成器使用用户分配的托管标识来读取映像、写入映像以及访问 Azure 存储帐户。 你授予标识在订阅中执行特定操作的权限。

> [!NOTE]
> 以前，Azure 映像生成器使用 Azure 映像生成器服务主体名称 (SPN) 向映像资源组授予权限。 建议不再使用 SPN。 请改用用户分配的托管标识。

下面的示例演示如何创建 Azure 用户分配的托管标识。 替换占位符设置以设置变量。

| 设置 | 说明 |
|---------|-------------|
| \<Resource group\> | 用于创建用户分配的托管标识的资源组。 |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

有关 Azure 用户分配的标识的详细信息，请参阅 [Azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)文档，了解如何创建标识。

## <a name="allow-image-builder-to-distribute-images"></a>允许映像生成器分发映像

要使 Azure 映像生成器（托管映像/共享映像库）能够分发映像，需要允许 Azure 映像生成器服务将映像注入这些资源组。 若要授予所需的权限，需要创建用户分配的托管标识，并授予其对生成映像的资源组的权限。 Azure 映像生成器没有访问订阅中其他资源组中资源的权限。 需要执行显式操作以允许访问，从而避免生成失败。

无需向用户分配的托管标识参与者授予对资源组的权限就能分发映像。 但是，用户分配的托管标识需要在分发资源组中具有以下 Azure `Actions` 权限：

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

如果要分发到共享映像库，还需要：

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>自定义现有映像的权限

要使 Azure VM 映像生成器从源自定义映像（托管映像/共享映像库）生成映像，需要允许 Azure 映像生成器服务将映像读取到这些资源组中。 若要授予所需的权限，需要创建用户分配的托管标识，并为其授予对映像所在资源组的权限。

从现有自定义映像生成：

```Actions
Microsoft.Compute/galleries/read
```

从现有的共享映像库版本生成：

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>用于自定义 VNET 上的映像的权限

Azure 映像生成器可以在订阅中部署和使用现有 VNET，从而允许自定义内容访问连接的资源。

无需向用户分配的托管标识参与者授予对资源组的权限就能将 VM 部署到现有的 VNET。 但是，用户分配的托管标识需要对 VNET 资源组具有以下 Azure `Actions` 权限：

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>创建 Azure 角色定义

以下示例根据前面部分中所述的操作创建 Azure 角色定义。 这些示例在资源组级别应用。 评估并测试示例是否足以满足你的要求。 你可能需要根据你的方案将其优化到特定的共享映像库。

映像操作允许进行读写。 确定适合你的环境的操作。 例如，创建一个角色以允许 Azure 映像生成器从资源组 example-rg-1 中读取映像并将相应映像写入资源组 example-rg-2 。

### <a name="custom-image-azure-role-example"></a>自定义映像 Azure 角色示例

以下示例会创建一个 Azure 角色，以使用和分发源自定义映像。 然后，将自定义角色授给 Azure 映像生成器的用户分配的托管标识。

若要简化示例中值的替换，请先设置以下变量。 替换占位符设置以设置变量。

| 设置 | 说明 |
|---------|-------------|
| \<Subscription ID\> | Azure 订阅 ID |
| \<Resource group\> | 自定义映像的资源组 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=$(az account show --query id --output tsv)
# Resource group - image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>现有的 VNET Azure 角色示例

以下示例会创建一个 Azure 角色，以使用和分发现有的 VNET 映像。 然后，将自定义角色授给 Azure 映像生成器的用户分配的托管标识。

若要简化示例中值的替换，请先设置以下变量。 替换占位符设置以设置变量。

| 设置 | 说明 |
|---------|-------------|
| \<Subscription ID\> | Azure 订阅 ID |
| \<Resource group\> | VNET 资源组 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=$(az account show --query id --output tsv)
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>使用托管标识访问 Azure 存储

如果要无缝向 Azure 存储进行身份验证并使用私有容器，Azure 映像生成器需要一个由用户分配的托管标识。 Azure 映像生成器使用该标识向 Azure 存储进行身份验证。

> [!NOTE]
> Azure 映像生成器仅在映像模板提交时使用标识。 编译 VM 在映像编译过程中无权访问标识。

使用 Azure CLI 创建用户分配的托管标识。

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

在映像生成器模板中，需要提供由用户分配的托管标识。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

替换以下占位符设置：

| 设置 | 说明 |
|---------|-------------|
| \<Region\> | 模板区域 |
| \<Resource group\> | 资源组 |
| \<Storage account container\> | 存储帐户容器名称 |
| \<Subscription ID\> | Azure 订阅 |

有关使用用户分配的托管标识的详细信息，请参阅[创建自定义映像以使用 Azure 用户分配的托管标识来无缝访问 Azure 存储中的文件](./image-builder-user-assigned-identity.md)。 快速入门介绍如何创建和配置用户分配的托管标识以访问存储帐户。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure 映像生成器概述](../image-builder-overview.md)。
