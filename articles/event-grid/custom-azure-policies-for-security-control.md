---
title: 针对事件网格资源配置自定义 Azure 策略以增强安全态势
description: 本文帮助你定义自定义的 Azure 策略以强制实施安全控制。
ms.topic: how-to
author: jfggdl
ms.author: jafernan
ms.date: 06/24/2021
ms.openlocfilehash: a9018150d75556f729db593ec1a76fb6aa174798
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992332"
---
# <a name="use-custom-azure-policies-to-enforce-security-controls"></a>使用自定义 Azure 策略强制实施安全控制

本文提供了示例自定义 Azure 策略用于控制可在事件网格的事件订阅中配置的目标。 [Azure Policy](../governance/policy/overview.md) 可帮助你强制实施组织标准和法规遵从性来解除各种忧虑，例如安全性、成本、资源一致性、管理，等等。若要解除这些忧虑，最重要的是实施安全合规标准来帮助维持组织的安全态势。 本文中所述的策略可帮助你实施安全控制，以防止数据渗透或事件被传送给未经授权的终结点或 Azure 服务。

> [!NOTE]
> Azure 事件网格提供合规方面的内置策略，以及与多个合规标准相关的安全控制。 可以在事件网格的 [Azure 安全基准](security-controls-policy.md#azure-security-benchmark)中找到这些内置策略。

为了防止数据渗透，组织可能希望限制事件网格可将事件传送到的目标。 为此，可以分配策略来允许创建或更新包含策略中批准的目标之一的[事件订阅](concepts.md#event-subscriptions)。 用于防止资源请求成功的策略效果为“[拒绝](../governance/policy/concepts/effects.md#deny)”。

以下各部分介绍了强制实施允许的目标列表的示例策略定义。 在定义策略时，可以搜索包含 ```destination``` 的[属性别名](../governance/policy/concepts/definition-structure.md#aliases)，并将其用作要与允许的目标列表进行比较的 ```field```。

可以按照[此文](../governance/policy/concepts/definition-structure.md#aliases)中所述，运行 CLI 或 PowerShell 命令找到为事件网格定义的属性别名（使用命名空间 ```Microsoft.EventGrid```）。

有关定义策略的详细信息，请参阅 [Azure Policy 定义结构](../governance/policy/concepts/definition-structure.md)一文。

 
## <a name="define-an-azure-policy-with-a-list-of-allowed-destinations-for-a-webhook-destination"></a>使用允许的目标列表针对 Webhook 目标定义 Azure 策略

以下策略定义限制系统主题的事件订阅中配置的 Webhook 终结点目标。

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/systemTopics/eventSubscriptions/destination.WebHook.endpointUrl",
        "in": "[parameters('allowedDestinationEndpointURLs')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedDestinationEndpointURLs": {
      "type": "Array",
      "metadata": {
        "description": "Allowed event destination endpoint URLs.",
        "displayName": "The list of allowed webhook endpoint destinations to which send events"
      },
        "allowedValues": [
          "https://www.your-valid-destination-goes-here-1.com",
          "https://www.your-valid-destination-goes-here-2.com",
          "https://www.your-valid-destination-goes-here-3.com"
        ]
    }
  }
}
```

## <a name="define-an-azure-policy-with-a-list-of-allowed-azure-service-resource-destinations"></a>使用允许的 Azure 服务资源目标列表定义 Azure 策略

以下策略定义限制自定义主题的事件订阅中配置的特定事件中心目标。 对于其他类型的[受支持 Azure 服务目标](event-handlers.md)，可以采用类似的方法。

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/eventSubscriptions/destination.EventHub.resourceId",
        "in": "[parameters('allowedResourceDestinations')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedResourceDestinations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed event delivery destinations.",
        "displayName": "Allowed event delivery destinations"
      },
        "allowedValues": [
          "/subscriptions/<your-event-subscription>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<event-hubs-namespace-name>/eventhubs/<your-event-hub-name>"
        ]
    }
  }
}
```

## <a name="next-steps"></a>后续步骤
- 若要详细了解 Azure Policy，请参阅以下文章： 
    - [什么是 Azure Policy？](../governance/policy/overview.md)
    - [Azure Policy 定义结构](../governance/policy/concepts/definition-structure.md)。
    - [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)。
    - [了解 Azure Policy 中的范围](../governance/policy/concepts/scope.md)。
    - [使用适用于 Visual Studio Code 的 Azure Policy 扩展](../governance/policy/how-to/extension-for-vscode.md)。
    - [以编程方式创建的策略](../governance/policy/how-to/programmatically-create.md)。
- 若要详细了解 Azure 事件网格，请参阅“概念”部分下的文章，例如[事件网格术语](concepts.md)。