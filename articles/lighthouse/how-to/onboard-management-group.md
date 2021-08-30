---
title: 加入管理组中的所有订阅
description: 可部署 Azure Policy，将管理组中的所有订阅委托给 Azure Lighthouse 管理租户。
ms.date: 08/13/2021
ms.topic: how-to
ms.openlocfilehash: 07f2bb850120b1f27f090aec9d0d3f97d9643d05
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206288"
---
# <a name="onboard-all-subscriptions-in-a-management-group"></a>加入管理组中的所有订阅

借助 [Azure Lighthouse](../overview.md)，可委托订阅和/或资源组，但不能委托[管理组](../../governance/management-groups/overview.md)。 不过，可部署 [Azure Policy](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups)，将管理组中的所有订阅委托给 Azure Lighthouse 管理租户。

该策略使用 [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) 效果检查管理组中的每个订阅是否已委托给指定的管理租户。 如果尚未委托订阅，策略将根据你在参数中提供的值创建 Azure Lighthouse 分配。 然后，你就可以访问管理组中的所有订阅，就如同它们已手动加入一样。

使用此策略时，请记住：

- 管理组中的每个订阅都将有同一组授权。 若要更改获得访问权限的用户和角色，必须手动加入订阅。
- 虽然管理组中的每个订阅都将加入，但无法通过 Azure Lighthouse 对管理组资源执行操作。 需要选择要使用的订阅，就如同它们是单独加入一样。

除非下面另有指定，否则必须由客户租户中具有相应权限的用户执行所有这些步骤。

> [!TIP]
> 尽管本主题中只是提到了服务提供商和客户，但[管理多个租户的企业](../concepts/enterprise.md)也可以使用相同的过程。

## <a name="register-the-resource-provider-across-subscriptions"></a>跨订阅注册资源提供程序

通常，在加入过程中为订阅注册 Microsoft.ManagedServices 资源提供程序。 使用策略加入管理组中的订阅时，必须提前注册资源提供程序。 此操作可由客户租户中的参与者或所有者用户（或有权为资源提供程序执行  `/register/action`  操作的任何用户）执行。 有关详细信息，请参阅 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。

可使用 [Azure 逻辑应用跨订阅自动注册资源提供程序](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-customer)。 可将此逻辑应用部署在客户的租户中，并设置有限的权限，使它能够在管理组的每个订阅中注册资源提供程序。

我们还提供了[可在服务提供商的租户中部署的 Azure 逻辑应用](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-partner)。 此逻辑应用可跨多个租户中的订阅分配资源提供程序，方法是向该逻辑应用[授予租户范围的管理员同意](../../active-directory/manage-apps/grant-admin-consent.md)。 若要授予租户范围的管理员同意，你需要作为经授权代表组织同意的用户登录。 请注意，即使使用此选项来跨多个租户注册提供程序，也必须为每个管理组单独部署策略。

## <a name="create-your-parameters-file"></a>创建参数文件

若要分配策略，需要从示例存储库部署 [deployLighthouseIfNotExistManagementGroup.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistManagementGroup.json) 文件，还需要部署使用特定租户和分配详细信息编辑的 [deployLighthouseIfNotExistsManagementGroup.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistsManagementGroup.parameters.json) 参数文件。 这两个文件包含的详细信息与用于[加入单个订阅](onboard-customer.md)的详细信息相同。

下面的示例显示了一个参数文件，它会将订阅委托给 Relecloud 托管服务租户，并向两个 principalID 授予访问权限：一个用于第 1 层支持，一个自动化帐户，后者可[将 delegateRoleDefinitionIds 分配给客户租户中的托管标识](deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": { 
        "managedByName": { 
            "value": "Relecloud Managed Services" 
        }, 
        "managedByDescription": { 
            "value": "Relecloud provides managed services to its customers" 
        }, 
        "managedByTenantId": { 
            "value": "00000000-0000-0000-0000-000000000000" 
        }, 
        "managedByAuthorizations": { 
            "value": [ 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Tier 1 Support", 
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Automation Account - Full access", 
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9", 
                    "delegatedRoleDefinitionIds": [ 
                        "b24988ac-6180-42a0-ab88-20f7382dd24c", 
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293", 
                        "91c1777a-f3dc-4fae-b103-61d183457e46" 
                    ] 
                }                 
            ] 
        } 
    } 
} 
```

## <a name="assign-the-policy-to-a-management-group"></a>将策略分配给管理组  

编辑策略来创建分配后，可在管理组级别分配该策略。 若要了解如何分配策略和查看符合性状态结果，请参阅 [快速入门：创建策略分配](../../governance/policy/assign-policy-portal.md)。

下面的 PowerShell 脚本演示如何使用所创建的模板和参数文件在指定的管理组下添加策略定义。 你需要为现有订阅创建分配和修正任务。

```azurepowershell-interactive
New-AzManagementGroupDeployment -Name <DeploymentName> -Location <location> -ManagementGroupId <ManagementGroupName> -TemplateFile <path to file> -TemplateParameterFile <path to parameter file> -verbose
```

## <a name="confirm-successful-onboarding"></a>确认成功载入

可通过多种方式确认订阅已成功加入。 有关详细信息，请参阅[确认成功加入](onboard-customer.md#confirm-successful-onboarding)。

如果使管理组的逻辑应用和策略保持活动状态，则添加到管理组中的任何新订阅也将加入。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[将客户加入 Azure Lighthouse](onboard-customer.md)。
- 了解 [Azure Policy](../../governance/policy/index.yml)。
- 了解 [Azure 逻辑应用](../../logic-apps/logic-apps-overview.md)。
