---
title: Azure Front Door 的 Web 应用程序防火墙自定义规则
description: 了解如何使用 Web 应用程序防火墙 (WAF) 自定义规则来保护 Web 应用程序免受恶意攻击。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475818"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Azure Front Door 的 Web 应用程序防火墙的自定义规则

通过 Front Door 的 Azure Web 应用程序防火墙 (WAF) 可以根据定义的条件来控制对 Web 应用程序的访问。 自定义 WAF 规则由优先级编号、规则类型、匹配条件和操作组成。 自定义规则有两种类型：匹配规则和速率限制规则。 匹配规则基于一组匹配条件控制访问权限，而速率限制规则则根据匹配条件和传入请求的速率控制访问权限。 可以禁用自定义规则以阻止对其进行计算，但仍保留配置。 

## <a name="priority-match-conditions-and-action-types"></a>优先级、匹配条件和操作类型

你可以使用自定义的 WAF 规则来控制访问权限，该规则可定义优先级编号、规则类型、匹配条件数组和操作。 

- **优先级：** 用于说明 WAF 规则计算顺序的唯一整数。 具有较低优先级值的规则先于值较高的规则进行计算。 优先级编号在所有自定义规则中必须是唯一的。

- **操作：** 定义在匹配 WAF 规则时如何路由请求。 可以选择在请求与自定义规则匹配时要应用的以下操作之一。

    - *允许* - WAF 将请求转发到后端，并在 WAF 日志中记录条目，然后退出。
    - *阻止* - 请求受阻，WAF 将响应发送到客户端，且不会将请求转发到后端。 WAF 在 WAF 日志中记录条目。
    - *记录* - WAF 在 WAF 日志中记录条目，并继续计算下一个规则。
    - *重定向* - WAF 将请求重定向到指定的 URI，并在 WAF 日志中记录条目，然后退出。

- **匹配条件：** 定义匹配变量、运算符和匹配值。 每个规则可能包含多个匹配条件。 匹配条件可以基于地理位置、客户端 IP 地址 (CIDR)、大小或字符串匹配。 字符串匹配可针对匹配变量列表使用。
  - **匹配变量：**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody
    - Cookie
  - **运算符：**
    - Any：通常用于定义默认操作（如果没有匹配的规则）。 Any 运算符表示全部匹配。
    - 等于
    - Contains
    - LessThan：大小约束
    - GreaterThan：大小约束
    - LessThanOrEqual：大小约束
    - GreaterThanOrEqual：大小约束
    - BeginsWith
    - EndsWith
    - 正则表达式
  
  - 正则表达式不支持以下操作： 
    - 反向引用和捕获子表达式
    - 任意零宽度断言
    - 子例程引用和递归模式
    - 条件模式
    - 回溯控制谓词
    - \C 单字节指令
    - \R 换行符匹配指令
    - \K 启动匹配重置指令
    - 标注和嵌入式代码
    - 原子分组和所有格限定符

  - **求反 [可选]：** 如果条件的结果应为求反，可以将“求反”条件设置为 true。
      
  - **转换 [可选]：** 一个字符串列表，其中包含尝试匹配之前执行的转换的名称。 这些转换可以是：
     - 大写 
     - 小写
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **匹配值：** 支持的 HTTP 请求方法值包括：
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - LOCK
     - UNLOCK
     - 配置文件
     - OPTIONS
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - 复制
     - MOVE

## <a name="examples"></a>示例

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>基于 http 参数的 WAF 自定义规则示例

下面的示例演示具有两个匹配条件的自定义规则配置。 请求来自定义为引用网站的指定站点，且查询字符串不包含“password”。

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
阻止“PUT”方法的示例配置如下所示：

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>大小约束

你可以构建自定义规则，用于指定对传入请求某部分的大小约束。 例如，下面的规则会阻止长度超过 100 个字符的 URL。

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>后续步骤
- [使用 Azure PowerShell 配置 Web 应用程序防火墙策略](waf-front-door-custom-rules-powershell.md) 
- 了解 [Front Door 的 Web 应用程序防火墙](afds-overview.md)
- 了解如何[创建 Front Door](../../frontdoor/quickstart-create-front-door.md)。

