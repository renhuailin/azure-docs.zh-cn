---
title: 调整 Azure Front Door 的 Web 应用程序防火墙 (WAF)
description: 本文介绍了如何调整 Front Door 的 WAF。
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b345491298c1481ccb9046f7ec27c45dd1bdd7a4
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110668807"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>调整 Azure Front Door 的 Web 应用程序防火墙 (WAF)
 
Azure 托管的默认规则集基于 [OWASP 核心规则集 (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev)，其设计极为严格。 用户普遍认为，WAF 规则需要进行调整，才能满足使用 WAF 的应用程序或组织的特定需求。 为实现此目的，用户通常会定义规则排除项、创建自定义规则，甚至禁用可能会引发问题或误报的规则。 如果本应通过 Web 应用程序防火墙 (WAF) 的请求被阻止，可以采取以下几种方法来解决。

首先，确保你已查看 [Front Door WAF 概述](afds-overview.md)和 [Front Door 的 WAF 策略](waf-front-door-create-portal.md)文档。 此外，确保你已启用 [WAF 监视和日志记录](waf-front-door-monitor.md)。 这些文章说明了 WAF 函数、WAF 规则集的工作原理，以及访问 WAF 日志的方法。
 
## <a name="understanding-waf-logs"></a>了解 WAF 日志
 
WAF 日志用于显示 WAF 匹配或阻止的每个请求。 它是所有已评估（已匹配或已阻止）的请求的集合。 如果你发现 WAF 阻止了原本不应该阻止的请求（误报），可以采取几种方法来解决问题。 首先缩小查找范围，找到特定的请求。 如果需要，可以[配置自定义响应消息](./waf-front-door-configure-custom-response-code.md)，将 `trackingReference` 字段包含在内，以便轻松发现事件，并针对该特定值执行日志查询。 查看日志，以找到请求的特定 URI、时间戳或客户端 IP。 找到相关的日志条目后，可以开始处理误报。 
 
例如，假设某个合法流量（你希望该流量通过 WAF）包含字符串 `1=1`。 该请求如下所示：

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

如果尝试请求，WAF 将阻止任何参数或字段中包含 *1=1* 字符串的流量。 此字符串通常与 SQL 注入攻击相关。 可以浏览日志，查看请求的时间戳，以及阻止/匹配的规则。
 
在下面的示例中，我们将探究由于规则匹配而生成的 `FrontdoorWebApplicationFirewallLog` 日志。 以下 Log Analytics 查询可用于查找在过去 24 小时内被阻止的请求：

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
在 `requestUri` 字段中可以看到，该请求已专门转变为 `/api/Feedbacks/`。 接下来，我们在 `ruleName` 字段中发现了规则 ID `942110`。 知道规则 ID 后，可以访问 [OWASP ModSecurity 核心规则集官方存储库](https://github.com/coreruleset/coreruleset)，并根据该[规则 ID](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) 进行搜索，以便查看其代码，并彻底了解此规则的匹配对象。 
 
然后，查看 `action` 字段，我们发现此规则设置为在匹配时阻止请求，然后确认该请求实际是被 WAF 阻止的，因为 `policyMode` 设置为 `prevention`。 
 
现在我们查看 `details` 字段中的信息。 你可以在其中查看 `matchVariableName` 和 `matchVariableValue` 信息。 我们了解到，触发此规则的原因是，有人将“1=1”输入到了 Web 应用的 `comment` 字段。
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
你还可以通过查看访问日志来进一步了解给定 WAF 事件。 接下来，我们查看 `FrontdoorAccessLog` 日志，该日志是对上述事件的响应。
 
这些日志的 `trackingReference` 值相同，从这一点可以看出这些日志相关。 日志中包含各种提供一般信息的字段（例如 `userAgent` 和 `clientIP`），我们从中看到了 `httpStatusCode` 和 `httpStatusDetails` 字段。 我们可以从此处确认，客户端已收到 HTTP 403 响应，该响应完全确认了此请求遭到了拒绝或阻止。 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>解决误报
 
若要明智地决定如何处理误报，必须熟悉应用程序所用的技术。 例如，假设技术堆栈中没有 SQL 服务器，但你收到了与这些规则相关的误报。 禁用这些规则不一定会削弱安全性。 

了解上述信息并知道规则 942110 是匹配示例中 `1=1` 字符串的规则后，可以通过执行以下操作，来阻止此合理请求遭到阻止：
 
* 使用排除列表
  * 有关排除列表的详细信息，请参阅 [Front Door 服务中 Web 应用程序防火墙 (WAF) 的排除列表](waf-front-door-exclusion.md)。 
* 更改 WAF 操作
  * 请参阅 [WAF 操作](afds-overview.md#waf-actions)，详细了解请求匹配规则的条件时 WAF 可能会执行哪些操作。
* 使用自定义规则
  * 有关自定义规则的详细信息，请参阅 [Azure Front Door 中 Web 应用程序防火墙的自定义规则](waf-front-door-custom-rules.md)。
* 禁用规则 

> [!TIP]
> 选择允许合理请求通过 WAF 的方法时，请尽量缩小此方法的作用范围。 例如，使用排除列表优于完全禁用规则。

### <a name="using-exclusion-lists"></a>使用排除列表

使用排除列表的优势之一是，对于给定请求，只有你选择要排除的匹配变量不再受到检查。 也就是说，可以在特定的请求标头、请求 cookie、查询字符串参数或请求正文 post 参数之间进行选择，以在满足特定条件时将这些内容排除，而非将整个请求排除在检查范围之外。 仍会正常检查其他未被指定的请求变量。
 
务必注意，排除项属于全局设置。 这意味着，配置的排除项将应用于通过 WAF 传递的所有流量，而不只是仅适用于特定 Web 应用或 URI。 例如，“1=1”在某个 Web 应用的正文中是有效请求，但在遵守同一个 WAF 策略的其他 Web 应用中无效，这种情况下排除项可能会引发问题。 如果可以将不同的排除列表用于不同的应用程序，请考虑将不同的 WAF 策略用于各个应用程序，并将这些策略应用于每个应用程序的前端。
 
为托管规则配置排除列表时，可以选择排除规则集内的所有规则、规则组中的所有规则或单个规则。 可以使用 [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject)、[AZURE CLI](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add)、[Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate) 或 Azure 门户配置排除列表。

* 规则级别的排除项
  * 如果在规则级别应用排除项，这意味着，只是单个相应规则将不分析指定的排除项，但是规则集中的所有其他规则仍将对其进行分析。 这是最精细的排除项级别，在针对事件进行故障排除时，可根据 WAF 日志中的信息通过该级别微调所管理的规则集。
* 规则组级别的排除项
  * 如果在规则组级别应用排除项，这意味着，特定规则类型集将不分析指定的排除项。 例如，如果选择“SQLI”作为要排除的规则组，这表示，特定于 SQLI 的任何一个规则都不检查定义的请求排除项，但其他组（如 PHP、RFI 或 XSS）中的规则仍会检查该项。 当我们确信应用程序不容易遭受特定类型的攻击时，可使用这种排除类型。 例如，没有任何 SQL 数据库的应用程序可以将所有 SQLI 规则排除，同时也不会降低其安全级别。
* 规则集级别的排除项 
  * 如果在规则集级别应用排除项，这意味着，该规则集中可用的任何一个安全规则都将不分析指定的排除项。 此排除范围非常广泛，应谨慎使用该排除。

在此示例中，我们将在最精细的级别执行排除（将排除应用于单个规则），我们希望排除包含 `comment` 的匹配变量“请求正文 post 参数名称”。 这一点很明显，因为可以在防火墙日志中看到匹配变量详细信息：`"matchVariableName": "PostParamValue:comment"`。 属性为 `comment`。 还可以通过其他几种方法找到此属性名称，具体请参阅[查找请求属性名称](#finding-request-attribute-names)。

![排除规则](../media/waf-front-door-tuning/exclusion-rules.png)

![用于特定规则的规则排除](../media/waf-front-door-tuning/exclusion-rule.png)

特定的参数偶尔会以一种不直观的方式传入 WAF。 例如，使用 Azure Active Directory 进行身份验证时会传递一个令牌。 令牌 `__RequestVerificationToken` 通常作为请求 cookie 传入。 但是，在某些情况下禁用 cookie 时，此令牌还会以请求 post 参数的形式传递。 因此，若要解决 Azure AD 令牌误报，需要确保 `__RequestVerificationToken` 添加到 `RequestCookieNames` 和 `RequestBodyPostArgsNames` 的排除列表。

排除字段名（选择器）意味着 WAF 将不再评估此值。 但是，字段名本身将继续受到评估，在极少数情况下，它可能会匹配 WAF 规则并触发操作。

![用于规则集的规则排除](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>更改 WAF 操作

处理 WAF 规则的另一种方式是，选择请求匹配规则条件时要执行的操作。 可用操作包括：[允许、阻止、日志记录和重定向](afds-overview.md#waf-actions)。

在此示例中，我们将规则 942110 的默认操作“阻止” 更改为“日志记录”。 这样 WAF 就会记录请求，并继续根据其他优先级较低的规则评估同一个请求。

![WAF 操作](../media/waf-front-door-tuning/actions.png)

执行同一个请求后，可以返回到日志，我们将看到此请求是规则 ID 942110 的匹配项，并且 `action_s` 字段现在指示“日志记录”，而不再是“阻止”。 然后，我们扩展了日志查询，以将 `trackingReference_s` 信息包含在内，并查看与此请求相关的其他事项。

![显示多个规则匹配项的日志](../media/waf-front-door-tuning/actions-log.png)

有趣的是，在处理完规则 ID 942110 后，我们很快就看到一个不同的 SQLI 规则匹配项。 同一个请求已与规则 ID 942310 匹配，这一次它触发了默认操作“阻止”。

在调整 WAF 或进行故障排除时使用“日志记录”操作的另一个优势是，可以确定特定规则组中的多个规则是否匹配和阻止给定请求。 然后，可以在适当的级别（在规则或规则组级别）创建排除项。 

### <a name="using-custom-rules"></a>使用自定义规则

确定导致 WAF 规则匹配的原因后，可以使用自定义规则调整 WAF 响应事件的方式。 自定义规则处理优先于托管规则，这些规则可以包含多个条件，它们的操作可以是[允许、拒绝、日志记录或重定向](afds-overview.md#waf-actions)。 当出现规则匹配项时，WAF 引擎将停止处理。 这意味着，不再执行优先级较低的其他自定义规则和托管规则。

在下面的示例中，我们创建了一个包含两个条件的自定义规则。 第一个条件是在请求正文中查找 `comment` 值。 第二个条件是在请求 URI 中查找 `/api/Feedbacks/` 值。

微调 WAF 规则以及处理误报时，可以使用自定义规则最大程度地实现精细化。 在本示例中，我们不只是仅根据 `comment` 请求正文值执行操作，因为此值可能在遵守同一个 WAF 策略的多个站点或应用中存在。 我们包含了另一个条件，以便也用于匹配特定请求 URI `/api/Feedbacks/`，以此方式确保此自定义规则真正适用于我们审查出来的这个确切用例。这样可确保，WAF 引擎仍会检查和预防针对不同条件执行的同一个攻击。

![日志](../media/waf-front-door-tuning/custom-rule.png)

浏览日志时，可发现 `ruleName_s` 字段包含我们创建的自定义规则的名称：`redirectcomment`。 在 `action_s` 字段中，可以看到针对此事件执行了“重定向”操作。 在 `details_matches_s` 字段中，可以看到两个条件匹配项的详细信息。

### <a name="disabling-rules"></a>禁用规则

避免误报的另一种方式是禁用被 WAF 认为是恶意的输入中匹配的规则。 由于你已分析 WAF 日志，并将规则查找范围缩小为 942110，因此可以在 Azure 门户中禁用它。 请参阅[使用 Azure 门户自定义 Web 应用程序防火墙规则](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules)。
 
如果确信满足特定条件的所有请求实际上都是合理请求，或者确信规则不适用于你的环境（例如，你的后端非 SQL，因此禁用 SQL 引入规则），禁用规则会带来好处。 
 
但是，禁用规则是一种全局设置，它会应用于所有与 WAF 策略关联的前端主机。 选择禁用规则时，可能就是在不对与 WAF 策略关联的任何其他前端主机进行保护或检测的情况下暴露漏洞。
 
如果要使用 Azure PowerShell 禁用托管规则，请参阅 [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) 对象文档。 如果要使用 Azure CLI，请参阅 [`az network front-door waf-policy managed-rules override`](/cli/azure/network/front-door/waf-policy/managed-rules/override) 文档。

![WAF 规则](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> 最好记录对 WAF 策略所做的任何更改。 其中包括可说明检测是误报的请求示例，并清楚地说明添加自定义规则、禁用规则或规则集或添加例外的原因。 如果未来要重新设计应用程序，并需要验证你的更改是否仍然有效，此文档可能会有所帮助。 如果你受到审核，或者需要证明你重新配置 WAF 策略默认设置的理由，此文档也可提供帮助。

## <a name="finding-request-fields"></a>查找请求字段

借助 [Fiddler](https://www.telerik.com/fiddler) 等浏览器代理，可以检查单个请求，并确定请求调用了网页的哪些特定字段。 需要使用 WAF 中的排除列表将某些字段排除在检查范围之外时，此功能非常有用。

### <a name="finding-request-attribute-names"></a>查找请求属性名称
 
在此示例中可以看到，输入了 `1=1` 字符串的字段名为 `comment`。 此数据已传入 POST 请求的正文。

![显示正文的 Fiddler 请求](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

这是一个可以排除的字段。 若要详细了解排除列表，请参阅 [Web 应用程序防火墙排除列表](./waf-front-door-exclusion.md)。 在本例中，可以通过配置以下排除项来排除评估：

![排除规则](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

还可以检查防火墙日志来获取信息，以确定需要将哪些内容添加到排除列表。 若要启用日志记录，请参阅[在 Azure Front Door 中监视指标和日志](./waf-front-door-monitor.md)。

根据要检查的请求的发生时间，查看 `PT1H.json` 文件中的防火墙日志。 `PT1H.json` 文件可从存储 `FrontDoorWebApplicationFirewallLog` 和 `FrontDoorAccessLog` 诊断日志的存储帐户容器中获取。

在此示例中可以看到，规则进行阻止的时间和请求发生的时间相同（具有相同的事务引用）：

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

通过 Azure 托管的规则集的工作原理（请参阅 [Azure Front Door 上的 Web 应用程序防火墙](afds-overview.md)），可发现是包含“操作: 阻止”属性的规则根据在请求正文中匹配的数据进行了阻止。 在详细信息中可以看到，它匹配了某个模式 (`1=1`)，而字段名为 `comment`。 请按照前面的相同步骤排除包含 `comment` 的请求正文 post 参数名称。

### <a name="finding-request-header-names"></a>查找请求标头名称

在 Fiddler 中还能够很方便地查找请求标头名称。 在以下屏幕截图中，可以看到此 GET 请求的标头，其中包括 Content-Type、User-Agent 等。 你也可以在 WAF 中使用请求标头创建排除项和自定义规则。

![显示标头的 Fiddler 请求](../media/waf-front-door-tuning/fiddler-request-header-name.png)

查看请求和响应标头的另一种方式是使用浏览器（如 Microsoft Edge 或 Chrome）的开发人员工具。 可以按 F12，或右键单击并选择“检查” -> “开发人员工具”->“网络”选项卡。  加载一个网页，然后单击要检查的请求。

![网络检查器请求](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>查找请求 Cookie 名称

如果请求包含 Cookie，可以选择“Cookie”选项卡以在 Fiddler 中查看 Cookie。 Cookie 信息也可用于在 WAF 中创建排除规则或自定义规则。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Web 应用程序防火墙](../overview.md)。
- 了解如何[创建 Front Door](../../frontdoor/quickstart-create-front-door.md)。
