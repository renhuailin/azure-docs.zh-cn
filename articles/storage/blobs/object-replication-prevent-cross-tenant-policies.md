---
title: 防止跨 Azure Active Directory 租户进行对象复制（预览版）
titleSuffix: Azure Storage
description: 防止跨租户对象复制
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 2fe98c0a15b1ec07ff1608e00aa030fd18360547
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599184"
---
# <a name="prevent-object-replication-across-azure-active-directory-tenants-preview"></a>防止跨 Azure Active Directory 租户进行对象复制（预览版）

对象复制将块 blob 从一个存储帐户中的容器异步复制到另一个存储帐户中的容器。 当你配置对象复制策略时，需要指定源存储帐户和容器以及目标帐户和容器。 策略配置完成后，Azure 存储将对源对象的创建、更新和删除操作的结果自动复制到目标对象。 有关 Azure 存储中的对象复制的详细信息，请参阅[针对块 blob 的对象复制](object-replication-overview.md)。

默认情况下，授权用户可以配置一个对象复制策略，其中源帐户和目标帐户位于不同的 Azure Active Directory (Azure AD) 租户中。 如果安全策略要求将对象复制限制为仅驻留在同一租户中的存储帐户，则可以禁止创建源帐户和目标帐户位于不同的租户中的策略（预览版）。 默认情况下，存储帐户的跨租户对象复制是启用的，除非你显式禁止它。

本文介绍了如何为存储帐户修正跨租户对象复制。 还介绍了如何创建策略来强制禁止对新的和现有的存储帐户进行跨租户对象复制。

有关如何配置对象复制策略（包括跨租户策略）的详细信息，请参阅[为块 blob 配置对象复制](object-replication-configure.md)。

## <a name="remediate-cross-tenant-object-replication"></a>修正跨租户对象复制

为防止跨 Azure AD 租户进行对象复制，可以将存储帐户的 AllowCrossTenantReplication 属性设置为 false。 如果存储帐户当前没有参与任何跨租户对象复制策略，则将 AllowCrossTenantReplication 属性设置为 false 可防止将来配置跨租户对象复制策略时将此存储帐户作为源或目标。 但是，如果存储帐户当前已参与一个或多个跨租户对象复制策略，则在删除现有的跨租户策略之前，不允许将 AllowCrossTenantReplication 属性设置为 false。

默认情况下，允许对存储帐户使用跨租户策略。 但是，默认情况下，不会为新的或现有的存储帐户设置 AllowCrossTenantReplication 属性，并且在你显式设置此属性之前不会返回值。 当属性值为 null 或 true 时，存储帐户可以参与跨租户对象复制策略。

### <a name="remediate-cross-tenant-replication-for-a-new-account"></a>为新帐户修正跨租户复制

若要禁止对新存储帐户进行跨租户复制，请使用 Azure 门户、PowerShell 或 Azure CLI。

#### <a name="portal"></a>[门户](#tab/portal)

若要禁止对新存储帐户进行跨租户对象复制，请执行以下步骤：

1. 在 Azure 门户中，导航到“存储帐户”页，然后选择“创建”。
1. 填写新存储帐户的“基本信息”选项卡。
1. 在“高级”选项卡上的“Blob 存储”部分中，找到“允许跨租户复制”设置，然后取消选中该框。

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-create-account.png" alt-text="显示如何禁止对新存储帐户进行跨租户对象复制的屏幕截图":::

1. 完成创建帐户的过程。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要禁止对新存储帐户进行跨租户对象复制，请调用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 命令，并包含值为 $false 的 `AllowCrossTenantReplication` 参数。

```azurepowershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account and disallow cross-tenant replication.
New-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -Location $location `
    -SkuName Standard_LRS
    -AllowCrossTenantReplication $false

# Read the property for the new storage account
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowCrossTenantReplication
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要对新存储帐户禁止跨租户对象复制，请调用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令，并包含值为 false 的 `allow-cross-tenant-replication` 参数。

```azurecli
# Create a storage account with cross-tenant replication disallowed.
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS
    --allow-cross-tenant-replication false

# Read the property for the new storage account
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowCrossTenantReplication \
    --output tsv
```

---

### <a name="remediate-cross-tenant-replication-for-an-existing-account"></a>为现有帐户修正跨租户复制

若要禁止对现有存储帐户进行跨租户复制，请使用 Azure 门户、PowerShell 或 Azure CLI。

#### <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要禁止对当前未参与任何跨租户策略的现有存储帐户进行跨租户对象复制，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在“数据管理”下，选择“对象复制”。
1. 选择“高级设置”。
1. 取消选中“允许跨租户复制”。 默认情况下，此框处于已选中状态，因为是允许对存储帐户进行跨租户对象复制的，除非你显式禁止它。

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-update-account.png" alt-text="显示如何禁止对现有存储帐户进行跨租户对象复制的屏幕截图":::

