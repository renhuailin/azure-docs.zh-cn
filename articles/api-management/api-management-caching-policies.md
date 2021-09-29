---
title: Azure API 管理缓存策略 | Microsoft 文档
description: 了解可在 Azure API 管理中使用的缓存策略。 请参阅示例，查看其他可用资源。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/08/2021
ms.author: danlep
ms.openlocfilehash: 014891a9ed16e075b8f21c0a177a38d416f2f3a9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629704"
---
# <a name="api-management-caching-policies"></a>API 管理缓存策略

本文提供有关以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](./api-management-policies.md)。

> [!IMPORTANT]
> 内置缓存是易失性的，由同一 API 管理服务中同一区域的所有单元共享。

## <a name="caching-policies"></a><a name="CachingPolicies"></a> 缓存策略

- 响应缓存策略
    - [从缓存中获取](#GetFromCache) - 执行缓存查找，并返回有效的缓存响应（如果有）。
    - [存储到缓存](#StoreToCache) - 根据指定的缓存控制配置来缓存响应。
- 值缓存策略
    - [从缓存中获取值](#GetFromCacheByKey) - 按密钥检索缓存项。
    - [在缓存中存储值](#StoreToCacheByKey) - 按密钥在缓存中存储项。
    - [从缓存中删除值](#RemoveCacheByKey) - 按密钥在缓存中删除项。

## <a name="get-from-cache"></a><a name="GetFromCache"></a> 从缓存中获取
使用 `cache-lookup` 策略执行缓存查找，并返回有效的缓存响应（如果有）。 当响应内容在某个时间段内保持静态时，即可应用该策略。 响应缓存可以降低后端 Web 服务器需要满足的带宽和处理能力要求，并可以减小 API 使用者能够察觉到的延迟。

> [!NOTE]
> 此策略必须已设置相应的[存储到缓存](#StoreToCache)策略。

### <a name="policy-statement"></a>策略语句

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

> [!NOTE]
> 使用 `vary-by-query-parameter` 时，可能需要在 rewrite-uri 模板中声明参数，或将属性 `copy-unmatched-params` 设置为 `false`。 停用此标志后，未声明的参数将发送到后端。

### <a name="examples"></a>示例

#### <a name="example"></a>示例

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>策略表达式使用示例
此示例演示如何配置 API 管理响应缓存持续时间，使之匹配由后端服务的 `Cache-Control` 指令指定的后端服务响应缓存。 有关配置和使用此策略的演示，请观看 [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)（Cloud Cover 第 177 集：Vlad Vinogradsky 讲述更多 API 管理功能），快进到 25:25。

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

有关详细信息，请参阅[策略表达式](api-management-policy-expressions.md)和[上下文变量](api-management-policy-expressions.md#ContextVariables)。

### <a name="elements"></a>元素

|名称|说明|必需|
|----------|-----------------|--------------|
|cache-lookup|根元素。|是|
|vary-by-header|开始按指定标头（例如 Accept、Accept-Charset、Accept-Encoding、Accept-Language、Authorization、Expect、From、Host、If-Match）的值缓存响应。|否|
|vary-by-query-parameter|根据指定查询参数的值开始缓存响应。 请输入一个或多个参数。 使用分号作为分隔符。 如果未指定任何参数，将使用所有查询参数。|否|

### <a name="attributes"></a>属性

| 名称                           | 说明                                                                                                                                                                                                                                                                                                                                                 | 必须 | 默认           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | 设置为 `true` 即可缓存包含 Authorization 标头的请求。                                                                                                                                                                                                                                                                        | 否       | false             |
| caching-type               | 在以下属性值之间进行选择：<br />- `internal` 使用内置的 API 管理缓存；<br />- `external` 使用外部缓存，如[在 Azure API 管理中使用外部 Azure Redis 缓存](api-management-howto-cache-external.md)中所述，<br />- `prefer-external` 如果外部缓存已配置，则使用外部缓存，否则使用内部缓存。 | 否       | `prefer-external` |
| downstream-caching-type        | 此属性必须设置为以下值之一。<br /><br /> -   none - 不允许下游缓存。<br />-   private - 允许下游专用缓存。<br />-   public - 允许专用和共享下游缓存。                                                                                                          | 否       | 无              |
| must-revalidate                | 启用下游缓存时，此属性会启用或关闭网关响应中的 `must-revalidate` 缓存控制指令。                                                                                                                                                                                                                      | 否       | 是              |
| vary-by-developer              | 设置为 `true` 以缓存每个开发人员帐户的响应，这些帐户具有请求中包含的[订阅密钥](./api-management-subscriptions.md)。                                                                                                                                                                                                                                                                                                  | 是      |         False          |
| vary-by-developer-groups       | 设置为 `true` 即可按[用户组](./api-management-howto-create-groups.md)缓存响应。                                                                                                                                                                                                                                                                                                             | 是      |       False            |

### <a name="usage"></a>使用情况
此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站
- **策略范围：** 所有范围

## <a name="store-to-cache"></a><a name="StoreToCache"></a> 存储到缓存
`cache-store` 策略根据指定的缓存设置缓存响应。 当响应内容在某个时间段内保持静态时，即可应用该策略。 响应缓存可以降低后端 Web 服务器需要满足的带宽和处理能力要求，并可以减小 API 使用者能够察觉到的延迟。

> [!NOTE]
> 此策略必须已设置相应的[从缓存中获取](api-management-caching-policies.md#GetFromCache)策略。

### <a name="policy-statement"></a>策略语句

```xml
<cache-store duration="seconds" cache-response="true | false" />
```

### <a name="examples"></a>示例

#### <a name="example"></a>示例

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>策略表达式使用示例
此示例演示如何配置 API 管理响应缓存持续时间，使之匹配由后端服务的 `Cache-Control` 指令指定的后端服务响应缓存。 有关配置和使用此策略的演示，请观看 [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)（Cloud Cover 第 177 集：Vlad Vinogradsky 讲述更多 API 管理功能），快进到 25:25。

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

有关详细信息，请参阅[策略表达式](api-management-policy-expressions.md)和[上下文变量](api-management-policy-expressions.md#ContextVariables)。

### <a name="elements"></a>元素

|名称|说明|必需|
|----------|-----------------|--------------|
|cache-store|根元素。|是|

### <a name="attributes"></a>属性

| 名称             | 说明                                                                                                                                                                                                                                                                                                                                                 | 必须 | 默认           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | 缓存条目的生存时间，以秒为单位指定。     | 是      | 空值               |
| cache-response         | 设置为 true 可缓存当前 HTTP 响应。 如果省略此属性或将其设置为 false，则只缓存状态代码为 `200 OK` 的 HTTP 响应。                           | 否      | false               |

### <a name="usage"></a>使用情况
此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略段：** 出站
- **策略范围：** 所有范围

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> 从缓存中获取值
使用 `cache-lookup-value` 策略，可以通过密钥执行缓存查找，并返回缓存的值。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。

> [!NOTE]
> 此策略必须已设置相应的[在缓存中存储值](#StoreToCacheByKey)策略。

### <a name="policy-statement"></a>策略语句

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>示例
有关此策略的详细信息和示例，请参阅 [Azure API 管理中的自定义缓存](./api-management-sample-cache-by-key.md)。

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>元素

|名称|说明|必需|
|----------|-----------------|--------------|
|cache-lookup-value|根元素。|是|

### <a name="attributes"></a>属性

| 名称             | 说明                                                                                                                                                                                                                                                                                                                                                 | 必须 | 默认           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 在以下属性值之间进行选择：<br />- `internal` 使用内置的 API 管理缓存；<br />- `external` 使用外部缓存，如[在 Azure API 管理中使用外部 Azure Redis 缓存](api-management-howto-cache-external.md)中所述，<br />- `prefer-external` 如果外部缓存已配置，则使用外部缓存，否则使用内部缓存。 | 否       | `prefer-external` |
| default-value    | 在缓存密钥查找未命中的情况下，会分配给变量的值。 如果未指定此属性，则会分 `null`。                                                                                                                                                                                                           | 否       | `null`            |
| key              | 要在查找中使用的缓存密钥值。                                                                                                                                                                                                                                                                                                                       | 是      | 不适用               |
| variable-name    | 在查找成功的情况下，会向其分配查找值的[上下文变量](api-management-policy-expressions.md#ContextVariables)的名称。 如果查找未命中，则不会设置该变量。                                       | 是      | 空值               |

### <a name="usage"></a>使用情况
此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站、出站、后端、错误时
- **策略范围：** 所有范围

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> 在缓存中存储值
`cache-store-value` 按密钥执行缓存存储。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。

> [!NOTE]
> 此策略将值存储到缓存中这项操作是异步执行的。 可以使用[从缓存获取值](#GetFromCacheByKey)策略检索存储的值。 但是，存储的值可能不会立即就能检索，因为将值存储到缓存中的异步操作可能仍在进行中。 

### <a name="policy-statement"></a>策略语句

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>示例
有关此策略的详细信息和示例，请参阅 [Azure API 管理中的自定义缓存](./api-management-sample-cache-by-key.md)。

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>元素

|名称|说明|必需|
|----------|-----------------|--------------|
|cache-store-value|根元素。|是|

### <a name="attributes"></a>属性

| 名称             | 说明                                                                                                                                                                                                                                                                                                                                                 | 必须 | 默认           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 在以下属性值之间进行选择：<br />- `internal` 使用内置的 API 管理缓存；<br />- `external` 使用外部缓存，如[在 Azure API 管理中使用外部 Azure Redis 缓存](api-management-howto-cache-external.md)中所述，<br />- `prefer-external` 如果外部缓存已配置，则使用外部缓存，否则使用内部缓存。 | 否       | `prefer-external` |
| duration         | 会根据提供的期间值（以秒为单位指定）将值缓存一段时间。                                                                                                                                                                                                                                                                                 | 是      | 不适用               |
| key              | 缓存密钥，会在其下存储值。                                                                                                                                                                                                                                                                                                                   | 是      | 空值               |
| value            | 要缓存的值。                                                                                                                                                                                                                                                                                                                                     | 是      | 空值               |
### <a name="usage"></a>使用情况
此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站、出站、后端、错误时
- **策略范围：** 所有范围

## <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> 从缓存中删除值
`cache-remove-value` 删除通过密钥标识的缓存项。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。

#### <a name="policy-statement"></a>策略语句

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>示例

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>元素

|名称|说明|必需|
|----------|-----------------|--------------|
|cache-remove-value|根元素。|是|

#### <a name="attributes"></a>属性

| 名称             | 说明                                                                                                                                                                                                                                                                                                                                                 | 必须 | 默认           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 在以下属性值之间进行选择：<br />- `internal` 使用内置的 API 管理缓存；<br />- `external` 使用外部缓存，如[在 Azure API 管理中使用外部 Azure Redis 缓存](api-management-howto-cache-external.md)中所述，<br />- `prefer-external` 如果外部缓存已配置，则使用外部缓存，否则使用内部缓存。 | 否       | `prefer-external` |
| key              | 以前所缓存的值（将从缓存中删除）的密钥。                                                                                                                                                                                                                                                                                        | 是      | 空值               |

#### <a name="usage"></a>使用情况
此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站、出站、后端、错误时
- **策略范围：** 所有范围

## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](./api-management-policies.md)，获取策略语句及其设置的完整列表
+ [策略示例](./policy-reference.md)
