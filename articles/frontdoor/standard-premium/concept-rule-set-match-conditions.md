---
title: 配置 Azure Front Door 标准版/高级版规则集匹配条件
description: 本文提供一系列可使用 Azure Front Door 标准版/高级版规则集执行的各种匹配条件。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: 9e8defa9e929d21f210c48ffbd3b22e44195c17d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061615"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure Front Door 标准版/高级版（预览版）规则集匹配条件

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

在 Azure Front Door 标准版/高级版[规则集](concept-rule-set.md)中，规则由零个或多个匹配条件以及一个操作组成。 本文详细介绍了可在 Azure Front Door 标准版/高级版规则集中使用的匹配条件。

规则的第一部分是一个匹配条件或一组匹配条件。 一个规则最多可包含 10 个匹配条件。 匹配条件指定要对其执行所定义操作的特定请求类型。 如果使用多个匹配条件，则匹配条件使用 AND 逻辑组合在一起。 对于支持多个值的所有匹配条件，使用 OR 逻辑。

使用匹配条件，可以进行以下筛选：

* 基于特定的 IP 地址、国家或地区筛选请求。
* 按标头信息筛选请求。
* 筛选来自移动设备或桌面设备的请求。
* 根据请求文件名和文件扩展名筛选请求。
* 根据请求 URL、协议、路径、查询字符串、Post 参数等筛选请求。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="device-type"></a><a name="IsDevice"></a> 设备类型

使用“设备类型”匹配条件来识别从移动设备或桌面设备发出的请求。  

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-------|------------------|
| 运算符 | <ul><li>在 Azure 门户中：`Equal`、`Not Equal`</li><li>在 ARM 模板中：`Equal`；使用 `negateCondition` 属性指定 Not Equal |
| 值 | `Mobile`, `Desktop` |

### <a name="example"></a>示例

在本示例中，我们将匹配所有被检测为来自移动设备的请求。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/device-type.png" alt-text="显示“设备类型”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a><a name="PostArgs"></a> Post 参数

使用“post 参数”匹配条件，可以根据 POST 请求正文中提供的参数来识别请求。 单个匹配条件匹配 POST 请求正文中的单个参数。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

