---
title: 配置 Azure Front Door 标准/高级规则集操作
description: 本文介绍了一系列可使用 Azure Front Door 规则集执行的各种操作。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 98f09b35bb51f4f93c7bf326683a333e79845de2
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109810744"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Azure Front Door 标准/高级（预览版）规则集操作

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

Azure Front Door 标准/高级[规则集](concept-rule-set.md)由包含匹配条件和操作组合的规则组成。 本文详细介绍了可在 Azure Front Door 标准/高级规则集中使用的操作。 操作定义了应用于匹配条件所识别的请求类型的行为。 在 Azure Front Door（标准/高级）规则集中，一个规则最多可以包含五个操作。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可在 Azure Front Door 规则集中使用以下操作。  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a>缓存过期

对于规则匹配条件指定的请求，使用缓存过期操作来覆盖终结点的生存时间 (TTL) 值。

> [!NOTE]
> 原点可能会使用值为 `no-cache`、`private` 或 `no-store` 的 `Cache-Control` 标头，指定不缓存特定的响应。 在这些情况下，Front Door 绝不会缓存内容，此操作不会产生任何影响。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-------|------------------|
| 缓存行为 | <ul><li>绕过缓存：不应缓存内容。 在 ARM 模板中，将 `cacheBehavior` 属性设置为 `BypassCache`。</li><li>替代：用操作中指定的值覆盖从原点返回的 TTL 值。 仅当响应可缓存时才会应用此行为。 在 ARM 模板中，将 `cacheBehavior` 属性设置为 `Override`。</li><li>缺少时进行设置：如果没有 TTL 值从原点返回，那么规则会将 TTL 设置为该操作中指定的值。 仅当响应可缓存时才会应用此行为。 在 ARM 模板中，将 `cacheBehavior` 属性设置为 `SetIfMissing`。</li></ul> |
| 缓存持续时间 | 当缓存行为设置为 `Override` 或 `Set if missing` 时，这些字段必须指定要使用的缓存持续时间。 最长持续时间为 366 天。<ul><li>在 Azure 门户中：指定天数、小时数、分钟数和秒数。</li><li>在 ARM 模板中：以 `d.hh:mm:ss` 格式指定持续时间。 |

### <a name="example"></a>示例

在此示例中，对于尚未指定缓存持续时间的匹配请求，我们将缓存过期时间替代为 6 小时。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="Portal screenshot showing cache expiration action.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a>缓存键查询字符串

使用缓存键查询字符串操作，可以基于查询字符串修改缓存键。 缓存键是 Front Door 标识缓存唯一请求的方式。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-------|------------------|
| 行为 | <ul><li>包含：在生成缓存键时，将在参数中指定的查询字符串包含在内。 在 ARM 模板中，将 `queryStringBehavior` 属性设置为 `Include`。</li><li>缓存每个唯一的 URL：每个唯一的 URL 都有其自己的缓存键。 在 ARM 模板中，使用 `IncludeAll` 的 `queryStringBehavior`。</li><li>排除：在生成缓存键时，排除参数中指定的查询字符串。 在 ARM 模板中，将 `queryStringBehavior` 属性设置为 `Exclude`。</li><li>忽略查询字符串：生成缓存键时不考虑查询字符串。 在 ARM 模板中，将 `queryStringBehavior` 属性设置为 `ExcludeAll`。</li></ul>  |
| 参数 | 查询字符串参数名称的列表，用逗号分隔。 |

### <a name="example"></a>示例

在此示例中，我们修改了缓存键，以加入一个名为 `customerId` 的查询字符串参数。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="Portal screenshot showing cache key query string action.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a>修改请求标头

使用修改请求标头操作，在请求发送到原点时修改请求中的标头。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-------|------------------|
| 运算符 | <ul><li>追加：将指定的标头添加到具有指定值的请求中。 如果该标头已存在，则会使用字符串串联将该值追加到现有标头值。 无需添加分隔符。 在 ARM 模板中，使用 `Append` 的 `headerAction`。</li><li>覆盖：将指定的标头添加到具有指定值的请求中。 如果该标头已存在，则指定的值将替代现有值。 在 ARM 模板中，使用 `Overwrite` 的 `headerAction`。</li><li>删除：如果规则中指定的标头已存在，则会从请求中删除该标头。 在 ARM 模板中，使用 `Delete` 的 `headerAction`。</li></ul> |
| 标头名称 | 要修改的标头名称。 |
| 标头值 | 要追加或覆盖的值。 |

