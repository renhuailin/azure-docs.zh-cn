---
title: 使用 Azure 密钥保管库和托管标识为 Azure Batch 帐户配置客户管理的密钥
description: 了解如何使用客户管理的密钥加密 Batch 数据。
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385058"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>使用 Azure 密钥保管库和托管标识为 Azure Batch 帐户配置客户管理的密钥

默认情况下，Azure Batch 使用平台管理的密钥来加密 Azure Batch 服务中存储的所有客户数据，例如证书、作业/任务元数据。 你也可以选择使用自己的密钥（即客户管理的密钥）来加密 Azure Batch 中存储的数据。

提供的密钥必须是在 [Azure 密钥保管库](../key-vault/general/basic-concepts.md)中生成的，并且必须可以通过 [Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)访问这些密钥。

有两种类型的托管标识：[系统分配的托管标识和用户分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 。

可以创建使用系统分配的托管标识的 Batch 帐户，也可以创建单独的、有权访问客户管理的密钥的用户分配的托管标识。 请查看[比较表](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)来了解不同之处，并考虑哪种做法最适合你的解决方案。 例如，如果你要使用同一个托管标识来访问多个 Azure 资源，则需要用户分配的托管标识。 否则，与 Batch 帐户关联的系统分配的托管标识可能足以实现这种访问。 使用用户分配的托管标识，还可以选择强制要求在创建 Batch 帐户时提供客户管理的密钥，如[以下示例中](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys)所示。

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>创建具有系统分配的托管标识的 Batch 帐户

如果你不需要单独的用户分配的托管标识，可以在创建 Batch 帐户时启用系统分配的托管标识。

### <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](https://portal.azure.com/)中创建 Batch 帐户时，请在“高级”选项卡下的标识类型中选择“系统分配” 。

![使用系统分配的标识类型的新 Batch 帐户的屏幕截图。](./media/batch-customer-managed-key/create-batch-account.png)

创建帐户后，可以在“属性”部分下的“标识主体 ID”字段中找到唯一的 GUID 。 “标识类型”将显示 `System assigned`。

![显示“标识主体 ID”字段中唯一 GUID 的屏幕截图。](./media/batch-customer-managed-key/linked-batch-principal.png)

需要提供此值才能向此 Batch 帐户授予对密钥保管库的访问权限。

### <a name="azure-cli"></a>Azure CLI

创建新的 Batch 帐户时，请为 `--identity` 参数指定 `SystemAssigned`。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

创建帐户后，可以验证是否已在此帐户中启用系统分配的托管标识。 请务必注意 `PrincipalId`，因为需要提供此值才能向该 Batch 帐户授予对密钥保管库的访问权限。

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> 在 Batch 帐户中创建的系统分配的托管标识仅用于从密钥保管库中检索客户管理的密钥。 此标识在 Batch 池中不可用。 若要在池中使用用户分配的托管标识，请参阅[在 Batch 池中配置托管标识](managed-identity-pools.md)。

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

如果需要，你可以[创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)用于访问客户管理的密钥。

需要提供此标识的“客户端 ID”值才能使它能够访问密钥保管库。

## <a name="configure-your-azure-key-vault-instance"></a>配置 Azure Key Vault 实例

要在其中生成密钥的 Azure 密钥保管库必须是在 Batch 帐户所在的同一租户中创建的。 它不需要位于同一资源组中，甚至不需要位于同一订阅中。

### <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

[创建 Azure 密钥保管库实例](../key-vault/general/quick-create-portal.md)（用于 Azure Batch 的客户管理的密钥）时，请确保“软删除”和“清除保护”都已启用 。

![密钥保管库创建屏幕的屏幕截图。](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>将访问策略添加到 Azure Key Vault 实例

在 Azure 门户中创建密钥保管库后，请在“设置”下的“访问策略”中，使用托管标识添加 Batch 帐户访问权限 。 在“密钥权限”下，选择“获取”、“包装密钥”和“解包密钥”   。

![显示“添加访问策略”屏幕的屏幕截图。](./media/batch-customer-managed-key/key-permissions.png)

在“主体”下的“选择”字段中，填写下列其中一项 ：

- 对于系统分配的托管标识：输入先前检索到的 `principalId`，或 Batch 帐户的名称。
- 对于用户分配的托管标识：输入先前检索到的“客户端 ID”，或用户分配的托管标识的名称。

![“主体”屏幕的屏幕截图。](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>在 Azure Key Vault 中生成密钥

在 Azure 门户中，转到“密钥”部分中的密钥保管库实例，选择“生成/导入” 。 为“密钥类型”选择 `RSA`，为“RSA 密钥大小”选择至少 `2048` 位 。 目前不支持将 `EC` 密钥类型用作 Batch 帐户上的客户管理的密钥。

![创建密钥](./media/batch-customer-managed-key/create-key.png)

创建密钥后，单击新建的密钥和当前版本，并复制“属性”部分下的“密钥标识符” 。  请确保在“允许的操作”下，“包装密钥”和“解包密钥”均已选中  。

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>在 Batch 帐户上启用客户管理的密钥

完成上述步骤后，可以在 Batch 帐户上启用客户管理的密钥。

### <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](https://portal.azure.com/)中，转到“Batch 帐户”页。 在“加密”部分下，启用“客户管理的密钥” 。 可以直接使用“密钥标识符”，或者，可以选择密钥保管库，然后单击“选择密钥保管库和密钥”。

![显示“加密”部分和用于启用客户管理的密钥的选项的屏幕截图](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

创建具有系统分配的托管标识的 Batch 帐户并为其授予对密钥保管库的访问权限后，使用 `keyVaultProperties` 参数下的 `{Key Identifier}` URL 更新 Batch 帐户。 另外，请将 encryption_key_source 设置为 `Microsoft.KeyVault`。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>创建具有用户分配的托管标识和客户管理的密钥的 Batch 帐户

使用 Batch Management .NET 客户端，可以创建具有用户分配的托管标识和客户管理的密钥的 Batch 帐户。

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>更新客户管理的密钥版本

创建密钥的新版本时，请将 Batch 帐户更新为使用该新版本。 按照以下步骤操作：

1. 在 Azure 门户中导航到你的 Batch 帐户，并显示“加密”设置。
2. 输入新密钥版本的 URI。 或者，可以再次选择密钥保管库和密钥以更新版本。
3. 保存更改。

还可以使用 Azure CLI 更新版本。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>使用不同的密钥进行 Batch 加密

若要更改用于 Batch 加密的密钥，请执行以下步骤：

1. 导航到你的 Batch 帐户，并显示“加密”设置。
2. 输入新密钥的 URI。 或者，可以选择密钥保管库和一个新密钥。
3. 保存更改。

还可以通过 Azure CLI 使用不同的密钥。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>常见问题

- 现有的 Batch 帐户是否支持客户管理的密钥？ 不是。 只有新 Batch 帐户才支持客户管理的密钥。
- 是否可以选择大于 2048 位的 RSA 密钥大小？ 是，还支持 `3072` 和 `4096` 位的 RSA 密钥大小。
- **吊销客户管理的密钥后可执行哪些操作？** 如果 Batch 失去了对客户管理的密钥的访问权限，则允许的唯一一项操作就是删除帐户。
- 如果我意外删除了密钥保管库密钥，应如何还原 Batch 帐户的访问权限？ 由于清除保护和软删除已启用，因此你可以还原现有密钥。 有关详细信息，请参阅[恢复 Azure 密钥保管库](../key-vault/general/key-vault-recovery.md)。
- 是否可以禁用客户管理的密钥？ 可随时将 Batch 帐户的加密类型重新设置为“Microsoft 管理的密钥”。 然后，你便可以随意删除或更改密钥。
- 如何轮换密钥？ 客户管理的密钥不会自动轮换。 若要轮换密钥，请更新与帐户关联的密钥标识符。
- 还原访问权限后，Batch 帐户在多长时间后可再次正常工作？ 还原访问权限后，最长可能需要经过 10 分钟才能访问该帐户。
- 当 Batch 帐户不可用时，我的资源会发生什么情况呢？ 当 Batch 失去客户管理的密钥的访问权限时正在运行的所有池将继续运行。 但是，节点将转换为不可用状态，且任务将停止运行（并重新排队）。 还原访问权限后，节点将再次可用，任务将重启。
- 此加密机制是否适用于 Batch 池中的 VM 磁盘？ 不是。 对于云服务配置池，不会对 OS 和临时磁盘应用加密。 对于虚拟机配置池，默认将使用 Microsoft 平台管理的密钥来加密 OS 和任何指定的数据磁盘。 目前，不能对这些磁盘指定你自己的密钥。 若要使用 Microsoft 平台管理的密钥来加密 Batch 池 VM 的临时磁盘，必须在[虚拟机配置](/rest/api/batchservice/pool/add#virtualmachineconfiguration)池中启用 [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) 属性。 对于高度敏感的环境，我们建议启用临时磁盘加密，并避免将敏感数据存储在 OS 和数据磁盘上。 有关详细信息，请参阅[创建包含已启用加密的磁盘的池](./disk-encryption.md)
- Batch 帐户中系统分配的托管标识是否在计算节点上可用？ 不是。 系统分配的托管标识目前仅用于访问 Azure 密钥保管库中的客户管理的密钥。 若要在计算节点上使用用户分配的托管标识，请参阅[在 Batch 池中配置托管标识](managed-identity-pools.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Batch 中的安全最佳做法](security-best-practices.md)。
- 详细了解 [Azure 密钥保管库](../key-vault/general/basic-concepts.md)。
