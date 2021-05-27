---
title: 将客户加入 Azure Lighthouse
description: 了解如何将客户加入到 Azure Lighthouse，以便能够使用 Azure 委托资源管理通过自己的租户访问和管理该客户的资源。
ms.date: 05/25/2021
ms.topic: how-to
ms.openlocfilehash: 7b64a189fdf6b33fada1750b667260989d2827e8
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376214"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>将客户加入 Azure Lighthouse

本文介绍作为服务提供商如何将客户加入到 Azure Lighthouse。 当你执行此操作时，租户中的用户可以通过 [Azure 委托资源管理](../concepts/architecture.md)来管理客户的 Azure Active Directory (Azure AD) 租户中的委托资源（订阅和/或资源组）。

> [!TIP]
> 虽然我们在本主题中提到的是服务提供商和客户，但[管理多个租户的企业](../concepts/enterprise.md)可以使用相同的过程来设置 Azure Lighthouse 并整合其管理体验。

可以为多个客户重复执行加入过程。 在具有相应权限的用户登录到你的管理租户时，可跨客户租赁范围向该用户授权执行管理操作，使其无需登录到每个单独的客户租户。

若要跟踪你对客户互动的影响并获得认可，请将你的 Microsoft 合作伙伴网络 (MPN) ID 与至少一个有权访问你加入的每个订阅的用户帐户相关联。 需要在服务提供商租户中执行此关联。 建议在租户中创建一个与你的 MPN ID 关联的服务主体帐户，然后在每次加入客户时都包含该服务主体。 有关详细信息，请参阅[链接合作伙伴 ID 以对委托资源启用合作伙伴赚取额度](partner-earned-credit.md)。

> [!NOTE]
> 或者，客户在购买[发布到 Azure 市场](publish-managed-services-offers.md)的托管服务产品/服务（公共或专用）时，也可以加入到 Azure Lighthouse。 还可以结合已发布到 Azure 市场的产品/服务使用此处所述的加入流程。

载入过程要求从服务提供商的租户和客户的租户中执行操作。 上述所有步骤均可参见本文。

## <a name="gather-tenant-and-subscription-details"></a>收集租户和订阅详细信息

要载入客户的租户，必须具备有效的 Azure 订阅。 需了解以下信息：

- 服务提供商租户的租户 ID（要在其中管理客户的资源）
- 客户租户的租户 ID（具有由服务提供商管理的资源）
- 客户租户中由服务提供商管理的每个特定订阅的订阅 ID（或包含将由服务提供商管理的资源组的订阅 ID）。

如果尚未准备好这些 ID 值，可通过以下方式之一进行检索。 确保在部署中使用这些确切的值。

### <a name="azure-portal"></a>Azure 门户

可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来查看租户 ID。 要选择并复制租户 ID，请从门户中搜索“Azure Active Directory”，然后选择“属性”并复制“目录 ID”字段中显示的值 。 要在客户租户中查找订阅 ID，请搜索“订阅”，然后选择相应的订阅 ID。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> 使用此处所述的过程加入订阅（或订阅中的一个或多个资源组）时，将为该订阅注册 **Microsoft.ManagedServices** 资源提供程序。

## <a name="define-roles-and-permissions"></a>定义角色和权限

作为服务提供商，你可能想要为单个客户执行多个任务，这需要针对不同范围的不同访问权限。 可以根据需要定义任意数量的授权，以便分配相应的 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。 每个授权都包含“principalId”，它指的是管理租户中的 Azure AD 用户、组或服务主体。

> [!NOTE]
> 除非显式指定，否则 Azure Lighthouse 文档中对“用户”的提及可能会适用于授权中的 Azure AD 用户、组或服务主体。

为了简化管理，建议尽可能为每个角色都使用 Azure AD 用户组，而不是使用单个用户。 这样，可以灵活地在具有访问权限的组中添加或删除单个用户，从而无需重复加入过程来进行用户更改。 还可以将角色分配给服务主体，这对于自动化方案可能会非常有用。

> [!IMPORTANT]
> 若要为 Azure AD 组添加权限，“组类型”必须设置为“安全”。 此选项是在创建组时选择的。 有关详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

在定义授权时，请务必遵循最低特权原则，使用户只具有完成其工作所需的权限。 有关支持的角色和最佳做法的详细信息，请参阅 [Azure Lighthouse 方案中的租户、用户和角色](../concepts/tenants-users-roles.md)。

> [!TIP]
> 还可以创建合格授权，使管理租户中的用户能够临时提升其角色。 此功能目前以公共预览版提供，具有特定的许可要求。 有关详细信息，请参阅[创建合格授权](create-eligible-authorizations.md)。

