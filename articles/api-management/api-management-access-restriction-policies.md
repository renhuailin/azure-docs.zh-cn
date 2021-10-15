---
title: Azure API 管理访问限制策略 | Microsoft 文档
description: 了解可在 Azure API 管理中使用的访问限制策略。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 08/20/2021
ms.author: danlep
ms.openlocfilehash: 32fa405a612026fc16257447cb2cc858101c729c
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536516"
---
# <a name="api-management-access-restriction-policies"></a>API 管理访问限制策略

本主题提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](./api-management-policies.md)。

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a>访问限制策略

-   [检查 HTTP 标头](#CheckHTTPHeader) - 强制必须存在和/或强制采用 HTTP 标头的值。
-   [按订阅限制调用速率](#LimitCallRate) - 根据订阅限制调用速率，避免 API 使用量暴增。
-   [按密钥限制调用速率](#LimitCallRateByKey) - 根据密钥限制调用速率，避免 API 使用量暴增。
-   [限制调用方 IP](#RestrictCallerIPs) - 筛选（允许/拒绝）来自特定 IP 地址和/或地址范围的调用。
-   [按订阅设置使用量配额](#SetUsageQuota) - 允许根据订阅强制实施可续订或有生存期的调用量和/或带宽配额。
-   [按密钥设置使用量配额](#SetUsageQuotaByKey) - 允许根据密钥强制消耗可续订或有生存期的调用量和/或带宽配额。
-   [验证 JWT](#ValidateJWT) - 强制从指定 HTTP 标头或指定查询参数提取的 JWT 必须存在且有效。
-  [验证客户端证书](#validate-client-certificate) - 强制客户端提供给 API 管理实例的证书与指定的验证规则和声明相匹配。

> [!TIP]
> 可以在不同的范围内为不同的目的使用访问限制策略。 例如，可以通过在 API 级别上应用 `validate-jwt` 策略来使用 AAD 身份验证保护整个 API，也可以在 API 操作级别上应用它并使用 `claims` 进行更细粒度的控制。

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a>检查 HTTP 标头

使用 `check-header` 策略强制请求具有指定的 HTTP 标头。 可以选择性地查看标头是否具有特定值，或者检查是否存在一系列允许的值。 如果检查失败，此策略会终止请求处理，并返回其所指定的 HTTP 状态代码和错误消息。

### <a name="policy-statement"></a>策略语句

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>示例

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>元素

| 名称         | 说明                                                                                                                                   | 必须 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | 根元素。                                                                                                                                 | 是      |
| value        | 允许的 HTTP 标头值。 指定了多个值元素时，如果任何一个值匹配，则检查将被视为成功。 | 否       |

### <a name="attributes"></a>属性

| 名称                       | 说明                                                                                                                                                            | 必须 | 默认 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | 在标头不存在或其值无效的情况下，需要在 HTTP 响应正文中返回的错误消息。 此消息必须对任何特殊字符正确地进行转义。 | 是      | 不适用     |
| failed-check-httpcode      | 在标头不存在或其值无效时需返回的 HTTP 状态代码。                                                                                        | 是      | 不适用     |
| header-name                | 要检查的 HTTP 标头的名称。                                                                                                                                  | 是      | 不适用     |
| ignore-case                | 可以设置为 True 或 False。 如果设置为 True，则在将标头值与一组可接受的值进行比较时，会忽略大小写。                                    | 是      | 空值     |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站、出站

-   **策略范围：** 所有范围

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a>按订阅限制调用速率

`rate-limit` 策略可以对调用速率进行限制，使每个指定时段的调用不超出指定的数目，避免单个订阅的 API 使用量暴增。 超过调用速率时，调用方会收到 `429 Too Many Requests` 响应状态代码。

> [!IMPORTANT]
> 每个策略文档只能使用此策略一次。
>
> [策略表达式](api-management-policy-expressions.md)不能用于此策略的任何策略属性。

> [!CAUTION]
> 由于限制体系结构的分布式性质，速率限制永远不可能完全准确。 允许的请求的配置数字和实际数字之间的差异因请求量和速度、后端延迟以及其他因素而异。

> [!NOTE]
> 若要了解速率限制和配额之间的差异，请参阅[速率限制和配额](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)。

### <a name="policy-statement"></a>策略语句

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds">
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" 
        retry-after-header-name="header name" 
        retry-after-variable-name="policy expression variable name"
        remaining-calls-header-name="header name"  
        remaining-calls-variable-name="policy expression variable name"
        total-calls-header-name="header name"/>
    </api>
</rate-limit>
```

### <a name="example"></a>示例

在下面的示例中，每个订阅的速率限制为每 90 秒 20 个调用。 每次执行策略后，在该时间段内允许的剩余调用存储在变量 `remainingCallsPerSubscription` 中。

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" remaining-calls-variable-name="remainingCallsPerSubscription"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>元素

| 名称       | 说明                                                                                                                                                                                                                                                                                              | 必需 |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | 根元素。                                                                                                                                                                                                                                                                                            | 是      |
| api        | 添加一个或多个此类元素，对产品中的 API 施加调用速率限制。 产品和 API 的调用速率限制是分别应用的。 可以通过 `name` 或 `id` 引用 API。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。                    | 否       |
| operation  | 添加一个或多个此类元素，对 API 中的操作施加调用速率限制。 产品、API 和操作的调用速率限制是分别应用的。 可以通过 `name` 或 `id` 引用 Operation。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。 | 否       |

### <a name="attributes"></a>属性

| 名称           | 说明                                                                                           | 必须 | 默认 |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | 要对其应用速率限制的 API 的名称。                                                | 是      | 空值     |
| calls          | 在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。 | 是      | 空值     |
| renewal-period | 滑动窗口的长度（以秒为单位），在此期间，允许的请求数不应超过 `calls` 中指定的值。 允许的最大值：300 秒。                                            | 是      | 空值     |
| retry-after-header-name    | 响应头的名称，其值为在超过指定的调用速率后建议的重试间隔（以秒为单位）。 |  否 | 空值  |
| retry-after-variable-name    | 策略表达式变量的名称，该变量用于存储在超过指定的调用速率后建议的重试间隔（以秒为单位）。 |  否 | 空值  |
| remaining-calls-header-name    | 响应头的名称，每次执行策略后，其值为在 `renewal-period` 中指定的时间间隔内允许的剩余调用数。 |  否 | 空值  |
| remaining-calls-variable-name    | 策略表达式变量的名称，该变量用于存储在每次执行策略后，`renewal-period` 中指定的时间间隔内允许的剩余调用数。 |  否 | 空值  |
| total-calls-header-name    | 响应头的名称，其值为 `calls` 中指定的值。 |  否 | 空值  |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站

-   **策略范围：** 产品、API、操作

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> 按密钥限制调用速率

> [!IMPORTANT]
> 此功能在 API 管理的“消耗”层中不可用。

`rate-limit-by-key` 策略可以对调用速率进行限制，使指定时段的调用不超出指定的数目，避免单个密钥的 API 使用量暴增。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。 可以添加可选增量条件，指定在决定是否到达限制值时应该进行计数的请求。 超过此调用速率时，调用方会收到 `429 Too Many Requests` 响应状态代码。

有关此策略的详细信息和示例，请参阅[使用 Azure API 管理进行高级请求限制](./api-management-sample-flexible-throttling.md)。

> [!CAUTION]
> 由于限制体系结构的分布式性质，速率限制永远不可能完全准确。 允许的请求的配置数字和实际数字之间的差异因请求量和速度、后端延迟以及其他因素而异。

> [!NOTE]
> 若要了解速率限制和配额之间的差异，请参阅[速率限制和配额](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)。

### <a name="policy-statement"></a>策略语句

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" 
                   retry-after-header-name="header name" retry-after-variable-name="policy expression variable name"
                   remaining-calls-header-name="header name"  remaining-calls-variable-name="policy expression variable name"
                   total-calls-header-name="header name"/> 

```

### <a name="example"></a>示例

在下面的示例中，可通过调用方 IP 地址对速率限制进行键控，将其控制为 每 60 秒 10 个调用。 每次执行策略后，在该时间段内允许的剩余调用存储在变量 `remainingCallsPerIP` 中。

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"
              remaining-calls-variable-name="remainingCallsPerIP"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>元素

| 名称              | 说明   | 必需 |
| ----------------- | ------------- | -------- |
| rate-limit-by-key | 根元素。 | 是      |

### <a name="attributes"></a>属性

| 名称                | 说明                                                                                           | 必须 | 默认 |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| calls               | 在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。 允许策略表达式。 | 是      | 空值     |
| counter-key         | 用于速率限制策略的密钥。                                                             | 是      | 空值     |
| increment-condition | 一个布尔表达式，指定是否应将请求计入速率 (`true`)。        | 否       | 空值     |
| renewal-period      | 滑动窗口的长度（以秒为单位），在此期间，允许的请求数不应超过 `calls` 中指定的值。 允许策略表达式。 允许的最大值：300 秒。                 | 是      | 空值     |
| retry-after-header-name    | 响应头的名称，其值为在超过指定的调用速率后建议的重试间隔（以秒为单位）。 |  否 | 空值  |
| retry-after-variable-name    | 策略表达式变量的名称，该变量用于存储在超过指定的调用速率后建议的重试间隔（以秒为单位）。 |  否 | 空值  |
| remaining-calls-header-name    | 响应头的名称，每次执行策略后，其值为在 `renewal-period` 中指定的时间间隔内允许的剩余调用数。 |  否 | 空值  |
| remaining-calls-variable-name    | 策略表达式变量的名称，该变量用于存储在每次执行策略后，`renewal-period` 中指定的时间间隔内允许的剩余调用数。 |  否 | 空值  |
| total-calls-header-name    | 响应头的名称，其值为 `calls` 中指定的值。 |  否 | 空值  |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站

-   **策略范围：** 所有范围

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> 限制调用方 IP

`ip-filter` 策略筛选（允许/拒绝）来自特定 IP 地址和/或地址范围的调用。

### <a name="policy-statement"></a>策略语句

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>示例

在下面的示例中，策略仅允许来自指定的单一 IP 地址或 IP 地址范围的请求

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>元素

| 名称                                      | 说明                                         | 必需                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | 根元素。                                       | 是                                                            |
| address                                   | 指定要对其进行筛选的单个 IP 地址。   | 至少一个 `address` 或 `address-range` 元素是必需的。 |
| address-range from="address" to="address" | 指定要对其进行筛选的 IP 地址范围。 | 至少一个 `address` 或 `address-range` 元素是必需的。 |

### <a name="attributes"></a>属性

| 名称                                      | 说明                                                                                 | 必须                                           | 默认 |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | 允许或拒绝其访问的某个 IP 地址范围。                                        | 使用 `address-range` 元素时必需。 | 空值     |
| ip-filter action="allow &#124; forbid"    | 指定是否应允许指定的 IP 地址和范围执行调用。 | 是                                                | 空值     |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站
-   **策略范围：** 所有范围

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a>按订阅设置使用量配额

`quota` 策略允许根据订阅强制实施可续订或有生存期的调用量和/或带宽配额。

> [!IMPORTANT]
> 每个策略文档只能使用此策略一次。
>
> [策略表达式](api-management-policy-expressions.md)不能用于此策略的任何策略属性。

> [!NOTE]
> 若要了解速率限制和配额之间的差异，请参阅[速率限制和配额](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)。

### <a name="policy-statement"></a>策略语句

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>示例

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>元素

| 名称      | 说明                                                                                                                                                                                                                                                                                  | 必需 |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | 根元素。                                                                                                                                                                                                                                                                                | 是      |
| api       | 添加一个或多个此类元素，对产品中的 API 设置调用配额。 产品和 API 的调用配额是分别应用的。 可以通过 `name` 或 `id` 引用 API。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。                    | 否       |
| operation | 添加一个或多个此类元素，对 API 中的操作设置调用配额。 产品、API 和操作的调用配额是分别应用的。 可以通过 `name` 或 `id` 引用 Operation。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。 | 否      |

### <a name="attributes"></a>属性

| 名称           | 说明                                                                                               | 必须                                                         | 默认 |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | 要向其应用配额的 API 或操作的名称。                                             | 是                                                              | 空值     |
| bandwidth      | 在 `renewal-period` 所指定的时间间隔内允许的最大总字节数（千字节）。 | 必须指定 `calls` 和/或 `bandwidth`。 | 空值     |
| calls          | 在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。     | 必须指定 `calls` 和/或 `bandwidth`。 | 空值     |
| renewal-period | 在重置配额之前等待的时间长度，以秒为单位。 设置为 `0` 时，时间段设置为无限。 | 是                                                              | 空值     |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站
-   **策略范围：** 产品

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> 按密钥设置使用量配额

> [!IMPORTANT]
> 此功能在 API 管理的“消耗”层中不可用。

`quota-by-key` 策略允许根据密钥强制实施可续订或有生存期的调用量和/或带宽配额。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。 可以添加可选增量条件，指定应在配额范围内的请求。 如果多个策略增加相同的键值，则每个请求的键值仅增加一次。 超过调用速率时，调用方会收到 `403 Forbidden` 响应状态代码。

有关此策略的详细信息和示例，请参阅[使用 Azure API 管理进行高级请求限制](./api-management-sample-flexible-throttling.md)。

> [!NOTE]
> 若要了解速率限制和配额之间的差异，请参阅[速率限制和配额](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)。

### <a name="policy-statement"></a>策略语句

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>示例

在下面的示例中，可通过调用方 IP 地址对配额进行键控。

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>元素

| 名称  | 说明   | 必需 |
| ----- | ------------- | -------- |
| quota | 根元素。 | 是      |

### <a name="attributes"></a>属性

| 名称                | 说明                                                                                               | 必须                                                         | 默认 |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandwidth           | 在 `renewal-period` 所指定的时间间隔内允许的最大总字节数（千字节）。 | 必须指定 `calls` 和/或 `bandwidth`。 | 空值     |
| calls               | 在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。     | 必须指定 `calls` 和/或 `bandwidth`。 | 空值     |
| counter-key         | 用于配额策略的密钥。                                                                      | 是                                                              | 空值     |
| increment-condition | 一个布尔表达式，指定是否应将请求计入配额 (`true`)             | 否                                                               | 空值     |
| renewal-period      | 在重置配额之前等待的时间长度，以秒为单位。 设置为 `0` 时，时间段设置为无限。                                                   | 是                                                              | 空值     |

> [!NOTE]
> 如果不想在其他 API 之间共享所有 API，则 API 管理中的 `counter-key` 属性值在所有 API 中必须唯一。

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站
-   **策略范围：** 所有范围

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> 验证 JWT

`validate-jwt` 策略强制要求从指定 HTTP 标头或指定查询参数提取的 JSON Web 令牌 (JWT) 必须存在且有效。

> [!IMPORTANT]
> `validate-jwt` 策略要求 `exp` 注册声明包括在 JWT 令牌中，除非 `require-expiration-time` 属性已指定并设置为 `false`。
> `validate-jwt` 策略支持 HS256 和 RS256 签名算法。 对于 HS256，必须在策略中以 base64 编码形式提供内联方式的密钥。 对于 RS256，密钥可以通过 Open ID 配置终结点来提供，或者通过提供包含公钥或公钥的模数指数对的已上传证书的 ID 来提供。
> `validate-jwt` 策略通过以下加密算法支持使用对称密钥加密的令牌：A128CBC-HS256、A192CBC-HS384、A256CBC-HS512。

### <a name="policy-statement"></a>策略语句

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>示例

#### <a name="simple-token-validation"></a>简单的令牌验证

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="token-validation-with-rsa-certificate"></a>使用 RSA 证书进行令牌验证

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certificate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory 令牌验证

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C 令牌验证

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>根据令牌声明授予操作访问权限

以下示例演示了如何使用[验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) 策略根据令牌声明值授予操作访问权限。

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>元素

| 元素             | 说明                                                                                                                                                                                                                                                                                                                                           | 必需 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | 根元素。                                                                                                                                                                                                                                                                                                                                         | 是      |
| audiences           | 包含一系列可接受且可存在于令牌上的受众声明。 如果存在多个受众值，则会对每个值进行尝试，直到有一个值成功（如果所有值都试完却没有一个成功，则表明验证失败）。 必须指定至少一个受众。                                                                     | 否       |
| issuer-signing-keys | 一系列 Base64 编码的安全密钥，用于验证签名的令牌。 如果存在多个安全密钥，则会对每个密钥进行尝试，直到所有密钥都试完（这种情况表明验证失败），或者直到有一个密钥成功（这对令牌滚动更新十分有用）。 密钥元素有一个可选的 `id` 属性，用于与 `kid` 声明进行比较。 <br/><br/>也可以使用以下项提供颁发者签名密钥：<br/><br/> -  格式 `<key certificate-id="mycertificate" />` 中的 `certificate-id`，用于指定[已上传](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add)到 API 管理的证书实体的标识符<br/>- 格式 `<key n="<modulus>" e="<exponent>" />` 中的 RSA 模数 `n` 和指数 `e` 对，用于以 base64url 编码格式指定 RSA 参数               | 否       |
| decryption-keys     | 用于解密令牌的 Base64 编码密钥列表。 如果存在多个安全密钥，则会对每个密钥进行尝试，直到所有密钥都试完（这种情况表明验证失败）或直到有一个密钥成功为止。 密钥元素有一个可选的 `id` 属性，用于与 `kid` 声明进行比较。<br/><br/>也可以使用以下项提供解密密钥：<br/><br/> -  格式 `<key certificate-id="mycertificate" />` 中的 `certificate-id`，用于指定[已上传](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add)到 API 管理的证书实体的标识符                                                 | 否       |
| issuers             | 一系列可接受的、已颁发了令牌的主体。 如果存在多个颁发者值，则会对每个值进行尝试，直到有一个值成功（如果所有值都试完却没有一个成功，则表明验证失败）。                                                                                                                                         | 否       |
| openid-config       | 一个元素，用于指定兼容的 Open ID 配置终结点，以便从该终结点获取签名密钥和颁发者。                                                                                                                                                                                                                        | 否       |
| required-claims     | 包含一系列应存在于令牌上的声明，否则令牌会被视为无效。 将 `match` 属性设置为 `all` 时，策略中的每个声明值都必须存在于令牌中，这样验证才会成功。 将 `match` 属性设置为 `any` 时，至少一个声明必须存在于令牌中，这样验证才会成功。 | 否       |

### <a name="attributes"></a>属性

| 名称                            | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                            | 必须                                                                         | 默认                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | 时间跨度。 用于指定令牌颁发者的系统时钟与 API 管理实例之间的最大预期时间差。                                                                                                                                                                                                                                                                                                               | 否                                                                               | 0 秒                                                                         |
| failed-validation-error-message | JWT 未通过验证时会在 HTTP 响应正文中返回的错误消息。 此消息必须对任何特殊字符正确地进行转义。                                                                                                                                                                                                                                                                                                 | 否                                                                               | 默认错误消息取决于验证问题，例如“JWT 不存在”。 |
| failed-validation-httpcode      | JWT 未通过验证时会返回的 HTTP 状态代码。                                                                                                                                                                                                                                                                                                                                                                                         | 否                                                                               | 401                                                                               |
| header-name                     | 包含令牌的 HTTP 标头的名称。                                                                                                                                                                                                                                                                                                                                                                                                         | 必须指定 `header-name`、`query-parameter-name`、`token-value` 中的一个。 | 不适用                                                                               |
| query-parameter-name            | 包含令牌的查询参数的名称。                                                                                                                                                                                                                                                                                                                                                                                                     | 必须指定 `header-name`、`query-parameter-name`、`token-value` 中的一个。 | 空值                                                                               |
| token-value                     | 一个表达式，返回的字符串包含 JWT 令牌。 令牌值中不得返回 `Bearer `。                                                                                                                                                                                                                                                                                                                                           | 必须指定 `header-name`、`query-parameter-name`、`token-value` 中的一个。 | 不适用                                                                               |
| id                              | 使用 `key` 元素的 `id` 属性可以指定一个字符串，该字符串将与令牌中的 `kid` 声明（如果存在）进行比较，以便找出进行签名验证时需要使用的适当密钥。                                                                                                                                                                                                                                           | 否                                                                               | 空值                                                                               |
| match                           | `claim` 元素的 `match` 属性用于指定：是否策略中的每个声明值都必须存在于令牌中验证才会成功。 可能的值为：<br /><br /> - `all` - 策略中的每个声明值都必须存在于令牌中验证才会成功。<br /><br /> - `any` - 至少一个声明值必须存在于令牌中验证才会成功。                                                       | 否                                                                               | all                                                                               |
| require-expiration-time         | 布尔值。 指定令牌中是否需要到期声明。                                                                                                                                                                                                                                                                                                                                                                               | 否                                                                               | 是                                                                              |
| require-scheme                  | 令牌方案的名称，例如“Bearer”。 设置了此属性时，策略将确保 Authorization 标头值中存在指定的方案。                                                                                                                                                                                                                                                                                    | 否                                                                               | 空值                                                                               |
| require-signed-tokens           | 布尔值。 指定令牌是否需要签名。                                                                                                                                                                                                                                                                                                                                                                                           | 否                                                                               | 是                                                                              |
| separator                       | 字符串。 指定要用于从多值声明中提取一组值的分隔符（例如 ","）。                                                                                                                                                                                                                                                                                                                                          | 否                                                                               | 空值                                                                               |
| url                             | Open ID 配置终结点 URL，可以从其获取 Open ID 配置元数据。 响应应符合以下 URL 中定义的规范：`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`。 对于 Azure Active Directory，请使用以下 URL：`https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration`，代之以目录租户名称，例如 `contoso.onmicrosoft.com`。 | 是                                                                              | 空值                                                                               |
| output-token-variable-name      | 字符串。 成功进行令牌验证后，将作为 [`Jwt`](api-management-policy-expressions.md) 类型的对象接收令牌值的上下文变量的名称                                                                                                                                                                                                                                                                                     | 否                                                                               | 空值                                                                               |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站
-   **策略范围：** 所有范围


## <a name="validate-client-certificate"></a>验证客户端证书

使用 `validate-client-certificate` 策略强制客户端提供给 API 管理实例的证书与指定的验证规则和声明（例如一个或多个证书标识的使用者或颁发者）相匹配。

要被视为有效，客户端证书必须匹配由顶级元素的属性定义的所有验证规则，并匹配至少一个已定义身份的所有已定义声明。 

使用此策略根据所需的属性检查传入的证书属性。 在这些情况下，还可以使用此策略替代客户端证书的默认验证：

* 如果已上传自定义 CA 证书以验证对托管网关的客户端请求
* 如果已将自定义证书颁发机构配置为验证对自托管网关的客户端请求

有关自定义 CA 证书和证书颁发机构的详细信息，请参阅[如何在 Azure API 管理中添加自定义 CA 证书](api-management-howto-ca-certificates.md)。 

### <a name="policy-statement"></a>策略语句

```xml
<validate-client-certificate 
    validate-revocation="true|false"
    validate-trust="true|false" 
    validate-not-before="true|false" 
    validate-not-after="true|false" 
    ignore-error="true|false">
    <identities>
        <identity 
            thumbprint="certificate thumbprint"
            serial-number="certificate serial number"
            common-name="certificate common name"
            subject="certificate subject string"
            dns-name="certificate DNS name"
            issuer-subject="certificate issuer"
            issuer-thumbprint="certificate issuer thumbprint"
            issuer-certificate-id="certificate identifier" />
    </identities>
</validate-client-certificate> 
```

### <a name="example"></a>示例

以下示例验证客户端证书以匹配策略的默认验证规则，并检查使用者和颁发者名称是否匹配指定值。

```xml
<validate-client-certificate 
    validate-revocation="true" 
    validate-trust="true" 
    validate-not-before="true" 
    validate-not-after="true" 
    ignore-error="false">
    <identities>
        <identity
            subject="C=US, ST=Illinois, L=Chicago, O=Contoso Corp., CN=*.contoso.com"
            issuer-subject="C=BE, O=FabrikamSign nv-sa, OU=Root CA, CN=FabrikamSign Root CA" />
    </identities>
</validate-client-certificate> 
```

### <a name="elements"></a>元素

| 元素             | 说明                                  | 必须 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-client-certificate        | 根元素。      | 是      |
|   identities      |  包含对客户端证书具有定义声明的标识列表。       |    否        |

### <a name="attributes"></a>属性

| 名称                            | 说明      | 必须 |  默认    |
| ------------------------------- | -----------------| -------- | ----------- |
| validate-revocation  | 布尔值。 指定是否根据在线吊销列表验证证书。  | 否   | True  |
| validate-trust | 布尔值。 指定在无法成功建立到受信任 CA 的链的情况下验证是否应失败。 | 否 | True |
| validate-not-before | 布尔值。 根据当前时间验证值。 | 否 | True |
| validate-not-after  | 布尔值。 根据当前时间验证值。 | 否 | True|
| ignore-error  | 布尔值。 指定在验证失败时，策略是继续执行下一个处理程序还是跳转到错误状态。 | 否 | False |
| 标识 | 字符串。 使证书有效的证书声明值的组合。 | 是 | 空值 |
| thumbprint | 证书指纹。 | 否 | 空值 |
| 序列号 | 证书序列号。 | 否 | 空值 |
| 公用名 | 证书公用名（Subject 字符串的一部分）。 | 否 | 空值 |
| subject | Subject 字符串。 必须遵循可分辨名称的格式。 | 否 | 空值 |
| dns-name | 使用者可选名称声明中 dnsName 条目的值。 | 否 | 空值 |
| issuer-subject | 颁发者的主题。 必须遵循可分辨名称的格式。 | 否 | 空值 |
| issuer-thumbprint | 颁发者指纹。 | 否 | 空值 |
| issuer-certificate-id | 表示颁发者公钥的现有证书实体标识符。 与其他颁发者属性互相排斥。  | 否 | 空值 |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站
-   **策略范围：** 所有范围

## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

-   [API 管理中的策略](api-management-howto-policies.md)
-   [转换 API](transform-api.md)
-   [策略参考](./api-management-policies.md)，获取策略语句及其设置的完整列表
-   [策略示例](./policy-reference.md)
