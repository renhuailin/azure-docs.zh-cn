---
title: 在 Azure 安全中心创建自定义安全策略 | Microsoft Docs
description: 受 Azure 安全中心监视的 Azure 自定义策略定义。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: 828dd2cc5b83737a313156876c54c290a2a67d73
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112238834"
---
# <a name="create-custom-security-initiatives-and-policies"></a>创建自定义安全计划和策略

为了帮助保护系统和环境，Azure 安全中心会生成安全建议。 这些建议基于行业最佳做法，并已合并在提供给所有客户的通用默认安全策略中。 此外，还可能会基于安全中心内有关行业和法规标准的知识来提供建议。

你可以利用此功能添加自己的自定义计划。 如果环境未遵循所创建的策略，则你会收到建议。 你创建的任何自定义计划将连同内置计划一起显示在法规符合性仪表板中，如教程[改善法规符合性](security-center-compliance-dashboard.md)中所述。

如 [Azure Policy 文档](../governance/policy/concepts/definition-structure.md#definition-location)中所述，当你为自定义计划指定位置时，该位置必须是某个管理组或订阅。 

> [!TIP]
> 有关本页关键概念的概述，请参阅[什么是安全策略、计划和建议？](security-policy-concept.md)。

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>将自定义计划添加到订阅 

1. 在安全中心的边栏中，打开“安全策略”页。

1. 选择要将自定义计划添加到的订阅或管理组。

    [![选择要为其创建自定义策略的订阅。](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 必须在订阅级别（或更高级别）添加自定义计划，才能在安全中心评估并显示它们。 建议选择最广泛的可用范围。

1. 在“安全策略”页中的“你的自定义计划”下，单击“添加自定义计划”。

    [![单击“添加自定义计划”。](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    此时会显示以下页：

    ![创建或添加策略。](media/custom-security-policies/create-or-add-custom-policy.png)

1. 在“添加自定义计划”页中，复查已在组织中创建的自定义策略的列表。 如果看到了你要分配到订阅的某个策略，请单击“添加”。 如果列表中没有符合需要的计划，请跳过此步骤。

1. 若要创建新的自定义计划，请执行以下操作：

    1. 单击“新建”。
    1. 输入定义的位置和名称。
    1. 选择要包含的策略，然后单击“添加”。
    1. 输入任何所需参数。
    1. 单击“ **保存**”。
    1. 在“添加自定义计划”页中，单击“刷新”。 新计划将显示为可用。
    1. 单击“添加”并将其分配到订阅。

    > [!NOTE]
    > 创建新计划需要使用订阅所有者凭据。 有关 Azure 角色的详细信息，请参阅 [Azure 安全中心内的权限](security-center-permissions.md)。

    新计划将会生效，你可以发现以下两个方面的影响：

    * 在安全中心边栏中的“策略和符合性”下，选择“法规符合性”。 符合性仪表板将会打开，其中显示了新的自定义计划以及内置计划。
    
    * 如果环境未遵循你已定义的策略，则你会开始收到建议。

1. 若要查看针对策略生成的建议，请在边栏中单击“建议”打开“建议”页。 显示的建议带有“自定义”标签，在大约一小时内会一直显示。

    [![自定义建议。](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>使用 REST API 在 Azure Policy 中配置安全策略

Azure 安全中心与 Azure Policy 实现了本机集成，借助它，可以利用 Azure Policy 的 REST API 来创建策略分配。 以下说明演示如何创建策略分配以及如何自定义现有的分配。 

Azure Policy 中的重要概念： 

- **策略定义** 是一种规则 

- **计划** 是策略定义（规则）的集合 

- **分配** 是将计划或策略应用于特定的范围（管理组、订阅等） 

安全中心有一项内置计划（[Azure 安全基准](/security/benchmark/azure/introduction)），它包括中心内的所有安全策略。 要评估对 Azure 资源的安全中心策略，应对管理组或希望评估的订阅创建一个分配。

内置计划默认启用所有安全中心策略。 可以选择禁用内置计划中的某些策略。 例如，若要应用除 Web 应用程序防火墙之外的所有安全中心策略，请将策略的效果参数的值更改为“禁用” 。

## <a name="api-examples"></a>API 示例

在下面的示例中，替换以下三个变量：

- {scope}，用于输入要应用策略的管理组或订阅的名称
- {policyAssignmentName}，用于输入相关策略分配的名称
- {name}，用于输入你的名字或批准策略更改的管理员的名字

本示例演示如何对订阅或管理组分配内置的安全中心计划
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

本示例演示如何对订阅分配内置的安全中心计划，且禁用以下策略： 

- 系统更新 ("systemUpdatesMonitoringEffect") 

- 安全配置 ("systemConfigurationsMonitoringEffect") 

- 终结点保护 ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
此示例演示如何删除分配：
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>利用详细信息增强自定义建议

Azure 安全中心提供的内置建议包括严重性级别和修正说明等详细信息。 如果要将此类信息添加到自定义建议，使其显示在 Azure 门户中或任何可供访问建议的位置，则需要使用 REST API。 

可添加的两种类型的信息为：

- **RemediationDescription** – 字符串
- **Severity** – 枚举 [Low, Medium, High]

对于属于自定义计划一部分的策略，应将元数据添加到策略定义中。 应将其包含在“securityCenter”属性中，如下所示：

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

下面是包含 metadata/securityCenter 属性的自定义策略示例：

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

有关使用 securityCenter 属性的另一个示例，请参阅 [REST API 文档的此部分](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)。


## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何创建自定义安全策略。 

其他相关材料，请参阅以下文章： 

- [安全策略概述](tutorial-security-policy.md)
- [内置安全策略列表](./policy-reference.md)