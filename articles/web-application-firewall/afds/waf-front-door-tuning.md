---
title: 优化 Web 应用程序防火墙 (WAF) 用于 Azure 前门
description: 本文介绍了如何调整前门的 WAF。
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: 8752886bc5304de420083212d29ccd3e1cb14084
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043688"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>优化 Web 应用程序防火墙 (WAF) 用于 Azure 前门
 
Azure 托管的默认规则集基于 [OWASP 核心规则集 (CRS) ](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) ，设计为严格现成。 通常，WAF 规则需要进行优化，以满足使用 WAF 的应用程序或组织的特定需要。 通常通过定义规则排除、创建自定义规则，甚至禁用可能导致问题或误报的规则来实现此目的。 如果本应通过 Web 应用程序防火墙 (WAF) 的请求被阻止，可以采取以下几种方法来解决。

首先，请确保已阅读前门 [WAF 概述](afds-overview.md) 以及前门文档的 [WAF 策略](waf-front-door-create-portal.md) 。 此外，请确保已启用 [WAF 监视和日志记录](waf-front-door-monitor.md)。 这些文章介绍了如何使用 WAF 函数、WAF 规则集的工作方式，以及如何访问 WAF 日志。
 
## <a name="understanding-waf-logs"></a>了解 WAF 日志
 
WAF 日志用于显示 WAF 匹配或阻止的每个请求。 它是所有匹配或阻止的已评估请求的集合。 如果你发现 WAF 阻止了原本不应该阻止的请求（误报），可以采取几种方法来解决问题。 首先缩小查找范围，找到特定的请求。 如果需要，可以 [配置自定义响应消息](./waf-front-door-configure-custom-response-code.md) 以包含字段， `trackingReference` 以便轻松地标识事件，并对该特定值执行日志查询。 查看日志以查找请求的特定 URI、时间戳或客户端 IP。 找到相关的日志条目后，就可以开始处理误报。 
 
例如，假设你有一个合法的流量，其中包含 `1=1` 你要通过 WAF 传递的字符串。 该请求如下所示：

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

如果尝试请求，WAF 将阻止任何参数或字段中包含 *1=1* 字符串的流量。 此字符串通常与 SQL 注入攻击相关。 可以浏览日志，查看请求的时间戳，以及阻止/匹配的规则。
 
