---
title: Azure 服务总线订阅规则 SQL 筛选器语法 | Microsoft Docs
description: 本文提供了有关 SQL 筛选器语法的详细信息。 SQL 筛选器支持 SQL-92 标准的子集。
ms.topic: article
ms.date: 04/30/2021
ms.openlocfilehash: 6b8190cf2a57b47fdce416fbe087fa8fa0485bda
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108743230"
---
# <a name="subscription-rule-sql-filter-syntax"></a>订阅规则 SQL 筛选器语法

SQL 筛选器是服务总线主题订阅的某个可用筛选器类型。 它是一个依赖于 SQL-92 标准子集的文本表达式。 筛选表达式与 [Azure 资源管理器模板](service-bus-resource-manager-namespace-topic-with-rule.md)中服务总线 `Rule` 的“sqlFilter”属性的 `sqlExpression` 元素一起使用，或者与 Azure CLI `az servicebus topic subscription rule create` 命令的 [`--filter-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) 参数以及几个允许管理订阅规则的 SDK 函数一起使用。

服务总线高级版还通过 JMS 2.0 API 支持 [JMS SQL 消息选择器语法](https://docs.oracle.com/javaee/7/api/javax/jms/Message.html)。

  
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
  
-   `<scope>` 是一个可选字符串，指示 `<property_name>` 的范围。 有效值为 `sys` or `user`进行求值的基于 SQL 语言的筛选器表达式。 
    - `sys` 值指示系统范围，其中的 `<property_name>` 是服务总线消息上的任何属性，如[消息、有效负载和序列化](service-bus-messages-payloads.md)中所述。
    - `user` 值指示用户范围，其中的 `<property_name>` 是可在发送到服务总线时在消息上设置的自定义属性的键。
    - `user` 范围是默认范围（如果 `<scope>` 未指定）。  
  
## <a name="remarks"></a>备注

尝试访问不存在的系统属性属于错误，而尝试访问不存在的用户属性不属于错误。 相反，不存在的用户属性在内部作为未知值进行求值。 运算符求值期间会对未知值进行特殊处理。  
  
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
  
`<regular_identifier>` 不能是保留关键字。  
  
`<delimited_identifier>` 是用左/右方括号 ([]) 括起来的任何字符串。 右方括号以两个右方括号表示。 下面是 `<delimited_identifier>`的示例：  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` 是指使用双引号引起来的任何字符串。 标识符中的双引号以两个双引号表示。 不建议使用带引号的标识符，因为很容易将其与字符串常量混淆。 如果可能，请使用分隔标识符。 下面是 `<quoted_identifier>` 的示例：  
  
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
  
-   `<integer_constant>` 是指不使用引号引起来且不包含小数点的数字字符串。 这些值作为 `System.Int64` 在内部存储，并具有相同的作用域。  
  
     下面是长常量的示例：  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` 是指不使用引号引起来但包含小数点的数字字符串。 这些值作为 `System.Double` 在内部存储，并具有相同的作用域/精度。  
  
     在未来版本中，此数字可能以其他数据类型存储，目的是支持确切的数字语义，因此不应依赖于 `<decimal_constant>` 的基础数据类型为 `System.Double` 这一事实。  
  
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
  
`newid()` 函数返回 `System.Guid.NewGuid()` 方法生成的 `System.Guid`。  
  
`property(name)` 函数返回 `name` 所引用的属性的值。 `name` 值可以是返回字符串值的任何有效表达式。  
  
## <a name="considerations"></a>注意事项
  
请考虑以下 SQL 筛选器语义：  
  
-   属性名称区分大小写。  
  
-   运算符尽可能遵循 C# 隐式转换语义。  
  
-   系统属性是服务总线消息上的任何属性，如[消息、有效负载和序列化](service-bus-messages-payloads.md)中所述。
  
    请注意以下 `IS [NOT] NULL` 语义：  
  
    -   `property IS NULL` 作为 `true` 求值（如果属性不存在，或者属性的值为 `null`）。  
  
### <a name="property-evaluation-semantics"></a>属性求值语义  
  
- 尝试对不存在的系统属性求值会引发 `FilterException` 异常。  
  
- 不存在的属性在内部作为未知进行求值。  
  
  算术运算符中的未知求值：  
  
- 对于二元运算符，如果操作数的左侧或右侧的求值结果为未知，则结果为未知 。  
  
- 对于一元运算符，如果操作数的求值结果为 **未知**，则结果为 **未知**。  
  
  二进制比较运算符中的未知求值：  
  
- 如果操作数的左侧或右侧的求值结果为未知，则结果为未知 。  
  
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
有关示例，请参阅[服务总线筛选器示例](service-bus-filter-examples.md)。

## <a name="next-steps"></a>后续步骤

- [SQLFilter 类 (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter 类 (.NET Framework)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SqlFilter 类 (Java)](/java/api/com.microsoft.azure.servicebus.rules.SqlFilter)
- [SqlRuleFilter (JavaScript)](/javascript/api/@azure/service-bus/sqlrulefilter)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)
