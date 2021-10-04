---
title: 使用客户管理的密钥加密注册表
description: 了解 Azure 容器注册表的静态加密，以及如何使用 Azure Key Vault 中存储的客户管理的密钥来加密高级注册表
ms.topic: how-to
ms.date: 09/13/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: a5fd3140474b1d6d7c36d686c14f2dc4e6a9ef73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561596"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>使用客户管理的密钥加密注册表

当你在 Azure 容器注册表中存储映像和其他项目时，Azure 会自动使用[服务托管的密钥](../security/fundamentals/encryption-models.md)对注册表内容进行静态加密。 可以使用你在 Azure Key Vault 中创建和管理的密钥（客户管理的密钥），通过一个附加的加密层来补充默认加密。 本文将引导你使用 Azure CLI、Azure 门户或资源管理器模板完成这些步骤。

使用客户管理的密钥进行服务器端加密是通过与 [Azure Key Vault](../key-vault/general/overview.md) 集成来实现的： 

* 你可以创建自己的加密密钥并将其存储在密钥保管库中，或使用 Azure Key Vault 的 API 来生成密钥。 
* 使用 Azure Key Vault 还可以审核密钥的使用情况。
* 当 Azure Key Vault 中有新的密钥版本时，Azure 容器注册表支持自动轮换注册表加密密钥。 你还可以手动轮换注册表加密密钥。

此功能在“高级”容器注册表服务层级中可用。 若要了解注册表服务层和限制，请参阅 [Azure 容器注册表服务层](container-registry-skus.md)。


## <a name="things-to-know"></a>使用须知

