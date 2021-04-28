---
title: Azure Front Door 中的 Web 应用程序防火墙排除列表 - Azure 门户
description: 本文介绍如何使用 Azure 门户在 Azure Front 在配置排除列表。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 83baf03c414d9b0f7acb6a93db03794a539a3c58
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860826"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>带有 Front Door 服务排除列表的 Web 应用程序防火墙 (WAF) 

有时，Web 应用程序防火墙 (WAF) 可能会阻止要允许应用程序发出的请求。 例如，Active Directory 插入用于身份验证的令牌。 这些令牌可包含特殊字符，这些字符可能会从 WAF 规则中可能触发误报。 WAF 排除列表允许你忽略 WAF 评估中的某些请求属性。  可以使用 [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject)、[Azure CLI](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add)、[Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate) 或 Azure 门户配置排除列表。 下面的示例显示 Azure 门户配置。 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>使用 Azure 门户配置排除列表
可从 WAF 门户的“托管规则”下访问“管理排除项” 

![管理排除项](../media/waf-front-door-exclusion/exclusion1.png)
![管理 exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 示例排除列表：![管理 exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

此示例不包括 user 标头字段中的值。 有效的请求可能包括含有可触发 SQL 注入规则的字符串的 user 字段。 在这种情况下，可以排除 user 参数，这样 WAF 就不会评估此字段中的任何内容。

可以按名称向排除列表添加以下属性。 不会根据 WAF 规则评估所使用的字段值，但会评估其名称。 排除列表删除了对该字段值的检查。

* 请求标头名称
* 请求 cookie 名称
* 查询字符串参数名称
* 请求正文 post 参数名称

可以指定请求标头、正文、cookie 或查询字符串属性的完全匹配项。  也可以选择指定部分匹配项。 以下运算符是受支持的匹配条件：

- **等于**：此运算符用于完全匹配。 例如，要选择名为“bearerToken”的标头，请结合使用等号运算符和设为“bearerToken”的选择器 。
- **开头为**：此运算符与以指定选择器值开头的所有字段匹配。
- **结尾为**：此运算符与以指定选择器值结尾的所有请求字段匹配。
- **包含**：此运算符与包含指定选择器值的所有请求字段匹配。
- **等于任何值**：此运算符与所有请求字段匹配。 * 是选择器值。

标头和 cookie 名称不区分大小写。

如果标头值、cookie 值、post 参数值或查询参数值对某些规则产生误报，则可以考虑将这部分请求从规则中排除：


|WAF 日志中的 matchVariableName  |门户中的规则排除  |
|---------|---------|
|CookieValue:SOME_NAME        |请求 cookie 名称等于 SOME_NAME|
|HeaderValue:SOME_NAME        |请求标头名称等于 SOME_NAME|
|PostParamValue:SOME_NAME     |请求正文 post 参数名称等于 SOME_NAME|
|QueryParamValue:SOME_NAME    |查询字符串参数名称等于 SOME_NAME|


目前仅支持 WAF 日志中上述 matchVariableNames 的规则排除项。 对于任何其他 matchVariableNames，必须禁用会产生误报的规则，或创建显式允许这些请求的自定义规则。 尤其是当 matchVariableName 为 CookieName、HeaderName、PostParamName 或 QueryParamName 时，这表示名称本身将触发规则。 规则排除目前不支持这些 matchVariableNames。


如果排除名为 FOO 的请求正文 post 参数，则应该没有规则会在 WAF 日志中将 PostParamValue:FOO 显示为 matchVariableName。 但是，你可能仍会看到一条规则，该规则具有与 post 参数 FOO 的值匹配的 matchVariableName InitialBodyContents，因为 post 参数值是 InitialBodyContents 的一部分。

可以将排除列表应用于托管规则集内的所有规则、特定规则组的规则或单条规则，如前面的示例所示。

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>基于 Web 应用程序防火墙日志定义排除
 [Azure Web 应用程序防火墙监视和日志记录](waf-front-door-monitor.md)显示已阻止请求的匹配详细信息。 如果标头值、cookie 值、post 参数值或查询参数值对某些规则产生误报，则可以考虑将这部分请求从规则中排除。 下表显示了 WAF 日志中的示例值和相应的排除条件。

|WAF 日志中的 matchVariableName    |门户中的规则排除|
|--------|------|
|CookieValue:SOME_NAME  |请求 cookie 名称等于 SOME_NAME|
|HeaderValue:SOME_NAME  |请求标头名称等于 SOME_NAME|
|PostParamValue:SOME_NAME|  请求正文 post 参数名称等于 SOME_NAME|
|QueryParamValue:SOME_NAME| 查询字符串参数名称等于 SOME_NAME|


## <a name="next-steps"></a>后续步骤

配置 WAF 设置后，即可了解如何查看 WAF 日志。 有关详细信息，请参阅 [Front Door 诊断](../afds/waf-front-door-monitor.md)。