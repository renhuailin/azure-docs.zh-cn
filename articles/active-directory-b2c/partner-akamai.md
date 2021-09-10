---
title: 为 Azure Active Directory B2C 配置 Akamai Web 应用程序防火墙的教程
titleSuffix: Azure AD B2C
description: 为 Azure AD B2C 配置 Akamai Web 应用程序防火墙的教程
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 09f35e9621f6704fb33720a43afb38fd99e9eec6
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123213907"
---
# <a name="tutorial-configure-akamai-with-azure-active-directory-b2c"></a>教程：为 Azure Active Directory B2C 配置 Akamai

在此示例教程中，可了解如何为使用自定义域的 Azure Active Directory (AD) B2C 租户启用 [Akamai Web 应用程序防火墙 (WAF)](https://www.akamai.com/us/en/resources/web-application-firewall.jsp) 解决方案。 Akamai WAF 可帮助组织防范其 Web 应用程序受到旨在利用安全漏洞进行的恶意攻击，如 SQL 注入和跨站点脚本。

>[!NOTE]
>此功能目前以公共预览版提供。

使用 Akamai WAF 解决方案的好处：

- 允许流入服务的流量管理的边缘平台。

- 可在 Azure AD B2C 租户之前配置。

- 允许对流量进行精细化操控，以保护标识基础结构。

此示例教程适用于 Akamai 提供的 [Web 应用程序保护程序 (WAP)](https://www.akamai.com/us/en/products/security/web-application-protector-enterprise-waf-firewall-ddos-protection.jsp) 和 [Kona Site Defender (KSD)](https://www.akamai.com/us/en/products/security/kona-site-defender.jsp) WAF 解决方案。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)。

- [Akamai WAF](https://www.akamai.com/us/en/akamai-free-trials.jsp) 帐户。
 
## <a name="scenario-description"></a>方案描述

Akamai WAF 集成包括以下组件：

- Azure AD B2C 租户：授权服务器，负责使用该租户中定义的自定义策略来验证用户的凭据。  其也被称为“标识提供者”。

- [Azure Front Door](../frontdoor/front-door-overview.md)：负责为 Azure B2C 租户启用自定义域。 Cloudflare WAF 中的所有流量都将在路由到 Azure Front Door 后，才能到达 Azure AD B2C 租户。

- [Akamai WAF](https://www.akamai.com/us/en/resources/waf.jsp)：Web 应用程序防火墙，用于管理发送到授权服务器的所有流量。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

1. 若要在 Azure AD B2C 中使用自定义域，需要使用 Azure Front Door 提供的自定义域功能。 了解如何[启用 Azure AD B2C 自定义域](./custom-domain.md?pivots=b2c-user-flow)。  

2. 使用 Azure Front Door 成功配置 Azure AD B2C 的自定义域后，请先[测试自定义域](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain)，再继续执行操作。  

## <a name="onboard-with-akamai"></a>通过 Akamai 加入

[注册](https://www.akamai.com)并创建 Akamai 帐户。

### <a name="create-and-configure-property"></a>创建和配置属性 

1. [创建新属性](https://control.akamai.com/wh/CUSTOMER/AKAMAI/en-US/WEBHELP/property-manager/property-manager-help/GUID-14BB87F2-282F-4C4A-8043-B422344884E6.html)。

2. 配置属性设置：  

| 属性 | 值 |
|:---------------|:---------------|
|属性版本 | 选择“标准 TLS”或“增强的 TLS”（首选） |
|属性主机名 | 添加属性主机名。 这是自定义域的名称，例如：login.domain.com。 <BR> 使用自定义域名的相应设置创建或修改证书。 有关详细信息，请参阅[此文](https://learn.akamai.com/en-us/webhelp/property-manager/https-delivery-with-property-manager/GUID-9EE0EB6A-E62B-4F5F-9340-60CBD093A429.html)。 |

3. 设置源服务器属性配置设置：

|属性| 值 |
|:-----------|:-----------|
| 源服务器类型 | 你的源 |
| 源服务器主机名 | yourafddomain.azurefd.net |
| 转发主机标头 | 传入主机标头 |
| 缓存键主机名| 传入主机标头  |

### <a name="configure-dns"></a>配置 DNS

在 DNS（如 login.domain.com）中创建一条指向“属性主机名”字段中的 Edge 主机名的 CNAME 记录。

### <a name="configure-akamai-waf"></a>配置 Akamai WAF

1. [配置 Akamai WAF](https://learn.akamai.com/en-us/webhelp/kona-site-defender/kona-site-defender-quick-start/GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A.html#GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A)。

2. 确保将“攻击组”下列出的所有项的“规则操作”设置为“拒绝”。

![图像显示规则操作设置为“拒绝”](./media/partner-akamai/rule-action-deny.png)

了解有关[控件的工作方式和配置选项](https://control.akamai.com/dl/security/GUID-81C0214B-602A-4663-839D-68BCBFF41292.html)的详细信息。

### <a name="test-the-settings"></a>测试设置

检查以下内容以确保流入 Azure AD B2C 的所有流量现在通过自定义域：

- 确保通过 Akamai WAF 并使用有效的 TLS 连接来路由流入 Azure AD B2C 自定义域的所有传入请求。
- 确保 Azure AD B2C 为自定义域正确设置了所有 cookie。
- “安全中心”控制台下可用的 Akamai WAF 仪表板显示了通过 WAF 的所有流量以及任何攻击流量的图表。

## <a name="next-steps"></a>后续步骤

- [在 Azure AD B2C 中配置自定义域](./custom-domain.md?pivots=b2c-user-flow)

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