* 目前只能在创建注册表时启用客户管理的密钥。 启用密钥时，可配置用户分配托管标识以访问密钥保管库。 稍后，可以根据需要启用注册表的系统管理标识以访问密钥保管库。
* 对注册表启用使用客户管理的密钥进行的加密后，无法禁用加密。  
* Azure 容器注册表仅支持 RSA 或 RSA-HSM 密钥。 当前不支持椭圆曲线密钥。
* 使用客户管理的密钥加密的注册表目前不支持[内容信任](container-registry-content-trust.md)。
* 在使用客户管理的密钥加密的注册表中，[ACR 任务](container-registry-tasks-overview.md)的运行日志目前只会保留 24 小时。 如果需要将日志保留更长时间，请参阅有关[导出和存储任务运行日志](container-registry-tasks-logs.md#alternative-log-storage)的指南。

## <a name="automatic-or-manual-update-of-key-versions"></a>密钥版本的自动或手动更新

使用客户管理的密钥加密的注册表的一个重要的安全注意事项是以何频率更新（轮换）加密密钥。 你的组织可能有合规性策略，要求当 Azure Key Vault 中存储的密钥[版本](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)用作客户管理的密钥时定期对其进行更新。 

配置使用客户管理的密钥进行的注册表加密时，可通过两个选项更新用于加密的密钥版本：

* 自动更新密钥版本 - 若要在 Azure Key Vault 中有新版本可用时自动更新客户管理的密钥的密钥版本，请在启用通过客户管理的密钥进行的注册表加密时省略密钥版本。 使用无版本的密钥对注册表加密时，Azure 容器注册表会定期检查密钥保管库中是否有新的密钥版本，在确定有新版本的情况下会在 1 小时内更新客户管理的密钥。 Azure 容器注册表自动使用最新版本的密钥。

* 手动更新密钥版本 - 若要使用特定版本的密钥进行注册表加密，请在启用通过客户管理的密钥进行的注册表加密时指定该密钥版本。 使用特定密钥版本对注册表加密时，Azure 容器注册表会使用该版本进行加密，直到你手动轮换客户管理的密钥。

有关详细信息，请参阅本文后面部分的[选择带或不带密钥版本的密钥 ID](#choose-key-id-with-or-without-key-version) 和[更新密钥版本](#update-key-version)。

## <a name="prerequisites"></a>先决条件

若要使用本文中所述的 Azure CLI 步骤，需要安装 Azure CLI 版本 2.2.0 或更高版本，或 Azure Cloud Shell。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="enable-customer-managed-key---cli"></a>启用客户管理的密钥 - CLI

### <a name="create-a-resource-group"></a>创建资源组

如果需要，请运行 [az group create][az-group-create] 命令创建一个资源组，以用于创建密钥保管库、容器注册表和其他所需资源。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

使用 [az identity create][az-identity-create] 命令为 Azure 资源创建用户分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 注册表将使用此标识来访问 Key Vault 服务。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

记下命令输出中的以下值：`id` 和 `principalId`。 在后续步骤中，需要使用这些值来配置注册表对密钥保管库的访问。

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

为方便起见，请将这些值存储在环境变量中：

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>创建密钥保管库

使用 [az keyvault create][az-keyvault-create] 创建一个密钥保管库来存储用于加密注册表的客户管理的密钥。 

默认情况下，在新的密钥保管库中会自动启用“软删除”设置。 为了防止意外删除密钥或密钥保管库而导致数据丢失，还请启用“清除保护”设置。

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

若要在后续步骤中使用，请获取密钥保管库的资源 ID：

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access-by-trusted-services"></a>启用受信任的服务进行密钥保管库访问

如果使用防火墙或虚拟网络（专用终结点）来保护密钥保管库，请启用网络设置，以允许[受信任的 Azure 服务](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)进行访问。 

有关详细信息，请参阅[配置 Azure Key Vault 网络设置](../key-vault/general/how-to-azure-key-vault-network-security.md?tabs=azure-cli)。


### <a name="enable-key-vault-access-by-managed-identity"></a>启用通过托管标识进行密钥保管库访问的功能

#### <a name="enable-key-vault-access-policy"></a>启用密钥保管库访问策略

一个选项是为密钥保管库配置策略，以便标识可以访问它。 在以下 [az keyvault set-policy][az-keyvault-set-policy] 命令中，请传递前面创建并存储在环境变量中的托管标识的主体 ID。 将密钥权限设置为 **get**、**unwrapKey** 和 **wrapKey**。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey

```
#### <a name="assign-rbac-role"></a>分配 RBAC 角色

或者，使用[用于密钥保管库的 Azure RBAC](../key-vault/general/rbac-guide.md) 为标识分配访问密钥保管库的权限。 例如，使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 命令将密钥保管库加密服务加密角色分配给标识：

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>创建密钥并获取密钥 ID

运行 [az keyvault key create][az-keyvault-key-create] 命令以在密钥保管库中创建密钥。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

记下命令输出中的密钥 ID `kid`。 在下一步骤中将会用到此 ID：

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>选择带或不带密钥版本的密钥 ID

为方便起见，请将你为密钥 ID 选择的格式存储在 $keyID 环境变量中。 你可以使用带版本的密钥 ID，也可以使用不带版本的密钥。

#### <a name="manual-key-rotation---key-id-with-version"></a>手动密钥轮换 - 带版本的密钥 ID

用来通过客户管理的密钥加密注册表时，此密钥只允许在 Azure 容器注册表中手动轮换密钥。

此示例存储密钥的 `kid` 属性：

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>自动密钥轮换 - 省略版本的密钥 ID 

用来通过客户管理的密钥加密注册表时，此密钥在 Azure Key Vault 中检测到新的密钥版本时会启用自动密钥轮换。

此示例删除密钥的 `kid` 属性中的版本：

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>使用客户管理的密钥创建注册表

运行 [az acr create][az-acr-create] 命令，以在“高级”服务层级创建注册表并启用客户管理的密钥。 传递托管标识 ID 和密钥 ID（以前已存储在环境变量中）：

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>显示加密状态

若要显示是否启用了使用客户管理的密钥进行注册表加密，请运行 [az acr encryption show][az-acr-encryption-show] 命令：

```azurecli
az acr encryption show --name <registry-name>
```

输出类似于以下内容，具体取决于用于加密注册表的密钥：

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>启用客户管理的密钥 - 门户

### <a name="create-a-managed-identity"></a>创建托管标识

在 Azure 门户中为 Azure 资源创建用户分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 有关步骤，请参阅[创建用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

在后续步骤中需要用到该标识的名称。

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="在 Azure 门户中创建用户分配的标识":::

### <a name="create-a-key-vault"></a>创建 key vault

有关创建密钥保管库的步骤，请参阅[快速入门：使用 Azure 门户创建密钥保管库](../key-vault/general/quick-create-portal.md)。

为客户管理的密钥创建密钥保管库时，请在“基本信息”选项卡中启用“清除保护”设置 。 此设置有助于防止意外删除密钥或密钥保管库而导致的数据丢失。

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="在 Azure 门户中创建密钥保管库":::

### <a name="enable-key-vault-access-by-trusted-services"></a>启用受信任的服务进行密钥保管库访问

如果使用防火墙或虚拟网络（专用终结点）来保护密钥保管库，请启用网络设置，以允许[受信任的 Azure 服务](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)进行访问。 

有关详细信息，请参阅[配置 Azure Key Vault 网络设置](../key-vault/general/how-to-azure-key-vault-network-security.md?tabs=azure-portal)。

### <a name="enable-key-vault-access-by-managed-identity"></a>启用通过托管标识进行密钥保管库访问的功能

#### <a name="enable-key-vault-access-policy"></a>启用密钥保管库访问策略

一个选项是为密钥保管库配置策略，以便标识可以访问它。

1. 导航到你的密钥保管库。
1. 选择“设置” > “访问策略”>“+添加访问策略”。
1. 选择“密钥权限”，然后选择“获取”、“解包密钥”和“包装密钥”。
1. 在“选择主体”中，选择你的用户分配的托管标识的资源名称。  
1. 依次选择“添加”、“保存”。

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="创建密钥保管库访问策略":::

#### <a name="assign-rbac-role"></a>分配 RBAC 角色    

或者，将“Key Vault 加密服务加密用户”角色分配给密钥保管库范围内用户分配的托管标识。

有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

### <a name="create-key-optional"></a>创建密钥（可选）

（可选）在密钥保管库中创建密钥，以用于加密注册表。 如果要选择特定的密钥版本作为客户管理的密钥，请执行以下步骤。 如果密钥保管库访问仅限于专用终结点或选定网络，则创建注册表之前可能还需要创建密钥。 

1. 导航到你的密钥保管库。
1. 选择“设置” > “密钥”。
1. 选择“+生成/导入”并输入密钥的唯一名称。
1. 接受剩余的默认值，然后选择“创建”。
1. 创建后，选择该密钥，然后选择当前版本。 复制密钥版本的密钥标识符。

### <a name="create-azure-container-registry"></a>创建 Azure 容器注册表

1. 选择“创建资源” > “容器” > “容器注册表”。  
1. 在“基本信息”选项卡中选择或创建一个资源组，然后输入注册表名称。 在“SKU”中选择“高级”。
1. 在“加密”选项卡上的“客户管理的密钥”中，选择“已启用”。
1. 在“标识”中，选择你创建的托管标识。
1. 在“加密”中，选择以下任一项：
    * 选择“从密钥保管库中选择”，然后选择现有密钥保管库和密钥或“新建” 。 你选择的密钥不受版本控制，可启用自动密钥轮换。
    * 选择“输入密钥 URI”，并提供现有密钥的标识符。 可以提供受版本控制的密钥 URI（适用于必须手动轮换的密钥）或不受版本控制的密钥 URI（该 URI 启用自动密钥轮换）。 有关创建密钥的步骤，请参阅上一部分。
1. 在“加密”选项卡中，选择“查看 + 创建”。
1. 选择“创建”以部署注册表实例。

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="在 Azure 门户中创建加密注册表":::

若要在门户中查看注册表的加密状态，请导航到注册表。 在“设置”下，选择“加密”。

## <a name="enable-customer-managed-key---template"></a>启用客户管理的密钥 - 模板

还可以使用资源管理器模板来创建注册表，并启用使用客户管理的密钥进行加密。

以下模板创建新的容器注册表和用户分配的托管标识。 将以下内容复制到新文件，并使用类似于 `CMKtemplate.json` 的文件名保存该文件。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

按照前面部分中的步骤创建以下资源：

* 密钥保管库，按名称标识
* 密钥保管库密钥，按密钥 ID 标识

运行以下 [az deployment group create][az-deployment-group-create] 命令，以使用前面的模板文件创建注册表。 根据指示提供新的注册表名称和托管标识名称，以及你创建的密钥保管库名称和密钥 ID。

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>显示加密状态

若要显示注册表加密的状态，请运行 [az acr encryption show][az-acr-encryption-show] 命令：

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>使用注册表

在注册表中启用客户管理的密钥后，可以像在未使用客户管理的密钥加密的注册表中执行操作一样，执行相同的注册表操作。 例如，可向注册表进行身份验证，以及推送 Docker 映像。 请参阅[推送和拉取映像](container-registry-get-started-docker-cli.md)中的示例命令。

## <a name="rotate-key"></a>轮换密钥

更新 Azure Key Vault 中的密钥版本，或者创建新密钥，然后更新注册表以使用该密钥加密数据。 可以使用 Azure CLI 或者在门户中执行这些步骤。

轮换密钥时，通常需要指定在创建注册表时所用的同一标识。 （可选）配置新的用户分配标识以用于进行密钥访问，或者启用并指定注册表的系统分配标识。

> [!NOTE]
> * 若要在门户中启用注册表的系统分配的标识，请选择“设置” > “标识”，并将系统分配的标识的状态设置为“启用”。  
> * 确保针对为进行密钥访问而配置的标识设置了所需的[密钥保管库访问权限](#enable-key-vault-access-by-managed-identity)。

### <a name="update-key-version"></a>更新密钥版本

更新密钥（用作客户管理的密钥）的版本是一种常见情况。 Azure 容器注册表中的客户管理的密钥会自动更新，或者必须手动更新，具体取决于注册表加密的配置情况。

### <a name="azure-cli"></a>Azure CLI

使用 [az keyvault key][az-keyvault-key] 命令来创建或管理密钥保管库密钥。 若要创建新的密钥版本，请运行 [az keyvault key create][az-keyvault-key-create] 命令：

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

下一步取决于注册表加密的配置情况：

* 如果注册表配置为检测密钥版本更新，则客户管理的密钥会在 1 小时内自动更新。

* 如果注册表配置为要求对新密钥版本进行手动更新，请运行 [az acr encryption rotate-key][az-acr-encryption-rotate-key] 命令并传递要配置的新密钥 ID 和标识：

若要手动更新客户管理的密钥的版本，请执行以下代码：

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> 运行 `az acr encryption rotate-key` 时，可以传递带版本的密钥 ID 或无版本的密钥 ID。 如果使用无版本的密钥 ID，则会将注册表配置为自动检测以后的密钥版本更新。

### <a name="portal"></a>门户

使用注册表的“加密”设置来更新客户管理的密钥所用的密钥保管库、密钥或标识设置。

例如，若要配置新密钥，请执行以下操作：

1. 在门户中导航到你的注册表。
1. 在“设置”下，选择“加密” > “更改密钥”。

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="在 Azure 门户中轮换密钥":::
1. 在“加密”中，选择以下任一项：
    * 选择“从密钥保管库中选择”，然后选择现有密钥保管库和密钥或“新建” 。 你选择的密钥不受版本控制，可启用自动密钥轮换。
    * 选择“输入密钥 URI”，并直接提供密钥标识符。 可以提供受版本控制的密钥 URI（适用于必须手动轮换的密钥）或不受版本控制的密钥 URI（该 URI 启用自动密钥轮换）。
1. 完成密钥选择，然后选择“保存”。

## <a name="revoke-key"></a>撤销密钥

撤销客户管理的加密密钥，方法是：更改针对密钥保管库的访问策略或权限，或者删除密钥。 例如，使用 [az keyvault delete-policy][az-keyvault-delete-policy] 命令更改注册表使用的托管标识的访问策略：

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

撤销密钥会有效阻止对所有注册表数据的访问，因为注册表无法访问加密密钥。 如果启用了对密钥的访问或者还原了已删除的密钥，则注册表将选取该密钥，使你可以再次访问已加密的注册表数据。 

## <a name="troubleshoot"></a>故障排除

### <a name="removing-managed-identity"></a>删除托管标识


如果你尝试从用于配置加密的注册表删除用户分配或系统分配的标识，你可能会看到类似于以下内容的错误消息：
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
你还将无法更改（轮换）加密密钥。 解决步骤取决于用于加密的标识的类型。

**用户分配的标识**

如果用户分配的标识出现此问题，请先使用 [az acr identity assign](/cli/azure/acr/identity/#az_acr_identity_assign) 命令重新分配标识。 传递标识的资源 ID，或在标识与注册表位于同一资源组中时使用该标识的名称。 例如：

```azurecli
az acr identity assign -n myRegistry \
    --identities "/subscriptions/mysubscription/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentity"
```
        
然后，在更改密钥并分配其他标识后，可以删除原始的用户分配的标识。

**系统分配的标识**

如果系统分配的标识出现此问题，请[创建 Azure 支持票证](https://azure.microsoft.com/support/create-ticket/)以帮助还原标识。

### <a name="enabling-key-vault-firewall"></a>启用密钥保管库防火墙

如果在创建加密的注册表后启用密钥保管库防火墙或虚拟网络，则可能会看到“HTTP 403”错误或其他进行映像导入或自动密钥轮换时会发生的错误。 若要解决此问题，请重新配置最初用于加密的托管标识和密钥。 请参阅[轮换密钥](#rotate-key)中的步骤。 

如果此问题仍然存在，请联系 Azure 支持。

### <a name="accidental-deletion-of-key-vault-or-key"></a>意外删除密钥保管库或密钥

删除密钥保管库或密钥（用于通过客户管理的密钥对注册表进行加密）会使注册表的内容无法访问。 如果在密钥保管库中启用了[软删除](../key-vault/general/soft-delete-overview.md)（默认选项），则可以恢复已删除的保管库或密钥保管库对象，继续注册表操作。

有关密钥保管库删除和恢复方案，请参阅[使用软删除和清除保护进行 Azure Key Vault 恢复管理](../key-vault/general/key-vault-recovery.md)。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)。
* 详细了解访问策略以及如何[保护对密钥保管库的访问](../key-vault/general/security-features.md)。


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-show]: /cli/azure/feature#az_feature_show
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-keyvault-create]: /cli/azure/keyvault#az_keyvault_create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az_keyvault_key_create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az_keyvault_delete_policy
[az-resource-show]: /cli/azure/resource#az_resource_show
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az_acr_encryption_rotate_key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az_acr_encryption_show
