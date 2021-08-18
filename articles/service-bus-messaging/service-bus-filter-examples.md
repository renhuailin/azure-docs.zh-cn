---
title: 在 Azure 服务总线中设置订阅筛选器 | Microsoft Docs
description: 本文提供了有关在“Azure 服务总线”主题订阅上定义筛选器和操作的示例。
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 8146e1be1af354b820ea0d3fed7ba9434a4d4e1d
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113302601"
---
# <a name="set-subscription-filters-azure-service-bus"></a>设置订阅筛选器（Azure 服务总线）
本文提供了一些有关在“服务总线”主题订阅上设置筛选器的示例。 有关筛选器的概念性信息，请参阅[筛选器](topic-filters.md)。

## <a name="filter-on-system-properties"></a>按系统属性筛选
若要在筛选器中引用系统属性，请使用以下格式：`sys.<system-property-name>`。 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>按消息属性筛选
下面是在筛选器中使用消息属性的示例。 可以使用 `user.property-name` 或仅 `property-name` 来访问消息属性。

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>按带有特殊字符的消息属性筛选
如果消息属性名称包含特殊字符，请使用双引号 (`"`) 将属性名称括起来。 例如，如果属性名称为 `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"`，则在筛选器中使用以下语法。 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>按带有数字值的消息属性筛选
下面的示例演示如何在筛选器中使用带有数字值的属性。 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>基于参数的筛选器
下面是使用基于参数的筛选器的几个示例。 在这些示例中，`DataTimeMp` 是 `DateTime` 类型的消息属性，`@dtParam` 是作为 `DateTime` 对象传递给筛选器的参数。

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>使用 IN 和 NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

有关 C# 示例，请参阅 [GitHub 上的主题筛选器示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)。


## <a name="correlation-filter-using-correlationid"></a>使用 CorrelationID 的关联筛选器

```csharp
new CorrelationFilter("Contoso");
```

它将筛选 `CorrelationID` 设置为 `Contoso` 的消息。 

## <a name="correlation-filter-using-system-and-user-properties"></a>使用系统属性和用户属性的关联筛选器

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

它等效于 `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

## <a name="next-steps"></a>后续步骤
请参阅以下示例： 

- [.NET - 使用筛选器进行的基本发送和接收操作的教程](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - 主题筛选器](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager 模板](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)
