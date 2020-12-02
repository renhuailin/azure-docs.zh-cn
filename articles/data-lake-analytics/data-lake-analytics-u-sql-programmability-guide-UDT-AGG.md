---
title: 用于 Azure Data Lake 的 U SQL UDT 和 UDAGG 可编程性指南
description: 了解 Azure Data Lake Analytics 中的 U SQL UDT 和 UDAGG 可编程性，使你能够创建良好的 SCRIPT.USQL 脚本。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: bf2e1e1bc30aeb3306d0a643eca4725d8765edac
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512557"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>U-SQL 可编程性指南-UDT 和 UDAGG



## <a name="use-user-defined-types-udt"></a>使用用户定义的类型 (UDT)
用户定义的类型 (UDT) 是 U-SQL 的另一个可编程性功能。 U-SQL UDT 的作用类似常规 C# 用户定义的类型。 C# 是一种强类型语言，允许使用内置的和自定义的用户定义的类型。

在行集中的顶点之间传递 UDT 时，U-SQL 无法隐式序列化或反序列化任意 UDT。 这意味着，用户必须使用 IFormatter 接口提供显式格式化程序。 这样，就为 U-SQL 提供了针对 UDT 的序列化和反序列化方法。

> [!NOTE]
> 即使设置了 IFormatter，U-SQL 的内置提取器和输出器目前也无法与文件来回序列化或反序列化 UDT 数据。 因此，在使用 OUTPUT 语句将 UDT 数据写入文件或者使用提取器读取 UDT 数据时，必须以字符串或字节数组的形式传递数据。 然后，显式调用序列化和反序列化代码（即，UDT 的 ToString() 方法）。 另一方面，用户定义的提取器和输出器可以读取和写入 UDT。

如果尝试在 EXTRACTOR 或 OUTPUTTER（在之前的 SELECT 以外）中使用 UDT，如下所示：

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

将收到以下错误：

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

要在输出器中处理 UDT，则必须使用 ToString() 方法将其序列化，或创建一个自定义输出器。

目前不能在 GROUP BY 中使用 UDT。 如果在 GROUP BY 中使用 UDT，将引发以下错误：

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

若要定义 UDT，则必须：

1. 添加以下命名空间：

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. 添加 UDT 接口可能需要的 `Microsoft.Analytics.Interfaces`。 此外，可能需要使用 `System.IO` 定义 IFormatter 接口。

3. 使用 SqlUserDefinedType 属性定义用户定义的类型。

**SqlUserDefinedType** 用于将程序集中的类型定义标记为 U-SQL 中的用户定义的类型 (UDT)。 特性上的属性反映 UDT 的物理特征。 此类不能被继承。

SqlUserDefinedType 是 UDT 定义必需的特性。

类的构造函数：  

* SqlUserDefinedTypeAttribute（类型格式化程序）

* 类型格式化程序：定义 UDT 格式化程序所需的参数 -- 具体而言，`IFormatter` 接口的类型必须在此处传递。

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* 典型的 UDT 还需要 IFormatter 接口的定义，如以下示例中所示：

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter`接口使用根类型对对象图进行序列化和反序列化 \<typeparamref name="T"> 。

\<typeparam name="T">要进行序列化和反序列化的对象图的根类型。

* **反序列化**：对所提供的流上的数据进行反序列化，并重构对象的图形。

* **序列化**：使用所提供流的给定根对对象或对象的图形进行序列化。

`MyType` 实例：类型的实例。  
`IColumnWriter` 写入器/`IColumnReader` 读取器：基础列流。  
`ISerializationContext` 上下文：用于定义一组标志的枚举，这些标志在序列化期间指定流的源和定义上下文。

* **Intermediate**：指定源或定义上下文不是持久存储区。

* **Persistence**：指定源或定义上下文是持久存储区。

U-SQL UDT 定义是常规 C# 类型，可包括对运算符（如 +/==/!=）的重写。 它还可包括静态方法。 例如，如果将此 UDT 用作 U-SQL MIN 聚合函数的参数，则必须定义 < 运算符重写。

本指南先前演示了示例：从 `Qn:Pn (Q1:P10)` 格式的特定日期确定会计时段。 以下示例展示如何定义会计时段值的自定义类型。

以下示例演示了代码隐藏部分和用于该部分的自定义 UDT 和 IFormatter 接口：

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

定义的类型包括两个数字：季度和月份。 运算符 `==/!=/>/<` 和静态方法 `ToString()` 在此处定义。

如前文所述，UDT 可在 SELECT 表达式中使用，但不能在未进行自定义序列化的情况下在 OUTPUTTER/EXTRACTOR 中使用。 必须使用 `ToString()` 将其序列化为字符串，或将其与自定义 OUTPUTTER/EXTRACTOR 配合使用。

接下来讨论 UDT 的用法。 在代码隐藏部分中，已将 GetFiscalPeriod 函数更改为以下值：

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

可以看到，它返回 FiscalPeriod 类型的值。

以下示例演示如何在 U-SQL 基本脚本中进一步使用 UDT。 此示例演示从 U-SQL 脚本调用 UDT 的不同形式。

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

下面是完整的代码隐藏部分的示例：

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>使用用户定义的聚合 (UDAGG)
用户定义的聚合是非随时随附于 U-SQL 的任何与聚合相关的函数。 示例包括：用于执行自定义数学计算、字符串串联或字符串操作的聚合等。

用户定义的聚合基类定义如下所示：

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** 指示该类型应注册为用户定义的聚合。 此类不能被继承。

SqlUserDefinedType 属性对于 UDAGG 定义是 **可选** 的。


基类允许传递三个抽象参数：两个输入参数和一个结果参数。 数据类型是可变的且应在类继承期间定义。

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** 在计算期间针对每个组进行一次调用。 它为每个聚合组提供初始化例程。  
* **Accumulate** 针对每个值执行一次。 它提供聚合算法的主要功能。 可用于聚合在类继承期间定义的各种数据类型的值。 可接受两个可变数据类型的参数。
* **Terminate** 在处理结束时按每个聚合组执行一次，以输出每组的结果。

若要声明正确的输入或输出数据类型，请使用类定义，如下所示：

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1：Accumulate 的第一个参数
* T2：要累积的第二个参数
* TResult：Terminate 的返回类型

例如：

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

或

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>在 U-SQL 中使用 UDAGG
若要使用 UDAGG，需首先在代码隐藏中定义它，或从现有可编程性 DLL 对其进行引用，如前文所述。

然后使用以下语法：

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

下面是 UDAGG 的示例：

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

基本 U-SQL 脚本：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此用例场景中，将串联特定用户的类 GUID。

## <a name="next-steps"></a>后续步骤
* [U-SQL 可编程性指南-概述](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 可编程性指南-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
