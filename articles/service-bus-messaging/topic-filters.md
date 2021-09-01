---
title: Azure 服务总线主题筛选器 | Microsoft Docs
description: 本文介绍订阅者如何通过指定筛选器来定义希望从主题接收的消息。
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 310393456b21c43fe6d0665fad9e2f505045253c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867082"
---
# <a name="topic-filters-and-actions"></a>主题筛选器和操作

订阅者可以定义他们希望从主题接收的消息。 这些消息采用一个或多个命名订阅规则的形式指定。 每个规则都包含用于选择特定消息的筛选器条件，并且（可选）包含对所选消息进行批注的操作  。 

所有未包含操作的规则都将使用 `OR` 条件进行合并，并最终在订阅上生成一条消息（即使你有多个匹配规则也是如此） 。 

每个包含操作的规则都会生成消息副本。 此消息将具有名为 `RuleName` 的属性，其值为匹配规则的名称。 操作可以添加或更新属性，或从原始消息中删除属性，以便在订阅中生成消息。 

假设出现了下面这种情景：

- 订阅具有五个规则。
- 两个规则包含操作。
- 三个规则未包含操作。

在此示例中，如果你发送一条与所有五个规则都匹配的消息，你会在订阅上收到三条消息。 其中两条是两个包含操作的规则的消息，一条是三个不包含操作的规则的消息。 

每个新创建的主题订阅都具有初始默认订阅规则。 如果未显式指定规则的筛选条件，则应用的筛选器是 **true** 筛选器，通过它可以将所有消息都选择到订阅中。 默认规则没有关联批注操作。

## <a name="filters"></a>筛选器
服务总线支持三个筛选条件：

-   *SQL 筛选器* - **SqlFilter** 包含类似 SQL 的条件表达式，它会在代理中针对到达的消息的用户定义属性和系统属性进行计算。 所有系统属性在条件表达式中必须带有前缀 `sys.`。 [筛选条件的 SQL 语言子集](service-bus-messaging-sql-filter.md)可测试属性是否存在 (`EXISTS`)，以及是否为 null 值 (`IS NULL`)、逻辑“非”/“与”/“或”、关系运算符、简单数值算术和简单文本模式匹配（使用 `LIKE`）。
-   *布尔筛选器* - 通过 **TrueFilter** 和 **FalseFilter** 可以为订阅选择所有到达消息 (**true**) 或不选择任何到达消息 (**false**)。 这两个筛选器派生自 SQL 筛选器。 
-   *相关筛选器* - **CorrelationFilter** 包含一组条件，这些条件按照到达消息的一个或多个用户和系统属性进行匹配。 常见的用法是根据 CorrelationId 属性进行匹配，但应用程序也可以选择根据以下属性进行匹配：

    - **ContentType**
     - **Label**
     - **MessageId**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **To**
     - 任何用户定义的属性。 
     
     当到达消息的某个属性值等于相关筛选器中指定的值时，便存在匹配。 对于字符串表达式，比较会区分大小写。 指定多个匹配属性时，筛选器会将它们合并为逻辑“与”条件，这意味着若要使筛选器匹配，所有条件都必须匹配。

所有筛选器都会计算消息属性。 筛选器无法评估消息正文。

复杂筛选器规则需要处理能力。 具体而言，使用 SQL 筛选规则会导致订阅、主题和命名空间级别的总体消息吞吐量降低。 应用程序应尽可能选择关联筛选器而不是类似 SQL 的筛选器，因为它们的处理效率要高得多，并且对吞吐量的影响较小。

## <a name="actions"></a>操作

使用 SQL 筛选条件，可以定义可通过添加、删除或替换属性及其值对消息进行批注的操作。 操作[使用类似 SQL 的表达式](service-bus-messaging-sql-rule-action.md)，该表达式有一点依赖于 SQL UPDATE 语句语法。 在消息匹配之后，在将消息选入订阅之前，对消息执行操作。 对消息属性进行的更改是复制到订阅中的消息所专有的。

## <a name="usage-patterns"></a>使用模式

主题的最简单使用方案是每个订阅都获取发送到主题的每个消息的副本，这样可实现广播模式。

筛选器和操作可实现更进一步的两组模式：分区和路由。

分区使用筛选器以可预测且互相排斥的方式在多个现有主题订阅间分发消息。 当系统进行扩大以在功能相同的隔离舱（每个隔离舱包含总体数据的子集；例如客户配置文件信息）中处理许多不同上下文时，可使用分区模式。 借助分区，发布者可将消息提交到主题中，而无需了解分区模型。 消息随后会移动到正确的订阅，分区的消息处理程序随后会从该订阅检索它。

路由使用筛选器以可预测、但不一定独占的方式在主题订阅间分发消息。 通过与[自动转发](service-bus-auto-forwarding.md)功能相结合，主题筛选器可以用于在服务总线命名空间中创建复杂路由图，以便在 Azure 区域中进行消息分发。 通过使 Azure Functions 或 Azure 逻辑应用充当 Azure 服务总线命名空间之间的桥梁，可以创建直接集成到业务线应用程序中的复杂全局拓扑。

## <a name="examples"></a>示例
有关示例，请参阅[服务总线筛选器示例](service-bus-filter-examples.md)。



> [!NOTE]
> 由于 Azure 门户现在支持 Service Bus Explorer 功能，因此可以从门户创建或编辑订阅筛选器。 

## <a name="next-steps"></a>后续步骤
尝试采用所选语言的示例，了解 Azure 服务总线功能。 

- [适用于 .NET（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [适用于 Java（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [适用于 Python 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [适用于 JavaScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [适用于 TypeScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

在下面查找早期 .NET 和 Java 客户端库示例：
- [适用于 .NET（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [适用于 Java（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)