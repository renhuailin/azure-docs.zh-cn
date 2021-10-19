---
title: 在 Azure 市场中为 SaaS 产品/服务添加技术详细信息
description: 在 Azure 市场中为服务型软件 (SaaS) 产品/服务提供技术详细信息。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 7efbb49565027ed7b982d37e99a27e1d66c59bf5
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728088"
---
# <a name="add-technical-details-for-a-saas-offer"></a>为 SaaS 产品/服务添加技术详细信息

本文介绍如何输入技术详细信息，以帮助 Microsoft 商业市场连接到你的解决方案。 使用此连接，可以为选择获取你的产品/服务的客户预配产品/服务并进行管理。 有关这些设置的详细信息，请参阅[技术信息](plan-saas-offer.md#technical-information)。

> [!NOTE]
> 如果选择单独处理事务，则不会看到此选项， 而是会跳到[如何销售 SaaS 产品/服务](create-new-saas-offer-marketing.md)。

## <a name="technical-configuration"></a>技术配置

在“技术配置”选项卡上，你将定义商业市场用来与 SaaS 应用程序或解决方案进行通信的技术细节。 

- 登录 URL（必需）– 定义一个 SaaS 网站 URL（例如 `https://contoso.com/signup`），客户从商业市场获得您的产品/服务以及从新创建的 SaaS 配置触发配置过程之后将登录该 URL。

  > [!IMPORTANT]
  > 登录页应该是全天候运行。 只有这样，您才能收到有关在商业市场上发布的 SaaS 产品/服务的新购买或产品/服务的有效订阅的配置请求的通知。

- 连接 Webhook（必需）- 对于 Microsoft 需向你发送的所有异步事件（例如，SaaS 订阅已取消），我们要求提供[连接 Webhook URL](./partner-center-portal/pc-saas-fulfillment-api-v2.md#implementing-a-webhook-on-the-saas-service)。 我们将调用此 URL 来向你通知事件。

  > [!IMPORTANT]
  > 你的 Webhook 应该是 24/7 全天候运行的，因为系统只采用这种方式来通知你关于客户提供商业市场购买的 SaaS 订阅的更新。

- Azure Active Directory 租户 ID（必需）–要查找 Azure Active Directory (Azure AD) 应用的租户 ID，请在 Azure Active Directory 中转到[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)边栏选项卡。 在“显示名称”列中，选择应用。 然后查找列出的目录（租户）ID号（例如，`50c464d3-4930-494c-963c-1e951d15360e`）。

- Azure Active Directory 应用程序 ID（必填）– 要找到你的应[用程序 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)，请在 Azure Active Directory 中转到[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)边栏选项卡。 在“显示名称”列中，选择应用。 然后查找列出的应用程序（客户端）ID 号（例如 `50c464d3-4930-494c-963c-1e951d15360e`）。

选择“保存草稿”，然后转到下一选项卡：计划概述。

## <a name="next-steps"></a>后续步骤

- [为 SaaS 产品/服务创建计划](create-new-saas-offer-plans.md)。