---
title: 如何在 Azure API 管理策略中使用命名值
description: 了解如何在 Azure API 管理策略中使用命名值。 命名值可以包含文本字符串、策略表达式和存储在 Azure Key Vault 中的机密。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: danlep
ms.openlocfilehash: 65f45758ab00d9e549ac0e52287a198bf5734acd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671196"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>在 Azure API 管理策略中使用命名值

[API 管理策略](api-management-howto-policies.md)是一项强大的系统功能，允许发布者通过配置更改 API 的行为。 策略是一组语句，在请求或 API 的响应时按顺序执行。 可以使用文字文本值、策略表达式和命名值构造策略语句。

“命名值”是每个 API 管理实例中的名称/值对的全局集合。 对该集合中的项数没有施加限制。 命名值可以用来管理所有 API 配置和策略中的常量字符串值和机密。 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Azure 门户中的命名值":::

## <a name="value-types"></a>值类型

|类型  |说明  |
|---------|---------|
|普通     |  文本字符串或策略表达式     |
|机密     |   由 API 管理加密的文本字符串或策略表达式      |
|[密钥保管库](#key-vault-secrets)     |  某个 Azure 密钥保管库中存储的机密的标识符。      |

明文值或机密可以包含[策略表达式](./api-management-policy-expressions.md)。 例如，表达式 `@(DateTime.Now.ToString())` 返回包含当前日期和时间的一个字符串。

有关命名值特性的详细信息，请参阅 API 管理 [REST API 参考](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)。

## <a name="key-vault-secrets"></a>密钥保管库机密

机密值可以存储为 API 管理中的加密字符串（自定义机密），也可以通过引用 [Azure Key Vault](../key-vault/general/overview.md) 中的机密进行存储。 

建议使用密钥保管库机密，因为它有助于提高 API 管理安全性：

* 密钥保管库中存储的机密可以在服务之间重复使用
* 可以为机密应用精细[访问策略](../key-vault/general/security-features.md#privileged-access)
* 在密钥保管库中更新的机密会自动在 API 管理中轮换。 在密钥保管库中更新后，API 管理中的命名值会在 4 小时内更新。 你还可以使用 Azure 门户或通过管理 REST API 手动刷新机密。

### <a name="prerequisites-for-key-vault-integration"></a>密钥保管库集成的先决条件

1. 有关创建密钥保管库的步骤，请参阅[快速入门：使用 Azure 门户创建密钥保管库](../key-vault/general/quick-create-portal.md)。
1. 在 API 管理实例中启用系统分配的或用户分配的[托管标识](api-management-howto-use-managed-service-identity.md)。
1. 将一个[密钥保管库访问策略](../key-vault/general/assign-access-policy-portal.md)分配给托管标识，该策略提供从保管库获取和列出机密的权限。 若要添加策略，请执行以下操作：
    1. 在门户中导航到你的密钥保管库。
    1. 选择“设置”>“访问策略”>“+添加访问策略”。
    1. 选择“机密权限”，然后选择“获取”和“列出”。 
    1. 在“选择主体”中，选择你的托管标识的资源名称。 如果你使用系统分配的标识，则主体为你的 API 管理实例的名称。
1. 在密钥保管库中创建或导入机密。 请参阅[快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索机密](../key-vault/secrets/quick-create-portal.md)。

若要使用密钥保管库机密，请[添加或编辑命名值](#add-or-edit-a-named-value)，并将类型指定为“密钥保管库”。 从密钥保管库中选择机密。

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>添加或编辑命名值

### <a name="add-a-key-vault-secret"></a>添加密钥保管库机密

请参阅[密钥保管库集成的先决条件](#prerequisites-for-key-vault-integration)。

> [!CAUTION]
> 在 API 管理中使用密钥保管库机密时，请注意不要删除机密、密钥保管库或用于访问密钥保管库的托管标识。

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在“API”下，选择“命名值” > “+添加”。 
1. 输入一个“名称”标识符，并输入用来在策略中引用此属性的“显示名称”。
1. 在“值类型”中，选择“密钥保管库”。
1. 输入密钥保管库机密的标识符（不含版本），或选择“选择”来选择密钥保管库中的机密。
    > [!IMPORTANT]
    > 如果你自己输入密钥保管库机密标识符，请确保它不包含版本信息。 否则，在密钥保管库中进行更新后，机密在 API 管理中不会自动轮换。
1. 在“客户端标识”中，选择一个系统分配的托管标识，或一个现有的用户分配的托管标识。 了解如何[在 API 管理服务中添加或修改托管标识](api-management-howto-use-managed-service-identity.md)。
    > [!NOTE]
    > 标识需要具有获取和列出密钥保管库中机密的权限。 如果你尚未配置对密钥保管库的访问权限，则 API 管理会提示你，你可以让其自动为标识配置必要的权限。
1. 添加一个或多个可帮助你组织命名值的可选标记，然后单击“保存”。
1. 选择“创建”  。

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="添加密钥保管库机密值":::

### <a name="add-a-plain-or-secret-value"></a>添加明文或机密值

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在“API”下，选择“命名值” > “+添加”。 
1. 输入一个“名称”标识符，并输入用来在策略中引用此属性的“显示名称”。
1. 在“值类型”中，选择“明文”或“机密”。
1. 在“值”中，输入一个字符串或策略表达式。
1. 添加一个或多个可帮助你组织命名值的可选标记，然后单击“保存”。
1. 选择“创建”  。

创建命名值后，可以通过选择名称对其进行编辑。 如果你更改显示名称，系统会自动更新引用该命名值的任何策略，以使用新的显示名称。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

开始使用 Azure CLI：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

若要添加命名值，请使用 [az apim nv create](/cli/azure/apim/nv#az_apim_nv_create) 命令：

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

创建命名值后，可使用 [az apim nv update](/cli/azure/apim/nv#az_apim_nv_update) 命令对其进行更新。 若要查看所有命名值，请运行 [az apim nv list](/cli/azure/apim/nv#az_apim_nv_list) 命令：

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

若要查看为此示例创建的命名值的详细信息，请运行 [az apim nv show](/cli/azure/apim/nv#az_apim_nv_show) 命令：

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

此示例是一个机密值。 上一个命令不返回值。 若要查看该值，请运行 [az apim nv show-secret](/cli/azure/apim/nv#az_apim_nv_show_secret) 命令：

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

若要删除命名值，请使用 [az apim nv delete](/cli/azure/apim/nv#az_apim_nv_delete) 命令：

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>使用命名值

本部分的示例使用下表中显示的命名值。

| 名称               | 值                      | 机密 | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | False  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | False  | 

若要在策略中使用某个命名值，请将其显示名称包含在一对双大括号中（例如 `{{ContosoHeader}}`），如以下示例所示：

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

在此示例中，`ContosoHeader` 用作 `set-header` 策略中一个标头的名称，`ContosoHeaderValue` 用作该标头的值。 在请求或响应 API 管理网关的过程中，如果要对该策略进行评估，则会将 `{{ContosoHeader}}` 和 `{{ContosoHeaderValue}}` 替换为各自的值。

命名值可以用作完整的特性或元素值（如前面的示例所示），但也可插入到文字文本表达式中或与该表达式的一部分组合在一起，如以下示例所示： 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

命名值还可以包含策略表达式。 以下示例使用了 `ExpressionProperty` 表达式。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

评估此策略时，会将 `{{ExpressionProperty}}` 替换为其值 `@(DateTime.Now.ToString())`。 由于该值是一个策略表达式，因此会对表达式进行评估并且策略将继续执行。

若要在 Azure 门户或[开发人员门户](api-management-howto-developer-portal.md)中对此进行测试，可调用其策略包含的命名值处于范围内的一个操作。 在以下示例中，调用了一个包含两个带命名值的前述示例性 `set-header` 策略的操作。 请注意，响应包含两个自定义标头，这两个标头是使用带命名值的策略配置的。

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="测试 API 响应":::

在出站 [API 跟踪](api-management-howto-api-inspector.md)中，如果查看包含前面两个示例策略（带命名值）的调用，则可以看到两个 `set-header` 策略，这两个策略已插入了命名值，并已针对包含策略表达式的命名值进行了策略表达式计算。

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API 检查器跟踪":::

> [!CAUTION]
> 如果策略引用了 Azure Key Vault 中的机密，则对允许进行 [API 请求跟踪](api-management-howto-api-inspector.md)的订阅具有访问权限的用户将能够查看该密钥保管库中的值。

虽然命名值可以包含策略表达式，但不能包含其他命名值。 如果将包含命名值引用的文本用作值（例如 `Text: {{MyProperty}}`），将不会解析和替换该引用。

## <a name="delete-a-named-value"></a>删除命名值

若要删除某个命名值，请选择该名称，然后从上下文菜单 (...) 中选择“删除”。

> [!IMPORTANT]
> 如果有任何 API 管理策略引用了该命名值，则无法删除它，除非将它从所有使用它的策略中删除。

## <a name="next-steps"></a>后续步骤

-   详细了解如何使用策略
    -   [API 管理中的策略](api-management-howto-policies.md)
    -   [策略参考](./api-management-policies.md)
    -   [策略表达式](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

