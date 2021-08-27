---
title: 为 Azure Active Directory B2C 配置 Cloudflare Web 应用程序防火墙的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何为 Azure Active Directory B2C 配置 Cloudflare Web 应用程序防火墙，以保护应用程序免遭恶意攻击
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/24/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f2b4f2e54c046ca7abc2172b8df33f14def902fd
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767894"
---
# <a name="tutorial-configure-cloudflare-with-azure-active-directory-b2c"></a>教程：为 Azure Active Directory B2C 配置 Cloudflare

在此示例教程中，可了解如何为使用自定义域的 Azure Active Directory (AD) B2C 租户启用[Cloudflare Web 应用程序防火墙 (WAF)](https://www.cloudflare.com/waf/)解决方案。 Cloudflare WAF 可帮助组织防范旨在利用安全漏洞进行的恶意攻击，如注 SQLi 和 XSS。

>[!NOTE]
>此功能目前以公共预览版提供。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)。

- 一个[Cloudflare](https://dash.cloudflare.com/sign-up)帐户。

## <a name="scenario-description"></a>方案描述

Cloudflare WAF 集成包括以下组件：

- Azure AD B2C 租户：授权服务器，负责使用该租户中定义的自定义策略来验证用户的凭据。  其也被称为“标识提供者”。

- [Azure Front Door](../frontdoor/front-door-overview.md)：负责为 Azure B2C 租户启用自定义域。 Cloudflare WAF 中的所有流量都将在路由到 Azure Front Door 后，才能到达 Azure AD B2C 租户。

- Cloudflare：Web 应用程序防火墙，用于管理发送到授权服务器的所有流量。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

若要在 Azure AD B2C 中使用自定义域，需要使用 Azure Front Door 提供的自定义域功能。 了解如何[启用 Azure AD B2C 自定义域](./custom-domain.md?pivots=b2c-user-flow)。  

使用 Azure Front Door 成功配置 Azure AD B2C 的自定义域后，请先[测试自定义域](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain)，再继续执行操作。  

## <a name="onboard-with-cloudflare"></a>使用 Cloudflare 加入

[注册](https://dash.cloudflare.com/sign-up) 并创建 Cloudflare 帐户。 若要启用 WAF，至少需要[PRO SKU](https://www.cloudflare.com/plans/)。

### <a name="configure-dns"></a>配置 DNS

1. 若要为域启用 WAF，必须从 DNS 控制台为 CNAME 条目启用代理设置，如域 id.contosobank.co.uk 示例中所示。

   ![显示如何选择代理设置的图像](./media/partner-cloudflare/select-proxy-settings.png)

1. 在 DNS 窗格下切换可用的“代理状态”选项。

1. 在将其切换到“已代理”后，它将变为橙色。 最终设置应如下所示：

   ![显示如何选择“已代理”状态的图像](./media/partner-cloudflare/select-proxied.png)

### <a name="configure-waf"></a>配置 WAF

在 Cloudflare 设置中[配置 WAF](https://www.cloudflare.com/waf/)。

### <a name="configure-firewall-rule"></a>配置防火墙规则

使用控制台顶部窗格中提供的“防火墙”选项添加、更新或删除防火墙规则。 例如，在将请求发送到 Azure Front Door 之前，以下防火墙设置将对 contosobank.co.uk 域的所有传入请求启用 CAPTCHA。 了解有关[防火墙设置规则](https://support.cloudflare.com/hc/articles/360016473712-Cloudflare-Firewall-Rules)的详细信息。

![显示如何强制执行 captcha 的图像](./media/partner-cloudflare/configure-firewall-rule.png)

### <a name="test-the-settings"></a>测试设置

1. 保存规则后，每次请求访问自定义域时都需要完成 CAPTCHA。

   ![显示 cloudflare waf 强制执行 CAPTCHA 的图像](./media/partner-cloudflare/enforce-captcha.png)

   > [!NOTE]
   > Cloudflare 还提供了创建自定义块页的[其他选项](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-)。  

2. 在成功完成 CAPTCHA 后，用户将采用 Azure AD B2C 策略。

   ![显示 Azure AD B2C 策略登录的图像](./media/partner-cloudflare/azure-ad-b2c-policy.png)


## <a name="additional-resources"></a>其他资源

- [对 Cloudflare 自定义页面问题进行故障排除](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-#5QWV2KVjLnaAQ8L4tjiguw)

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)

## <a name="next-steps"></a>后续步骤 
 
- [在 Azure AD B2C 中配置自定义域](./custom-domain.md?pivots=b2c-user-flow)
