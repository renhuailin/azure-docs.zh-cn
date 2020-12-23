---
title: Azure 服务总线订阅规则 SQL 筛选器语法 |Microsoft Docs
description: 本文提供了有关 SQL 筛选器语法的详细信息。 SQL 筛选器支持 SQL-92 标准的子集。
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 9bff18b2161e419d728c360c9ed950ac2867fea8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498670"
---
# <a name="subscription-rule-sql-filter-syntax"></a>订阅规则 SQL 筛选器语法

*SQL 筛选器* 是服务总线主题订阅的一个可用筛选器类型。 这是倾向标准的92子集上的文本表达式。 筛选表达式与 `sqlExpression` Azure 资源管理器模板中的服务总线的 "sqlFilter" 属性的元素 `Rule` 、Azure CLI [Azure Resource Manager template](service-bus-resource-manager-namespace-topic-with-rule.md) `az servicebus topic subscription rule create` 命令的 [`--filter-sql-expression`](/cli/azure/servicebus/topic/subscription/rule?preserve-view=true&view=azure-cli-latest#az_servicebus_topic_subscription_rule_create) 参数以及允许管理订阅规则的多个 SDK 函数结合使用。

服务总线高级版还支持通过 JMS 2.0 API 的 [JMS SQL 消息选择器语法](https://docs.oracle.com/javaee/7/api/javax/jms/Message.html) 。

  
``` 
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>参数  
  
-   `<scope>` 是一个可选字符串，指示 `<property_name>` 的范围。 有效值为 `sys` or `user`进行求值的基于 SQL 语言的筛选器表达式。 `sys` 值指示系统范围，其中 `<property_name>` 是 [BrokeredMessage 类](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)的公共属性名称。 `user` 指示用户范围，其中 `<property_name>` 是 [BrokeredMessage 类](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)字典的项。 `user` 如果未指定，则作用域为默认作用域 `<scope>` 。  
  
## <a name="remarks"></a>备注

尝试访问不存在的系统属性是错误的，而尝试访问不存在的用户属性不是错误。 相反，不存在的用户属性在内部作为未知值进行求值。 运算符求值期间会对未知值进行特殊处理。  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>参数  

 `<regular_identifier>` 是字符串，由以下正则表达式表示：  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
此语法是指任何以字母开头且后跟一个或多个下划线/字母/数字的字符串。  
  
`[:IsLetter:]` 是指分类为 Unicode 字母的任何 Unicode 字符。 `System.Char.IsLetter(c)` 返回 `true`（如果 `c` 为 Unicode 字母）。  
  
`[:IsDigit:]` 是指分类为十进制数字的任何 Unicode 字符。 `System.Char.IsDigit(c)` 返回 `true`（如果 `c` 为 Unicode 数字）。  
  
`<regular_identifier>`不能是保留关键字。  
  
`<delimited_identifier>` 是用左/右方括号 ([]) 括起来的任何字符串。 右方括号以两个右方括号表示。 下面是 `<delimited_identifier>`的示例：  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` 是指使用双引号引起来的任何字符串。 标识符中的双引号以两个双引号表示。 不建议使用带引号的标识符，因为可以轻松地将其与字符串常量混淆。 如果可能，请使用分隔标识符。 下面是一个示例 `<quoted_identifier>` ：  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>备注
  
`<pattern>` 必须是作为字符串进行求值的表达式。 它用作 LIKE 运算符的模式。      它可以包含以下通配符：  
  
-   `%`：包含零个或多个字符的任意字符串。  
  
-   `_`：任何单个字符。  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>备注  

`<escape_char>` 必须是作为长度为 1 的字符串进行求值的表达式。 它用作 LIKE 运算符的转义符。  
  
 例如，`property LIKE 'ABC\%' ESCAPE '\'` 匹配 `ABC%`，而不匹配以 `ABC` 开头的字符串。  
  
## <a name="constant"></a>constant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>参数  
  
-   `<integer_constant>` 不是用引号括起来并且不包含小数点的数字字符串。 这些值作为 `System.Int64` 在内部存储，并具有相同的作用域。  
  
     下面是长常量的示例：  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` 不是用引号括起来并且包含小数点的数字字符串。 这些值作为 `System.Double` 在内部存储，并具有相同的作用域/精度。  
  
     在将来的版本中，此数字可能存储在不同的数据类型中以支持确切的数字语义，因此您不应依赖于的基础数据类型 `System.Double` `<decimal_constant>` 。  
  
     下面是十进制常量的示例：  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` 是指使用科学记数法书写的数字。 这些值作为 `System.Double` 在内部存储，并具有相同的作用域/精度。 下面是近似数常量的示例：  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>备注  

布尔常量以关键字 **TRUE** 或 **FALSE** 表示。 这些值作为 `System.Boolean`存储。  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>备注  

字符串常量使用单引号引起来，并包含任何有效的 Unicode 字符。 字符串常量中嵌入的单引号以两个单引号表示。  
  
## <a name="function"></a>function  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>备注
  
`newid()`函数返回 `System.Guid` 由方法生成的 `System.Guid.NewGuid()` 。  
  
`property(name)` 函数返回 `name` 所引用的属性的值。 `name` 值可以是返回字符串值的任何有效表达式。  
  
## <a name="considerations"></a>注意事项
  
请注意以下 [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) 语义：  
  
-   属性名称区分大小写。  
  
-   运算符尽可能遵循 C# 隐式转换语义。  
  
-   系统属性是值在 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 实例中公开的公共属性。  
  
    请注意以下 `IS [NOT] NULL` 语义：  
  
    -   `property IS NULL` 作为 `true` 求值（如果属性不存在，或者属性的值为 `null`）。  
  
### <a name="property-evaluation-semantics"></a>属性求值语义  
  
- 尝试对不存在的系统属性求值会引发 [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) 异常。  
  
- 不存在的属性在内部被视为 **未知**。  
  
  算术运算符中的未知求值：  
  
- 对于二元运算符，如果操作数的左侧或右侧的求值结果为 " **未知**"，则结果为 " **未知**"。  
  
- 对于一元运算符，如果操作数的求值结果为 **未知**，则结果为 **未知**。  
  
  二进制比较运算符中的未知求值：  
  
- 如果操作数的左侧或右侧的求值结果为 " **未知**"，则结果为 " **未知**"。  
  
  `[NOT] LIKE`中的未知求值：  
  
- 如果任何操作数的求值结果为“未知”，则结果为“未知”。  
  
  `[NOT] IN`中的未知求值：  
  
- 如果左侧操作数的求值结果为“未知”，则结果为“未知”。  
  
  **AND** 运算符中的未知求值：  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 **OR** 运算符中的未知求值：  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>运算符绑定语义
  
-   在进行数据类型提升和隐式转换时，比较运算符（例如 `>`、`>=`、`<`、`<=`、`!=` 和 `=`）与 C# 运算符绑定遵循相同的语义。  
  
-   在进行数据类型提升和隐式转换时，算术运算符（例如 `+`、`-`、`*`、`/` 和 `%`）与 C# 运算符绑定遵循相同的语义。


## <a name="examples"></a>示例

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

### <a name="sql-filter-on-a-system-property"></a>系统属性上的 SQL 筛选器

```csharp
sys.Label LIKE '%bus%'`
```

### <a name="using-or"></a>使用或 

```csharp
 sys.Label LIKE '%bus%'` OR `user.tag IN ('queue', 'topic', 'subscription')
```

### <a name="using-in-and-not-in"></a>使用 IN 和 NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

有关 c # 示例，请参阅 [GitHub 上的主题筛选器示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)。


## <a name="next-steps"></a>后续步骤

- [SQLFilter 类 (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter 类 (.NET Framework)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SqlFilter 类 (Java) ](/java/api/com.microsoft.azure.servicebus.rules.SqlFilter)
- [SqlRuleFilter (JavaScript) ](/javascript/api/@azure/service-bus/sqlrulefilter)
- [az](/cli/azure/servicebus/topic/subscription/rule)
- [新-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)