如果要定义授权，需要知道服务提供商租户中要向其授予访问权限的每个用户、用户组或服务主体的 ID 值。 还需知道要分配的每个内置角色的角色定义 ID。 如果尚未获得这些 ID，可以从服务提供商租户内运行以下命令来检索它们。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> 我们建议在加入客户时分配[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)，这样租户中的用户之后可以[删除对委派的访问权限](remove-delegation.md)（如果需要）。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

## <a name="create-an-azure-resource-manager-template"></a>创建 Azure 资源管理器模板

若要加入客户，需要使用以下信息为你的产品/服务创建 [Azure 资源管理器](../../azure-resource-manager/index.yml)模板。 客户可以在 Azure 门户的[服务提供商页](view-manage-service-providers.md)中看到“mspOfferName”和“mspOfferDescription”值 。

|字段  |定义  |
|---------|---------|
|**mspOfferName**     |描述此定义的名称。 此值将作为产品/服务的标题显示给客户，并且必须是唯一值。        |
|**mspOfferDescription**     |产品/服务的简短说明（例如“Contoso VM 管理产品/服务”）。      |
|**managedByTenantId**     |租户 ID。          |
|**authorizations**     |租户中用户/组/SPN 的 **principalId** 值，每个值都带有一个 **principalIdDisplayName**（帮助客户了解授权的目的）并且已映射到内置 **roleDefinitionId** 值以指定访问级别。      |