> [!NOTE]
> “post 参数”匹配条件适用于 `application/x-www-form-urlencoded` 内容类型。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| Post 参数 | 一个表示 POST 参数名称的字符串值。 |
| 运算符 | [标准运算符列表](#operator-list)中的任何运算符。 |
| 值 | 一个或多个字符串或整数值，表示要匹配的 POST 参数的值。 如果指定多个值，则使用 OR 逻辑对其进行评估。 |
| 大小写转换 | `Lowercase`, `Uppercase` |

### <a name="example"></a>示例

在本示例中，我们将匹配所有 POST 请求，其中，`customerName` 参数是在请求正文中提供的，并且 `customerName` 值以字母 `J` 或 `K` 开头。 我们使用大小写转换将输入值转换为大写，这样所有以 `J`、`j`、`K` 和 `k` 开头的值都会匹配。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/post-args.png" alt-text="显示“post 参数”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a><a name="QueryString"></a> 查询字符串

使用“查询字符串”匹配条件，可以标识包含特定查询字符串的请求。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

> [!NOTE]
> 整个查询字符串是作为单个字符串匹配的，不包含前导 `?`。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 运算符 | [标准运算符列表](#operator-list)中的任何运算符。 |
| 查询字符串 | 一个或多个字符串或整数值，表示要匹配的查询字符串的值。 不要在查询字符串的开头加上 `?`。 如果指定多个值，则使用 OR 逻辑对其进行评估。 |
| 大小写转换 | `Lowercase`, `Uppercase` |

### <a name="example"></a>示例

在本示例中，我们将匹配查询字符串包含 `language=en-US` 字符串情况下的所有请求。 我们需要让匹配条件区分大小写，因此我们不转换大小写。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/query-string.png" alt-text="显示“查询字符串”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a><a name="RemoteAddress"></a> 远程地址

“远程地址”匹配条件根据请求者的位置或 IP 地址来识别请求。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

* 在指定 IP 地址块时使用 CIDR 表示法。 也就是说，IP 地址块的语法是，基本 IP 地址后跟一个正斜杠和前缀大小。 例如：
    * IPv4 示例：`5.5.5.64/26` 匹配来自地址 5.5.5.64 到 5.5.5.127 的所有请求。
    * IPv6 示例：`1:2:3:/48` 匹配来自地址 1:2:3:0:0:0:0:0 到 1:2:3: ffff:ffff:ffff:ffff:ffff 的所有请求。
* 当你指定多个 IP 地址和 IP 地址块时，将应用“OR”逻辑。
    * IPv4 示例：如果添加两个 IP 地址 `1.2.3.4` 和 `10.20.30.40`，当有任何来自 1.2.3.4 或 10.20.30.40 地址的请求时，会匹配此条件。
    * IPv6 示例：如果添加两个 IP 地址 `1:2:3:4:5:6:7:8` 和 `10:20:30:40:50:60:70:80`，当有任何来自 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 地址的请求时，会匹配此条件。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 运算符 | <ul><li>在 Azure 门户中：`Geo Match`、`Geo Not Match`、`IP Match` 或 `IP Not Match`</li><li>在 ARM 模板中：`GeoMatch` 和 `IPMatch`；使用 `negateCondition` 属性来指定 Geo Not Match或 IP Not Match</li></ul> |
| 值 | <ul><li>对于 `IP Match` 或 `IP Not Match` 运算符：指定一个或多个 IP 地址范围。 如果指定多个 IP 地址范围，则使用 OR 逻辑对其进行评估。</li><li>对于 `Geo Match` 或 `Geo Not Match` 运算符：使用国家/地区代码指定一个或多个位置。</li></ul> |

### <a name="example"></a>示例

在本示例中，我们将匹配不是源自美国的所有请求。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/remote-address.png" alt-text="显示“远程地址”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a><a name="RequestBody"></a> 请求正文

“请求正文”匹配条件根据请求正文中出现的特定文本识别请求。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

> [!NOTE]
> 如果请求正文的大小超过 64 KB，那么“请求正文”匹配条件只考虑前面的 64 KB。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 运算符 | [标准运算符列表](#operator-list)中的任何运算符。 |
| 值 | 一个或多个字符串或整数值，表示要匹配的请求正文文本的值。 如果指定多个值，则使用 OR 逻辑对其进行评估。 |
| 大小写转换 | `Lowercase`, `Uppercase` |

### <a name="example"></a>示例

在本示例中，我们将匹配请求正文包含 `ERROR` 字符串情况下的所有请求。 在评估匹配之前，我们将请求正文转换为大写，这样 `error` 和其他大小写变体也会触发此匹配条件。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-body.png" alt-text="显示“请求正文”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a><a name="UrlFileName"></a> 请求文件名

“请求文件名”匹配条件标识在请求 URL 中包含指定文件名的请求。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 运算符 | [标准运算符列表](#operator-list)中的任何运算符。 |
| 值 | 一个或多个字符串或整数值，表示要匹配的请求文件名的值。 如果指定多个值，则使用 OR 逻辑对其进行评估。 |
| 大小写转换 | `Lowercase`, `Uppercase` |

### <a name="example"></a>示例

在本示例中，我们将匹配请求文件名为 `media.mp4` 的所有请求。 在评估匹配之前，我们将文件名转换为小写，这样 `MEDIA.MP4` 和其他大小写变体也会触发此匹配条件。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-name.png" alt-text="显示“请求文件名”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a><a name="UrlFileExtension"></a> 请求文件扩展名

“请求文件扩展名”匹配条件标识在请求 URL 的文件名中包含指定文件扩展名的请求。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

> [!NOTE]
> 不要加上前导句点。 例如，请使用 `html` 而不是 `.html`。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 运算符 | [标准运算符列表](#operator-list)中的任何运算符。 |
| 值 | 一个或多个字符串或整数值，表示要匹配的请求文件扩展名的值。 不要加上前导句点。 如果指定多个值，则使用 OR 逻辑对其进行评估。 |
| 大小写转换 | `Lowercase`, `Uppercase` |

### <a name="example"></a>示例

在本示例中，我们将匹配请求文件扩展名为 `pdf` 或 `docx` 的所有请求。 在评估匹配之前，我们将请求文件扩展名转换为小写，这样 `PDF`、`DocX` 和其他大小写变体也会触发此匹配条件。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="显示“请求文件扩展名”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a><a name="RequestHeader"></a> 请求头

“请求头”匹配条件标识请求中包含特定标头的那些请求。 你可以使用此匹配条件来检查标头是否存在（不考虑它的值），或检查标头是否与指定的值匹配。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 标头名称 | 一个表示 POST 参数名称的字符串值。 |
| 运算符 | [标准运算符列表](#operator-list)中的任何运算符。 |
| 值 | 一个或多个字符串或整数值，表示要匹配的请求头的值。 如果指定多个值，则使用 OR 逻辑对其进行评估。 |
| 大小写转换 | `Lowercase`, `Uppercase` |

### <a name="example"></a>示例

在本示例中，我们将匹配请求中包含名为 `MyCustomHeader` 的标头（不考虑其值）的所有请求。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-header.png" alt-text="显示“请求头”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a><a name="RequestMethod"></a> 请求方法

“请求方法”匹配条件标识使用指定 HTTP 请求方法的那些请求。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 运算符 | <ul><li>在 Azure 门户中：`Equal`、`Not Equal`</li><li>在 ARM 模板中：`Equal`；使用 `negateCondition` 属性指定 Not Equal |
| 请求方法 | 以下来源的一个或多个 HTTP 方法：`GET`、`POST`、`PUT`、`DELETE`、`HEAD`、`OPTIONS` 和 `TRACE`。 如果指定多个值，则使用 OR 逻辑对其进行评估。 |

### <a name="example"></a>示例

在本示例中，我们将匹配使用 `DELETE` 方法的所有请求。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-method.png" alt-text="显示“请求方法”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a><a name="UrlPath"></a> 请求路径

“请求路径”匹配条件标识在请求 URL 中包含指定路径的请求。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

> [!NOTE]
> 该路径是 URL 的一部分，位于主机名和斜杠之后。 例如，在 URL `https://www.contoso.com/files/secure/file1.pdf` 中，路径为 `files/secure/file1.pdf`。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 运算符 | [标准运算符列表](#operator-list)中的任何运算符。 |
| 值 | 一个或多个字符串或整数值，表示要匹配的请求路径的值。 不要加上前导斜杠。 如果指定多个值，则使用 OR 逻辑对其进行评估。 |
| 大小写转换 | `Lowercase`, `Uppercase` |

### <a name="example"></a>示例

在本示例中，我们将匹配请求文件路径以 `files/secure/` 开头的所有请求。 在评估匹配之前，我们将请求文件扩展名转换为小写，这样对 `files/SECURE/` 和其他大小写变体的请求也将触发此匹配条件。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-path.png" alt-text="显示“请求路径”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a><a name="RequestScheme"></a> 请求协议

“请求协议”匹配条件标识使用指定协议（HTTP 或 HTTPS）的请求。

> [!NOTE]
> “协议”有时也称为“方案”。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 运算符 | <ul><li>在 Azure 门户中：`Equal`、`Not Equal`</li><li>在 ARM 模板中：`Equal`；使用 `negateCondition` 属性指定 Not Equal |
| 请求方法 | `HTTP`, `HTTPS` |

### <a name="example"></a>示例

在本示例中，我们将匹配使用 `HTTP` 协议的所有请求。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-protocol.png" alt-text="显示“请求协议”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a><a name="RequestUrl"></a> 请求 URL

识别那些与指定 URL 匹配的请求。 将评估整个 URL，包括协议和查询字符串，但不包括片段。 你可以指定要匹配的多个值，这些值将使用 OR 逻辑组合在一起。

> [!TIP]
> 使用此规则条件时，请务必包含协议。 例如，使用 `https://www.contoso.com` 而不只是 `www.contoso.com`。

### <a name="properties"></a>属性

| 属性 | 支持的值 |
|-|-|
| 运算符 | [标准运算符列表](#operator-list)中的任何运算符。 |
| 值 | 一个或多个字符串或整数值，表示要匹配的请求 URL 的值。 如果指定多个值，则使用 OR 逻辑对其进行评估。 |
| 大小写转换 | `Lowercase`, `Uppercase` |

### <a name="example"></a>示例

在本示例中，我们将匹配请求 URL 以 `https://api.contoso.com/customers/123` 开头的所有请求。 在评估匹配之前，我们将请求文件扩展名转换为小写，这样对 `https://api.contoso.com/Customers/123` 和其他大小写变体的请求也将触发此匹配条件。

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-match-conditions/request-url.png" alt-text="显示“请求 URL”匹配条件的门户屏幕截图。":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a><a name = "operator-list"></a> 运算符列表

对于那些接受标准运算符列表中的值的规则，以下运算符有效：

| 运算符                   | 说明                                                                                                                    | ARM 模板支持                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 任意                        | 当存在任何值时匹配，不考虑具体值。                                                                     | `operator`: `Any`                                               |
| 等于                      | 当值与指定的字符串完全匹配时匹配。                                                                   | `operator`: `Equal`                                             |
| Contains                   | 当值包含指定的字符串时匹配。                                                                          | `operator`: `Contains`                                          |
| 小于                  | 当值的长度小于指定的整数时匹配。                                                       | `operator`: `LessThan`                                          |
| 大于               | 当值的长度大于指定的整数时匹配。                                                    | `operator`: `GreaterThan`                                       |
| 小于或等于         | 当值的长度小于或等于指定的整数时匹配。                                           | `operator`: `LessThanOrEqual`                                   |
| 大于或等于      | 当值的长度大于或等于指定的整数时匹配。                                        | `operator`: `GreaterThanOrEqual`                                |
| 开头为                | 当值以指定的字符串开头时匹配。                                                                       | `operator`: `BeginsWith`                                        |
| 结尾为                  | 当值以指定的字符串结尾时匹配。                                                                         | `operator`: `EndsWith`                                          |
| 正则表达式                      | 当值匹配指定的正则表达式时匹配。 [详见下文。](#regular-expressions)        | `operator`: `RegEx`                                             |
| 非任意                    | 在没有值的情况下匹配。                                                                                                | `operator`: `Any` 和 `negateCondition` : `true`                |
| Not Equal                  | 当值与指定的字符串不匹配时进行匹配。                                                                    | `operator`: `Equal` 和 `negateCondition` : `true`              |
| 不包含               | 当值不包含指定的字符串时匹配。                                                                  | `operator`: `Contains` 和 `negateCondition` : `true`           |
| 不小于              | 当值的长度不小于指定的整数时匹配。                                                   | `operator`: `LessThan` 和 `negateCondition` : `true`           |
| 不大于           | 当值的长度不大于指定的整数时匹配。                                                | `operator`: `GreaterThan` 和 `negateCondition` : `true`        |
| 不小于或等于     | 当值的长度不小于或等于指定的整数时匹配。                                       | `operator`: `LessThanOrEqual` 和 `negateCondition` : `true`    |
| 不大于或等于 | 当值的长度不大于或等于指定的整数时匹配。                                    | `operator`: `GreaterThanOrEqual` 和 `negateCondition` : `true` |
| 开头不是            | 当值不以指定的字符串开头时匹配。                                                               | `operator`: `BeginsWith` 和 `negateCondition` : `true`         |
| 结尾不是              | 当值不以指定的字符串结尾时匹配。                                                                 | `operator`: `EndsWith` 和 `negateCondition` : `true`           |
| 非正则表达式                  | 当值不匹配指定的正则表达式时进行匹配。 [详见下文。](#regular-expressions) | `operator`: `RegEx` 和 `negateCondition` : `true`              |

> [!TIP]
> 对于数字运算符（例如“小于”和“大于或等于”），使用的比较基于长度。 匹配条件中的值应该是一个整数，指定你要比较的长度。

### <a name="regular-expressions"></a><a name="regular-expressions"></a> 正则表达式

正则表达式不支持以下操作：

* 向后引用和捕获子表达式。
* 任意零宽断言。
* 子例程引用和递归模式。
* 条件模式。
* 回溯控制谓词。
* `\C` 单字节指令。
* `\R` 换行符匹配指令。
* `\K` 启动匹配重置指令。
* 标注和嵌入代码。
* 原子组和所有格限定符。

## <a name="next-steps"></a>后续步骤

* 详细了解[规则集](concept-rule-set.md)。
* 了解如何[配置你的第一个规则集](how-to-configure-rule-set.md)。
* 详细了解[规则集操作](concept-rule-set-actions.md)。
