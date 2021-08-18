---
title: Key Vault 机密与 Bicep
description: 介绍如何在 Bicep 部署过程中以参数形式从密钥保管库传递机密。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e940a812b4e010e9499a9a85cfd400b679d43781
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112376299"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-bicep-deployment"></a>在 Bicep 部署过程中使用 Azure Key Vault 传递安全参数值

在部署过程中，可以从 [Azure Key Vault](../../key-vault/general/overview.md) 检索一个安全值（例如密码），而不是直接在 Bicep 文件或参数文件中放置该值。 当[模块](./modules.md)需要具有 `secure:true` 修饰符的 `string` 参数时，可以使用 [getSecret 函数](bicep-functions-resource.md#getsecret)来获取密钥保管库机密。 值永远不会公开，因为仅引用其密钥保管库 ID。

> [!IMPORTANT]
> 本文重点介绍如何将敏感值作为模板参数传递。 机密作为参数传递时，密钥保管库与部署到的资源组不需要位于同一订阅中。 
>
> 本文并不涉及如何将虚拟机属性设置为密钥保管库中证书的 URL。 有关该方案的快速入门模板，请参阅[在虚拟机上安装来自 Azure Key Vault 的证书](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/vm-winrm-keyvault-windows)。

## <a name="deploy-key-vaults-and-secrets"></a>部署密钥保管库和机密

若要在 Bicep 部署过程中访问密钥保管库，请将密钥保管库上的 `enabledForTemplateDeployment` 设置为 `true`。

如果你已有密钥保管库，请确保它允许进行模板部署。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

若要创建新的密钥保管库并添加机密，请使用：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

作为密钥保管库的所有者，你可以自动获得创建机密的权限。 如果使用机密的用户不是密钥保管库的所有者，请使用以下命令授予访问权限：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

若要详细了解如何创建密钥保管库和添加机密，请参阅：

- [使用 CLI 设置和检索机密](../../key-vault/secrets/quick-create-cli.md)
- [使用 PowerShell 设置和检索机密](../../key-vault/secrets/quick-create-powershell.md)
- [使用门户设置和检索机密](../../key-vault/secrets/quick-create-portal.md)
- [使用 .NET 设置和检索机密](../../key-vault/secrets/quick-create-net.md)
- [使用 Node.js 设置和检索机密](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>授予对机密的访问权限

部署 Bicep 文件的用户必须在资源组和密钥保管库范围内具有 `Microsoft.KeyVault/vaults/deploy/action` 权限。 [所有者](../../role-based-access-control/built-in-roles.md#owner)和[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色均授予该访问权限。 如果是你创建了密钥保管库，那么你就是所有者且具有相关权限。

以下过程展示了如何创建具有最小权限的角色，以及如何分配用户。

1. 创建自定义角色定义 JSON 文件：

    ```json
    {
      "Name": "Key Vault Bicep deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a Bicep file with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```

    将“00000000-0000-0000-0000-000000000000”替换为订阅 ID。

2. 使用 JSON 文件创建新角色：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    此示例在资源组级别为用户分配自定义角色。

将密钥保管库与 Bicep 文件配合用于[托管应用程序](../managed-applications/overview.md)时，必须向设备资源提供程序服务主体授予访问权限。 有关详细信息，请参阅[部署 Azure 托管应用程序时访问 Key Vault 机密](../managed-applications/key-vault-access.md)。

## <a name="use-getsecret-function"></a>使用 getSecret 函数

可以使用 [getSecret 函数](./bicep-functions-resource.md#getsecret)获取密钥保管库机密并将值传递给模块的 `string` 参数。 只能对 `Microsoft.KeyVault/vaults` 资源调用 `getSecret` 函数，并且该函数只能与带有 `@secure()` 修饰器的参数一起使用。

以下 Bicep 文件创建 Azure SQL Server。 `adminPassword` 参数具有 `@secure()` 修饰器。

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

让我们使用前面的 Bicep 文件作为模块，假定文件的名称为 sql.bicep，与主 Bicep 文件位于同一目录。

以下 Bicep 文件使用 sql.bicep 作为模块。  Bicep 文件引用现有的密钥保管库，并调用 `getSecret` 函数来检索密钥保管库机密，然后将值作为参数传递到模块中。

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="reference-secrets-in-parameter-file"></a>在参数文件中引用机密

如果你不想使用模块，可以直接在参数文件中引用密钥保管库。 下图显示了参数文件如何引用机密并将该值传递到 Bicep 文件。

![资源管理器密钥保管库集成关系图](./media/key-vault-parameter/statickeyvault.png)

以下 Bicep 文件部署包含管理员密码的 SQL Server。 密码参数设置为安全字符串。 但是，Bicep 未指定该值的来源。

```bicep
param adminLogin string

@secure()
param adminPassword string

param sqlServerName string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

---

现在，为上述 Bicep 文件创建参数文件。 在参数文件中，指定与 Bicep 文件中的参数名称匹配的参数。 对于参数值，请从 key vault 中引用机密。 可以通过传递密钥保管库的资源标识符和机密的名称来引用机密：

在以下参数文件中，密钥保管库机密必须已存在，而且你为其资源 ID 提供了静态值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "value": "exampleadmin"
    },
    "adminPassword": {
      "reference": {
        "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
        },
        "secretName": "ExamplePassword"
      }
    },
    "sqlServerName": {
      "value": "<your-server-name>"
    }
  }
}
```

如果需要使用当前版本以外的机密版本，请包括 `secretVersion` 属性。

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

部署模板并传入参数文件：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-file <Bicep-file> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile <Bicep-file> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="next-steps"></a>后续步骤

- 有关密钥保管库的一般信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)
- 有关引用密钥的完整示例，请参阅 GitHub 上的[密钥保管库示例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。
- 有关介绍从密钥保管库中传递安全值的 Microsoft Learn 模块，请参阅[使用高级 ARM 模板功能管理复杂云部署](/learn/modules/manage-deployments-advanced-arm-template-features/)。
