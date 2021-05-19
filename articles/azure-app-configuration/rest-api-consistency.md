---
title: Azure 应用程序配置 REST API - 一致性
description: 使用 Azure 应用程序配置 REST API 确保实时一致性的参考页
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932619"
---
# <a name="real-time-consistency"></a>实时一致性

由于某些分布式系统的性质，很难隐式强制实施请求之间的实时一致性。 解决方案是以多个同步令牌的形式提供协议支持。 同步令牌是可选的。

## <a name="initial-request"></a>初始请求

为了保证不同客户端实例和请求之间的实时一致性，请使用可选的 `Sync-Token` 请求和响应标头。

语法：

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|参数|描述|
|--|--|
| `<id>` | 令牌 ID（不透明） |
| `<value>` | 令牌值（不透明）。 允许 base64 编码的字符串。 |
| `<sn>` | 令牌序列号（版本）。 此值越高意味着同一令牌的版本越新。 可提供更好的并发性和客户端缓存。 客户端可以选择只使用令牌的最新版本，因为令牌版本是包含性的。 请求无需此参数。 |

## <a name="response"></a>响应

服务为每个响应提供一个 `Sync-Token` 标头。

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>后续请求

任何后续请求都获得与提供的 `Sync-Token` 相关的实时一致响应保证。

```http
Sync-Token: <id>=<value>
```

如果请求中省略了 `Sync-Token` 标头，那么该服务就有可能在短时间内（最多几秒钟）使用缓存的数据进行响应，然后再进行内部处理。 如果刚好在读取前进行了更改，则此行为可能会导致读取不一致。

## <a name="multiple-sync-tokens"></a>多个同步令牌

对于单个请求，服务器可以使用多个同步令牌进行响应。 为了在下一请求中保持实时一致性，客户端必须使用所有收到的同步令牌进行响应。 多个标头值必须以逗号分隔。

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
