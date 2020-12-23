---
title: 如何在 Azure API 管理策略中使用命名值
description: 了解如何在 Azure API 管理策略中使用命名值。 命名值可以包含文本字符串、策略表达式和存储在 Azure Key Vault 中的机密。
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 12/14/2020
ms.author: apimpm
ms.openlocfilehash: 4cde4dadee33ec1c3f91ab4770dbfe697289cef3
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504726"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>使用 Azure API 管理策略中的命名值

[API 管理策略](api-management-howto-policies.md) 是一项强大的系统功能，允许发布者通过配置更改 API 的行为。 策略是一组语句，在请求或 API 的响应时按顺序执行。 可以使用文字文本值、策略表达式和命名值构造策略语句。

*命名值* 是每个 API 管理实例中名称/值对的全局集合。 对该集合中的项数没有施加限制。 命名值可用于管理所有 API 配置和策略的常量字符串值和机密。 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Azure 门户中的命名值":::

## <a name="value-types"></a>值类型

|类型  |说明  |
|---------|---------|
|普通     |  文本字符串或策略表达式     |
|Secret     |   由 API 管理加密的文本字符串或策略表达式      |
|[密钥保管库](#key-vault-secrets)     |  Azure 密钥保管库中存储的密钥的标识符。      |

纯值或机密可以包含 [策略表达式](./api-management-policy-expressions.md)。 例如，表达式 `@(DateTime.Now.ToString())` 返回包含当前日期和时间的字符串。

有关命名值特性的详细信息，请参阅 API 管理 [REST API 引用](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate)。

## <a name="key-vault-secrets"></a>密钥保管库机密

机密值可以存储为 API 管理中的加密字符串 (自定义机密) 或通过引用 [Azure Key Vault](../key-vault/general/overview.md)中的机密。 

建议使用密钥保管库机密，因为这有助于提高 API 管理安全性：

* 密钥保管库中存储的机密可以在服务之间重复使用
* 精细的 [访问策略](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) 可以应用于机密
* 在密钥保管库中更新的机密会在 API 管理中自动轮替。 更新密钥保管库中的后，API 管理中的命名值在4小时内更新。 

### <a name="prerequisites-for-key-vault-integration"></a>密钥保管库集成的先决条件

1. 有关创建密钥保管库的步骤，请参阅 [快速入门：使用 Azure 门户创建密钥保管库](../key-vault/general/quick-create-portal.md)。
1. 在 API 管理实例中启用系统分配的或用户分配的 [托管标识](api-management-howto-use-managed-service-identity.md) 。
1. 将 [密钥保管库访问策略](../key-vault/general/assign-access-policy-portal.md) 分配给有权从保管库获取和列出机密的托管标识。 添加策略的步骤：
    1. 在门户中导航到你的密钥保管库。
    1. 选择 " **设置" > 访问策略 > "+ 添加访问策略**"。
    1. 选择 " **机密权限**"，然后选择 " **获取** " 和 " **列表**"。
    1. 在 " **选择主体**" 中，选择托管标识的资源名称。 如果你使用系统分配的标识，则主体为你的 API 管理实例的名称。
1. 创建密钥保管库或将其导入到密钥保管库。 请参阅 [快速入门：使用 Azure 门户设置和检索 Azure Key Vault 中的机密](../key-vault/secrets/quick-create-portal.md)。

若要使用密钥保管库机密，请 [添加或编辑命名的值](#add-or-edit-a-named-value)，并指定 **密钥保管库** 的类型。 选择密钥保管库中的机密。

> [!CAUTION]
> 在 API 管理中使用密钥保管库机密时，请注意不要删除密钥、密钥保管库或用于访问密钥保管库的托管标识。

如果在密钥保管库上启用 [Key Vault 防火墙](../key-vault/general/network-security.md) ，以下是使用密钥保管库机密的其他要求：

* 必须使用 API 管理实例的 **系统分配** 的托管标识访问密钥保管库。
* 在 Key Vault 防火墙中，启用 " **允许受信任的 Microsoft 服务跳过此防火墙** " 选项。

如果在虚拟网络中部署了 API 管理实例，还应配置下列网络设置：
* 启用 [服务终结点](../key-vault/general/overview-vnet-service-endpoints.md) ，以便在 API 管理子网上 Azure Key Vault。
* 将网络安全组 (NSG) 规则配置为允许到 AzureKeyVault 和 AzureActiveDirectory [服务标记](../virtual-network/service-tags-overview.md)的出站流量。 

有关详细信息，请参阅 [连接到虚拟网络](api-management-using-with-vnet.md#-common-network-configuration-issues)中的网络配置详细信息。

## <a name="add-or-edit-a-named-value"></a>添加或编辑命名值

### <a name="add-a-key-vault-secret"></a>添加密钥保管库机密

请参阅 [密钥保管库集成的先决条件](#prerequisites-for-key-vault-integration)。

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在 " **api**" 下，选择 "**命名值**  >  **+ 添加**"。
1. 输入 **名称** "标识符"，并输入用于在 "策略" 中引用属性的 **显示名称** 。
1. 在 " **值类型**" 中，选择 **Key vault**。
1. 输入密钥保管库机密的标识符 (不) 版本，或选择 " **选择** " 以从密钥保管库中选择密钥。
    > [!IMPORTANT]
    > 如果自己输入密钥保管库机密标识符，请确保它不包含版本信息。 否则，密钥保管库中的更新后，机密不会自动在 API 管理中轮替。
1. 在 " **客户端标识**" 中，选择系统分配的或现有的用户分配的托管标识。 了解如何 [在 API 管理服务中添加或修改托管标识](api-management-howto-use-managed-service-identity.md)。
    > [!NOTE]
    > 标识需要权限才能获取密钥保管库中的机密并列出这些机密。 如果尚未配置对密钥保管库的访问权限，API 管理会提示你，使其可以使用所需的权限自动配置标识。
1. 添加一个或多个可选标记以帮助组织命名值，然后 **保存**。
1. 选择“创建”  。

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="添加密钥保管库机密值":::

### <a name="add-a-plain-or-secret-value"></a>添加纯值或机密值

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在 " **api**" 下，选择 "**命名值**  >  **+ 添加**"。
1. 输入 **名称** "标识符"，并输入用于在 "策略" 中引用属性的 **显示名称** 。
1. 在 " **值类型**" 中选择 " **普通** " 或 " **机密**"。
1. 在 " **值**" 中，输入字符串或策略表达式。
1. 添加一个或多个可选标记以帮助组织命名值，然后 **保存**。
1. 选择“创建”  。

创建命名值后，可通过选择名称进行编辑。 如果更改显示名称，则引用该命名值的任何策略都将自动更新为使用新的显示名称。

## <a name="use-a-named-value"></a>使用命名值

本节中的示例使用下表中所示的命名值。

| 名称               | 值                      | Secret | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | False  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | False  | 

若要在策略中使用已命名的值，请将其显示名称放在双向大括号（如）中， `{{ContosoHeader}}` 如下面的示例中所示：

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

命名值还可以包含策略表达式。 在下面的示例中， `ExpressionProperty` 使用表达式。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

评估此策略时， `{{ExpressionProperty}}` 会将替换为其值 `@(DateTime.Now.ToString())` 。 由于该值是一个策略表达式，因此会对表达式进行评估并且策略将继续执行。

可以在 Azure 门户或 [开发人员门户](api-management-howto-developer-portal.md) 中测试此操作，方法是调用具有在作用域中具有命名值的策略的操作。 在以下示例中，调用了一个包含两个带命名值的前述示例性 `set-header` 策略的操作。 请注意，响应包含两个自定义标头，这些标头是使用具有命名值的策略配置的。

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="测试 API 响应":::

如果查看包含具有命名值的两个前面的示例策略的调用的出站 [API 跟踪](api-management-howto-api-inspector.md) ，则可以看到两个 `set-header` 策略，其中插入了命名值以及包含策略表达式的命名值的策略表达式计算。

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="API 检查器跟踪":::

> [!CAUTION]
> 如果策略在 Azure Key Vault 中引用了机密，则对启用了 [API 请求跟踪](api-management-howto-api-inspector.md)的订阅的用户可以看到 Key Vault 中的值。

虽然命名值可以包含策略表达式，但不能包含其他命名值。 如果将包含命名值引用的文本用作值（例如 `Text: {{MyProperty}}`），将不会解析和替换该引用。

## <a name="delete-a-named-value"></a>删除命名值

若要删除某个命名值，请选择该名称，然后从上下文菜单中选择 "**删除** **" () "。**

> [!IMPORTANT]
> 如果命名值由任何 API 管理策略引用，则在从使用该命名值的所有策略中删除该命名值之前，不能将其删除。

## <a name="next-steps"></a>后续步骤

-   详细了解如何使用策略
    -   [API 管理中的策略](api-management-howto-policies.md)
    -   [策略参考](./api-management-policies.md)
    -   [策略表达式](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