加入过程需要 Azure 资源管理器模板（在[示例存储库](https://github.com/Azure/Azure-Lighthouse-samples/)中提供）以及相应的参数文件（可修改此文件，使其与你的配置匹配并定义你的授权）。

> [!IMPORTANT]
> 此处所述的过程要求为每个正在加入的订阅都单独进行部署，即使是在同一客户租户中加入订阅也是如此。 如果要在同一客户租户中加入不同订阅中的多个资源组，也需要单独部署。 但是，可在一个部署中载入单个订阅中的多个资源组。
>
> 对于应用于同一订阅（或订阅内的资源组）的多个产品/服务，还需要单独部署。 所应用的每个产品/服务必须使用不同的 **mspOfferName**。

所选的模板取决于你是要加入整个订阅，还是要加入订阅中的一个资源组或多个资源组。 我们还提供了一个模板，可供购买了你发布 Azure 市场的托管服务产品/服务的客户使用；如果你偏向于按此方式载入其资源，则可使用它。

|加入此内容  |使用此 Azure 资源管理器模板  |修改此参数文件 |
|---------|---------|---------|
|订阅   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|资源组   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|订阅内的多个资源组   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|订阅（使用发布到 Azure 市场的产品/服务时）   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> 虽然无法在一个部署中加入整个管理组，但可以[在管理组级别部署策略](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups)。 该策略使用 [deployIfNotExists 效果](../../governance/policy/concepts/effects.md#deployifnotexists)检查管理组中的每个订阅是否已委托给指定的管理租户，如果没有，则会基于提供的值来创建分配。 然后，你有权访问该管理组中的所有订阅，尽管必须将其作为单独的订阅进行处理（而不是将该管理组作为一个整体对其执行操作）。

以下示例显示了一个修改后的 **delegatedResourceManagement.parameters.json** 文件，该文件可用于加入订阅。 资源组参数文件（位于 [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) 文件夹）很类似，但还带有一个 rgName 参数，它用于标识要载入的特定资源组。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "00000000-0000-0000-0000-000000000000"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

上面示例中的最后一个授权添加了具有用户访问管理员角色 (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) 的 principalId。 在分配此角色时，必须包含“delegatedRoleDefinitionIds”属性以及一个或多个受支持的 Azure 内置角色。 在此授权中创建的用户将能够在客户租户中将这些角色分配给[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，这是[部署可修正的策略](deploy-policy-remediation.md)需要执行的操作。  用户还可以创建支持事件。 通常与用户访问管理员角色关联的其他权限均不适用于此“principalId”。

## <a name="deploy-the-azure-resource-manager-templates"></a>部署 Azure 资源管理器模板

在更新了参数文件后，客户租户中的用户必须在其租户中部署 Azure 资源管理器模板。 每个要加入的订阅（或者，每个包含要加入的资源组的订阅）都需要单独进行部署。

> [!IMPORTANT]
> 此部署必须由客户租户中的非来宾帐户完成，该帐户对于正在加入的订阅（或包含正在加入的资源组的订阅）拥有具备 `Microsoft.Authorization/roleAssignments/write` 权限的角色，如 [所有者](../../role-based-access-control/built-in-roles.md#owner)。 如果要查找所有可以委托订阅的用户，客户租户中的用户可在 Azure 门户中选择订阅，打开“访问控制 (IAM)”，然后[查看具有所有者角色的所有用户](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。
>
> 如果订阅是通过[云解决方案提供商 (CSP) 计划](../concepts/cloud-solution-provider.md)创建的，则在服务提供商租户中具有[管理员代理](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles)角色的任何用户都可以执行部署。

该部署可以在 Azure 门户中使用 PowerShell 或使用 Azure CLI 来完成，如下所示。

### <a name="azure-portal"></a>Azure 门户

1. 在我们的 [GitHub 存储库](https://github.com/Azure/Azure-Lighthouse-samples/)中，选择要使用的模板旁边显示的“部署到 Azure”按钮。 Azure 门户中会打开模板。
1. 输入“Msp 产品/服务名称”、“Msp 产品/服务描述”、“按租户 Id 管理”和“授权”的值   。 如果需要，可选择“编辑参数”，以在参数文件中直接输入 `mspOfferName`、`mspOfferDescription`、`managedbyTenantId` 和 `authorizations` 的值。 请确保更新这些值，而不是使用模板中的默认值。
1. 依次选择“查看并创建”、“创建” 。

几分钟后，应该会看到已完成部署的通知。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>确认成功载入

在客户订阅成功加入 Azure Lighthouse 后，服务提供商租户中的用户将能够查看订阅及其资源（前提是通过上述过程单独或者作为具有相应权限的 Azure AD 组的成员向这些用户授予了访问此内容的权限）。 要确认此信息，请查看确保订阅以下列方式之一显示。  

### <a name="azure-portal"></a>Azure 门户

在服务提供商的租户中：

1. 导航到[“我的客户”页面](view-manage-customers.md)。
2. 选择“客户”。
3. 确认可使用在资源管理器模板中提供的产品/服务名称查看订阅。

> [!IMPORTANT]
> 如果要在[我的客户](view-manage-customers.md)中查看委托的订阅，在加入订阅时，必须向服务提供商租户中的用户授予[读者](../../role-based-access-control/built-in-roles.md#reader)角色（或其他包括读者访问权限的内置角色）。

在客户的租户中：

1. 导航到[服务提供商页面](view-manage-service-providers.md)。
2. 选择“服务提供商产品/服务”。
3. 确认可使用在资源管理器模板中提供的产品/服务名称查看订阅。

> [!NOTE]
> 在部署完成后，可能最多需要 15 分钟时间才能在 Azure 门户中显示更新。 如果通过刷新浏览器、登录和注销或请求新令牌来更新 Azure 资源管理器令牌，则可以更快看到更新。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

如果在加入了客户之后需要进行更改，可以[更新委托](update-delegation.md)。 还可以完全[删除对委托的访问权限](remove-delegation.md)。

## <a name="troubleshooting"></a>故障排除

如果无法成功加入客户，或者，如果用户在访问委托的资源时遇到问题，请查看以下提示和要求，然后重试。

- `managedbyTenantId` 值不可与正在加入的订阅的租户 ID 相同。
- 在同一范围内，不能有多个具有相同 `mspOfferName` 的分配。
- 必须为委托的订阅注册 Microsoft.ManagedServices 资源提供商。 此注册操作应该会在部署过程中自动发生，但是，如果没有发生，则可以[手动注册](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)。
- 授权不得包含任何具有[所有者](../../role-based-access-control/built-in-roles.md#owner)内置角色的用户或任何具有 [DataActions](../../role-based-access-control/role-definitions.md#dataactions) 的内置角色。
- 必须创建组，并将[组类型](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types)设置为“安全”而不是“Microsoft 365”  。
- 在对[嵌套组](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)启用访问权限之前，可能会有额外的延迟。
- 需要在 Azure 门户中查看资源的用户必须具有[读者](../../role-based-access-control/built-in-roles.md#reader)角色（或其他包含读者访问权限的内置角色）。
- 在授权中包含的 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)不得包含任何已弃用的角色。 如果某个 Azure 内置角色变成被弃用的，那么，任何使用该角色加入的用户都将失去访问权限，并且你将无法加入其他委托。 若要解决此问题，请将模板更新为只使用受支持的内置角色，然后执行新的部署。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)。
- 了解如何[更新](update-delegation.md)或[删除](remove-delegation.md)委托。
