---
title: Azure API 管理中的订阅 | Microsoft Docs
description: 了解 Azure API 管理中的订阅的概念。 使用者可以使用 Azure API 管理中的订阅来访问 API。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/27/2021
ms.author: apimpm
ms.openlocfilehash: 9bbdc914eae50a7226b22952b40cdb4b28849239
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111664"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API 管理中的订阅

在 Azure API 管理中，订阅是 API 使用者访问通过 API 管理实例发布的 API 时最常用的方式。 本文概述了此概念。

## <a name="what-are-subscriptions"></a>什么是订阅？

通过借助 API 管理发布 API，你可以使用订阅密钥轻松保护 API 访问。 调用这些 API 时，可在 HTTP 请求中包含有效的订阅密钥以使用已发布的 API。 如果没有有效的订阅密钥，调用将：
* 被 API 管理网关立即拒绝。 
* 不会被转发到后端服务。

若要访问 API，需要订阅和订阅密钥。 订阅是一个命名容器，对应于一对订阅密钥。 

定期重新生成密钥是一种常见的安全预防措施，因此大多数需要订阅密钥的 Azure 产品都会成对生成密钥。 使用该服务的每个应用程序都可以从密钥 A 切换到密钥 B，并在中断最少的情况下重新生成密钥 A，反之亦然 。 

此外，

* 开发者无需 API 发布者批准即可获取订阅。 
* API 发布者可以直接为 API 使用者创建订阅。

> [!TIP]
> API 管理还支持使用其他机制来保护对 API 的访问，包括以下示例：
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [客户端证书](api-management-howto-mutual-certificates-for-clients.md)
> - [限制调用方 IP](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>订阅范围

订阅可以与各种范围相关联：产品、所有 API 或单个 API。

### <a name="subscriptions-for-a-product"></a>产品的订阅

一直以来，API 管理中的订阅与单个 [API 产品](api-management-terminology.md)范围关联。 开发人员：
* 在开发人员门户上找到产品列表。 
* 提交对要使用的产品的订阅请求。 
* 使用这些订阅中的密钥（自动批准或由 API 发布者批准）访问产品中的所有 API。 
    * 无论订阅范围（产品、全局或 API）如何，你都可以访问具有或不具有订阅密钥的 API。

目前，开发人员门户只在“用户配置文件”部分显示产品范围订阅。 

> [!NOTE]
> 如果使用 API 范围内的订阅密钥，则产品范围内配置的任何策略都不适用于该订阅。

![产品订阅](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> 有时候，API 发布者可能希望在不要求订阅的情况下将 API 产品发布给公众。 发布者可以在 Azure 门户中在产品的“设置”页上取消选择“需要订阅”选项。 这样一来，用户就可以在没有 API 密钥的情况下访问该产品的所有 API。

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>订阅所有 API 或单个 API

通过添加 API 管理的[消耗](https://aka.ms/apimconsumptionblog)层，订阅密钥管理变得更加简单。 

#### <a name="two-more-subscription-scopes"></a>另外两个订阅范围

现在，订阅范围不再局限于 API 产品，你可以创建密钥，授予对以下任一项的访问权限：
* 单个 API，或 
* API 管理实例中的所有 API。 

在将 API 添加到产品之前，无需创建产品。 

每个 API 管理实例现在都有一个不可变的包含所有 API 的订阅。 此订阅使得在测试控制台中测试和调试 API 更为容易且简单明了。

#### <a name="standalone-subscriptions"></a>独立订阅

API 管理现在允许独立订阅。 不再需要将订阅与开发人员帐户关联。 此功能在类似于多个开发人员或团队共享订阅的方案中非常有用。

在不分配所有者的情况下创建订阅会将其创建为独立订阅。 若要向开发人员和团队的其余人员授予对独立订阅密钥的访问权限，请执行以下任一操作：
* 手动共享订阅密钥。
* 使用自定义系统使订阅密钥可供团队使用。

#### <a name="creating-subscriptions-in-azure-portal"></a>在 Azure 门户中创建订阅

API 发布者现在可以直接在 Azure 门户中[创建订阅](api-management-howto-create-subscriptions.md)：

![灵活的订阅](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>后续步骤
获取有关 API 管理的详细信息：

+ 了解 API 管理中的其他[概念](api-management-terminology.md)。
+ 按[教程](import-and-publish.md)操作，详细了解 API 管理。
+ 查看[常见问题解答页](api-management-faq.yml)，了解常见问题。
