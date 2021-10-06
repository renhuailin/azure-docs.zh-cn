---
title: Microsoft 商业市场中的 SaaS 履行 API
description: 可用于在 Microsoft AppSource 和 Azure 市场中集成 SaaS 产品/服务的履行 API 简介。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: 50bb572d7fc9c05bb22c8ab35851df7a58dc2562
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455931"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Microsoft 商业市场中的 SaaS 履行 API

通过 SaaS 履行 API，发布者（也称为独立软件供应商 (ISV)）可以在 Microsoft AppSource、Azure 市场和 Azure 门户中发布和销售其 SaaS 应用程序。 这些 API 使 ISV 应用程序能够参与所有启用了商务的渠道：直接、合作伙伴引导（经销商）和现场引导。  需要与这些 API 集成才能在合作伙伴中心创建和发布可交易的 SaaS 产品/服务。

ISV 必须通过将以下 API 流添加到 SaaS 服务代码使 ISV 和 Microsoft 保持同一订阅状态来实现这些流：

* 登陆页面流：Microsoft 告知发布者，发布者的 SaaS 产品/服务已由市场中的客户购买。
* 激活流：发布者告知 Microsoft，一个新购买的 SaaS 帐户已在发布者端配置。
* 更新流：更改购买的计划和/或购买的席位数。
* 暂停和恢复流：如果客户的付款方式不再有效，则暂停购买的 SaaS 产品/服务。 解决付款方式问题后，可以恢复暂停的产品/服务。
* Webhook 流：Microsoft 将告知发布者，客户从 Microsoft 端触发的 SaaS 订阅更改和取消。

对于已购买的 SaaS 订阅的取消，集成是可选的，因为该操作可以从 Microsoft 端由客户完成。

与 SaaS 履行 API 的正确集成对于确保

* 已由 Microsoft 向购买发布者的 SaaS 产品/服务的最终客户正确收费至关重要。
* 最终客户会获得在市场中购买、配置、使用和管理 SaaS 订阅的正确用户体验。

这些 API 使发布者的产品/服务能够参与所有启用了商务的渠道：

* 直通
* 合作伙伴引导（经销商，CSP）
* 现场引导

在经销商 (CSP) 方案中，CSP 会代表最终客户购买 SaaS 产品/服务。 客户预期使用 SaaS 产品/服务，但 CSP 是执行以下操作的实体：

* 向客户收费
* 更改订阅计划/购买的席位数
* 取消订阅

对于此方案，无需发布者即可以不同的方式实现任何 API 调用流。

有关 CSP 的详细信息，请参阅 https://partner.microsoft.com/licensing 。

>[!Warning]
>此 API 的当前版本是版本 2，该版本应用于所有新的 SaaS 产品/服务。 API 的版本 1 已弃用，并将进行维护以支持现有的产品/服务。

>[!Note]
>SaaS 履行 API 仅用于从发布者的后端服务中调用。 不支持直接从发布者的网页与 API 集成。 应仅使用服务到服务身份验证流。

## <a name="next-steps"></a>后续步骤

如果尚未这样做，请在 [Azure 门户](https://ms.portal.azure.com)中注册 SaaS 应用程序，如[注册 Azure AD 应用程序](./pc-saas-registration.md)中所述。  之后，使用此接口的最新版本进行开发：[SaaS 履行 API 版本 2](./pc-saas-fulfillment-api-v2.md)。
