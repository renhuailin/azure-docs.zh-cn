---
title: U-SQL 用户定义的应用器可编程性指南（适用于 Azure Data Lake）
description: 了解 U-SQL UDO 可编程性指南 - 用户定义的应用器。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512518"
---
# <a name="use-user-defined-applier"></a>使用用户定义的应用器 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL UDO：用户定义的应用器
使用 U-SQL 用户定义的应用器可为查询的外部表表达式返回的每一行调用自定义 C# 函数。 为左侧输入的每一行计算右侧输入，并合并生成的行以得到最终输出。 APPLY 运算符生成的数个列是左侧和右侧输入中的列集的组合。


## <a name="how-to-define-and-use-user-defined-applier"></a>如何定义和使用用户定义的应用器
用户定义的应用器将作为 USQL SELECT 表达式的一部分进行调用。

对用户定义的应用器的典型调用如下所示：

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

有关在 SELECT 表达式中使用应用器的详细信息，请参阅 [U-SQL SELECT：从 CROSS APPLY 和 OUTER APPLY 中选择](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply)。

用户定义的应用器基类定义如下所示：

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

若要定义用户定义的应用器，需使用 [`SqlUserDefinedApplier`] 属性创建 `IApplier` 接口，这对于用户定义的应用器定义是可选的。

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* 针对外部表的每一行进行调用。 返回 `IUpdatableRow` 输出行集。
* 构造函数类用于将参数传递到用户定义的应用器。

**SqlUserDefinedApplier** 指示该类型应注册为用户定义的应用器。 此类不能被继承。

**SqlUserDefinedApplier** 对于用户定义的应用器是 **可选** 的。


主要可编程性对象如下所示：

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

输入行集作为 `IRow` 输入进行传递。 输出行将生成为 `IUpdatableRow` 输出接口。

可通过调用 `IRow` 架构方法确定各列名称。

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

为从传入 `IRow` 获取实际数据值，需使用 `IRow` 接口的 Get() 方法。

```csharp
mycolumn = row.Get<int>("mycolumn")
```

或使用架构列名称：

```csharp
row.Get<int>(row.Schema[0].Name)
```

必须使用 `IUpdatableRow` 输出设置输出值：

```csharp
output.Set<int>("mycolumn", mycolumn)
```

请务必了解，自定义应用器仅输出使用 `output.Set` 方法调用定义的列和值。

通过调用 `yield return output.AsReadOnly();` 触发实际输出。

可将用户定义的应用器参数传递到构造函数。 应用器可返回数量不等的列，这些列需要在基本 U-SQL 脚本中应用器调用期间进行定义。

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

下面是用户定义的应用器示例：

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

下面是用户定义的应用器的基本 U-SQL 脚本：

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此用例场景中，用户定义的应用器充当车队属性的逗号分隔值分析器。 输入文件行如下所示：

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

它是典型的制表符分隔 TSV 文件，具有包含制造商和型号等汽车属性的属性列。 这些属性必须解析为表中的列。 提供的应用器还可让你基于传递的参数在结果行集中生成动态数量的属性。 可以生成所有属性，或仅生成特定的属性集。

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

用户定义的应用器可作为应用器对象的新实例进行调用：

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

或使用包装器工厂方法的调用实现：

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>后续步骤
* [U-SQL 可编程性指南 - 概述](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 可编程性指南 - UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)