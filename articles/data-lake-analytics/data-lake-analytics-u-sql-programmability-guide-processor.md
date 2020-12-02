---
title: 用于 Azure Data Lake 的 U SQL 用户定义处理器可编程性指南
description: 了解有关 U-SQL UDO 可编程性 guide-用户定义的处理器。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512526"
---
# <a name="use-user-defined-processor"></a>使用用户定义的处理器

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL UDO：用户定义的处理器
用户定义的处理器 (UDP) 是一种 U-SQL UDO，可让你通过应用可编程性功能处理传入的行。 使用 UDP 可以根据需要合并列、修改值和添加新列。 从基本意义上说，它有助于处理行集，以生成所需的数据元素。

## <a name="how-to-define-and-use-user-defined-processor"></a>如何定义和使用用户定义的处理器
若要定义 UDP，需使用 `SqlUserDefinedProcessor` 属性创建 `IProcessor` 接口，这对于 UDP 是可选的。

此接口应包含 `IRow` 接口行集重写的定义，如以下示例中所示：

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** 指示该类型应注册为用户定义的处理器。 此类不能被继承。

SqlUserDefinedProcessor 属性对于 UDP 定义是 **可选** 的。

主要可编程性对象是输入和输出。 输入对象用于枚举输入列，输出对象用于将输出数据设置为处理器活动的结果。

对于输入列枚举，此处使用 `input.Get` 方法。

```csharp
string column_name = input.Get<string>("column_name");
```

`input.Get` 方法的参数是一个列，该列作为 U-SQL 基本脚本的 `PROCESS` 语句的 `PRODUCE` 子句的一部分传递。 此处需要使用正确的数据类型。

对于输出，请使用 `output.Set` 方法。

请务必了解，自定义生成器仅输出使用 `output.Set` 方法调用定义的列和值。

```csharp
output.Set<string>("mycolumn", mycolumn);
```

通过调用 `return output.AsReadOnly();` 触发实际处理器输出。

下面是一个处理器示例：

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

在此用例场景中，处理器通过合并现有列（在本例中为大写的“user”以及“des”）生成名为“full_description”的新列。 它还重新生成一个 GUID，并返回 GUID 的原始值和新值。

从上面的示例中看到，可在 `output.Set` 方法调用期间调用 C# 方法。

下面是使用自定义处理器的基本 U-SQL 脚本示例：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>后续步骤
* [U-SQL 可编程性指南-概述](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 可编程性指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)