在下面的示例中，我们将浏览 `FrontdoorWebApplicationFirewallLog` 由于规则匹配而生成的日志。 以下 Log Analytics 查询可用于查找在过去24小时内被阻止的请求：

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
在 `requestUri` 字段中，你可以看到该请求是对的 `/api/Feedbacks/` 。 接下来，我们在 `942110` 该字段中找到规则 ID `ruleName` 。 知道规则 ID 后，可以使用 [OWASP ModSecurity Core 规则集官方存储库](https://github.com/coreruleset/coreruleset) ，并按该 [规则 id](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) 搜索以查看其代码，并确切了解此规则的匹配项。 
 
然后，通过检查该 `action` 字段，可以看到此规则设置为 "在匹配时阻止请求"，我们确认该请求确实被 WAF 阻止，因为 `policyMode` 设置为 `prevention` 。 
 
现在，让我们来查看字段中的信息 `details` 。 您可以在其中查看 `matchVariableName` 和 `matchVariableValue` 信息。 我们了解到，此规则是因为用户在 web 应用的字段中输入 *1 = 1* 而触发的 `comment` 。
 
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
 
还可以通过检查访问日志来扩展有关给定 WAF 事件的知识。 下面我们查看 `FrontdoorAccessLog` 作为对上述事件的响应生成的日志。
 
你可以看到这些日志是基于值相同的相关日志 `trackingReference` 。 在提供一般见解的各个字段（如 `userAgent` 和）中， `clientIP` 我们会注意 `httpStatusCode` 和 `httpStatusDetails` 字段。 在这里，我们可以确认客户端已收到 HTTP 403 响应，该响应完全确认此请求已被拒绝和阻止。 
 
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

使用此信息，如果规则942110与我们的示例中的字符串匹配，则 `1=1` 可以执行以下操作来阻止此合法请求被阻止：
 
* 使用排除列表
  * 有关排除列表的详细信息，请参阅 [Web 应用程序防火墙 (带有前门服务排除列表的 WAF) ](waf-front-door-exclusion.md) 。 
* 更改 WAF 操作
  * 有关请求与规则条件匹配时可执行的操作的详细信息，请参阅 [WAF 操作](afds-overview.md#waf-actions) 。
* 使用自定义规则
  * 有关自定义规则的详细信息，请参阅 [与 Azure 前门相关的 Web 应用程序防火墙的自定义规则](waf-front-door-custom-rules.md) 。
* 禁用规则 

> [!TIP]
> 选择允许合法请求通过 WAF 的方法时，请尽量缩小此方法。 例如，使用排除列表比完全禁用规则更好。

### <a name="using-exclusion-lists"></a>使用排除列表

使用排除列表的一个优点是，将不会再针对该给定请求检查选择要排除的匹配变量。 也就是说，如果满足特定条件，则可以在特定的请求标头、请求 cookie、查询字符串参数或请求正文 post 参数之间进行选择，而不是将整个请求排除在检查范围之外。 仍会正常检查请求的其他非指定变量。
 
考虑排除是全局设置，这一点很重要。 这意味着，配置的排除将应用于通过 WAF 传递的所有流量，而不只是特定的 web 应用或 URI。 例如，如果 *1 = 1* 是特定 web 应用的正文中的有效请求，而不是同一 WAF 策略下的其他请求，则可能需要考虑这一点。 如果对不同的应用程序使用不同的排除列表是有意义的，请考虑对每个应用程序使用不同的 WAF 策略，并将它们应用于每个应用程序的前端。
 
为托管规则配置排除列表时，可以选择排除规则集内的所有规则、规则组中的所有规则或单个规则。 可以使用 [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-4.7.0&viewFallbackFrom=azps-3.5.0)、 [AZURE CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add)、 [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)或 Azure 门户配置排除列表。

* 规则级别的排除项
  * 如果在规则级别应用排除项，则意味着不会针对单个规则对指定的排除项进行分析，而规则集中的所有其他规则仍将对其进行分析。 这是最细化的排除级别，可用于根据在对事件进行故障排除时在 WAF 日志中找到的信息来微调托管规则集。
* 规则组级别的排除项
  * 在规则组级别应用排除意味着不会根据特定规则类型集分析指定的排除项。 例如，如果选择 " *注 sqli* " 作为排除的规则组，则指示定义的请求排除不会由任何特定于注 sqli 的规则进行检查，但其他组中的规则仍将对其进行检查，如 *PHP*、 *RFI* 或 *XSS*。 当我们确保应用程序不容易遭受特定类型的攻击时，这种类型的排除会很有用。 例如，没有任何 SQL 数据库的应用程序可能会将所有 *注 sqli* 规则排除，而不会损害其安全级别。
* 规则集级别的排除项 
  * 在规则集级别应用排除意味着不会根据该规则集中可用的任何安全规则来分析指定的排除项。 这是一项全面的排除，因此应该谨慎使用。

在此示例中，我们将以最精细的级别执行排除 (将排除应用到单个规则) ，我们希望排除包含的匹配变量 **请求正文 post 参数名称** `comment` 。 这一点很明显，因为你可以在防火墙日志中看到匹配的变量详细信息： `"matchVariableName": "PostParamValue:comment"` 。 该属性为 `comment` 。 还可以通过其他几种方法找到此属性名称，具体请参阅[查找请求属性名称](#finding-request-attribute-names)。

![排除规则](../media/waf-front-door-tuning/exclusion-rules.png)

![特定规则的规则排除](../media/waf-front-door-tuning/exclusion-rule.png)

特定的参数偶尔会以一种不直观的方式传入 WAF。 例如，使用 Azure Active Directory 进行身份验证时会传递一个令牌。 此令牌 `__RequestVerificationToken` 通常作为请求 cookie 传入。 但是，在某些情况下禁用 cookie 时，还会将此令牌作为请求 post 参数传入。 出于此原因，若要解决 Azure AD 标记误报，需要确保 `__RequestVerificationToken` 将添加到和的排除列表 `RequestCookieNames` `RequestBodyPostArgsNames` 。

字段名 (*选择器*) 的排除意味着 WAF 将不再计算此值。 但是，字段名称本身会继续进行计算，在极少数情况下，它可能会匹配 WAF 规则并触发操作。

![规则集的规则排除](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>更改 WAF 操作

处理 WAF 规则行为的另一种方法是，选择当请求与规则条件匹配时将采取的操作。 可用的操作包括： [允许、阻止、记录和重定向](afds-overview.md#waf-actions)。

在此示例中，我们将默认操作 *块* 更改为规则942110上的 *日志* 操作。 这将导致 WAF 记录请求，并继续针对剩余的低优先级规则评估相同的请求。

![WAF 操作](../media/waf-front-door-tuning/actions.png)

执行同一请求后，我们可以返回到日志，并看到此请求是规则 ID 942110 的匹配项，并且该 `action_s` 字段现在指示 *日志* 而不是 *块*。 然后展开日志查询以包含 `trackingReference_s` 信息，并查看此请求还发生了什么情况。

![显示多个规则匹配的日志](../media/waf-front-door-tuning/actions-log.png)

有趣的是，在处理完规则 ID 942110 后，会看到不同的注 SQLI 规则匹配。 与规则 ID 942310 匹配的同一请求，这一次触发了默认操作 *块* 。

在 WAF 优化或故障排除期间使用 *Log* 操作的另一个优点是，您可以确定特定规则组中的多个规则是否匹配和阻止给定请求。 然后，你可以在相应的级别创建排除项，即在规则或规则组级别。 

### <a name="using-custom-rules"></a>使用自定义规则

确定导致 WAF 规则匹配的原因后，可以使用自定义规则调整 WAF 响应事件的方式。 自定义规则在托管规则之前进行处理，这些规则可以包含多个条件，并且它们的操作可以是 ["允许"、"拒绝"、"日志" 或 "重定向](afds-overview.md#waf-actions)"。 当规则匹配时，WAF 引擎将停止处理。 这意味着不再执行优先级较低的其他自定义规则和托管规则。

在下面的示例中，我们创建了一个包含两个条件的自定义规则。 第一种情况是 `comment` 在请求正文中查找值。 第二个条件是 `/api/Feedbacks/` 在请求 URI 中查找值。

使用自定义规则可以在微调 WAF 规则和处理误报时最精细。 在这种情况下，我们并不仅基于 `comment` 请求正文值采取操作，该值可以在同一 WAF 策略下跨多个站点或应用存在。 通过添加另一个条件以同时在特定请求 URI 上进行匹配 `/api/Feedbacks/` ，我们可以确保此自定义规则确实适用于我们审查的这个明确用例。这可确保 WAF 引擎仍会检查和阻止相同的攻击（如果针对不同的条件执行）。

![日志](../media/waf-front-door-tuning/custom-rule.png)

浏览日志时，可以看到该 `ruleName_s` 字段包含为我们创建的自定义规则指定的名称： `redirectcomment` 。 在 `action_s` 字段中，可以看到此事件执行了 *重定向* 操作。 在 `details_matches_s` 字段中，可以看到两个条件匹配的详细信息。

### <a name="disabling-rules"></a>禁用规则

避免误报的另一种方式是禁用被 WAF 认为是恶意的输入中匹配的规则。 由于您已经分析了 WAF 日志，并将该规则缩小到942110，因此您可以在 Azure 门户中禁用它。 请参阅 [使用 Azure 门户自定义 Web 应用程序防火墙规则](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules)。
 
如果你确信满足特定条件的所有请求都确实是合法请求，或者当你确定该规则仅适用于你的环境 (例如，禁用 SQL 注入规则，因为你具有非 SQL 后端) ，则禁用规则是一项好处。 
 
但是，禁用规则是适用于所有与 WAF 策略关联的前端主机的全局设置。 当你选择禁用规则时，可能会在没有保护或检测到与 WAF 策略关联的任何其他前端主机的情况下，留下公开的漏洞。
 
如果要使用 Azure PowerShell 禁用托管规则，请参阅 [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject?preserve-view=true&view=azps-4.7.0) 对象文档。 如果要使用 Azure CLI，请参阅 [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override?preserve-view=true&view=azure-cli-latest) 文档。

![WAF 规则](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> 最好是记录对 WAF 策略所做的任何更改。 包括说明误报检测的示例请求，并清楚地说明添加自定义规则、禁用规则或规则集或添加例外的原因。 如果你在将来重新设计应用程序，并需要验证你的更改是否仍然有效，此文档可能会有所帮助。 如果你曾被审核，或者需要证明你已从其默认设置中重新配置了 WAF 策略，该方法也会有所帮助。

## <a name="finding-request-fields"></a>查找请求字段

使用浏览器代理（如 [Fiddler](https://www.telerik.com/fiddler)），可以检查单个请求，并确定要调用网页的哪些特定字段。 当我们需要使用 WAF 中的排除列表排除检查中的某些字段时，这非常有用。

### <a name="finding-request-attribute-names"></a>查找请求属性名称
 
在此示例中，可以看到在其中输入了 `1=1` 字符串的字段 `comment` 。 此数据已在 POST 请求的正文中传递。

![显示正文的 Fiddler 请求](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

这是一个可以排除的字段。 若要详细了解排除列表，请参阅 [Web 应用程序防火墙排除列表](./waf-front-door-exclusion.md)。 在本例中，可以通过配置以下排除项来排除评估：

![排除规则](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

还可以检查防火墙日志来获取信息，以确定需要将哪些内容添加到排除列表。 若要启用日志记录，请参阅 [在 Azure 前门中监视指标和日志](./waf-front-door-monitor.md)。

检查文件中的防火墙日志中 `PT1H.json` 是否有要检查的请求发生的小时数。 `PT1H.json` 文件可用于 `FrontDoorWebApplicationFirewallLog` 存储和诊断日志的存储帐户容器 `FrontDoorAccessLog` 。

在此示例中，可以看到用相同的事务引用阻止请求 (的规则) 并在同一时间发生：

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

了解 Azure 托管规则设置工作方式的知识 (参阅 [Azure 前门上的 Web 应用程序防火墙](afds-overview.md)) 知道 *操作： Block* 属性的规则基于请求正文中匹配的数据进行阻止。 您可以在详细信息中看到，它与 () 模式匹配 `1=1` ，并且该字段的名称为 `comment` 。 按照前面的相同步骤排除包含的请求正文 post 参数名称 `comment` 。

### <a name="finding-request-header-names"></a>查找请求标头名称

在 Fiddler 中还能够很方便地查找请求标头名称。 在以下屏幕截图中，可以看到此 GET 请求的标头，其中包括 Content-Type、User-Agent 等。 你还可以在 WAF 中使用请求标头来创建排除项和自定义规则。

![显示标头的 Fiddler 请求](../media/waf-front-door-tuning/fiddler-request-header-name.png)

查看请求和响应标头的另一种方法是在浏览器的开发人员工具（如 Edge 或 Chrome）中查看。 可以按 F12，或右键单击并选择“检查” -> “开发人员工具”->“网络”选项卡。  加载一个网页，然后单击要检查的请求。

![网络检查器请求](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>查找请求 Cookie 名称

如果请求包含 Cookie，可以选择“Cookie”选项卡以在 Fiddler 中查看 Cookie。 Cookie 信息还可用于在 WAF 中创建排除规则或自定义规则。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Web 应用程序防火墙](../overview.md)。
- 了解如何[创建 Front Door](../../frontdoor/quickstart-create-front-door.md)。
