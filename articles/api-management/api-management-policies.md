---
title: Azure API 管理策略 | Microsoft Docs
description: 了解可在 Azure API 管理中使用的策略。 策略允许发布者通过配置更改 API 行为。
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 07/19/2021
ms.author: apimpm
ms.openlocfilehash: c6856226c1eda0e8d5fde7df6d2b251007a47fa2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468511"
---
# <a name="api-management-policies"></a>API 管理策略
本部分提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](api-management-howto-policies.md)。

 策略是一项强大的系统功能，允许发布者通过配置更改 API 的行为。 策略是一组语句，在请求或响应 API 时按顺序执行。 常用的语句包括从 XML 到 JSON 的格式转换，并调用速率限制来限制从一名开发人员传入的调用量。 许多策略开箱即用。

 在任何 API 管理策略中，策略表达式可以用作属性值或文本值，除非该策略另外指定。 某些策略（如[控制流](api-management-advanced-policies.md#choose)和[设置变量](api-management-advanced-policies.md#set-variable)策略）基于策略表达式。 有关详细信息，请参阅[高级策略](api-management-advanced-policies.md#AdvancedPolicies)和[策略表达式](api-management-policy-expressions.md)。

##  <a name="policies"></a><a name="ProxyPolicies"></a> 策略

-   [访问限制策略](api-management-access-restriction-policies.md#AccessRestrictionPolicies)
    -   [检查 HTTP 标头](api-management-access-restriction-policies.md#CheckHTTPHeader) - 必须存在 HTTP 标头和/或强制采用 HTTP 标头的值。
    -   [按订阅限制调用速率](api-management-access-restriction-policies.md#LimitCallRate) - 根据订阅限制调用速率，避免 API 使用量暴增。
    -   [按密钥限制调用速率](api-management-access-restriction-policies.md#LimitCallRateByKey) - 根据密钥限制调用速率，避免 API 使用量暴增。
    -   [限制调用方 IP](api-management-access-restriction-policies.md#RestrictCallerIPs) - 筛选（允许/拒绝）来自特定 IP 地址和/或地址范围的调用。
    -   [按订阅设置使用量配额](api-management-access-restriction-policies.md#SetUsageQuota) - 允许根据订阅强制实施可续订或有生存期的调用量和/或带宽配额。
    -   [按密钥设置使用量配额](api-management-access-restriction-policies.md#SetUsageQuotaByKey) - 允许根据密钥强制消耗可续订或有生存期的调用量和/或带宽配额。
    -   [验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) - 强制从指定 HTTP 标头或指定查询参数提取的 JWT 必须存在且有效。
    -   [验证客户端证书](api-management-access-restriction-policies.md#validate-client-certificate) - 强制客户端提供给 API 管理实例的证书与指定的验证规则和声明相匹配。
-   [高级策略](api-management-advanced-policies.md#AdvancedPolicies)
    -   [控制流](api-management-advanced-policies.md#choose) - 根据布尔表达式的求值，有条件地应用策略语句。
    -   [转发请求](api-management-advanced-policies.md#ForwardRequest) - 将请求转发到后端服务。
    -   [限制并发](api-management-advanced-policies.md#LimitConcurrency) - 阻止括住的策略一次执行超过指定数量的请求。
    -   [记录到事件中心](api-management-advanced-policies.md#log-to-eventhub) - 将指定格式的消息发送到记录器实体定义的消息目标。
    -   [发出指标](api-management-advanced-policies.md#emit-metrics) - 在执行时将自定义指标发送到 Application Insights。
    -   [模拟响应](api-management-advanced-policies.md#mock-response) - 中止管道执行，将模拟的响应直接返回给调用方。
    -   [重试](api-management-advanced-policies.md#Retry) - 重试执行括住的策略语句，直到符合条件为止。 系统会按指定的时间间隔重复执行，直到达到指定的重试计数为止。
    -   [返回响应](api-management-advanced-policies.md#ReturnResponse) - 中止管道执行，将指定的响应直接返回给调用方。
    -   [发送单向请求](api-management-advanced-policies.md#SendOneWayRequest) - 将请求发送到指定的 URL，无需等待响应。
    -   [发送请求](api-management-advanced-policies.md#SendRequest) - 将请求发送到指定的 URL。
    -   [设置 HTTP 代理](api-management-advanced-policies.md#SetHttpProxy) - 允许通过 HTTP 代理路由转发请求。
    -   [设置变量](api-management-advanced-policies.md#set-variable) - 保存命名上下文变量中的值供以后访问。
    -   [设置请求方法](api-management-advanced-policies.md#SetRequestMethod) - 允许更改请求的 HTTP 方法。
    -   [设置状态代码](api-management-advanced-policies.md#SetStatus) - 将 HTTP 状态代码更改为指定的值。
    -   [跟踪](api-management-advanced-policies.md#Trace) - 将自定义跟踪添加到 [API 检查器](./api-management-howto-api-inspector.md)输出、Application Insights 遥测和资源日志。
    -   [等待](api-management-advanced-policies.md#Wait) - 在继续下一步之前，等待括住的[发送请求](api-management-advanced-policies.md#SendRequest)、[从缓存中获取值](api-management-caching-policies.md#GetFromCacheByKey)或[控制流](api-management-advanced-policies.md#choose)策略完成。
-   [身份验证策略](api-management-authentication-policies.md#AuthenticationPolicies)
    -   [使用基本方法进行身份验证](api-management-authentication-policies.md#Basic) - 使用基本身份验证方法向后端服务进行身份验证。
    -   [使用客户端证书进行身份验证](api-management-authentication-policies.md#ClientCertificate) - 使用客户端证书向后端服务进行身份验证。
    -   [使用托管标识进行身份验证](api-management-authentication-policies.md#ManagedIdentity) - 使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)向后端服务进行身份验证。
-   [缓存策略](api-management-caching-policies.md#CachingPolicies)
    -   [从缓存中获取](api-management-caching-policies.md#GetFromCache) - 执行缓存查找，并返回有效的缓存响应（如果有）。
    -   [存储到缓存](api-management-caching-policies.md#StoreToCache) - 根据指定的缓存控制配置来缓存响应。
    -   [从缓存中获取值](api-management-caching-policies.md#GetFromCacheByKey) - 按密钥检索缓存项。
    -   [在缓存中存储值](api-management-caching-policies.md#StoreToCacheByKey) - 按密钥在缓存中存储项。
    -   [从缓存中删除值](api-management-caching-policies.md#RemoveCacheByKey) - 按密钥在缓存中删除项。
-   [跨域策略](api-management-cross-domain-policies.md#CrossDomainPolicies)
    -   [允许跨域调用](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - 使 API 能够通过 Adobe Flash 和基于 Microsoft Silverlight 浏览器的客户端进行访问。
    -   [CORS](api-management-cross-domain-policies.md#CORS) - 向操作或 API 添加跨源资源共享 (CORS) 支持，允许从基于浏览器的客户端进行跨域调用。
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - 向操作或 API 添加填充型 JSON (JSONP) 支持，以便从基于 JavaScript 浏览器的客户端执行跨域调用。
-   [转换策略](api-management-transformation-policies.md#TransformationPolicies)
    -   [将 JSON 转换为 XML](api-management-transformation-policies.md#ConvertJSONtoXML) - 将请求或响应正文从 JSON 转换为 XML。
    -   [将 XML 转换为 JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - 将请求或响应正文从 XML 转换为 JSON。
    -   [查找并替换正文中的字符串](api-management-transformation-policies.md#Findandreplacestringinbody) - 查找请求或响应子字符串，并将其替换为不同的子字符串。
    -   [在内容中屏蔽 URL](api-management-transformation-policies.md#MaskURLSContent) - 重写（屏蔽）响应正文中的链接，使其通过网关指向等效的链接。
    -   [设置后端服务](api-management-transformation-policies.md#SetBackendService) - 更改传入请求的后端服务。
    -   [设置正文](api-management-transformation-policies.md#SetBody) - 设置传入和传出请求的消息正文。
    -   [设置 HTTP 标头](api-management-transformation-policies.md#SetHTTPheader) - 向现有的响应和/或请求标头赋值，或者添加新的响应和/或请求标头。
    -   [设置查询字符串参数](api-management-transformation-policies.md#SetQueryStringParameter) - 添加、删除请求查询字符串参数或替换其值。
    -   [重写 URL](api-management-transformation-policies.md#RewriteURL) - 将请求 URL 从其公用格式转换为 Web 服务所需的格式。
    -   [使用 XSLT 转换 XML](api-management-transformation-policies.md#XSLTransform) - 在请求或响应正文中将 XSL 转换应用到 XML。
- [Dapr 集成策略](api-management-dapr-policies.md)
    - [向服务发送请求](api-management-dapr-policies.md#invoke) - 使用 Dapr 运行时查找 Dapr 微服务并与之进行可靠的通信。
    -  [将消息发送到发布/订阅主题](api-management-dapr-policies.md#pubsub) - 使用 Dapr 运行时将消息发布到某个发布/订阅主题。
    -  [触发输出绑定](api-management-dapr-policies.md#bind) - 使用 Dapr 运行时通过输出绑定调用外部系统。
- [验证策略](validation-policies.md)
    - [验证内容](validation-policies.md#validate-content) - 根据 API 架构验证请求或响应正文的大小或 JSON 架构。
. 
    - [验证参数](validation-policies.md#validate-parameters) - 根据 API 架构验证请求标头、查询或路径参数。
    - [验证标头](validation-policies.md#validate-headers) - 根据 API 架构验证响应标头。
    - [验证状态代码](validation-policies.md#validate-status-code) - 根据 API 架构验证响应中的 HTTP 状态代码。

## <a name="next-steps"></a>后续步骤
有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略示例](./policy-reference.md)
