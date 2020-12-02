---
title: 用于 Azure Data Lake 的 U SQL 用户定义的输出器可编程性指南
description: 了解有关 U-SQL UDO 可编程性 guide 用户定义的输出器。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512517"
---
# <a name="use-user-defined-outputter"></a>使用用户定义的输出器

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO：用户定义的输出器
用户定义的输出器是另一种 U-SQL UDO，允许扩展内置 U-SQL 功能。 与提取程序类似，内置输出器也有几种。

* *Outputters.Text()*：将数据写入不同编码的分隔文本文件中。
* *Outputters.Csv()*：将数据写入不同编码的逗号分隔值 (CSV) 文件中。
* *Outputters.Tsv()*：将数据写入不同编码的制表符分隔值 (TSV) 文件中。

自定义输出器允许以自定义格式编写数据。 这有助于完成以下任务：

* 将数据写入半结构化或非结构化文件。
* 编写编码方式不受支持的数据。
* 修改输出数据或添加自定义属性。

## <a name="how-to-define-and-use-user-defined-outputter"></a>如何定义和使用用户定义的输出器
若要定义用户定义的输出器，需要创建 `IOutputter` 接口。

下面是基本 `IOutputter` 类的实现：

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

输出器的的所有输入参数（如列/行分隔符、编码等）需要在类的构造函数中定义。 `IOutputter` 接口还应包含 `void Output` 重写的定义。 （可选）可设置 `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` 属性以进行原子文件处理。 有关详细信息，请参阅以下详细信息。

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* 为每个输入行调用 `Output`。 返回 `IUnstructuredWriter output` 行集。
* 构造函数类用于将参数传递到用户定义的输出器。
* `Close` 用于选择性地进行重写以发布开销状态或确定最后一行的写入时间。

**SqlUserDefinedOutputter** 属性指示该类型应注册为用户定义的输出器。 此类不能被继承。

SqlUserDefinedOutputter 是用户定义的输出器定义的可选属性。 用于定义 AtomicFileProcessing 属性。

* bool     AtomicFileProcessing   

* **true** = 指示此输出器需要原子输出文件（JSON、XML 等）
* **false** = 指示此输出器可以处理拆分文件/分布式文件（CSV、SEQ 等）

主要可编程性对象是行和输出。 **row** 对象用于将输出数据枚举为 `IRow` 接口。 **Output** 用于将输出数据设置为目标文件。

可通过 `IRow` 接口访问输出数据。 一次将输出数据传递到一行。

通过调用 IRow 接口的 Get 方法枚举各个值。

```csharp
row.Get<string>("column_name")
```

可通过调用 `row.Schema` 确定各列名称：

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

使用此方法可为任何元数据架构构建灵活的输出器。

使用 `System.IO.StreamWriter` 将输出数据写入文件。 流参数设置为 `output.BaseStream`（作为 `IUnstructuredWriter output` 的一部分）。

每次行迭代后，必须将数据缓冲区刷新到文件中。 此外，在默认启用 Disposable 属性并使用 **using** 关键字的情况下，必须使用 `StreamWriter` 对象：

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

否则，需要在每次迭代后显式调用 Flush() 方法。 以下示例对此做了演示。

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>为用户定义的输出器设置页眉和页脚
若要设置页眉，请使用单个迭代执行流。

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

第一个 `if (isHeaderRow)` 块中的代码仅执行一次。

若要设置页脚，请使用对 `System.IO.Stream` 对象的实例的引用 (`output.BaseStream`)。 在 `IOutputter` 接口的 Close() 方法中编写页脚。  （有关详细信息，请参阅以下示例。）

下面是用户定义的输出器示例：

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

U-SQL 基本脚本：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

这是一个 HTML 输出器，它使用表数据创建 HTML 文件。

### <a name="call-outputter-from-u-sql-base-script"></a>从 U-SQL 基本脚本调用输出器
若要从基的 U-SQL 脚本调用自定义输出器，则必须创建输出器对象的新实例。

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

为避免在基本脚本中创建对象的实例，可以创建函数包装器，如以下示例中所示：

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

在这种情况下，原始调用如下所示：

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>后续步骤
* [U-SQL 可编程性指南-概述](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 可编程性指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)