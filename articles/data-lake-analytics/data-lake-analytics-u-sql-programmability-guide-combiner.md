---
title: 用于 Azure Data Lake 的 U SQL 用户定义的合并器可编程性指南
description: 了解有关合并器的详细信息。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512527"
---
# <a name="use-user-defined-combiner"></a>使用用户定义的合并器

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL UDO：用户定义的合并器
使用用户定义的合并器 (UDC) 可基于自定义逻辑合并左侧和右侧行集中的行。 用户定义的合并器与 COMBINE 表达式配合使用。

## <a name="how-to-define-and-use-user-defined-combiner"></a>如何定义和使用用户定义的合并器

合并器通过 COMBINE 表达式进行调用，此表达式提供关于两个输入行集、分组列、预期结果架构的必要信息和其他信息。

若要在基本 U-SQL 脚本中调用合并器，需使用以下语法：

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

有关详细信息，请参阅 [COMBINE 表达式 (U-SQL)](/u-sql/statements-and-expressions/combine-expression)。

若要定义用户定义的合并器，需使用 [`SqlUserDefinedCombiner`] 属性创建 `ICombiner` 接口，这对于用户定义的合并器定义是可选的。

基本 `ICombiner` 类定义：

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

`ICombiner` 接口的自定义实现应包含 `IEnumerable<IRow>` 合并重写的定义。

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

**SqlUserDefinedCombiner** 属性指示该类型应注册为用户定义的合并器。 此类不能被继承。

**SqlUserDefinedCombiner** 用于定义合并器模式属性。 它也是用户定义的合并器定义的可选属性。

CombinerMode 模式

CombinerMode 枚举可采用以下值：

* Full  (0)：每个输出行可能依赖于左侧和右侧具有相同密钥值的所有输入行。

* Left  (1)：每个输出行依赖于左侧的单个输入行（还可能依赖于右侧具有相同密钥值的所有行）。

* Right (2)：每个输出行依赖于右侧的单个输入行（还可能依赖于左侧具有相同密钥值的所有行）。

* Inner  (3)：每个输出行依赖于左侧和右侧具有相同值的单个行。

示例：[`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


主要可编程性对象是：

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

输入行集作为左侧和右侧 `IRowset` 类型的接口进行传递。 必须同时枚举这两个行集以进行处理。 由于只能枚举每个接口一次，因此必须在必要时对其进行枚举和缓存。

出于缓存目的，我们可以创建一个列表 \<T\> 类型作为 LINQ 查询执行的结果，尤其是列出<`IRow`>。 还可在枚举期间使用匿名数据类型。

有关 LINQ 查询的详细信息，请参阅 [Linq 查询简介 (c # ) ](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) ，以及 [ienumerable \<T\> 接口](/dotnet/api/system.collections.generic.ienumerable-1) 的详细信息 \<T\> 。

为从传入 `IRowset` 获取实际数据值，需使用 `IRow` 接口的 Get() 方法。

```csharp
mycolumn = row.Get<int>("mycolumn")
```

可通过调用 `IRow` 架构方法确定各列名称。

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

或使用架构列名称：

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

使用 LINQ 实现的常规枚举如下所示：

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

枚举两个行集后，将循环访问所有行。 对于左侧行集中的每一行，将查找满足合并器条件的所有行。

必须使用 `IUpdatableRow` 输出设置输出值。

```csharp
output.Set<int>("mycolumn", mycolumn)
```

通过调用 `yield return output.AsReadOnly();` 触发实际输出。

下面是一个合并器示例：

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

在此用例场景中，将为零售商构建分析报表。 目的是查找所有此类产品：成本超过 $20,000，且在特定时间范围内在网站上的销售速度快于常规零售方式。

以下是基本 U-SQL 脚本。 可对比常规联接和合并器的逻辑：

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

用户定义的合并器可作为填充器对象的新实例进行调用：

```csharp
USING new MyNameSpace.MyCombiner();
```


或使用包装器工厂方法的调用实现：

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>后续步骤
* [U-SQL 可编程性指南-概述](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 可编程性指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)