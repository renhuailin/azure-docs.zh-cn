---
title: Azure CDN 标准规则引擎参考 | Microsoft Docs
description: Azure 内容分发网络 (Azure CDN) 标准规则引擎中的匹配条件和操作的参考文档。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242202"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN 的标准规则引擎参考

在 Azure 内容分发网络 (Azure CDN) [标准规则引擎](cdn-standard-rules-engine.md)中，规则由一个或多个匹配条件和一个操作组成。 本文将详细介绍 Azure CDN 标准规则引擎中提供的匹配条件和功能。

设计规则引擎的目的，是让其充当一个最终授权机构，控制标准 Azure CDN 处理特定类型的请求的方式。

**规则的常见用途**：

- 重写或定义自定义缓存策略。
- 将请求重定向。
- 修改 HTTP 请求和响应标头。

## <a name="terminology"></a>术语

若要在规则引擎中定义规则，请设置[匹配条件](cdn-standard-rules-engine-match-conditions.md)和[操作](cdn-standard-rules-engine-actions.md)：

 ![Azure CDN 规则结构](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每个规则最多可以有 10 个匹配条件和 5 个操作。 每个 Azure CDN 终结点最多可以有 25 个规则。 

此限制中包含一个默认的全局规则。 全局规则没有匹配条件；全局规则中定义的操作将始终被触发。

   > [!IMPORTANT]
   > 多个规则的列出顺序会影响处理规则的方式。 规则中指定的操作可能会被后续规则覆盖。

## <a name="limits-and-pricing"></a>限制和定价 

每个 Azure CDN 终结点最多可以有 25 个规则。 每个规则最多可以有 10 个匹配条件和 5 个操作。 规则引擎的定价遵循以下维度： 
- 规则：每个规则每月 $1 
- 处理的请求数：每百万个请求 $0.60
- 前 5个规则始终免费

## <a name="syntax"></a>语法

规则中特殊字符的处理方式取决于不同的匹配条件和操作处理文本值的方式。 匹配条件或操作可以采用下述某种方式解释文本：

- [文本值](#literal-values)
- [通配符值](#wildcard-values)


### <a name="literal-values"></a>文本值

可解释为文本值的文本会将所有特殊字符（% 字符除外）视为规则中必须匹配的值的一部分。 例如，仅当找到确切值 `'*'` 时，才会满足设为 `'*'` 的文本匹配条件。

百分比符号用于指示 URL 编码（例如 `%20`）。

### <a name="wildcard-values"></a>通配符值

当前支持在标准规则引擎的 UrlPath 匹配条件中使用通配符。 \* 字符是通配符，代表一个或多个字符。 

## <a name="next-steps"></a>后续步骤

- [标准规则引擎中的匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [标准规则引擎中的操作](cdn-standard-rules-engine-actions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)
