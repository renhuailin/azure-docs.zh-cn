---
title: 创建合格授权
description: 将客户加入 Azure Lighthouse 时，可让管理租户中的用户即时提升其角色。
ms.date: 06/11/2021
ms.topic: how-to
ms.openlocfilehash: 938b0ae8f2d105d79237164287b00ec4fdf4d607
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060735"
---
# <a name="create-eligible-authorizations"></a>创建合格授权

将客户加入 Azure Lighthouse 时，可以创建授权，以便向管理租户中的用户授予指定的 Azure 内置角色。 还可以创建使用 [Azure Active Directory (Azure AD) Privileged Identity Management (PIM )](../../active-directory/privileged-identity-management/pim-configure.md) 的合格授权，以允许管理租户中的用户暂时提升其角色。 这样，你便可以即时授予其他权限，使用户仅在设定的持续时间内拥有这些权限。

创建合格的授权可以最大程度地减少永久将用户分配到特权角色的次数，帮助降低租户中的用户运用访问特权所带来的安全风险。

本主题介绍合格授权的工作原理，以及在[将客户加入 Azure Lighthouse](onboard-customer.md) 时如何创建此类授权。

> [!IMPORTANT]
> 合格授权目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="license-requirements"></a>许可要求

创建合格授权需要“企业移动性 + 安全性 E5”(EMS E5) 或 Azure AD Premium P2 许可证。 若要根据需要查找合适的许可证，请参阅[比较免费版、基本版和高级版的正式发布功能](https://azure.microsoft.com/pricing/details/active-directory/)。

EMS E5 或 Azure AD Premium P2 许可证必须由管理租户（而不是客户租户）持有。

任何与合格角色相关的额外费用均只会在用户提升其对该角色的访问权限的时段内才产生。

有关用户许可证的信息，请参阅[使用 Privileged Identity Management 所要满足的许可证要求](../../active-directory/privileged-identity-management/subscription-requirements.md)。

## <a name="how-eligible-authorizations-work"></a>合格授权的工作原理

合格授权定义一个角色分配，要求用户在需要执行特权任务时激活该角色。 当用户激活合格角色时，他们将在指定的时段内拥有该角色授予的完全访问权限。

在加入过程之前，客户租户中的用户可以查看所有角色分配，包括合格授权中的角色分配。

一旦用户成功激活合格角色，他们就会在预配置的时段内，在委托的范围拥有该提升的角色，此外，还拥有该范围的永久性角色分配。

管理租户中的管理员可以通过查看管理租户中的审核日志来查看所有 Privileged Identity Management 活动。 客户可以在委托订阅的 Azure 活动日志中查看这些操作。

创建合格授权时，需要定义三个元素：用户、角色和访问策略。

- “用户”可以是管理租户中的单个用户或 Azure AD 组。 如果定义了组，则该组的任何成员都可以根据访问策略提升自己个人对角色的访问权限。 不能将合格授权与服务主体配合使用。
- “角色”可以是 Azure 委托资源管理支持的任何 Azure 内置角色，但“用户访问管理员”除外。
- “访问策略”定义多重身份验证 (MFA) 要求，以及在角色过期之前在该角色中激活用户的时长。 可为任一角色指定的最大时长为 8 小时。

下面详细介绍了这些元素及其定义方式。

## <a name="create-eligible-authorizations-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建合格授权

若要将客户加入 Azure Lighthouse，请使用 [Azure 资源管理器模板以及你修改的相应参数文件](onboard-customer.md#create-an-azure-resource-manager-template)。 所选的模板取决于你是要加入整个订阅，还是要加入订阅中的一个资源组或多个资源组。

> [!NOTE]
> 尽管也可以使用 Azure 市场中的托管服务产品/服务加入客户，但目前无法在这些产品/服务中包含合格授权。

若要在加入客户时包含合格授权，请使用[示例存储库的 delegated-resource-management-eligible-authorizations 节](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations)中提到的模板之一。

|若要加入此项（包含合格授权）  |使用此 Azure 资源管理器模板  |修改此参数文件 |
|---------|---------|---------|
|订阅   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.Parameters.json)    |
|资源组   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.parameters.json)    |
|订阅内的多个资源组   |[multiple-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.parameters.json)    |

