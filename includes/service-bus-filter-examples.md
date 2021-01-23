---
title: include 文件
description: include 文件
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742671"
---
## <a name="examples"></a>示例

### <a name="filter-on-system-properties"></a>系统属性的筛选器
若要在筛选器中引用系统属性，请使用以下格式： `sys.<system-property-name>` 。 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>筛选消息属性
下面是在筛选器中使用消息属性的示例。 您可以使用或来访问消息属性 `user.property-name` `property-name` 。

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>筛选带有特殊字符的消息属性
如果消息属性名称包含特殊字符，请使用双引号 (`"`) 将属性名称括起来。 例如，如果属性名称为 `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` ，则在筛选器中使用以下语法。 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>筛选具有数字值的消息属性
下面的示例演示如何在筛选器中使用具有数字值的属性。 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>基于参数的筛选器
下面是使用基于参数的筛选器的几个示例。 在这些示例中， `DataTimeMp` 是类型的消息属性 `DateTime` ， `@dtParam` 是作为对象传递到筛选器的参数 `DateTime` 。

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>使用 IN 和 NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

有关 C# 示例，请参阅 [GitHub 上的主题筛选器示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)。


### <a name="set-rule-action-for-a-sql-filter"></a>为 SQL 筛选器设置规则操作

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>使用 CorrelationID 的关联筛选器

```csharp
new CorrelationFilter("Contoso");
```

它将筛选 `CorrelationID` 设置为 `Contoso` 的消息。 

### <a name="correlation-filter-using-system-and-user-properties"></a>使用系统属性和用户属性的关联筛选器

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

它等效于 `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

