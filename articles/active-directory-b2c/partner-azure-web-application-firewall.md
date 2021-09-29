---
title: 为 Azure Active Directory B2C 配置 Azure Web 应用程序防火墙的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何为 Azure Active Directory B2C 配置 Azure Web 应用程序防火墙，以保护应用程序免遭恶意攻击
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 54cb0cdbff2ac11334f168e41a18107d3d0dfe87
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763211"
---
# <a name="tutorial-configure-azure-web-application-firewall-with-azure-active-directory-b2c"></a>教程：为 Azure Web 应用程序防火墙配置 Azure Active Directory B2C

在此示例教程中，可了解如何为使用自定义域的 Azure Active Directory (AD) B2C 租户启用[Azure Web 应用程序防火墙 (WAF)](https://azure.microsoft.com/services/web-application-firewall/#overview)解决方案。 Azure WAF 可在出现常见攻击和漏洞时为 Web 应用程序提供集中保护。

>[!NOTE]
>此功能目前以公共预览版提供。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅 – 如果你没有订阅，可以申请[一个免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](tutorial-create-tenant.md) – 授权服务器，负责使用该租户中定义的自定义策略来验证用户的凭据。  其也被称为“标识提供者”。

- [Azure Front Door (AFD)](../frontdoor/index.yml) – 负责为 Azure AD B2C 租户启用自定义域。  

- [Azure WAF](https://azure.microsoft.com/services/web-application-firewall/#overview) – 管理发送到授权服务器的所有流量。

## <a name="azure-ad-b2c-setup"></a>Azure AD B2C 设置

若要在 Azure AD B2C 中使用自定义域，需要使用 AFD 提供的自定义域功能。 了解如何[启用 Azure AD B2C 自定义域](./custom-domain.md?pivots=b2c-user-flow)。  

使用 AFD 成功配置 Azure AD B2C 的自定义域后，请先[测试自定义域](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain)，再继续执行操作。  

## <a name="onboard-with-azure-waf"></a>使用 Azure WAF 加入

若要启用 Azure WAF，请配置 WAF 策略，并将该策略关联到 AFD 进行保护。

### <a name="create-a-waf-policy"></a>创建 WAF 策略

在 [Azure 门户](https://portal.azure.com)中创建具有托管默认规则集 (DRS) 的基本 WAF 策略。

1. 转到 [Azure 门户](https://portal.azure.com)。 选择“创建资源”，然后搜索 Azure WAF。 选择“Azure Web 应用程序防火墙” > “创建”。 

2. 转到“创建 WAF 策略”页，选择“基本”选项卡。输入以下信息，对于其余设置，接受默认值。 

| 值 | 说明 |
|:--------|:-------|
| 策略适用于 | 全球 WAF (Front Door)|
| Front Door SKU | 在基本、标准或高级 SKU 之间进行选择 |
|订阅 | 选择 Front Door 订阅名称 |
| 资源组 | 选择 Front Door 资源组名称 |
| 策略名称 | 输入 WAF 策略的唯一名称 |
| 策略状态 | 设置为“已启用” |
| 策略模式 | 设置为“检测” |

3. 选择“查看 + 创建”

4. 转到“创建 WAF 策略”页的“关联”选项卡，选择“+ 关联 Front Door 配置文件”，输入以下设置 

| 值 | 描述 |
|:----|:------|
| Front Door | 选择与 Azure AD B2C 自定义域关联的 Front Door 名称 |
| 域 | 选择要将 WAF 策略关联到的 Azure AD B2C 自定义域|

5. 选择 **添加** 。

6. 依次选择“查看 + 创建”、“创建”。  

### <a name="change-policy-mode-from-detection-to-prevention"></a>改变策略模式从检测到预防

创建 WAF 策略时，默认情况下该策略处于检测模式。 在“检测”模式下，WAF 不会阻止任何请求，而会在 WAF 日志中记录与 WAF 规则匹配的请求。 有关 WAF 日志记录的详细信息，请参阅 [Azure WAF 监视和日志记录](../web-application-firewall/afds/waf-front-door-monitor.md)。

示例查询显示了过去 24 小时内被 WAF 策略阻止的所有请求。 详细信息包括规则名称、请求数据、策略采取的操作以及策略模式。

![图中显示了被阻止的请求](./media/partner-azure-web-application-firewall/blocked-requests-query.png)

![图中显示了被阻止的请求详细信息](./media/partner-azure-web-application-firewall/blocked-requests-details.png)

建议让 WAF 在检测模式下捕获请求。 查看 WAF 日志以确定策略中是否有任何规则导致误报结果。 然后[根据 WAF 日志排除 WAF 规则](../web-application-firewall/afds/waf-front-door-exclusion.md#define-exclusion-based-on-web-application-firewall-logs)。

要查看 WAF 的实际效果，请使用切换到预防模式，以从“检测”模式更改为“预防”模式。 与默认规则集 (DRS) 中定义的规则匹配的所有请求都会被阻止并记录在 WAF 日志中。

![图中显示了切换到预防模式](./media/partner-azure-web-application-firewall/switch-to-prevention-mode.png)

若要切换回检测模式，可以使用“切换到检测模式”选项进行切换。

![图中显示了切换到检测模式](./media/partner-azure-web-application-firewall/switch-to-detection-mode.png)

## <a name="next-steps"></a>后续步骤

- [Azure WAF 监视和日志记录](../web-application-firewall/afds/waf-front-door-monitor.md)

- [包含 Front Door 服务排除列表的 WAF](../web-application-firewall/afds/waf-front-door-exclusion.md)