1. 选择“确定”，保存所做更改。

如果存储帐户当前正在参与一个或多个跨租户复制策略，那么，在你删除这些策略之前，无法禁止跨租户对象复制。 在此场景下，此设置在 Azure 门户不可用，如下图所示。

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/cross-tenant-object-replication-policies-in-effect-portal.png" alt-text="屏幕快照":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要禁止对当前未参与任何跨租户策略的现有存储帐户进行跨租户对象复制，请先安装 [Az.Storage PowerShell 模块](https://www.powershellgallery.com/packages/Az.Storage)\,版本 3.7.0 或更高版本。 接下来，为存储帐户配置 AllowCrossTenantReplication 属性。

以下示例演示如何使用 PowerShell 禁止对现有存储帐户进行跨租户对象复制。 请记得将括号中的占位符值替换为你自己的值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowCrossTenantReplication $false
```

如果存储帐户当前正在参与一个或多个跨租户复制策略，那么，在你删除这些策略之前，无法禁止跨租户对象复制。 PowerShell 提供了一个错误，指示操作因现有的跨租户复制策略而失败。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要禁止对当前未参与任何跨租户策略的现有存储帐户进行跨租户对象复制，请先安装 Azure CLI 版本 2.24.0 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 接下来，为新的或现有的存储帐户配置 allowCrossTenantReplication 属性。

以下示例演示如何使用 Azure CLI 禁止对现有存储帐户进行跨租户对象复制。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-cross-tenant-replication false
```

如果存储帐户当前正在参与一个或多个跨租户复制策略，那么，在你删除这些策略之前，无法禁止跨租户对象复制。 Azure CLI 提供了一个错误，指示操作因现有的跨租户复制策略而失败。

---

禁止跨租户复制后，尝试使用存储帐户作为源或目标配置跨租户策略会失败。 Azure 存储返回一个错误，指示不允许对存储帐户进行跨租户对象复制。

