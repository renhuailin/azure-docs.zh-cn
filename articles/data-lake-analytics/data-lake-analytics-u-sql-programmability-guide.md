---
title: 适用于 Azure Data Lake 的 U-SQL 可编程性指南
description: 了解 Azure Data Lake Analytics 中的 U-SQL 概述和 UDF 可编程性，以便能够创建优质 USQL 脚本。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96510856"
---
# <a name="u-sql-programmability-guide-overview"></a>U-SQL 可编程性指南概述

U-SQL 是为大数据类型的工作负荷设计的查询语言。 U-SQL 的一个独有特点是将类似 SQL 的声明性语言与 C# 提供的可扩展性和可编程性结合起来。 本指南着重介绍通过 C# 实现的 U-SQL 语言的可扩展性和可编程性。

## <a name="requirements"></a>要求

下载并安装[针对 Visual Studio 的 Azure Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)。

## <a name="get-started-with-u-sql"></a>U-SQL 入门  

查看以下 U-SQL 脚本：

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

此脚本定义两个行集：`@a` 和 `@results`。 行集 `@results` 从 `@a`定义。

## <a name="c-types-and-expressions-in-u-sql-script"></a>U-SQL 脚本中的 C# 类型及表达式

U-SQL 表达式是与 U-SQL 逻辑运算（如 `AND`、`OR` 和 `NOT`）配合使用的 C# 表达式。 U-SQL 表达式可与 SELECT、0EXTRACT、WHERE、HAVING、GROUP BY 和 DECLARE 配合使用。 例如，以下脚本将字符串分析为 DateTime 值。

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

以下代码片段将字符串分析为 DECLARE 语句中的 DateTime 值。

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>使用 C# 表达式进行数据类型转换

以下示例演示如何使用 C# 表达式执行日期时间数据转换。 在此特定方案中，字符串日期/时间数据转换为标准日期时间（采用午夜 00:00:00 时间表示法）。

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>使用 C# 表达式显示今天的日期

可使用下面的 C# 表达式拉取今天的日期：`DateTime.Now.ToString("M/d/yyyy")`

以下示例演示如何在脚本中使用此表达式：

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>使用 .NET 程序集

U-SQL 扩展性模型很大程度取决于从 .NET 程序集添加自定义代码的能力。 

### <a name="register-a-net-assembly"></a>注册 .NET 程序集

使用 `CREATE ASSEMBLY` 语句将 .NET 程序集放入 U-SQL 数据库。 之后，U-SQL 脚本可以通过 `REFERENCE ASSEMBLY` 语句使用这些程序集。 

以下代码演示如何注册程序集：

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

以下代码演示如何引用程序集：

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

请参阅[程序集注册说明](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog)，了解本主题的详细信息。


### <a name="use-assembly-versioning"></a>使用程序集版本控制
目前，U-SQL 使用 .NET Framework 版本 4.7.2。 因此请确保自己的程序集与该版本的运行时兼容。

如上所述，U-SQL 以 64 位 (x64) 格式运行代码。 因此请确保将代码编译为在 x64 上运行。 否则将收到上面所示的不正确格式错误。

每个上传的程序集 DLL、资源文件（如不同的运行时、本机程序集或配置文件）最大可为 400 MB。 部署的资源（通过 DEPLOY RESOURCE 或引用程序集部署）的总大小及其附加文件不能超过 3 GB。

最后请注意，每个 U-SQL 数据库仅可包含任何给定程序集的一个版本。 例如，如果需要 NewtonSoft Json.NET 库的 7 和 8 两个版本，则需要将它们注册到两个不同的数据库中。 此外，每个脚本仅可引用给定程序集 DLL 的一个版本。 在这一方面，U-SQL 遵循 C# 程序集管理和版本控制语义。

## <a name="use-user-defined-functions-udf"></a>使用用户定义的函数 (UDF)
U-SQL 用户定义的函数或 UDF 是编程例程，可接受参数、执行操作（例如复杂计算）并将操作的结果以值的形式返回。 UDF 的返回值只能是单个标量。 与任何其他 C# 标量函数相似，U-SQL UDF可在 U-SQL 基本脚本中进行调用。

我们建议将 U-SQL 用户定义的函数初始化为 **公共** 和 **静态**。

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

首先来看一个创建 UDF 的简单示例。

在此用例方案中，需要确定会计时段，包括特定用户首次登录的会计季度和会计月份。 本方案中适用年份的第一个会计月份为 6 月。

为计算会计时段，引入以下 C# 函数：

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
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

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

它能简单地计算会计月份及季度并返回字符串值。 对于 6 月（第一个会计季度的第一个月），我们使用“Q1:P1”。 对于 7 月，我们使用“Q1:P2”，以此类推。

这是用于 U-SQL 项目的一个常规 C# 函数。

下面是此方案中代码隐藏部分的样子：

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
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

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

现在从基本 U-SQL 脚本调用此函数。 为此，必须提供函数的完全限定名称，包括命名空间（在本例中为 NameSpace.Class.Function(parameter)）。
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

下面是实际的 U-SQL 基本脚本：
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

下面是脚本执行的输出文件：

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

本示例演示 U-SQL 中内联 UDF 的简单用法。

### <a name="keep-state-between-udf-invocations"></a>保持 UDF 调用之间的状态
在通过代码隐藏全局变量利用交互性时，U-SQL C# 可编程性对象会更加复杂。 来看看以下这个企业用例场景。

在大型组织中，用户可在多种内部应用程序之间切换。 其中可能包括 Microsoft Dynamics CRM、Power BI 等。 客户希望对用户切换不同应用程序的方式、使用趋势等进行遥测分析。 企业的目标是优化应用程序的使用。 他们可能会合并不同的应用程序或特定的登录例程。

要实现此目标，必须确定会话 ID 和上次发生的会话之间的延迟时间。

需要找到上一次登录，并将其分配到正在生成到相同应用程序中的所有会话中。 第一个难题是 U-SQL 基本脚本将不允许使用 LAG 函数对已计算的列进行计算。 第二个难题是必须为同一时间段内的所有会话保留特定会话。

为解决此问题，我们使用了代码隐藏部分内部的全局变量：`static public string globalSession;`。

此全局变量在脚本执行期间将应用于整个行集。

下面是 U-SQL 程序的代码隐藏部分：

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

本示例显示全局变量 `static public string globalSession;` 在 `getStampUserSession` 函数内部使用，并且会在每次更改会话参数时重新进行初始化。

U-SQL 基本脚本如下所示：

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

`USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` 函数在第二次“内存行集”计算期间在此处进行调用。 它传递 `UserSessionTimestamp` 列，并在 `UserSessionTimestamp` 更改之前返回值。

输出文件如下所示：

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

此示例演示一个更复杂的用例场景，其中将使用代码隐藏部分（应用于整个内存行集）内部的全局变量。

## <a name="next-steps"></a>后续步骤
* [U-SQL 可编程性指南 - UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [U-SQL 可编程性指南 - UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)