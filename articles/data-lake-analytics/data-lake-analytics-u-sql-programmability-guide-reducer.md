---
title: 用于 Azure Data Lake 的 U SQL 用户定义的化简器可编程性指南
description: 了解有关化简器的详细信息。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512516"
---
# <a name="use-user-defined-reducer"></a>使用用户定义的化简器

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL UDO：用户定义的化简器

使用 U-SQL 可在 C# 中编写自定义行集化简器的功能，方法是通过实现 IReducer 接口来使用用户定义的运算符扩展性框架。

用户定义的化简器 (UDR) 可用于在数据提取（导入）期间消除不必要的行。 它还可用于操作和评估行与列。 它还可根据可编程性逻辑定义需要提取的行。

## <a name="how-to-define-and-use-user-defined-reducer"></a>如何定义和使用用户定义的化简器
若要定义 UDR 类，需要使用可选的 `SqlUserDefinedReducer` 属性创建 `IReducer` 接口。

此类接口应包含 `IEnumerable` 接口行集重写的定义。

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**SqlUserDefinedReducer** 属性指示该类型应注册为用户定义的化简器。 此类不能被继承。
**SqlUserDefinedReducer** 是用户定义的化简器定义的可选属性。 可用于定义 IsRecursive 属性。

* bool     IsRecursive    
* **true** = 指示此化简器是否关联和可交换

主要可编程性对象是输入和输出。 input 对象用于枚举输入行。 Output 用于将输出行设置为化简活动的结果。

对于输入行枚举，需使用 `Row.Get` 方法。

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

`Row.Get` 方法的参数是一个列，该列作为 U-SQL 基本脚本的 `REDUCE` 语句的 `PRODUCE` 类的一部分传递。 此处还需使用正确的数据类型。

对于输出，请使用 `output.Set` 方法。

请务必了解，自定义化简器仅输出使用 `output.Set` 方法调用定义的值。

```csharp
output.Set<string>("mycolumn", guid);
```

通过调用 `yield return output.AsReadOnly();` 触发实际化简器输出。

下面是一个化简器示例：

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

在此用例场景中，化简器将跳过具有空用户名的行。 对于行集中的每一行，它读取所需的每一列，并计算用户名的长度。 仅当用户名值长度大于 0 时，它才输出实际行。

下面是使用自定义化简器的基本 U-SQL 脚本：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>后续步骤
* [U-SQL 可编程性指南-概述](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 可编程性指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)