### <a name="example"></a>示例

在此示例中，我们将值 `AdditionalValue` 追加到 `MyRequestHeader` 请求标头。 如果原点将响应标头设置为值 `ValueSetByClient`，则在应用此操作后，请求标头的值将为 `ValueSetByClientAdditionalValue`。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="Portal screenshot showing modify request header action.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a>修改响应标头

使用修改响应标头操作来修改响应中存在的标头，然后将它们返回到客户端。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-------|------------------|
| 运算符 | <ul><li>追加：将指定的标头添加到具有指定值的响应中。 如果该标头已存在，则会使用字符串串联将该值追加到现有标头值。 无需添加分隔符。 在 ARM 模板中，使用 `Append` 的 `headerAction`。</li><li>覆盖：将指定的标头添加到具有指定值的响应中。 如果该标头已存在，则指定的值将替代现有值。 在 ARM 模板中，使用 `Overwrite` 的 `headerAction`。</li><li>删除：如果规则中指定的标头已存在，则会从响应中删除该标头。  在 ARM 模板中，使用 `Delete` 的 `headerAction`。</li></ul> |
| 标头名称 | 要修改的标头名称。 |
| 标头值 | 要追加或覆盖的值。 |

### <a name="example"></a>示例

在此示例中，我们先从响应中删除名为 `X-Powered-By` 的标头，然后再将其返回到客户端。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="Portal screenshot showing modify response header action.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a>URL 重定向

使用 URL 重定向操作可将客户端重定向到新 URL。 从 Front Door 向客户端发送重定向响应。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|----------|------------------|
| 重定向类型 | 要返回给请求方的响应类型。 <ul><li>在 Azure 门户中：“已找到”(302)、“已移动”(301)、“临时重定向”(307)、“永久重定向”(308)。</li><li>在 ARM 模板中：`Found`、`Moved`、`TemporaryRedirect`、`PermanentRedirect`</li></ul> |
| 重定向协议 | <ul><li>在 Azure 门户中：`Match Request`、`HTTP`、`HTTPS`</li><li>在 ARM 模板中：`MatchRequest`、`Http`、`Https`</li></ul> |
| 目标主机 | 要将请求重定向到的主机名。 留空会保留传入主机。 |
| 目标路径 | 要在重定向中使用的路径。 包含前导 `/`。 留空会保留传入路径。 |
| 查询字符串 | 重定向中使用的查询字符串。 请勿包含前导 `?`。 留空会保留传入的查询字符串。 |
| 目标片段 | 要在重定向中使用的片段。 留空会保留传入片段。 |

### <a name="example"></a>示例

