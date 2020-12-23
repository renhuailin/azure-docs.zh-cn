---
title: 用于 Azure Data Lake 的 U SQL 用户定义的提取程序可编程性指南
description: 了解有关 U-SQL UDO 可编程性 guide-用户定义的提取程序。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: ad7f6336753903533771033de21aec8262425a61
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608011"
---
# <a name="use-user-defined-extractor"></a>使用用户定义的提取器

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO：用户定义的提取程序
U-SQL 允许通过使用 EXTRACT 语句导入外部数据。 EXTRACT 语句可以使用内置 UDO 提取器：  

* *Extractors.Text()*：提供从不同编码的分隔文本文件中进行的提取。

* *Extractors.Csv()*：提供从不同编码的逗号分隔值 (CSV) 文件中进行的提取。

* *Extractors.Tsv()*：提供从不同编码的制表符分隔值 (TSV) 文件中进行的提取。

它可用于开发自定义提取器。 可在数据导入期间借助此语句执行以下任何任务：

* 通过拆分列、修改各个值来修改输入数据。 使用 PROCESSOR 功能可以更好地合并列。
* 分析非结构化数据（如网页和电子邮件）或半非结构化数据（如 XML/JSON）。
* 分析编码方式不受支持的数据。

## <a name="how-to-define-and-use-user-defined-extractor"></a>如何定义和使用用户定义的提取程序
若要定义用户定义的提取器 (UDE)，需要创建 `IExtractor` 接口。 提取器的所有输入参数（如列/行分隔符、编码）需要在类的构造函数中定义。 `IExtractor` 接口还应包含 `IEnumerable<IRow>` 重定的定义，如下所示：

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**SqlUserDefinedExtractor** 属性指示该类型应注册为用户定义的提取器。 此类不能被继承。

SqlUserDefinedExtractor 是 UDE 定义的可选特性。 用于定义 UDE 对象的 AtomicFileProcessing 属性。

* bool     AtomicFileProcessing   

* **true** = 指示此提取器需要原子输入文件（JSON、XML 等）
* **false** = 指示此提取器可以处理拆分文件/分布式文件（CSV、SEQ 等）

主要 UDE 可编程性对象包括输入和输出。 输入对象用于将输入数据枚举为 `IUnstructuredReader`。 输出对象用于将输出数据设置为提取器活动的结果。

可通过 `System.IO.Stream` 和 `System.IO.StreamReader` 访问输入数据。

为枚举输入列，此处首先使用行分隔符拆分输入流。

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

然后进一步将输入行拆分为列部分。

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

为了设置输出数据，此处使用 `output.Set` 方法。

请务必了解，自定义提取器仅返回使用输出定义的列和值。 所定义的列/值。

```csharp
output.Set<string>(count, part);
```

通过调用 `yield return output.AsReadOnly();` 触发实际的提取器输出。

下面是提取器的示例：

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

在此用例场景中，提取器将重新生成“guid”列的 GUID，将“user”列的值转换为大写。 通过对输入数据进行分析和操作，自定义提取器可生成更复杂的结果。

下面是使用自定义提取器的基本 U-SQL 脚本：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>后续步骤
* [U-SQL 可编程性指南-概述](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 可编程性指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)