可用于加入包含合格授权的订阅的 subscription.json 模板如下所示。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "type": "string",
            "metadata": {
                "description": "Specify a unique name for your offer"
            },
            "defaultValue": "<to be filled out by MSP> Specify a title for your offer"
        },
        "mspOfferDescription": {
            "type": "string",
            "metadata": {
                "description": "Name of the Managed Service Provider offering"
            },
            "defaultValue": "<to be filled out by MSP> Provide a brief description of your offer"
        },
        "managedByTenantId": {
            "type": "string",
            "metadata": {
                "description": "Specify the tenant id of the Managed Service Provider"
            },
            "defaultValue": "<to be filled out by MSP> Provide your tenant id"
        },
        "authorizations": {
            "type": "array",
            "metadata": {
                "description": "Specify an array of objects, containing tuples of Azure Active Directory principalId, a Azure roleDefinitionId, and an optional principalIdDisplayName. The roleDefinition specified is granted to the principalId in the provider's Active Directory and the principalIdDisplayName is visible to customers."
            },
            "defaultValue": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM_Group" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46",
                    "principalIdDisplayName": "PIM_Group" 
                }   
            ]
        }, 
        "eligibleAuthorizations": { 
            "type": "array", 
            "metadata": { 
                "description": "Provide the authorizations that will have just-in-time role assignments on customer environments" 
            },
           "defaultValue": [ 
                { 
                        "justInTimeAccessPolicy": { 
                            "multiFactorAuthProvider": "Azure", 
                            "maximumActivationDuration": "PT8H" 
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "PIM_Group",
                        "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608" 
                        
                }                    
            ]    

        }                 
    },
    "variables": {
        "mspRegistrationName": "[guid(parameters('mspOfferName'))]",
        "mspAssignmentName": "[guid(parameters('mspOfferName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedServices/registrationDefinitions",
            "apiVersion": "2020-02-01-preview",
            "name": "[variables('mspRegistrationName')]",
            "properties": {
                "registrationDefinitionName": "[parameters('mspOfferName')]",
                "description": "[parameters('mspOfferDescription')]",
                "managedByTenantId": "[parameters('managedByTenantId')]",
                "authorizations": "[parameters('authorizations')]", 
                "eligibleAuthorizations": "[parameters('eligibleAuthorizations')]" 
            }
        },
        {
            "type": "Microsoft.ManagedServices/registrationAssignments",
            "apiVersion": "2020-02-01-preview",
            "name": "[variables('mspAssignmentName')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
            ],
            "properties": {
                "registrationDefinitionId": "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
            }
        }
    ],
    
    "outputs": {
        "mspOfferName": {
            "type": "string",
            "value": "[concat('Managed by', ' ', parameters('mspOfferName'))]"
        },
        "authorizations": {
            "type": "array",
            "value": "[parameters('authorizations')]"
        }, 
        "eligibleAuthorizations": { 
            "type": "array", 
            "value": "[parameters('eligibleAuthorizations')]" 

        } 
    }
}
```

### <a name="define-eligible-authorizations-in-your-parameters-file"></a>在参数文件中定义合格授权

加入订阅时，可以使用 [subscription.Parameters.json 示例模板](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.Parameters.json)来定义授权（包括合格授权）。

必须在 `eligibleAuthorizations` 参数中定义每个合格授权。 此示例包含一个合格授权。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Relecloud Managed Services"
        },
        "mspOfferDescription": {
            "value": "Relecloud Managed Services"
        },
        "managedByTenantId": {
            "value": "<insert the managing tenant id>"
        },
        "authorizations": {
            "value": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM group"
                }
            ]
        }, 
        "eligibleAuthorizations":{
            "value": [
                {
                        "justInTimeAccessPolicy": {
                            "multiFactorAuthProvider": "Azure",
                            "maximumActivationDuration": "PT8H"
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "Tier 2 Support",
                        "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"

                }
            ]
        }
    }
}
```

在 `eligibleAuthorizations` 参数中，`principalId` 指定此合格授权要应用到的 Azure AD 用户或组的 ID。 请不要使用服务主体帐户的 ID，因为服务主体帐户目前无法提升其访问权限和使用合格角色。

> [!IMPORTANT]
> 请务必在模板的 `authorizations` 节中包含相同的 `principalId`，并包含一个与合格授权不同的角色，例如“读取者”（或者其他具有读取者访问权限的 Azure 内置角色）。 否则，用户无法在 Azure 门户中提升其角色。

`roleDefinitionId` 包含用户将有资格即时使用的 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)的角色定义 ID。

`justInTimeAccessPolicy` 指定两个元素：

- `multiFactorAuthProvider` 可设置为 Azure（需要使用 Azure 多重授权 (MFA) 进行身份验证）或 None（如果不需要多重身份验证） 。
- `maximumActivationDuration` 设置用户拥有合格角色的总时长。 此值必须使用 ISO 8601 持续时间格式。 最小值为 PT30M（30 分钟），最大值为 PT8H（8 小时）。

> [!NOTE]
> 注意：即时访问权限不适用于可由“用户访问管理员”[分配给托管标识](deploy-policy-remediation.md)的 `delegatedRoleDefinitionIds`。 无法将这些角色分配创建为合格授权。 同样，无法为用户访问管理员角色本身创建合格授权。

## <a name="elevation-process-for-users"></a>用户的提升过程

将客户加入 Azure Lighthouse 后，包含的任何合格角色将可供指定的用户（或任何指定组中的用户）使用。

每个用户随时都可以通过以下方式提升其访问权限：在 Azure 门户中访问“我的客户”页，选择一个委托，然后选择“管理合格角色” 。 此后，他们可以在 Azure AD Privileged Identity Management 中执行[激活角色的步骤](../../active-directory/privileged-identity-management/pim-how-to-activate-role.md)。

:::image type="content" source="../media/manage-eligible-roles.png" alt-text="此屏幕截图显示了 Azure 门户中的“管理合格角色”按钮。":::

激活合格角色后，用户将在合格授权中指定的整个持续时间内拥有该角色。 在该时段过后，除非重复执行提升过程并再次提升其访问权限，否则他们不再可以使用该角色。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 ARM 模板将客户加入 Azure Lighthouse](onboard-customer.md)。
- 详细了解 [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)。
- 详细了解 [Azure Lighthouse 中的租户、用户和角色](../concepts/tenants-users-roles.md)。