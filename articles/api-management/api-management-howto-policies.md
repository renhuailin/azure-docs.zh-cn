---
title: Azure API 管理中的策略 | Microsoft Docs
description: 了解如何在 API 管理中创建、编辑和配置策略。 查看代码示例和其他可用资源。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/25/2021
ms.author: apimpm
ms.openlocfilehash: c4b069d76d795bd8ae830b811c97dfe58c2f842c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123106006"
---
# <a name="policies-in-azure-api-management"></a>Azure API 管理中的策略

在 Azure API 管理中，API 发布者可以使用策略通过配置来更改 API 行为。 策略是针对 API 的请求或响应按顺序执行的一组语句。 常用的语句包括：

* 从 XML 格式转换为 JSON。
* 调用速率限制以限制开发人员的传入调用数。 

许多策略开箱即用。

策略在网关内部应用，该网关位于 API 使用者和托管 API 之间。 当网关接收请求并将其原封不动地转发到基础 API 后，策略可以向入站请求和出站响应应用更改。

在任何 API 管理策略中，策略表达式都可以用作属性值或文本值，除非该策略另外指定。 某些策略（如[控制流][Control flow]和[设置变量][Set variable]）基于策略表达式。 有关详细信息，请参阅[高级策略][Advanced policies]和[策略表达式][Policy expressions]文章。

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>了解策略配置

策略定义是简单的 XML 文档，用于描述一个入站和出站语句序列。 可以直接在定义窗口中编辑 XML，该窗口还提供：
* 右侧的语句列表。
* 适用于当前范围的语句已启用并已突出显示。

单击启用的语句会在定义视图中的光标位置添加相应的 XML。 

> [!NOTE]
> 如果无法启用要添加的策略，请确保为该策略设置的范围是正确的。 每个策略语句都设计有特定的使用范围，需在特定的策略部分使用。 若要查看某个策略的策略部分和范围，请参阅[策略参考][Policy Reference]中的“用法”部分。

配置划分为 `inbound`、`backend`、`outbound` 和 `on-error`。 指定的策略语句系列将针对请求和响应按顺序执行。

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

如果在处理请求的过程中出现错误：
* 将跳过 `inbound`、`backend` 或 `outbound` 部分中所有的剩余步骤。
* 执行将跳转到 `on-error` 部分中的语句。

通过将策略语句置于 `on-error` 部分，你可以：
* 使用 `context.LastError` 属性查看错误。
* 使用 `set-body` 策略检查和自定义错误响应。
* 配置发生错误时的应对措施。 

有关详细信息，请参阅 [API 管理策略中的错误处理](./api-management-error-handling-policies.md)，以了解针对以下内容的错误代码：
* 内置步骤
* 在处理策略语句的过程中可能发生的错误。 

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>如何配置策略

有关如何配置策略的信息，请参阅[设置或编辑策略](set-edit-policies.md)。

## <a name="policy-reference"></a>策略参考

请参阅[策略参考](./api-management-policies.md)了解政策说明完整列表及其设置。

## <a name="policy-samples"></a>策略示例

请参阅[策略示例](./policy-reference.md)获取更多代码示例。

## <a name="examples"></a>示例

### <a name="apply-policies-specified-at-different-scopes"></a>应用在不同范围指定的策略

如果在全局级别有一个策略并且为 API 配置了一个策略，则只要使用该特定 API，这两种策略都会被应用。 API 管理允许通过 `base` 元素实现组合策略声明的确定性排序。 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

在上述示例策略定义中：
* `cross-domain` 语句会在执行任何更高版本的策略前执行。
* `find-and-replace` 策略会在执行任何更高版本的策略后执行。 

>[!NOTE]
> 如果删除 API 范围内的 `<base />` 标记，则只会应用 API 范围内配置的策略。 不会应用产品范围策略或全局范围策略。

### <a name="restrict-incoming-requests"></a>限制传入的请求

要添加新的语句以限制到指定 IP 地址的入站请求，请将光标置于 `inbound` XML 元素的内容中，然后单击“限制调用方 IP”语句。

![限制策略][policies-restrict]

这会将 XML 代码片段添加到 `inbound` 元素，提供如何配置该语句的指导。

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

要限制入站请求并接受来自 IP 地址 1.2.3.4 的那些，请修改 XML，如下所示：

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [转换 API](transform-api.md)
+ [策略参考](./api-management-policies.md)，获取策略语句及其设置的完整列表
+ [策略示例](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
