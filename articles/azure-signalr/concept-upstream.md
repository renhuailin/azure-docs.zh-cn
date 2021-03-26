---
title: Azure SignalR 服务中的上游设置
description: 介绍了上游设置和上游消息的协议。
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 6752a9564dc0d9351d1c21f5be14eb626186ac0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724049"
---
# <a name="upstream-settings"></a>上游设置

Upstream 是一项预览功能，它允许 Azure SignalR 服务以无服务器模式将消息和连接事件发送到一组终结点。 你可以使用上游在无服务器模式下从客户端调用某个中心方法，并让终结点在客户端建立连接或断开连接时获得通知。

> [!NOTE]
> 只有无服务器模式可以配置上游设置。

## <a name="details-of-upstream-settings"></a>上游设置的详细信息

上游设置由一系列顺序敏感项组成。 每个项都包含：

* 一个 URL 模板，它指定将消息发送到的位置。
* 一组规则。
* 身份验证配置。 

当发生指定的事件时，将按顺序逐个检查项的规则。 消息将发送到第一个匹配项的上游 URL。

### <a name="url-template-settings"></a>URL 模板设置

可以将 URL 参数化以支持各种模式。 有三个预定义的参数：

|预定义的参数|说明|
|---------|---------|
|{hub}| 中心是 Azure SignalR 服务的一个概念。 中心是一个隔离单元。 用户和消息传递的作用域被限定为某个中心。|
|{category}| 类别可以是下列值之一： <ul><li>**连接**：连接生存期事件。 它在客户端建立连接或断开连接时引发。 它包括“已连接”事件和“已断开连接”事件。</li><li>**消息**：当客户端调用某个中心方法时引发。 它包含除“连接”类别中事件之外的所有其他事件。</li></ul>|
|{event}| 对于“消息”类别，事件是客户端发送的[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目标。 对于“连接”类别，只使用“已连接”和“已断开连接” 。|

这些预定义参数可用于 URL 模式。 在评估上游 URL 时，参数将替换为某个指定的值。 例如： 
```
http://host.com/{hub}/api/{category}/{event}
```
当“聊天”中心内建立了某个客户端连接时，会向以下 URL 发送消息：
```
http://host.com/chat/api/connections/connected
```
当“聊天”中心内的客户端调用中心方法 `broadcast` 时，会向以下 URL 发送消息：
```
http://host.com/chat/api/messages/broadcast
```

### <a name="key-vault-secret-reference-in-url-template-settings"></a>URL 模板设置中的 Key Vault 机密引用

上游的 URL 不会静态加密。 如果有任何敏感信息，建议使用 Key Vault 将其保存在访问控制更为安全的地方。 基本上可以启用 Azure SignalR 服务的托管标识，然后授予对 Key Vault 实例的读取权限，并在上游 URL 模式中使用 Key Vault 引用而不是纯文本。

1. 添加系统分配的标识或用户分配的标识。 请参阅[如何在 Azure 门户中添加托管标识](./howto-use-managed-identity.md#add-a-system-assigned-identity)

2. 在 Key Vault 的访问策略中授予对托管标识的机密读取权限。 请参阅[使用 Azure 门户分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-portal.md)

3. 在上游 URL 模式中将敏感文本替换为语法 `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}`。

> [!NOTE]
> 仅在更改上游设置或托管标识时，机密内容才会重新读取。 使用 Key Vault 机密引用之前，请确保你已授予对托管标识的机密读取权限。

### <a name="rule-settings"></a>规则设置

你可以分别为“中心规则”、“类别规则”和“事件规则”设置规则。 匹配规则支持三种格式。 以事件规则为例：
- 使用星号 (*) 来匹配任何事件。
- 使用逗号 (,) 来联接多个事件。 例如，`connected, disconnected` 匹配“已连接”和“已断开连接”事件。
- 使用完整的事件名称来匹配事件。 例如，`connected` 匹配“已连接”事件。

> [!NOTE]
> 如果使用 Azure Functions 和 [SignalR 触发器](../azure-functions/functions-bindings-signalr-service-trigger.md)，则 SignalR 触发器将按以下格式公开单个终结点：`<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>`。
> 只需为此 URL 配置“URL 模板设置”，并将“规则设置”保留为默认值 。 有关如何查找 `<Function_App_URL>` 和 `<API_KEY>` 的详细信息，请参阅 [SignalR 服务集成](../azure-functions/functions-bindings-signalr-service-trigger.md#signalr-service-integration)。

### <a name="authentication-settings"></a>身份验证设置

你可以分别为每个上游设置项配置身份验证。 配置身份验证时，会在上游消息的 `Authentication` 标头中设置令牌。 目前，Azure SignalR 服务支持以下身份验证类型：
- `None`
- `ManagedIdentity`

选择 `ManagedIdentity` 时，必须提前在 Azure SignalR 服务中启用托管标识，可以指定资源。 有关详细信息，请参阅 [Azure SignalR 服务的托管标识](howto-use-managed-identity.md)。

## <a name="create-upstream-settings-via-the-azure-portal"></a>通过 Azure 门户创建上游设置

1. 转到“Azure SignalR 服务”。
2. 选择“设置”并将“服务模式”切换到“无服务器”。 此时会显示上游设置：

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="上游设置":::

3. 在“上游 URL 模式”下添加 URL。 然后，设置（如“中心规则”）会显示默认值。
4. 若要设置“中心规则”、“事件规则”、“类别规则”和“上游身份验证”的设置，请选择“中心规则”的值。     此时会显示一个页面，你可以在其中编辑设置：

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Upstream 设置详细信息":::

5. 若要设置“上游身份验证”，请确保已先启用托管标识。 然后选择“使用托管标识”。 可以根据需要选择“身份验证资源 ID”下的任何选项。 有关详细信息，请参阅 [Azure SignalR 服务的托管标识](howto-use-managed-identity.md)。

## <a name="create-upstream-settings-via-resource-manager-template"></a>通过资源管理器模板创建上游设置

若要使用 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)创建上游设置，请在 `properties` 属性中设置 `upstream` 属性。 以下代码片段显示了如何设置用于创建和更新上游设置的 `upstream` 属性。

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>无服务器协议

Azure SignalR 服务将消息发送到遵循以下协议的终结点。 可以将 [SignalR 服务触发器绑定](../azure-functions/functions-bindings-signalr-service-trigger.md)用于处理这些协议的 Function App。

### <a name="method"></a>方法

POST

### <a name="request-header"></a>请求头

|名称 |说明|
|---------|---------|
|X-ASRS-Connection-Id |客户端连接的连接 ID。|
|X-ASRS-Hub |客户端连接所属的中心。|
|X-ASRS-Category |消息所属的类别。|
|X-ASRS-Event |消息所属的事件。|
|X-ASRS-Signature |一个基于哈希的消息身份验证代码 (HMAC)，用于验证。 有关详细信息，请参阅[签名](#signature)。|
|X-ASRS-User-Claims |客户端连接的一组声明。|
|X-ASRS-User-Id |发送消息的客户端的用户标识。|
|X-ASRS-Client-Query |客户端连接到服务时的请求的查询。|
|身份验证 |使用 `ManagedIdentity` 时的一个可选令牌。 |

### <a name="request-body"></a>请求正文

#### <a name="connected"></a>连续

Content-Type: application/json

#### <a name="disconnected"></a>已断开连接

Content-Type：`application/json`

|名称  |类型  |说明  |
|---------|---------|---------|
|错误 |string |已关闭连接的错误消息。 当连接无错关闭时为空。|

#### <a name="invocation-message"></a>调用消息

Content-Type：`application/json` 或 `application/x-msgpack`

|名称  |类型  |说明  |
|---------|---------|---------|
|InvocationId |string | 一个表示调用消息的可选字符串。 可以在[调用](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)中找到详细信息。|
|目标 |string | 与事件相同，并且与[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的目标相同。 |
|参数 |对象的数组 |一个数组，其中包含要应用于 `Target` 中所引用方法的参数。 |

### <a name="signature"></a>签名

服务将同时使用主要访问密钥和辅助访问密钥作为 `HMAC` 密钥来计算 `X-ASRS-Connection-Id` 值的 SHA256 代码。 向上游发出 HTTP 请求时，服务会在 `X-ASRS-Signature` 标头中设置该代码：
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>后续步骤

- [Azure SignalR 服务的托管标识](howto-use-managed-identity.md)
- [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](signalr-concept-serverless-development-config.md)
- [处理来自 SignalR 服务的消息（触发绑定）](../azure-functions/functions-bindings-signalr-service-trigger.md)
- [SignalR 服务触发器绑定示例](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)