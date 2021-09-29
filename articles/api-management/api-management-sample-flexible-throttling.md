---
title: 使用 Azure API 管理进行高级请求限制
description: 了解如何使用 Azure API 管理创建并应用灵活的配额和速率限制策略。
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: danlep
ms.openlocfilehash: 177d393041b397e504c1c8c155fe9b2b1f99b451
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653678"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>使用 Azure API 管理进行高级请求限制
限制传入请求是 Azure API 管理的重要功能。 通过控制请求的速率或传输的请求/数据总量，API 管理让 API 提供程序能够保护其 API 不被滥用，为不同的 API 产品层创造价值。

## <a name="rate-limits-and-quotas"></a>速率限制和配额
速率限制和配额用于不同目的。

### <a name="rate-limits"></a>速率限制
速率限制通常用于防止短暂而密集的数据量突发。 例如，如果你知道后端服务的数据库在调用量大的情况下存在瓶颈，则可使用此设置将 `rate-limit-by-key` 策略设置为不允许大调用量。

### <a name="quotas"></a>配额
配额通常用于控制较长一段时间的调用率。 例如，可以设置特定订阅者在给定月份内能够发出的调用总数。 为了通过 API 获利，还可以为基于层级的订阅设置不同的配额。 例如，基本层订阅每月最多可以进行 10,000 次调用，而高级层订阅每月最多可以进行 100,000,000 次调用。

在 Azure API 管理中，速率限制在节点间的传播速度通常更快，以防出现峰值。 相对而言，使用配额信息会在较长的时间内使用，因此其实现有所不同。

> [!CAUTION]
> 由于限制体系结构的分布式性质，速率限制永远不可能完全准确。 允许的请求的配置数字和实际数字之间的差异因请求量和速度、后端延迟以及其他因素而异。

## <a name="product-based-throttling"></a>基于产品的限制
范围限于特定订阅的速率限制功能有助于 API 提供程序对已注册使用其 API 的开发人员应用限制。 但是，在限制单个最终用户使用 API 方面，此功能没有用。 可让开发人员的应用程序的单个用户使用整个配额，然后阻止开发人员的其他客户使用该应用程序。 此外，生成大量请求的多个客户可能限制临时用户的访问权限。

## <a name="custom-key-based-throttling"></a>基于自定义密钥的限制

> [!NOTE]
> `rate-limit-by-key` 和 `quota-by-key` 策略在 Azure API 管理的消耗层中不可用。 

[rate-limit-by-key](./api-management-access-restriction-policies.md#LimitCallRateByKey) 和 [quota-by-key](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) 策略提供更灵活的流量控制解决方案。 使用这些策略可以定义表达式，识别用于跟踪流量使用的密钥。 最好是举一个例子来演示其工作原理。 

## <a name="ip-address-throttling"></a>IP 地址限制
以下策略限制单个客户端 IP 地址每一分钟只有 10 个调用，等于每个月总数为 1,000,000 个调用和 10,000 KB 带宽。 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

如果 Internet 上的所有客户端都使用唯一 IP 地址，这是可能是限制用户使用量的有效方式。 但有可能多个用户共享单个公共 IP 地址，由于他们通过 NAT 设备访问 Internet。 尽管如此，对允许未经身份验证访问的 API 而言，`IpAddress` 可能是最佳选项。

## <a name="user-identity-throttling"></a>用户标识限制
如果用户经过身份验证，可以根据该用户的唯一标识生成限制密钥。

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

本示例展示了如何提取授权标头，将它转换为 `JWT` 对象，然后使用令牌的使用者来识别用户，并将它用作速率限制密钥。 如果用户标识作为声明之一存储在 `JWT` 中，可以改用该值。

## <a name="combined-policies"></a>组合策略
尽管基于用户的限制策略比基于订阅的限制策略具有更广泛的控制性，但将两种功能相结合仍然是有价值的。 基于产品订阅密钥的限制（[按订阅限制调用率](./api-management-access-restriction-policies.md#LimitCallRate)和[按订阅设置使用量配额](./api-management-access-restriction-policies.md#SetUsageQuota)）根据使用级别收费，是让 API 赚钱的最好方法。 更精细的按用户控制限制与其互补，防止一个用户的行为降低另一个用户的体验。 

## <a name="client-driven-throttling"></a>客户端驱动的限制
使用[策略表达式](./api-management-policy-expressions.md)定义限制密钥时，API 提供程序将选择如何设置限制范围。 但是，开发人员可以控制自己客户的速率限制。 API 提供程序可以通过导入自定义标头来做到这一点，允许开发人员的客户端应用程序向 API 传递密钥。

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

这样，开发人员的客户端应用程序便可以选择如何创建速率限制密钥。 客户端开发人员可以通过分配密钥集给用户和轮流使用密钥，创建自己的速率层。

## <a name="summary"></a>总结
Azure API 管理提供速率和配额限制，不但能保护 API 服务，而且能为 API 服务增加价值。 使用新的限制策略与自定义范围规则，可以更精细地控制这些策略，让客户构建更好的应用程序。 本文中的示例演示如何使用这些新策略，分别使用客户端 IP 地址、用户标识和客户端生成的值来创建速率限制密钥。 但是，消息中还有许多其他部分可供使用，例如用户代理、URL 路径段和消息大小。

## <a name="next-steps"></a>后续步骤
请将对此主题的反馈作为 GitHub 问题提供给我们。 我们很想知道是否还可以在方案中合理地选择其他可能的密钥值。
