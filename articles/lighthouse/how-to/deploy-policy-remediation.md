---
title: 部署可修正的策略
description: 若要通过 Azure Lighthouse 部署使用修正任务的策略，需要在客户租户中创建托管标识。
ms.date: 09/13/2021
ms.topic: how-to
ms.openlocfilehash: 2270644a2d3e841a40046743bd6092a3ba44105d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611446"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>部署可以在委派的订阅中修正的策略

服务提供商可以通过 [Azure Lighthouse](../overview.md) 在委托订阅中创建和编辑策略定义。 但是，若要部署使用[修正任务](../../governance/policy/how-to/remediate-resources.md)的策略（即，具有 [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) 或 [modify](../../governance/policy/concepts/effects.md#modify) 效果的策略），需在客户租户中创建[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 此托管标识可供 Azure Policy 用来在策略中部署模板。 在载入 Azure Lighthouse 的客户时以及部署策略本身时，需要执行相关步骤来启用此方案。

> [!TIP]
> 尽管本主题中只是提到了服务提供商和客户，但[管理多个租户的企业](../concepts/enterprise.md)也可以使用相同的过程。

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>创建一个可以在客户租户中将角色分配给托管标识的用户

将客户载入到 Azure Lighthouse 时，将使用 [Azure 资源管理器模板](onboard-customer.md#create-an-azure-resource-manager-template)以及参数文件来定义授予对客户租户中委派资源访问权限的授权。 每个授权都将指定一个 principalId（对应于管理租户中的 Azure AD 用户、组或服务主体）和 roleDefinitionId（对应于将被授予的 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)）。

若要允许 **principalId** 在客户租户中创建托管标识，必须将其 **roleDefinitionId** 设置为“用户访问管理员”。 虽然此角色通常不受支持，但可以将它用在此特定方案中，允许具有此权限的用户帐户将一个或多个特定的内置角色分配给托管标识。 这些角色在 delegatedRoleDefinitionIds 属性中定义，可以包括除“用户访问管理员”或“所有者”之外的任何[受支持的 Azure 内置角色](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)。

将客户加入以后，在此授权中创建的 **principalId** 即可将这些内置角色分配给客户租户中的托管标识。 但是，他们不会有通常与“用户访问管理员”角色关联的任何其他权限。

下面的示例显示了一个 **principalId**，该 ID 将有“用户访问管理员”角色。 此用户可将两个内置角色分配给客户租户中的托管标识：参与者和 Log Analytics 参与者。

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>部署可修正的策略

按照上述说明创建具有必需权限的用户后，该用户就可以在委派的客户订阅中部署使用修正任务的策略。

例如，假设你想在客户租户的 Azure Key Vault 资源上启用诊断，如此[示例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring)所示。 管理租户中具有适当权限（如上所述）的用户会部署 [Azure 资源管理器模板](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json)来启用此方案。

请注意，目前必须通过 API 而不是 Azure 门户创建与委托订阅配合使用的策略分配。 这样做时，必须将 **apiVersion** 设置为 **2019-04-01-preview**，其中包含新的 **delegatedManagedIdentityResourceId** 属性。 此属性允许包括驻留在客户租户中的托管标识（该租户位于已载入到 Azure Lighthouse 的订阅或资源组中）。

以下示例显示具有 **delegatedManagedIdentityResourceId** 的角色分配。

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> 这里有[类似的示例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag)，该示例演示如何部署策略，以便为委托的订阅添加或删除标记（使用 modify 效果）。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Policy](../../governance/policy/index.yml)。
- 了解 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
