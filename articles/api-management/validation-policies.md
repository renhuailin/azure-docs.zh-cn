---
title: Azure API 管理验证策略 | Microsoft Docs
description: 了解可以使用 Azure API 管理中的哪些策略来验证请求和响应。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 08/20/2021
ms.author: apimpm
ms.openlocfilehash: 6946dcaf713bda83e5bce7e823b5f7e26ccd6e92
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694604"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>用于验证请求和响应的 API 管理策略

本文提供有关以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](./api-management-policies.md)。

使用验证策略可以根据 OpenAPI 架构验证 API 请求和响应，并防范头注入或有效负载注入等漏洞。 虽然验证策略不能取代 Web 应用程序防火墙，但它们可让用户灵活应对依赖于静态预定义规则的安全产品所不能涵盖的其他威胁种类。

## <a name="validation-policies"></a>验证策略

- [验证内容](#validate-content) - 根据 API 架构验证请求或响应正文的大小或 JSON 架构。 
- [验证参数](#validate-parameters) - 根据 API 架构验证请求头、查询或路径参数。
- [验证标头](#validate-headers) - 根据 API 架构验证响应标头。
- [验证状态代码](#validate-status-code) - 根据 API 架构验证响应中的 HTTP 状态代码。

> [!NOTE]
> 验证策略可使用的 API 架构的最大大小为 4 MB。 如果架构超过此限制，验证策略将在运行时返回错误。 若要提高限制，请与[支持部门](https://azure.microsoft.com/support/options/)联系。 

## <a name="actions"></a>操作

每个验证策略包含一个用于指定操作的特性，该操作将由 API 管理在根据 API 架构验证 API 请求或响应中的实体时执行。 可为 API 架构中表示的元素指定操作，并可以根据策略为 API 架构中未表示的元素指定操作。 在策略的子元素中指定的操作将替代针对其父级指定的操作。

可用操作：

| 操作         | 说明          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignore | 跳过验证。 |
| prevent | 阻止请求或响应处理，记录详细的[验证错误](#validation-errors)，并返回错误。 检测到第一组错误时便中断处理。 
| 检测 (detect) | 记录[验证错误](#validation-errors)，但不中断请求或响应处理。 |

## <a name="logs"></a>日志

有关在执行策略期间出现的验证错误的详细信息将记录到策略根元素的 `errors-variable-name` 特性中指定的 `context.Variables` 内的变量。 在 `prevent` 操作中进行配置后，验证错误会阻止进一步的请求或响应处理，并会传播到 `context.LastError` 属性。 

若要调查错误，请使用[跟踪](api-management-advanced-policies.md#Trace)策略将上下文变量中的错误记录到 [Application Insights](api-management-howto-app-insights.md)。

## <a name="performance-implications"></a>性能影响

添加验证策略可能会影响 API 吞吐量。 以下一般原则适用：
* API 架构越大，吞吐量越低。 
* 请求或响应中的有效负载越大，吞吐量越低。 
* 相比于有效负载大小，API 架构大小对性能的影响更大。 
* 在某些情况下，根据若干 MB 大小的 API 架构进行验证可能会导致请求或响应超时。 在“消耗”和“开发人员”服务层级中，这种影响更为明显。  

我们建议在预期的生产工作负载中执行负载测试，以评估验证策略对 API 吞吐量的影响。

## <a name="validate-content"></a>验证内容

`validate-content` 策略根据 API 架构验证请求或响应正文的大小或 JSON 架构。 不支持 JSON 以外的格式。

### <a name="policy-statement"></a>策略语句

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>示例

在以下示例中，将以检测模式验证请求和响应中的 JSON 有效负载。 将阻止有效负载大于 100 KB 的消息。 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>元素

| 名称         | 说明                                                                                                                                   | 必需 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-content | 根元素。                                                                                                                               | 是      |
| 内容 | 添加其中的一个或多个元素可以验证请求或响应中的内容类型，并执行指定的操作。  | 否 |

### <a name="attributes"></a>属性

| 名称                       | 描述                                                                                                                                                            | 必须 | 默认 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| unspecified-content-type-action | 针对其内容类型未在 API 架构中指定的请求或响应执行的[操作](#actions)。 |  是     | 空值   |
| max_size | 请求或响应正文的最大长度（以字节为单位），根据 `Content-Length` 标头进行检查。 如果请求正文或响应正文经过压缩，则此值是解压缩后的长度。 最大允许值：102,400 字节 (100 KB)。 （如果需要增加此限制，请联系[支持](https://azure.microsoft.com/support/options/)。） | 是       | 空值   |
| size-exceeded-action | 针对其正文超过 `max-size` 中指定大小的请求或响应执行的[操作](#actions)。 |  是     | 空值   |
| errors-variable-name | `context.Variables` 中的要将验证错误记录到的变量的名称。  |   否    | 空值   |
| type | 要对其执行正文验证的内容类型，根据 `Content-Type` 头检查。 此值不区分大小写。 如果为空，此值将应用到 API 架构中指定的每个内容类型。 |   否    |  空值  |
| validate-as | 用于验证具有匹配内容类型的请求或响应正文的验证引擎。 目前唯一支持的值是“json”。   |  是     |  空值  |
| action | 要对其正文与指定内容类型不匹配的请求或响应执行的[操作](#actions)。  |  是      | 空值   |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站、出站、错误时

-   **策略范围：** 所有范围

## <a name="validate-parameters"></a>验证参数

`validate-parameters` 策略根据 API 架构验证请求中的头、查询或路径参数。

> [!IMPORTANT]
> 如果使用低于 `2021-01-01-preview` 的管理 API 版本导入了某个 API，`validate-parameters` 策略可能不起作用。 可能需要使用管理 API 版本 `2021-01-01-preview` 或更高版本[重新导入你的 API](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate)。


### <a name="policy-statement"></a>策略语句

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>示例

在此示例中，将以防护模式验证所有查询和路径参数，以检测模式验证头。 替代多个头参数的验证：

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>元素

| 名称         | 说明                                                                                                                                   | 必需 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-parameters | 根元素。 针对请求中的所有参数指定默认验证操作。                                                                                                                              | 是      |
| headers | 添加此元素可以替代针对请求中的头参数执行的默认验证操作。   | 否 |
| query | 添加此元素可以替代针对请求中的查询参数执行的默认验证操作。  | 否 |
| path | 添加此元素可以替代针对请求中的 URL 路径参数执行的默认验证操作。  | 否 |
| 参数 (parameter) | 为命名参数添加一个或多个元素可以替代验证操作的更高级别的配置。 | 否 |

### <a name="attributes"></a>属性

| 名称                       | 描述                                                                                                                                                            | 必须 | 默认 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| specified-parameter-action | 针对 API 架构中指定的请求参数执行的[操作](#actions)。 <br/><br/> 在 `headers`、`query` 或 `path` 元素中提供时，该值将替代 `specified-parameter-action` 元素中的 `validate-parameters` 值。  |  是     | 空值   |
| unspecified-parameter-action | 针对未在 API 架构中指定的请求参数执行的[操作](#actions)。 <br/><br/>在 `headers` 或 `query` 元素中提供时，该值将替代 `validate-parameters` 元素中的 `unspecified-parameter-action` 值。 |  是     | 空值   |
| errors-variable-name | `context.Variables` 中的要将验证错误记录到的变量的名称。  |   否    | 空值   |
| name | 要替代其验证操作的参数的名称。 此值不区分大小写。  | 是 | 空值 |
| action | 针对具有匹配名称的参数执行的[操作](#actions)。 如果该参数已在 API 架构中指定，此值将替代更高级别的 `specified-parameter-action` 配置。 如果该参数未在 API 架构中指定，此值将替代更高级别的 `unspecified-parameter-action` 配置。| 是 | 空值 | 

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** 入站

-   **策略范围：** 所有范围

## <a name="validate-headers"></a>验证头

`validate-headers` 策略根据 API 架构验证响应头。

> [!IMPORTANT]
> 如果使用低于 `2021-01-01-preview` 的管理 API 版本导入了某个 API，`validate-headers` 策略可能不起作用。 可能需要使用管理 API 版本 `2021-01-01-preview` 或更高版本重新导入该 API。

### <a name="policy-statement"></a>策略语句

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>示例

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>元素

| 名称         | 描述                                                                                                                                   | 必需 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-headers | 根元素。 针对响应中的所有头指定默认验证操作。                                                                                                                              | 是      |
| 标头 | 为命名的头添加一个或多个元素可以替代针对响应中的头执行的默认验证操作。 | 否 |

### <a name="attributes"></a>属性

| 名称                       | 描述                                                                                                                                                            | 必须 | 默认 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| specified-header-action | 针对 API 架构中指定的响应头执行的[操作](#actions)。  |  是     | 空值   |
| unspecified-header-action | 针对未在 API 架构中指定的响应头执行的[操作](#actions)。  |  是     | 空值   |
| errors-variable-name | `context.Variables` 中的要将验证错误记录到的变量的名称。  |   否    | 空值   |
| name | 要替代其验证操作的头的名称。 此值不区分大小写。 | 是 | 空值 |
| action | 针对具有匹配名称的头执行的[操作](#actions)。 如果该头已在 API 架构中指定，此值将替代 `specified-header-action` 元素中的 `validate-headers` 值。 否则，它将替代 validate-headers 元素中的 `unspecified-header-action` 值。 | 是 | 空值 | 

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** outbound、on-error

-   **策略范围：** 所有范围

## <a name="validate-status-code"></a>验证状态代码

`validate-status-code` 策略根据 API 架构验证响应中的 HTTP 状态代码。 此策略可用于防止泄漏后端错误（可能包含堆栈跟踪）。 

### <a name="policy-statement"></a>策略语句

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>示例

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>元素

| 名称         | 说明                                                                                                                                   | 必需 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-status-code | 根元素。                                                                                                | 是      |
| status-code | 为 HTTP 状态代码添加一个或多个元素可以替代针对响应中的状态代码执行的默认验证操作。 | 否 |

### <a name="attributes"></a>属性

| 名称                       | 说明                                                                                                                                                            | 必须 | 默认 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| unspecified-status-code-action | 要对 API 架构中未指定的响应中的 HTTP 状态代码执行的[操作](#actions)。  |  是     | 空值   |
| errors-variable-name | `context.Variables` 中的要将验证错误记录到的变量的名称。  |   否    | 空值   |
| code | 要替代其验证操作的 HTTP 状态代码。 | 是 | 空值 |
| action | 要对 API 架构中未指定的匹配状态代码执行的[操作](#actions)。 如果该状态代码已在 API 架构中指定，此替代将不会生效。 | 是 | 空值 | 

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

-   **策略节：** outbound、on-error

-   **策略范围：** 所有范围


## <a name="validation-errors"></a>验证错误
下表列出了验证策略的所有可能错误。 

* **详细信息** - 可用于调查错误。 不应公开共享。
* **公共响应** - 返回到客户端的错误。 不会泄漏实现详细信息。

当验证策略指定 `prevent` 操作并产生错误时，API 管理的响应将包括 HTTP 状态代码：在入站部分中应用该策略时为 400，在出站部分应用该策略时为 502。


| **名称**   | **类型**                                                        | **验证规则** | **详细信息**                                                                                                                                       | **公共响应**                                                                                                                       | **操作**           |
|----|----|---|---|---|----|
| **validate-content** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | 请求正文的长度为 {size} 个字节，超过了配置的限制({maxSize} 字节)。                                                       | 请求正文的长度为 {size} 个字节，超过了限制({maxSize} 字节)。                                                          | detect / prevent |
||ResponseBody                                                    | SizeLimit           | 响应正文的长度为 {size} 个字节，超过了配置的限制({maxSize} 字节)。                                                      | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| {messageContentType}                 | RequestBody                                                     | 未指定         | 不允许未指定的内容类型 {messageContentType}。                                                                                     | 不允许未指定的内容类型 {messageContentType}。                                                                             | detect / prevent |
| {messageContentType}                 | ResponseBody                                                    | 未指定         | 不允许未指定的内容类型 {messageContentType}。                                                                                     | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| | ApiSchema                                                       |                     | API 的架构不存在或无法解析。                                                                                          | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
|                                      | ApiSchema                                                       |                     | API 的架构未指定定义。                                                                                                        | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| {messageContentType}                 | RequestBody / ResponseBody                                      | MissingDefinition   | API 的架构不包含与内容类型 {messageContentType} 关联的定义 {definitionName}。                        | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | 请求正文不符合与内容类型 {messageContentType} 关联的定义 {definitionName}。<br/><br/>{valError.Message} 行: {valError.LineNumber}，位置: {valError.LinePosition}                  | 请求正文不符合与内容类型 {messageContentType} 关联的定义 {definitionName}。<br/><br/>{valError.Message} 行: {valError.LineNumber}，位置: {valError.LinePosition}            | detect / prevent |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | 响应正文不符合与内容类型 {messageContentType} 关联的定义 {definitionName}。<br/><br/>{valError.Message} 行: {valError.LineNumber}，位置: {valError.LinePosition}                                       | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
|                                      | RequestBody                                                     | ValidationException | 无法验证 {messageContentType} 内容类型的请求正文。<br/><br/>{exception details}                                                                | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
|                                      | ResponseBody                                                    | ValidationException | 无法验证 {messageContentType} 内容类型的响应正文。<br/><br/>{exception details}                                                                | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| **validate-parameters / validate-headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName} / {headerName}           | QueryParameter / PathParameter / RequestHeader                  | 未指定         | 不允许未指定的 {path parameter / query parameter / header} {paramName}。                                                               | 不允许未指定的 {path parameter / query parameter / header} {paramName}。                                                       | detect / prevent |
| {headerName}                         | ResponseHeader                                                  | 未指定         | 不允许未指定的头 {headerName}。                                                                                                   | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
|                                      |ApiSchema                                                       |                     | API 的架构不存在或无法解析。                                                                                            | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
|                                       | ApiSchema                                                       |                     | API 架构未指定定义。                                                                                                          | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | API 的架构不包含与 {query parameter / path parameter / header} {paramName} 关联的定义 {definitionName}。  | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | 请求不能包含 {query parameter / path parameter / header} {paramName} 的多个值。                                           | 请求不能包含 {query parameter / path parameter / header} {paramName} 的多个值。                                   | detect / prevent |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | 响应不能包含头 {headerName} 的多个值。                                                                              | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | {query parameter / path parameter / header} {paramName} 的值不符合定义。<br/><br/>{valError.Message} 行: {valError.LineNumber}，位置: {valError.LinePosition}                                          | {query parameter / path parameter / header} {paramName} 的值不符合定义。<br/><br/>{valError.Message} 行: {valError.LineNumber}，位置: {valError.LinePosition}                              | detect / prevent |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | 头 {headerName} 的值不符合定义。<br/><br/>{valError.Message} 行: {valError.LineNumber}，位置: {valError.LinePosition}                                                                              | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | 无法根据定义分析 {query parameter / path parameter / header} {paramName} 的值。 <br/><br/>{ex.Message}                                | 无法根据定义分析 {query parameter / path parameter / header} {paramName} 的值。 <br/><br/>{ex.Message}                      | detect / prevent |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | 无法根据定义分析头 {headerName} 的值。                                                                  | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | 无法验证 {Query parameter / Path parameter / Header} {paramName}。<br/><br/>{exception details}                                                                      | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| {headerName}                         | ResponseHeader                                                  | ValidationError     | 无法验证头 {headerName}。<br/><br/>{exception details}                                                                                                          | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |
| **validate-status-code**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-code}                        | StatusCode                                                      | 未指定         | 不允许响应状态代码 {status-code}。                                                                                                | 由于发生内部错误，无法处理请求。 请与 API 所有者联系。                                                       | detect / prevent |


下表列出了验证错误的所有可能原因值以及可能的消息值：

|  **原因**         |    **消息** |
|---|---|  
| 错误的请求       |     针对上下文变量的 {Details}，针对客户端的 {Public response}|
| 不允许响应  | 针对上下文变量的 {Details}，针对客户端的 {Public response} |






## <a name="next-steps"></a>后续步骤

有关使用策略的详细信息，请参阅：

-   [API 管理中的策略](api-management-howto-policies.md)
-   [转换 API](transform-api.md)
-   [策略参考](./api-management-policies.md)，其中提供了策略语句及其设置的完整列表
-   [策略示例](./policy-reference.md)
-   [错误处理](./api-management-error-handling-policies.md)
