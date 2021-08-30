---
title: Azure Front Door - URL 重写 | Microsoft Docs
description: 本文介绍 Azure Front Door 如何为路由执行 URL 重写（如果已配置）。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4aedc6e92b02cf81003ecf4b40a5096bf80c7448
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441039"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL 重写（自定义转发路径）
Azure Front Door 支持 URL 重写，方法是配置可选的自定义转发路径，以便在构造要转发到后端的请求时使用。 默认情况下，如果未提供自定义转发路径，则 Front Door 会将传入的 URL 路径复制到转发请求中使用的 URL。 转发请求中使用的主机标头与为选定后端配置的主机标头一样。 阅读[后端主机标头](front-door-backend-pool.md#hostheader)，了解它的作用以及配置方式。

使用自定义转发路径进行 URL 重写的强大之处在于，它会将与通配符路径匹配的传入路径的任何部分复制到转发路径（这些路径段是下面示例中的绿色段）：
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Azure Front Door URL 重写":::

## <a name="url-rewrite-example"></a>URL 重写示例
考虑配置了以下前端主机和路径组合的路由规则：

| 主机      | 路径       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

下表的第一列显示了传入请求的示例，第二列显示了“最确切”的匹配路由“路径”。  下表的第三列和后续列显示了已配置的自定义转发路径的示例。

例如，如果查看第二行，可发现其表示对于传入请求 `www.contoso.com/sub`，如果自定义转发路径为 `/`，则转发的路径将为 `/sub`。 如果自定义转发路径为 `/fwd/`，则转发的路径为 `/fwd/sub`。 其余列以此类推。 下方路径中 **强调** 的部分表示通配符匹配的部分。

| 传入的请求       | 最确切的匹配路径 | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |

> [!NOTE]
> Azure Front Door 仅支持将 URL 从静态路径重写到另一个静态路径。 Azure Front Door 标准/高级 SKU 支持保留不匹配路径。 有关详细信息，请参阅[保留不匹配路径](standard-premium/concept-rule-set-url-redirect-and-rewrite.md#preserve-unmatched-path)。
> 

## <a name="optional-settings"></a>可选设置
还可以为任何给定的路由规则设置指定其他可选设置：

* **缓存配置** - 如果禁用或未指定，与此路由规则匹配的请求将不会尝试使用缓存内容，而是会始终从后端提取内容。 阅读关于[使用 Front Door 缓存](front-door-caching.md)的详细信息。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