禁止对某个存储帐户进行跨租户对象复制后，你使用该帐户创建的任何新的对象复制策略都必须包括源帐户和目标帐户的完整 Azure 资源管理器 ID。 Azure 存储需要使用完整的资源 ID 来验证源帐户和目标帐户是否驻留在同一租户中。 有关详细信息，请参阅[指定源帐户和目标帐户的完整资源 ID](object-replication-overview.md#specify-full-resource-ids-for-the-source-and-destination-accounts)。

AllowCrossTenantReplication 属性仅支持用于使用 Azure 资源管理器部署模型的存储帐户。 有关哪些存储帐户使用 Azure 资源管理器部署模型的信息，请参阅[存储帐户的类型](../common/storage-account-overview.md#types-of-storage-accounts)。

## <a name="permissions-for-allowing-or-disallowing-cross-tenant-replication"></a>允许或禁止跨租户复制的权限

若要设置存储帐户的 AllowCrossTenantReplication 属性，用户必须有权创建和管理存储帐户。 提供这些权限的 Azure 基于角色的访问控制 (Azure RBAC) 角色包含 Microsoft.Storage/storageAccounts/write 或 Microsoft.Storage/storageAccounts/\* 操作 。 具有此操作的内置角色包括：

- Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色
- Azure 资源管理器[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

这些角色不提供通过 Azure Active Directory (Azure AD) 对存储帐户中数据的访问权限。 但是，它们包含 Microsoft.Storage/storageAccounts/listkeys/action，可以授予对帐户访问密钥的访问权限。 借助此权限，用户可以使用帐户访问密钥访问存储帐户中的所有数据。

角色分配的范围必须限定为存储帐户级别或更高级别，用户才能够允许或禁止对存储帐户进行跨租户对象复制。 有关角色作用域的详细信息，请参阅[了解 Azure RBAC 的作用域](../../role-based-access-control/scope-overview.md)。

请注意，仅向需要能够创建存储帐户或更新其属性的用户分配这些角色。 使用最小特权原则确保用户拥有完成任务所需的最少权限。 有关使用 Azure RBAC 管理访问权限的详细信息，请参阅 [Azure RBAC 最佳做法](../../role-based-access-control/best-practices.md)。

> [!NOTE]
> 经典订阅管理员角色“服务管理员”和“共同管理员”具有 Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色的等效权限。 所有者角色包括所有操作，因此具有这些管理角色之一的用户也可以创建和管理存储帐户。 有关详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

## <a name="use-azure-policy-to-audit-for-compliance"></a>使用 Azure Policy 审核合规性

如果你有大量存储帐户，可能需要执行审核，确保将这些帐户配置为阻止跨租户对象复制。 若要审核一组存储帐户的合规性，请使用 Azure Policy。 Azure Policy 是一项服务，可用于创建、分配和管理将规则应用于 Azure 资源的策略。 Azure Policy 可帮助你确保这些资源始终符合公司标准和服务级别协议。 有关详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md)。

### <a name="create-a-policy-with-an-audit-effect"></a>创建具有 Audit 效果的策略

Azure Policy 支持的效果决定了针对资源评估某个策略规则时会发生什么情况。 当资源不合规时，Audit 效果会创建一个警告，但不会停止请求。 有关效果的详细信息，请参阅[了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)。

若要通过 Azure 门户为存储帐户的跨租户对象复制设置创建具有“审核”效果的策略，请执行以下步骤：

1. 在 Azure 门户中，导航到 Azure Policy 服务。
1. 在“创作”部分下，选择“定义”。
1. 选择“添加策略定义”以创建新的策略定义。
1. 对于“定义位置”字段，选择“更多”按钮以指定审核策略资源所在的位置。
1. 指定策略的名称。 还可以指定说明和类别。
1. 在“策略规则”下，将以下策略定义添加到“policyRule”部分。

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. 保存策略。

### <a name="assign-the-policy"></a>分配策略

接下来，将策略分配给资源。 策略的作用域对应于该资源及其下的所有资源。 有关策略分配的详细信息，请参阅 [Azure Policy 分配结构](../../governance/policy/concepts/assignment-structure.md)。

若要在 Azure 门户中分配策略，请执行下列步骤：

1. 在 Azure 门户中，导航到 Azure Policy 服务。
1. 在“创作”部分下，选择“分配”。
1. 选择“分配策略”以创建新的策略分配。
1. 对于“作用域”字段，请选择策略分配的作用域。
1. 对于“策略定义”字段，请选择“更多”按钮，然后从列表中选择你在上一部分定义的策略。
1. 提供策略分配的名称。 说明是可选的。
1. 让“策略强制实施”设置为“启用”状态。 此设置对审核策略没有影响。
1. 选择“查看 + 创建”以创建分配。

### <a name="view-compliance-report"></a>查看合规性报告

分配策略后，你可以查看合规性报告。 审核策略的合规性报告提供有关哪些存储帐户仍允许跨租户对象复制策略的信息。 有关详细信息，请参阅[获取策略合规性数据](../../governance/policy/how-to/get-compliance-data.md)。

创建策略分配后，合规性报告可能需要几分钟时间才会变得可用。

若要在 Azure 门户中查看合规性报告，请执行以下步骤：

1. 在 Azure 门户中，导航到 Azure Policy 服务。
1. 选择“合规性”。
1. 筛选你在上一步创建的策略分配名称的结果。 该报告显示不符合策略的资源。
1. 你可以在报告中向下钻取以获取更多详细信息，包括不合规的存储帐户的列表。

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/compliance-report-cross-tenant-audit-effect-policy.png" alt-text="显示 blob 跨租户对象复制的审核策略的合规性报告的屏幕截图":::

## <a name="use-azure-policy-to-enforce-same-tenant-replication-policies"></a>使用 Azure Policy 强制实施同租户复制策略

Azure Policy 可以确保 Azure 资源符合要求和标准，从而为云治理提供支持。 若要确保组织中的存储帐户禁止跨租户复制，你可以创建一个策略，阻止创建允许跨租户对象复制策略的新存储帐户。 强制实施策略会使用“拒绝”效果来阻止将创建或修改一个允许跨租户对象复制的存储帐户的请求。 如果一个现有帐户的跨租户对象复制设置不符合策略，则“拒绝”策略还会阻止对该帐户进行的所有配置更改。 有关“拒绝”效果的详细信息，请参阅[了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)。

若要为跨租户对象复制创建具有“拒绝”效果的策略，请执行[使用 Azure Policy 审核合规性](#use-azure-policy-to-audit-for-compliance)中所述的相同步骤，但在策略定义的“policyRule”部分中提供以下 JSON：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

创建具有“拒绝”效果的策略并将其分配给作用域后，用户便无法创建允许跨租户对象复制的存储帐户。 用户也不能对当前允许跨租户对象复制的现有存储帐户进行任何配置更改。 如果尝试这样做，将会导致错误。 必须将存储帐户的 AllowCrossTenantReplication 属性设置为 false，继续进行帐户创建或配置更新，以符合该策略。

下图显示了在以下情况下发生的错误：当具有“拒绝”效果的策略要求禁止跨租户对象复制时，你尝试创建允许跨租户对象复制（针对新帐户的默认设置）的存储帐户。

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-replication-deny-policy-error-portal.png" alt-text="屏幕截图显示了在违反策略的情况下创建存储帐户时出现的错误":::

## <a name="see-also"></a>另请参阅

- [块 blob 的对象复制](object-replication-overview.md)
- [为块 blob 配置对象复制](object-replication-configure.md)
