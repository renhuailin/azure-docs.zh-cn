---
title: 排查在 Key Vault 上实现 Azure Policy 的问题
description: 排查在 Key Vault 上实现 Azure Policy 的问题
author: sebansal
ms.author: sebansal
ms.date: 08/17/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 8b6084f411ec948eb7655c5c7c6b54bf7d2e2c30
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858974"
---
# <a name="troubleshooting-issues-with-implementing-azure-policy-on-key-vault"></a>排查在 Key Vault 上实现 Azure Policy 的问题

本文指导如何排查在设置 [Key Vault 的 Azure Policy](./azure-policy.md) 时可能发生的一般错误，并推荐解决这些错误的方法。

## <a name="about-azure-policy-for-key-vault"></a>关于 Key Vault 的 Azure Policy

[Azure Policy](../../governance/policy/index.yml) 是一种管理工具，使用户能够大规模审核和管理其 Azure 环境。 Azure Policy 提供将护栏放置在 Azure 资源上的功能，以确保它们符合分配的策略规则。 它允许用户对其 Azure 环境执行审核、实时强制和修正。 策略执行的审核的结果将显示在合规性仪表板中，用户可以在其中查看合规和不合规的资源和组件的明细。

### <a name="logging"></a>日志记录

为监视策略评估的执行方式，用户可以查看 Key Vault 日志。 为此，可以启用 Azure Key Vault 日志记录，以便在提供的 Azure 存储帐户中保存信息。 有关设置此操作的分步指南，请参阅[如何启用 Key Vault 日志记录](howto-logging.md)。

启用记录时，将自动创建名为 AzurePolicyEvaluationDetails 的新容器，以收集指定存储帐户中与策略相关的记录信息。 

> [!NOTE]
> 应严格控制对监视数据（尤其是日志文件）的访问，因为它们可能包含敏感信息。 了解如何应用[内置监视 Azure 角色](../../azure-monitor/roles-permissions-security.md)和限制访问。
> 
> 

每个 Blob 存储为文本，并格式化为 JSON Blob。 我们来看一个密钥策略的日志条目示例：[密钥应设置到期日期](azure-policy.md?tabs=keys#secrets-should-have-expiration-date-set-preview)。 此策略评估密钥保管库中的所有密钥，并将未设置到期日期的密钥标记为不合规。

```json
{
  "ObjectName": "example",
  "ObjectType": "Key",
  "IsComplianceCheck": false,
  "EvaluationDetails": [
    {
      "AssignmentId": "<subscription ID>",
      "AssignmentDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "DefinitionId": "<definition ID>",
      "DefinitionDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "Outcome": "NonCompliant",
      "ExpressionEvaluationDetails": [
        {
          "Result": "True",
          "Expression": "type",
          "ExpressionKind": "Field",
          "ExpressionValue": "Microsoft.KeyVault.Data/vaults/keys",
          "TargetValue": "Microsoft.KeyVault.Data/vaults/keys",
          "Operator&quot;: &quot;Equals"
        },
        {
          "Result": "True",
          "Expression": "Microsoft.KeyVault.Data/vaults/keys/attributes.expiresOn",
          "ExpressionKind": "Field",
          "ExpressionValue": "******",
          "TargetValue": "False",
          "Operator&quot;: &quot;Exists"
        }
      ]
    }
  ]
}
```

下表列出了字段的名称和描述：

| 字段名称 | 说明 |
| --- | --- |
| **ObjectName** |对象的名称 |
| **ObjectType** |密钥保管库对象的类型，即证书、机密或密钥 |
| IsComplianceCheck |如果在夜间审核期间发生评估，则为 true；如果在创建或更新资源期间发生评估，则为 false |
| **结果** | 返回策略评估 |
| ExpressionEvaluationDetails | 有关评估字段、表达式值的详细信息 |


### <a name="frequently-asked-questions"></a>常见问题

#### <a name="key-vault-recovery-blocked-by-azure-policy"></a>Azure Policy 阻止了 Key Vault 恢复

其中一个原因可能是订阅（或管理组）具有阻止恢复的策略。 解决方法是调整该策略，使其在恢复保管库时不应用。

如果在恢复时看到因内置策略导致的错误类型 ```RequestDisallowedByPolicy```，请确保使用最新的版本。  

如果使用自己的逻辑创建了自定义策略，下面是可用于要求软删除的策略的部分示例。 恢复软删除保管库时使用的 API 与创建或更新保管库所使用的 API 相同。 但是，它包含一个值为“recover”的“createMode”属性，无需指定保管库的属性。 保管库将使用删除时所拥有的任何属性进行还原。 除非配置了特定属性，否则用于阻止请求的策略也会阻止恢复软删除的保管库。 解决方法是包含一个子句，该子句将使策略忽略“createMode”是“recover”的请求：

将会看到策略有一个子句，该子句使策略仅在“createMode”不等于“recover”时应用：

```

    "policyRule": { 
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals&quot;: &quot;Microsoft.KeyVault/vaults"
          }, 
          {
            "not": {
              "field": "Microsoft.Keyvault/vaults/createMode",
              "equals&quot;: &quot;recover"
            }
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "exists&quot;: &quot;false"
              },
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "equals&quot;: &quot;false"
              }
            ]
          }
        ]
      },
      "then": {
        "effect&quot;: &quot;[parameters('effect')]"
      }
    }
```

#### <a name="latency-on-azure-policy-assignment-delete-on-key-vault"></a>Key Vault 中的 Azure Policy 分配删除延迟 

Microsoft.KeyVault.Data：删除的策略分配可能需要 24 小时才能停止强制执行。 

缓解：将策略分配的效果更新为“已禁用”。


#### <a name="secret-creation-via-arm-template-missing-out-policy-evaluation"></a>通过 ARM 模板创建机密缺少策略评估

用于评估创建机密的数据平面策略不适用于在创建机密时[通过 ARM 模板创建的机密](../secrets/quick-create-template.md?tabs=CLI)。 24 小时后，将进行自动合规性检查，并可以查看合规性结果。


## <a name="next-steps"></a>后续步骤

* 了解如何[排查 Azure Policy 使用错误](../../governance/policy/troubleshoot/general.md)
* 了解 [Azure Policy 已知问题](https://github.com/azure/azure-policy#known-issues)
