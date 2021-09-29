---
title: Azure API 管理 Dapr 集成策略 | Microsoft Docs
description: 了解用于与 Dapr 微服务扩展进行交互的 Azure API 管理策略。
author: dlepow
ms.author: danlep
ms.date: 02/18/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 4251d87727fafbb136b29eb2479ac0a70f2d027d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647549"
---
# <a name="api-management-dapr-integration-policies"></a>API 管理 Dapr 集成策略

本主题提供 Dapr 集成 API 管理策略的参考。 Dapr 是一种可移植运行时，用于通过任何语言或框架来构建无状态和有状态的基于微服务的应用程序。 它能对常见的微服务模式进行编码，例如使用内置重试逻辑进行服务发现和调用，使用至少一次的传递语义进行发布和订阅，或者使用可插拔的绑定资源来简化使用外部服务的组合。 请转到 [dapr.io](https://dapr.io) 以获取有关如何开始使用 Dapr 的详细信息和说明。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](api-management-howto-policies.md)。

> [!IMPORTANT]
> 本主题中引用的策略仅在启用了 Dapr 支持的 [API 管理网关的自承载版本](self-hosted-gateway-overview.md)中有效。

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>在自承载网关中启用 Dapr 支持

若要在自承载网关中启用 Dapr 支持，请将下面的 [Dapr 注释](https://github.com/dapr/docs/blob/master/README.md)添加到 [ Kubernetes 部署模板](how-to-deploy-self-hosted-gateway-kubernetes.md)中，将“app-name”替换为所需的名称。 [此处](https://aka.ms/apim/dapr/walkthru)提供了有关通过 Dapr 设置和使用 API 管理的完整演练。
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>分布式 Application Runtime (Dapr) 集成策略

-  [发送对服务的请求](api-management-dapr-policies.md#invoke)：使用 Dapr 运行时查找 Dapr 微服务并与之进行可靠的通信。 若要了解有关 Dapr 中服务调用的详细信息，请参阅这个 [README](https://github.com/dapr/docs/blob/master/README.md#service-invocation) 文件中的说明。
-  [将消息发送到发布/订阅主题](api-management-dapr-policies.md#pubsub)：使用 Dapr 运行时将消息发布到发布/订阅主题。 若要了解有关 Dapr 中发布/订阅消息的详细信息，请参阅这个 [README](https://github.com/dapr/docs/blob/master/README.md) 文件中的说明。
-  [触发器输出绑定](api-management-dapr-policies.md#bind)：使用 Dapr 运行时通过输出绑定调用外部系统。 若要了解有关 Dapr 中绑定的详细信息，请参阅此 [README](https://github.com/dapr/docs/blob/master/README.md) 文件中的说明。

## <a name="send-request-to-a-service"></a><a name="invoke"></a> 发送对服务的请求

此策略将当前请求的目标 URL 设置为 `http://localhost:3500/v1.0/invoke/{app-id}[.{ns-name}]/method/{method-name}`，并且用策略语句中指定的值替换模板参数。

策略假设 Dapr 在网关所在的 pod 中的 sidecar 容器中运行。 接收到请求后，Dapr 运行时执行服务发现和实际调用，包括 HTTP 和 gRPC 之间可能的协议转换、重试、分布式跟踪和错误处理。

### <a name="policy-statement"></a>策略语句

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" dapr-namespace="ns-name" />
```

### <a name="examples"></a>示例

#### <a name="example"></a>示例

下面的示例演示如何在名为“echo”的微服务上调用名为“back”的方法。 `set-backend-service` 策略将目标 URL 设置为 `http://localhost:3500/v1.0/invoke/echo.echo-app/method/back`。 `forward-request` 策略将请求分派到 Dapr 运行时，Dapr 运行时将请求传递给微服务。

为清楚起见，`forward-request` 策略在这里显示。 策略通常通过 `base` 关键字从全局范围“继承”。

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" dapr-namespace="echo-app" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>元素

| 元素             | 说明  | 必须 |
|---------------------|--------------|----------|
| set-backend-service | Root 元素 | 是      |

### <a name="attributes"></a>属性

| 属性        | 说明                     | 必须 | 默认 |
|------------------|---------------------------------|----------|---------|
| backend-id       | 必须设置为“dapr”           | 是      | 不适用     |
| dapr-app-id      | 目标微服务的名称。 用于在 Dapr 中形成 [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) 参数。| 是 | 不适用 |
| dapr-method      | 要在目标微服务上调用的方法或 URL 的名称。 映射到 Dapr 中的 [method-name](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) 参数。| 是 | 空值 |
| dapr-namespace   | 目标微服务所驻留的命名空间的名称。 用于在 Dapr 中形成 [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) 参数。| 否 | 空值 |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站
- **策略范围：** 所有范围

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> 将消息发送到发布/订阅主题

此策略指示 API 管理网关向 Dapr 发布/订阅主题发送消息。 策略通过向 `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` 发出 HTTP POST 请求、替换模板参数并添加策略语句中指定的内容来实现这一点。

策略假设 Dapr 运行时正在网关所在的 pod 中的 sidecar 容器中运行。 Dapr 运行时实现发布/订阅语义。

### <a name="policy-statement"></a>策略语句

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>示例

#### <a name="example"></a>示例

下面的示例演示如何将当前请求的主体发送到“orders”发布/订阅[组件](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)的“new”[主题](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)。 从 Dapr 运行时接收到的响应存储在 [context](api-management-policy-expressions.md#ContextVariables) 对象的“变量”集合的“daprresponse”项中。

例如，如果 Dapr 运行时找不到目标主题，并且响应错误，则会触发“on-error”部分。 从 Dapr 运行时收到的响应被逐字返回给调用方。 否则，返回默认的 `200 OK` 响应。

“backend”部分为空，请求不会转发到后端。

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>元素

| 元素             | 说明  | 必需 |
|---------------------|--------------|----------|
| publish-to-dapr     | Root 元素 | 是      |

### <a name="attributes"></a>属性

| 属性        | 说明                     | 必须 | 默认 |
|------------------|---------------------------------|----------|---------|
| pubsub-name      | 目标 PubSub 组件的名称。 映射到 Dapr 中的 [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) 参数。 如果不存在，则 topic 属性值必须采用 `pubsub-name/topic-name` 形式。    | 否       | 无    |
| 主题            | 主题的名称。 映射到 Dapr 中的 [topic](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) 参数。               | 是      | 空值     |
| ignore-error     | 如果设置为 `true`，则指示策略在收到来自 Dapr 运行时的错误时不触发[“on error”](api-management-error-handling-policies.md)部分 | 否 | `false` |
| response-variable-name | 用于存储来自 Dapr 运行时的响应的[变量](api-management-policy-expressions.md#ContextVariables)集合项的名称 | 否 | 无 |
| timeout | 等待 Dapr 运行时做出响应的时间（以秒为单位）。 范围为 1 到 240 秒。 | 否 | 5 |
| template | 用于转换消息内容的模板引擎。 “Liquid”是唯一支持的值。 | 否 | 无 |
| content-type | 消息内容的类型。 “application/json”是唯一支持的值。 | 否 | 无 |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站、出站、错误时
- **策略范围：** 所有范围

## <a name="trigger-output-binding"></a><a name="bind"></a> 触发器输出绑定

此策略指示 API 管理网关触发出站 Dapr [绑定](https://github.com/dapr/docs/blob/master/README.md)。 策略通过向 `http://localhost:3500/v1.0/bindings/{{bind-name}}` 发出 HTTP POST 请求、替换模板参数并添加策略语句中指定的内容来实现这一点。

策略假设 Dapr 运行时正在网关所在的 pod 中的 sidecar 容器中运行。 Dapr 运行时负责调用绑定表示的外部资源。

### <a name="policy-statement"></a>策略语句

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>示例

#### <a name="example"></a>示例

下面的示例演示了如何触发名为“external-systems”的出站绑定，其操作名称为“create”，元数据由两个名为“source”和“client-ip”的键/值项组成，并且主体来自原始请求。 从 Dapr 运行时接收到的响应在 [context](api-management-policy-expressions.md#ContextVariables) 对象的“变量”集合的“bind-response”项中捕获。

如果 Dapr 运行时由于某种原因而失败并返回错误，则会触发“on-error”部分，并且从 Dapr 运行时收到的响应会逐字返回给调用方。 否则，返回默认的 `200 OK` 响应。

“backend”部分为空，请求不会转发到后端。

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>元素

| 元素             | 说明  | 必需 |
|---------------------|--------------|----------|
| invoke-dapr-binding | Root 元素 | 是      |
| metadata            | 以键/值对的形式绑定特定的元数据。 映射到 Dapr 中的[元数据](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings)属性。 | 否 |
| 数据            | 消息的内容。 映射到 Dapr 中的[数据](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings)属性。 | 否 |


### <a name="attributes"></a>属性

| 属性        | 说明                     | 必须 | 默认 |
|------------------|---------------------------------|----------|---------|
| name            | 目标绑定名称。 必须与 Dapr 中的[已定义](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure)的绑定名称匹配。           | 是      | 不适用     |
| operation       | 目标操作名称（特定于绑定）。 映射到 Dapr 中的[操作](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings)属性。 | 否 | 无 |
| ignore-error     | 如果设置为 `true`，则指示策略在收到来自 Dapr 运行时的错误时不触发[“on error”](api-management-error-handling-policies.md)部分 | 否 | `false` |
| response-variable-name | 用于存储来自 Dapr 运行时的响应的[变量](api-management-policy-expressions.md#ContextVariables)集合项的名称 | 否 | 无 |
| timeout | 等待 Dapr 运行时做出响应的时间（以秒为单位）。 范围为 1 到 240 秒。 | 否 | 5 |
| template | 用于转换消息内容的模板引擎。 “Liquid”是唯一支持的值。 | 否 | 无 |
| content-type | 消息内容的类型。 “application/json”是唯一支持的值。 | 否 | 无 |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站、出站、错误时
- **策略范围：** 所有范围