在此示例中，我们将请求重定向到 `https://contoso.com/exampleredirection?clientIp={client_ip}`，同时保留片段。 使用 HTTP 临时重定向 (307)。 客户端的 IP 地址通过使用 `client_ip` [服务器变量](#server-variables)来代替 URL 中的 `{client_ip}` 令牌。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="Portal screenshot showing URL redirect action.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a>URL 重写

使用 URL 重写操作可以重写路由到原点的请求的路径。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|----------|------------------|
| 源模式 | 定义要替换的 URL 路径中的源模式。 当前，源模式使用基于前缀的匹配。 若要匹配所有 URL 路径，请使用正斜杠（`/`）作为源模式值。 |
| 目标 | 定义要在重写中使用的目标路径。 目标路径会覆盖源模式。 |
| 暂留不匹配的路径 | 如果设置为“是”，则会将源模式后面的剩余路径追加到新的目标路径。 |

### <a name="example"></a>示例

在此示例中，我们将所有请求重写到路径 `/redirection`，并且不保留路径的其余部分。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="Portal screenshot showing URL rewrite action.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="origin-group-override"></a><a name="OriginGroupOverride"></a> 源组替代

使用“源组替代”操作来更改请求应路由到的源组。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|----------|------------------|
| 源组 | 请求应路由到的源组。 这会替代 Front Door 终结点路由中指定的配置。 |

### <a name="example"></a>示例

在此示例中，我们将所有匹配的请求路由到名为 `SecondOriginGroup` 的源组，而不考虑 Front Door 终结点路由中的配置。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/origin-group-override.png" alt-text="Portal screenshot showing origin group override action.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "OriginGroupOverride",
  "parameters": {
    "originGroup": {
      "id": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Cdn/profiles/<profile-name>/originGroups/SecondOriginGroup"
    },
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleOriginGroupOverrideActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'OriginGroupOverride'
  parameters: {
    originGroup: {
      id: '/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Cdn/profiles/<profile-name>/originGroups/SecondOriginGroup'
    }
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleOriginGroupOverrideActionParameters'
  }
}
```

---

## <a name="server-variables"></a>服务器变量

规则集服务器变量可提供对请求的结构化信息的访问。 可以使用服务器变量来动态更改请求/响应标头或 URL 重写路径/查询字符串（例如，在新页面加载或发布窗体时）。

### <a name="supported-variables"></a>支持的变量

| 变量名称    | 说明                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | 与 Azure Front Door 边缘建立直接连接的 IP 地址。 如果客户端使用 HTTP 代理或负载均衡器来发送请求，则值 `socket_ip` 为该代理或负载均衡器的 IP 地址。                                                                      |
| `client_ip`      | 发出原始请求的客户端的 IP 地址。 如果请求中有 `X-Forwarded-For` 标头，则从标头中提取客户端 IP 地址。                                                                                                               |
| `client_port`    | 发出请求的客户端的 IP 端口。                                                                                                                                                                                                                                          |
| `hostname`       | 来自客户端的请求中的主机名。                                                                                                                                                                                                                                             |
| `geo_country`    | 指示申请者所在的国家/地区（用国家/地区代码表示）。                                                                                                                                                                                                       |
| `http_method`    | 用于发出 URL 请求的方法，如 `GET` 或 `POST`。                                                                                                                                                                                                                         |
| `http_version`   | 请求协议。 通常为 `HTTP/1.0`、`HTTP/1.1` 或 `HTTP/2.0`。                                                                                                                                                                                                                      |
| `query_string`   | 请求的 URL 中“?”后面的变量/值对列表。<br />例如，在请求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` 中，`query_string` 的值将为 `id=123&title=fabrikam`。                                                      |
| `request_scheme` | 请求方案：`http` 或 `https`。                                                                                                                                                                                                                                                    |
| `request_uri`    | 完整的原始请求 URI（带参数）。<br />例如，在请求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` 中，`request_uri` 的值将为 `/article.aspx?id=123&title=fabrikam`。                                                                     |
| `ssl_protocol`   | 已建立的 TLS 连接的协议。                                                                                                                                                                                                                                            |
| `server_port`    | 接受请求的服务器端口。                                                                                                                                                                                                                                           |
| `url_path`       | 标识 Web 客户端要访问的主机中的特定资源。 这是请求 URI 中没有参数的部分。<br />例如，在请求 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` 中，`uri_path` 的值将为 `/article.aspx`。 |

### <a name="server-variable-format"></a>服务器变量格式    

可以使用以下格式指定服务器变量：

* `{variable}`：加入整个服务器变量。 例如，如果客户端 IP 地址为 `111.222.333.444`，则 `{client_ip}` 令牌的计算结果将为 `111.222.333.444`。
* `{variable:offset}`：在指定的偏移量之后加入服务器变量，直至变量末尾。 偏移量从零开始。 例如，如果客户端 IP 地址为 `111.222.333.444`，则 `{client_ip:3}` 令牌的计算结果将为 `.222.333.444`。
* `{variable:offset:length}`：在指定的偏移量之后加入服务器变量，直至达到指定长度。 偏移量从零开始。 例如，如果客户端 IP 地址为 `111.222.333.444`，则 `{client_ip:4:3}` 令牌的计算结果将为 `222`。

### <a name="supported-actions"></a>支持的操作

支持对以下操作执行服务器变量：

* 缓存键查询字符串
* 修改请求标头
* 修改响应标头
* URL 重定向
* URL 重写

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Front Door 标准/高级规则集](concept-rule-set.md)。
* 详细了解[规则集匹配条件](concept-rule-set-match-conditions.md)。
