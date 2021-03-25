---
title: Azure 应用程序配置 REST API - 限制
description: 帮助了解使用 Azure 应用程序配置 REST API 时的限制的参考页面
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b4472fdcacfe5b62c0cded089224c6d41e32f47d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932432"
---
# <a name="throttling"></a>遏制

配置存储对其可提供服务的请求存在限制。 超过配置存储分配的配额的请求都将收到 HTTP 429（请求太多）响应。

限制分为不同的配额策略：

- **总请求数** - 请求总数
- **总带宽** - 出站数据（以字节为单位）
- **存储量** - 用户数据的总存储大小（以字节为单位）

## <a name="handling-throttled-responses"></a>处理受限响应

达到给定配额的速率限制后，服务器将使用状态代码 429 来响应此类型的后续请求。 429 响应将包含一个 retry-after-ms 标头，它向客户端提供在准许补充请求配额之前建议的等待时间（毫秒） 。

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

在上述示例中，客户端超过了它允许的配额，系统建议它放慢速度，等待 10 毫秒，然后再尝试任何后续请求。 客户端还应考虑渐进式回退。

## <a name="other-retry"></a>其他重试

此服务可识别除限制外需要客户端重试的其他情况（例如：503 服务不可用）。 在所有这类情况下，都将提供 `retry-after-ms` 响应头。 为了提高可靠性，建议客户端采用建议的时间间隔并执行重试。

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
