---
title: 查询加速 SQL 语言参考
titleSuffix: Azure Storage
description: 了解如何使用查询加速 sql 语法。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: f696a6b071d353c98e87387d5640e35ff579460e
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110477803"
---
# <a name="query-acceleration-sql-language-reference"></a>查询加速 SQL 语言参考

查询加速支持使用类似 ANSI SQL 的语言表示对 blob 内容的查询。  查询加速 SQL 方言是 ANSI SQL 的一个子集，支持的数据类型、运算符等有限，但它也在 ANSI SQL 上扩展以支持对层次结构半结构化数据格式（如 JSON）的查询。 

## <a name="select-syntax"></a>SELECT 语法

查询加速支持的唯一 SQL 语句是 SELECT 语句。 此示例返回表达式为其返回 true 的每一行。

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

对于 CSV 格式的数据，表必须为 `BlobStorage`。  这意味着，将针对 REST 调用中指定的任何 blob 运行查询。
对于 JSON 格式的数据，表是“表描述符”。   请参阅本文的[表描述符](#table-descriptors)部分。

在以下示例中，对于 WHERE 表达式为其返回 true 的每一行，此语句将返回通过计算每个投影表达式而得到的新行。


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

可以将一个或多个特定列指定为 SELECT 表达式的一部分（例如：`SELECT Title, Author, ISBN`）。 

> [!NOTE]
> 可在 SELECT 表达式中使用的特定列的最大数目为 49。 如果需要 SELECT 语句返回超过 49 列，请为 SELECT 表达式使用通配符 (`*`)（例如：`SELECT *`）。 

以下示例在表达式返回 true 的每一行上返回聚合计算（例如特定列的平均值）。 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

以下示例为拆分 CSV 格式的 blob 返回合适的偏移量。  请参阅本文的 [Sys.Split](#sys-split)部分。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>数据类型

|数据类型|说明|
|---------|-------------------------------------------|
|INT      |64 位带符号整数。                     |
|FLOAT    |64 位（双精度）浮点。|
|STRING   |可变长度的 Unicode 字符串。            |
|TIMESTAMP|时间点。                           |
|BOOLEAN  |True 或 False。                             |

从 CSV 格式的数据读取值时，所有值都作为字符串读取。  使用 CAST 表达式可以将字符串值转换为其他类型。  根据上下文，值可以隐式强制转换为其他类型。 有关详细信息，请参阅[数据类型优先级 (transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql)。

## <a name="expressions"></a>表达式

### <a name="referencing-fields"></a>引用字段

对于 JSON 格式的数据或带有标题行的 CSV 格式的数据，可以按名称引用字段。  字段名称可以带引号或不带引号。 带引号的字段名称用双引号字符 (") 括起来，可以包含空格，并且区分大小写。  未加引号的字段名称不区分大小写，并且不能包含任何特殊字符。

在 CSV 格式的数据中，字段也可以按序号引用，并以下划线 (_) 字符作为前缀。  例如，第一个字段可引用为 _1，或者第 11 个字段可引用为 _11。  按序号引用字段适用于不包含标题行的 CSV 格式的数据，在这种情况下，按序号引用是引用特定字段的唯一方法。

### <a name="operators"></a>运算符

支持以下标准 SQL 运算符：

|运算符|说明|
|--|--|
|[=](/sql/t-sql/language-elements/equals-transact-sql)    |比较两个表达式的等价性（比较运算符）。|
|[!=](/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |测试某个表达式是否不等于另一个表达式（比较运算符）。|
|[<>](/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |比较两个表达式，表示不等于（比较运算符）。|
|[<](/sql/t-sql/language-elements/less-than-transact-sql)    |比较两个表达式，表示小于（比较运算符）。|
|[<=](/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |比较两个表达式，表示小于或等于（比较运算符）。|
|[>](/sql/t-sql/language-elements/greater-than-transact-sql)    |比较两个表达式，表示大于（比较运算符）。 |
|[>=](/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |比较两个表达式以得出大于或等于的结果（比较运算符）。|
|[+](/sql/t-sql/language-elements/add-transact-sql)    |两个数相加。 这个加法算术运算符也可以将一个以天为单位的数字加到日期中。|
|[-](/sql/t-sql/language-elements/subtract-transact-sql)    |将两个数相减（减法算术运算符）。 |
|[/](/sql/t-sql/language-elements/divide-transact-sql)    |用一个数除以另一个数（算术除法运算符）。|
|[*](/sql/t-sql/language-elements/multiply-transact-sql)    |两个表达式相乘（算术乘法运算符）。|
|[%](/sql/t-sql/language-elements/modulo-transact-sql)    |返回两数相除后的余数。|
|[AND](/sql/t-sql/language-elements/bitwise-and-transact-sql)    |在两个整数值之间执行“逻辑位与”运算。|
|[或者](/sql/t-sql/language-elements/bitwise-or-transact-sql)    |将两个指定的整数值转换为二进制表达式后执行逻辑位或运算。|
|[NOT](/sql/t-sql/language-elements/not-transact-sql)    |对布尔型输入取反。|
|[CAST](/sql/t-sql/functions/cast-and-convert-transact-sql)    |将一种数据类型的表达式转换为另一种。|
|[BETWEEN](/sql/t-sql/language-elements/between-transact-sql)    |指定测试范围。|
|[IN](/sql/t-sql/language-elements/in-transact-sql)    |确定指定的值是否与子查询或列表中的值相匹配。|
|[NULLIF](/sql/t-sql/language-elements/nullif-transact-sql)    |如果两个指定的表达式相等，则返回空值。|
|[COALESCE](/sql/t-sql/language-elements/coalesce-transact-sql)    |按顺序计算变量并返回最初不等于 NULL 的第一个表达式的当前值。|

如果运算符左侧和右侧的数据类型不同，则将根据此处指定的规则执行自动转换：[数据类型优先级 (Transact-SQL) ](/sql/t-sql/data-types/data-type-precedence-transact-sql)。

查询加速 SQL 语言仅支持本文中讨论的数据类型的一小部分。  请参阅本文的[数据类型](#data-types)部分。

### <a name="casts"></a>转换

根据此处的规则：[数据类型转换（数据库引擎）](/sql/t-sql/data-types/data-type-conversion-database-engine)，查询加速 SQL 语言支持 CAST 运算符。  

查询加速 SQL 语言仅支持本文中讨论的数据类型的很小一部分。  请参阅本文的[数据类型](#data-types)部分。

### <a name="string-functions"></a>字符串函数

查询加速 SQL 语言支持以下标准 SQL 字符串函数：

|函数|说明|
|--|--|
|CHAR_LENGTH    | 如果字符串表达式是字符数据类型，则按字符返回字符串表达式的长度；否则，按字节返回字符串表达式的长度（最小整数不小于位数除以 8）。 （此函数与 CHARACTER_LENGTH 函数相同。）|
|CHARACTER_LENGTH    |如果字符串表达式是字符数据类型，则按字符返回字符串表达式的长度；否则，按字节返回字符串表达式的长度（最小整数不小于位数除以 8）。 （此函数与 CHAR_LENGTH 函数相同）|
|[LOWER](/sql/t-sql/functions/lower-transact-sql)    |将大写字符数据转换为小写字符数据后返回字符表达式。|
|[UPPER](/sql/t-sql/functions/upper-transact-sql)    |返回小写字符数据转换为大写的字符表达式。|
|[SUBSTRING](/sql/t-sql/functions/substring-transact-sql)    |返回 SQL Server 中的字符、二进制文件、文本或图像表达式的一部分。|
|[TRIM](/sql/t-sql/functions/trim-transact-sql)    |删除字符串开头和结尾的空格字符 char(32) 或其他指定字符。|
|LEADING    |删除字符串开头的空格字符 char(32) 或其他指定字符。|
|TRAILING    |删除字符串结尾的空格字符 char(32) 或其他指定字符。|

下面是几个示例：

|函数|示例|结果|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>日期函数

支持以下标准 SQL 日期函数：

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

目前，我们转换了所有[标准 IS08601 的日期格式](https://www.w3.org/TR/NOTE-datetime)。 

#### <a name="date_add-function"></a>DATE_ADD 函数

对于 ``DATE_ADD`` 函数，查询加速 SQL 语言支持年、月、日、小时、分钟和秒。

示例：

``sql DATE_ADD(datepart, quantity, timestamp) DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRACT 函数

对于 EXTRACT，除 ``DATE_ADD`` 函数支持的日期部分外，查询加速 SQL 语言还支持将 timezone_hour 和 timezone_minute 作为日期部分。

示例：

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING 函数

示例：

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

下表描述了可用于指定 ``TO_STRING`` 函数的输出格式的字符串。

|格式字符串    |输出                               |
|-----------------|-------------------------------------|
|yy               |以 2 位数格式表示年份 - 1999 年为“99”|
|y                |以 4 位数格式表示年份               |
|yyyy             |以 4 位数格式表示年份               |
|M                |年度月份 - 1                    |
|MM               |零填充月份 - 01               |
|MMM              |缩写 年度月份 - JAN            |
|MMMM             |全拼月份 - May                      |
|d                |月份日期 (1-31)                  |
|dd               |零填充月份日期 (01-31)     |
|a                |上午或下午                             |
|h                |一天的小时时段 (1-12)                   |
|hh               |零填充的一天的小时时段 (01-12)     |
|H                |一天的小时时段 (0-23)                   |
|HH               |零填充的一天的小时时段 (00-23)      |
|m                |分钟 (0-59)                |
|mm               |零填充分钟 (00-59)           |
|s                |秒 (0-59)             |
|ss               |零填充秒 (00-59)          |
|S                |秒的小数部分 (0.1-0.9)        |
|SS               |秒的小数部分 (0.01-0.99)      |
|SSS              |秒的小数部分 (0.001-0.999)    |
|X                |偏移量（小时）                      |
|XX 或 XXXX       |偏移量（小时和分钟）(+0430)  |
|XXX 或 XXXXX     |偏移量（小时和分钟）(-07:00) |
|x                |偏移量（小时） (7)                  |
|xx 或 xxxx       |偏移量（小时和分钟）(+0530)    |
|Xxx 或 xxxxx     |偏移量（小时和分钟）(+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP 函数

仅支持 IS08601 格式。

示例：

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> 还可以使用 ``UTCNOW`` 函数获取系统时间。


## <a name="aggregate-expressions"></a>聚合表达式

SELECT 语句可能包含一个或多个投影表达式或单个聚合表达式。  支持以下聚合表达式：

|表达式|说明|
|--|--|
|[COUNT(\*)](/sql/t-sql/functions/count-transact-sql)    |返回与谓词表达式匹配的记录数。|
|[COUNT(expression)](/sql/t-sql/functions/count-transact-sql)    |返回表达式为非 null 的记录数。|
|[AVERAGE(expression)](/sql/t-sql/functions/avg-transact-sql)    |返回表达式非 null 值的平均值。|
|[MIN(expression)](/sql/t-sql/functions/min-transact-sql)    |返回表达式的最小非 null 值。|
|[MAX(expression](/sql/t-sql/functions/max-transact-sql)    |返回表达式的最大非 null 值。|
|[SUM(expression)](/sql/t-sql/functions/sum-transact-sql)    |返回表达式的所有非 null 值的总和。|

### <a name="missing"></a>MISSING

``IS MISSING`` 运算符是查询加速 SQL 语言支持的唯一非标准用法。  对于 JSON 数据，如果特定输入记录中缺少某个字段，则表达式字段 ``IS MISSING`` 将计算为布尔值 true。

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>表描述符

对于 CSV 数据，表名称始终为 `BlobStorage`。  例如：

```sql
SELECT * FROM BlobStorage
```

对于 JSON 数据，可以使用其他选项：

```sql
SELECT * FROM BlobStorage[*].path
```

这允许对 JSON 数据的子集进行查询。

对于 JSON 查询，你可以在 FROM 子句中的某部分提及路径。 这些路径将帮助分析 JSON 数据的子集。 这些路径可以引用 JSON 数组和对象值。

我们来看一个示例，以便更详细地了解这一点。

这是示例数据：

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

你可能只对上述数据的 `warehouses` JSON 对象感兴趣。 `warehouses` 对象是一个 JSON 数组类型，因此你可以在 FROM 子句中提及它。 示例查询将如下所示：

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

查询将获取所有字段，但仅选择纬度。

如果只想访问 `dimensions` JSON 对象的值，则可以在查询中使用引用该对象。 例如：

```sql
SELECT length FROM BlobStorage[*].dimensions
```

这还限制了对 `dimensions` 对象成员的访问。 如果要访问 json 字段的其他成员和 JSON 对象的内部值，则可以使用如下面的示例中所示的查询：

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage 和 BlobStorage[\*] 均引用整个对象。 但是，如果在 FROM 子句中有路径，则需要使用 BlobStorage [\*]. 路径

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys.Split

这是 SELECT 语句的一种特殊形式，仅适用于 CSV 格式的数据。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

如果要成批下载并批处理 CSV 数据记录，请使用此语句。 通过这种方式，你可以并行处理记录，而无需一次下载所有记录。 此语句不从 CSV 文件返回记录。 而是返回批大小的集合。 然后，可以使用每个批大小检索一批数据记录。 

使用 split_size 参数可指定每个批包含的字节数。 例如，如果希望一次只处理 10 MB 的数据，则语句将如下所示：`SELECT sys.split(10485760)FROM BlobStorage`，因为 10 MB 等于 10,485,760 字节。 每个批最多可容纳 10 MB 的记录。 

在大多数情况下，每个批的大小将略高于你指定的数字。 这是因为批不能包含部分记录。 如果批中的最后一条记录在阈值结束之前开始，则该批需要更大的容量才能包含完整记录。 最后一个批的大小可能小于指定的大小。

>[!NOTE]
> split_size 必须至少为 10 MB (10485760)。

## <a name="see-also"></a>请参阅

- [Azure Data Lake Storage 查询加速](data-lake-storage-query-acceleration.md)
- [使用 Azure Data Lake Storage 查询加速来筛选数据](data-lake-storage-query-acceleration-how-to.md)