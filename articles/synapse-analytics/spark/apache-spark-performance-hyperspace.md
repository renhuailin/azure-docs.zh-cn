---
title: Apache Spark 的超空间索引
description: 使用 Hyperspace 索引优化 Apache Spark 性能
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/12/2020
ms.author: euang
ms.reviewer: euang
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: e3baa9782cac6c410e83eec63e801d6bf5e6f822
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030707"
---
# <a name="hyperspace-an-indexing-subsystem-for-apache-spark"></a>超空间： Apache Spark 的索引子系统

超空间引入了 Apache Spark 用户为其数据集创建索引（如 CSV、JSON 和 Parquet）的功能，并将其用于潜在的查询和工作负荷加速。

在本文中，我们重点介绍了超空间的基本知识，强调其简易性，并展示了它是如何由任何人使用的。

免责声明：超空间有助于在以下两种情况下加速工作负荷或查询：

* 查询包含对具有高选择性的谓词的筛选器。 例如，你可能想要从一百万个候选行中选择100匹配行。
* 查询包含需要大量洗牌的联接。 例如，你可能想要使用 10 GB 的数据集加入 100 GB 数据集。

你可能需要认真监视工作负荷，并确定索引是否正在根据具体情况帮助你。

此文档也可以在笔记本窗体、 [Python](https://github.com/microsoft/hyperspace/blob/master/notebooks/python/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)、 [c #](https://github.com/microsoft/hyperspace/blob/master/notebooks/csharp/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)和 [Scala](https://github.com/microsoft/hyperspace/blob/master/notebooks/scala/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)中使用。

## <a name="setup"></a>设置

首先，启动新的 Spark 会话。 由于本文档只是说明超空间可以提供的内容的教程，因此你将进行配置更改，使我们能够突出显示在小型数据集上执行的超空间。 

默认情况下，当联接的一侧的数据大小 (较小时，Spark 使用广播联接优化联接查询，这是我们在本教程中使用的示例数据) 的情况。 因此，我们禁用了广播联接，以便以后在运行联接查询时，Spark 使用排序合并联接。 这主要用于演示如何在大规模中使用超空间索引以加速联接查询。

运行以下单元格的输出显示对已成功创建的 Spark 会话的引用并输出 "-1" 作为已修改的联接配置的值，这表示已成功禁用广播联接。

:::zone pivot = "programming-language-scala"

```scala
// Start your Spark session
spark

// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

// Verify that BroadcastHashJoin is set correctly
println(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))

```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Start your Spark session.
spark

# Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

# Verify that BroadcastHashJoin is set correctly 
print(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.Conf().Set("spark.sql.autoBroadcastJoinThreshold", -1);

// Verify that BroadcastHashJoin is set correctly.
Console.WriteLine(spark.Conf().Get("spark.sql.autoBroadcastJoinThreshold"));
```

::: zone-end

结果：

```console
res3: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@297e957d
-1
```

## <a name="data-preparation"></a>数据准备

要准备您的环境，您需要创建示例数据记录并将其保存为 Parquet 数据文件。 Parquet 用于说明，但也可以使用其他格式（如 CSV）。 在后续单元中，你将了解如何在此示例数据集上创建多个超空间索引，并使 Spark 在运行查询时使用这些索引。

示例记录对应于两个数据集：部门和员工。 应配置 "empLocation" 和 "deptLocation" 路径，以便在存储帐户上指向所需位置，以保存生成的数据文件。

运行下面的单元格的输出将我们的数据集的内容显示为 triplets 的列表，后跟 dataFrames 创建的引用，用于在首选位置保存每个数据集的内容。

:::zone pivot = "programming-language-scala"

```scala
import org.apache.spark.sql.DataFrame

// Sample department records
val departments = Seq(
      (10, "Accounting", "New York"),
      (20, "Research", "Dallas"),
      (30, "Sales", "Chicago"),
      (40, "Operations", "Boston"))

// Sample employee records
val employees = Seq(
      (7369, "SMITH", 20),
      (7499, "ALLEN", 30),
      (7521, "WARD", 30),
      (7566, "JONES", 20),
      (7698, "BLAKE", 30),
      (7782, "CLARK", 10),
      (7788, "SCOTT", 20),
      (7839, "KING", 10),
      (7844, "TURNER", 30),
      (7876, "ADAMS", 20),
      (7900, "JAMES", 30),
      (7934, "MILLER", 10),
      (7902, "FORD", 20),
      (7654, "MARTIN", 30))

// Save sample data in the Parquet format
import spark.implicits._
val empData: DataFrame = employees.toDF("empId", "empName", "deptId")
val deptData: DataFrame = departments.toDF("deptId", "deptName", "location")

val empLocation: String = "/<yourpath>/employees.parquet"       //TODO ** customize this location path **
val deptLocation: String = "/<yourpath>/departments.parquet"     //TODO ** customize this location path **
empData.write.mode("overwrite").parquet(empLocation)
deptData.write.mode("overwrite").parquet(deptLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql.types import StructField, StructType, StringType, IntegerType

# Sample department records
departments = [(10, "Accounting", "New York"), (20, "Research", "Dallas"), (30, "Sales", "Chicago"), (40, "Operations", "Boston")]

# Sample employee records
employees = [(7369, "SMITH", 20), (7499, "ALLEN", 30), (7521, "WARD", 30), (7566, "JONES", 20), (7698, "BLAKE", 30)]

# Create a schema for the dataframe
dept_schema = StructType([StructField('deptId', IntegerType(), True), StructField('deptName', StringType(), True), StructField('location', StringType(), True)])
emp_schema = StructType([StructField('empId', IntegerType(), True), StructField('empName', StringType(), True), StructField('deptId', IntegerType(), True)])

departments_df = spark.createDataFrame(departments, dept_schema)
employees_df = spark.createDataFrame(employees, emp_schema)

#TODO ** customize this location path **
emp_Location = "/<yourpath>/employees.parquet"
dept_Location = "/<yourpath>/departments.parquet"

employees_df.write.mode("overwrite").parquet(emp_Location)
departments_df.write.mode("overwrite").parquet(dept_Location)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Sql.Types;

// Sample department records
var departments = new List<GenericRow>()
{
    new GenericRow(new object[] {10, "Accounting", "New York"}),
    new GenericRow(new object[] {20, "Research", "Dallas"}),
    new GenericRow(new object[] {30, "Sales", "Chicago"}),
    new GenericRow(new object[] {40, "Operations", "Boston"})
};

// Sample employee records
var employees = new List<GenericRow>() {
      new GenericRow(new object[] {7369, "SMITH", 20}),
      new GenericRow(new object[] {7499, "ALLEN", 30}),
      new GenericRow(new object[] {7521, "WARD", 30}),
      new GenericRow(new object[] {7566, "JONES", 20}),
      new GenericRow(new object[] {7698, "BLAKE", 30}),
      new GenericRow(new object[] {7782, "CLARK", 10}),
      new GenericRow(new object[] {7788, "SCOTT", 20}),
      new GenericRow(new object[] {7839, "KING", 10}),
      new GenericRow(new object[] {7844, "TURNER", 30}),
      new GenericRow(new object[] {7876, "ADAMS", 20}),
      new GenericRow(new object[] {7900, "JAMES", 30}),
      new GenericRow(new object[] {7934, "MILLER", 10}),
      new GenericRow(new object[] {7902, "FORD", 20}),
      new GenericRow(new object[] {7654, "MARTIN", 30})
};

// Save sample data in the Parquet format
var departmentSchema = new StructType(new List<StructField>()
{
    new StructField("deptId", new IntegerType()),
    new StructField("deptName", new StringType()),
    new StructField("location", new StringType())
});
var employeeSchema = new StructType(new List<StructField>()
{
    new StructField("empId", new IntegerType()),
    new StructField("empName", new StringType()),
    new StructField("deptId", new IntegerType())
});

DataFrame empData = spark.CreateDataFrame(employees, employeeSchema); 
DataFrame deptData = spark.CreateDataFrame(departments, departmentSchema); 

string empLocation = "/<yourpath>/employees.parquet";       //TODO ** customize this location path **
string deptLocation = "/<yourpath>/departments.parquet";     //TODO ** customize this location path **
empData.Write().Mode("overwrite").Parquet(empLocation);
deptData.Write().Mode("overwrite").Parquet(deptLocation);

```

::: zone-end

结果：

```console
departments: Seq[(Int, String, String)] = List((10,Accounting,New York), (20,Research,Dallas), (30,Sales,Chicago), (40,Operations,Boston))  
employees: Seq[(Int, String, Int)] = List((7369,SMITH,20), (7499,ALLEN,30), (7521,WARD,30), (7566,JONES,20), (7698,BLAKE,30), (7782,CLARK,10), (7788,SCOTT,20), (7839,KING,10), (7844,TURNER,30), (7876,ADAMS,20), (7900,JAMES,30), (7934,MILLER,10), (7902,FORD,20), (7654,MARTIN,30))  

empData: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
empLocation: String = /your-path/employees.parquet  
deptLocation: String = /your-path/departments.parquet  
```

让我们验证所创建的 Parquet 文件的内容，确保它们包含正确格式的预期记录。 稍后，我们将使用这些数据文件创建超空间索引并运行示例查询。

运行以下单元将生成和输出，以表格形式显示员工和部门 dataFrames 中的行。 应该有14名员工和4个部门，每个部门都与您在上一单元中创建的 triplets 之一匹配。

:::zone pivot = "programming-language-scala"

```scala
// empLocation and deptLocation are the user defined locations above to save parquet files
val empDF: DataFrame = spark.read.parquet(empLocation)
val deptDF: DataFrame = spark.read.parquet(deptLocation)

// Verify the data is available and correct
empDF.show()
deptDF.show()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# emp_Location and dept_Location are the user-defined locations above to save parquet files
emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

# Verify the data is available and correct
emp_DF.show()
dept_DF.show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// empLocation and deptLocation are the user-defined locations above to save parquet files
DataFrame empDF = spark.Read().Parquet(empLocation);
DataFrame deptDF = spark.Read().Parquet(deptLocation);

// Verify the data is available and correct
empDF.Show();
deptDF.Show();

```

::: zone-end

结果：

```console
empDF: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDF: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

```console
|EmpId|EmpName|DeptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
| 7900|  JAMES|    30|
| 7934| MILLER|    10|
| 7839|   KING|    10|
| 7566|  JONES|    20|
| 7698|  BLAKE|    30|
| 7782|  CLARK|    10|
| 7788|  SCOTT|    20|
| 7902|   FORD|    20|
| 7654| MARTIN|    30|  
```

&nbsp; &nbsp;

```console
|DeptId|  DeptName|Location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="indexes"></a>索引

超空间使你可以对从保存的数据文件扫描的记录创建索引。 成功创建后，与索引对应的条目将添加到超空间的元数据中。 Apache Spark 的优化器 (将使用此元数据，在查询处理过程中，我们的扩展) 用于查找和使用适当的索引。

创建索引后，可以执行多个操作：

* **如果基础数据发生更改，则刷新。** 可以刷新现有索引来捕获更改。
* **如果索引不是必需的，则删除。** 您可以执行软删除，也就是说，索引不会被物理删除，而是标记为 "已删除"，这样就不会再用于工作负荷。
* **如果不再需要索引，则为真空度。** 可以真空索引，这会强制从超空间的元数据中实际删除索引内容和关联元数据。

刷新如果基础数据发生更改，则可以刷新现有索引来捕获数据。
删除如果索引不是必需的，则可以执行软删除，即索引不会被物理删除，而是将其标记为 "已删除"，因此不会再用于工作负荷。

以下部分说明了如何在超空间中执行此类索引管理操作。

首先，需要导入所需的库，并创建超空间的实例。 稍后，你将使用此实例调用不同的超空间 Api 来创建示例数据的索引，并修改这些索引。

运行以下单元格的输出显示对超空间的已创建实例的引用。

:::zone pivot = "programming-language-scala"

```scala
// Create an instance of Hyperspace
import com.microsoft.hyperspace._

val hyperspace: Hyperspace = Hyperspace()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
from hyperspace import *

# Create an instance of Hyperspace
hyperspace = Hyperspace(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create an instance of Hyperspace
using Microsoft.Spark.Extensions.Hyperspace;

Hyperspace hyperspace = new Hyperspace(spark);

```

::: zone-end

结果：

```console
hyperspace: com.microsoft.hyperspace.Hyperspace = com.microsoft.hyperspace.Hyperspace@1432f740
```

## <a name="create-indexes"></a>创建索引

若要创建超空间索引，需要提供两条信息：

* 一个 Spark 数据帧，它引用要编制索引的数据。
* 索引配置对象 IndexConfig，它指定索引名称以及索引的索引和包含列。

首先，对示例数据创建三个超空间索引：名为 "deptIndex1" 的部门数据集和 "deptIndex2" 的两个索引，以及一个名为 "empIndex" 的员工数据集的索引。 对于每个索引，需要一个相应的 IndexConfig 来捕获名称，以及索引列和包含列的列列表。 运行以下单元将创建这些 IndexConfigs，其输出会列出它们。

> [!Note]
> 索引列是显示在筛选器或联接条件中的列。 "包含列" 是在选择/项目中显示的列。

例如，在下面的查询中：

```sql
SELECT X
FROM T
WHERE Y = 2
```

Y 可以是索引列，X 可以是包含列。

:::zone pivot = "programming-language-scala"

```scala
// Create index configurations
import com.microsoft.hyperspace.index.IndexConfig

val empIndexConfig: IndexConfig = IndexConfig("empIndex", Seq("deptId"), Seq("empName"))
val deptIndexConfig1: IndexConfig = IndexConfig("deptIndex1", Seq("deptId"), Seq("deptName"))
val deptIndexConfig2: IndexConfig = IndexConfig("deptIndex2", Seq("location"), Seq("deptName"))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create index configurations

emp_IndexConfig = IndexConfig("empIndex1", ["deptId"], ["empName"])
dept_IndexConfig1 = IndexConfig("deptIndex1", ["deptId"], ["deptName"])
dept_IndexConfig2 = IndexConfig("deptIndex2", ["location"], ["deptName"])

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Extensions.Hyperspace.Index;

var empIndexConfig = new IndexConfig("empIndex", new string[] {"deptId"}, new string[] {"empName"});
var deptIndexConfig1 = new IndexConfig("deptIndex1", new string[] {"deptId"}, new string[] {"deptName"});
var deptIndexConfig2 = new IndexConfig("deptIndex2", new string[] {"location"}, new string[] {"deptName"});

```

::: zone-end

结果：

```console
empIndexConfig: com.microsoft.hyperspace.index.IndexConfig = [indexName: empIndex; indexedColumns: deptid; includedColumns: empname]  
deptIndexConfig1: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex1; indexedColumns: deptid; includedColumns: deptname]  
deptIndexConfig2: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex2; indexedColumns: location; includedColumns: deptname]  
```
现在，使用索引配置创建三个索引。 为此，请在超空间实例上调用 "createIndex" 命令。 此命令需要索引配置和包含要编制索引的行的数据帧。 运行以下单元将创建三个索引。

:::zone pivot = "programming-language-scala"

```scala
// Create indexes from configurations
import com.microsoft.hyperspace.index.Index

hyperspace.createIndex(empDF, empIndexConfig)
hyperspace.createIndex(deptDF, deptIndexConfig1)
hyperspace.createIndex(deptDF, deptIndexConfig2)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create indexes from configurations

hyperspace.createIndex(emp_DF, emp_IndexConfig)
hyperspace.createIndex(dept_DF, dept_IndexConfig1)
hyperspace.createIndex(dept_DF, dept_IndexConfig2)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create indexes from configurations
hyperspace.CreateIndex(empDF, empIndexConfig);
hyperspace.CreateIndex(deptDF, deptIndexConfig1);
hyperspace.CreateIndex(deptDF, deptIndexConfig2);

```

::: zone-end

## <a name="list-indexes"></a>列出索引

下面的代码演示了如何列出超空间实例中所有可用的索引。 它使用 "索引" API，该 API 将现有索引的相关信息作为 Spark 数据帧返回，以便您可以执行其他操作。 

例如，可以调用此数据帧上的有效操作来检查其内容或进一步分析 (例如，筛选特定索引，或者根据所需的属性) 对它们进行分组。

以下单元格使用数据帧的 "show" 操作完全打印行，并以表格形式显示索引的详细信息。 对于每个索引，可以在元数据中看到超空间中存储的所有信息。 你将立即注意到以下内容：

* indexName、indexedColumns、includedColumns 和 status。 status 是用户通常所引用的字段。
* dfSignature 是由超空间自动生成的，并且对于每个索引都是唯一的。 超空间在内部使用此签名来维护索引，并在查询时对其进行利用。


在下面的输出中，所有三个索引都应将 "活动" 设置为 "状态"，其名称、索引列和包含列应与上面的索引配置中定义的内容相匹配。

:::zone pivot = "programming-language-scala"

```scala
hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.Indexes().Show();

```

::: zone-end

结果：

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="delete-indexes"></a>删除索引

您可以使用 "deleteIndex" API 并提供索引名称来删除现有索引。 删除索引需要软删除：它主要将超空间元数据中的索引状态从 "活动" 更新为 "已删除"。 这将从任何未来的查询优化中排除删除的索引，超空间不再为任何查询选择该索引。 

但是，已删除索引的索引文件仍然仍可用 (因为它是软删除的) ，因此，如果用户请求，则可以还原索引。

以下单元格删除名为 "deptIndex2" 的索引，并在之后列出超空间元数据。 输出应类似于 "列表索引" 的 "列表索引" 单元格，但 "deptIndex2" 除外，此时应将其状态更改为 "已删除"。

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
hyperspace.DeleteIndex("deptIndex2");

hyperspace.Indexes().Show();

```

::: zone-end

结果：

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="restore-indexes"></a>还原索引

可以使用 "restoreIndex" API 还原已删除的索引。 这会将最新版本的索引恢复为活动状态，并使其再次可用于查询。 以下单元格显示了 "restoreIndex" 用法的示例。 删除 "deptIndex1" 并将其还原。 输出显示在调用 "deleteIndex" 命令后，"deptIndex1" 首先进入 "已删除" 状态，并在调用 "restoreIndex" 后恢复为 "活动" 状态。

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex1")

hyperspace.indexes.show

hyperspace.restoreIndex("deptIndex1")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex1")
hyperspace.indexes().show()
hyperspace.restoreIndex("deptIndex1")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.DeleteIndex("deptIndex1");
hyperspace.Indexes().Show();
hyperspace.RestoreIndex("deptIndex1");
hyperspace.Indexes().Show();

```

::: zone-end

结果：

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.indexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

&nbsp; &nbsp;

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="vacuum-indexes"></a>真空索引

可以使用 **vacuumIndex** 命令执行硬删除（即，删除索引的完全删除文件和元数据条目）。 此操作不可逆。 它会以物理方式删除所有索引文件，这就是硬删除的原因。

以下单元 vacuums "deptIndex2" 索引，并在清除后显示超空间元数据。 你应看到两个索引 "deptIndex1" 和 "empIndex" 的元数据条目，它们均为 "活动" 状态，没有 "deptIndex2" 的条目。

:::zone pivot = "programming-language-scala"

```scala
hyperspace.vacuumIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.vacuumIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.VacuumIndex("deptIndex2");
hyperspace.Indexes().Show();

```

::: zone-end

结果：

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="enable-or-disable-hyperspace"></a>启用或禁用超空间

超空间提供使用 Spark 启用或禁用索引使用的 Api。

* 通过使用 **enableHyperspace** 命令，超空间优化规则将对 Spark 优化器可见，并利用现有超空间索引来优化用户查询。
* 通过使用 **disableHyperspace** 命令，在查询优化期间，超空间规则将不再适用。 禁用超空间不会影响已创建的索引，因为这些索引仍保持不变。

以下单元格演示了如何使用这些命令来启用或禁用超空间。 输出显示对已更新其配置的现有 Spark 会话的引用。

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

// Disable Hyperspace
spark.disableHyperspace
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

# Disable Hyperspace
Hyperspace.disable(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

// Disable Hyperspace
spark.DisableHyperspace();

```

::: zone-end

结果：

```console
res48: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb  
res51: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb
```

## <a name="index-usage"></a>索引使用情况

若要使 Spark 在查询处理过程中使用超空间索引，需要确保启用超空间。

以下单元格启用超空间，并创建两个 DataFrames，其中包含用于运行示例查询的示例数据记录。 对于每个数据帧，会打印几个示例行。

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

val empDFrame: DataFrame = spark.read.parquet(empLocation)
val deptDFrame: DataFrame = spark.read.parquet(deptLocation)

empDFrame.show(5)
deptDFrame.show(5)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

emp_DF.show(5)
dept_DF.show(5)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

DataFrame empDFrame = spark.Read().Parquet(empLocation);
DataFrame deptDFrame = spark.Read().Parquet(deptLocation);

empDFrame.Show(5);
deptDFrame.Show(5);

```

::: zone-end

结果：

```console
res53: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.Spark™Session@39fe1ddb  
empDFrame: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDFrame: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
```

&nbsp; &nbsp;

```console
|empId|empName|deptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
```

&nbsp;&nbsp;这仅显示前5行 &nbsp;&nbsp;

```console
|deptId|  deptName|location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="index-types"></a>索引类型

目前，超空间包含用于对两组查询利用索引的规则：

* 带有查找或范围选择筛选谓词的选择查询。
* 联接带有相等联接谓词的查询， (即同等联接) 。

## <a name="indexes-for-accelerating-filters"></a>用于加速筛选器的索引

第一个示例查询查找部门记录，如下面的单元中所示。 在 SQL 中，此查询类似于以下示例：

```sql
SELECT deptName
FROM departments
WHERE deptId = 20
```

运行以下单元格的输出显示：

* 查询结果，它是一个部门名称。
* Spark 用于运行查询的查询计划。

在查询计划中，计划底部的 **FileScan** 运算符显示从中读取记录的数据源。 此文件的位置指示最新版本的 "deptIndex1" 索引的路径。 此信息显示根据查询和使用超空间优化规则，Spark 决定在运行时利用正确的索引。

:::zone pivot = "programming-language-scala"

```scala
// Filter with equality predicate

val eqFilter: DataFrame = deptDFrame.filter("deptId = 20").select("deptName")
eqFilter.show()

eqFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with equality predicate

eqFilter = dept_DF.filter("""deptId = 20""").select("""deptName""")
eqFilter.show()

eqFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame eqFilter = deptDFrame.Filter("deptId = 20").Select("deptName");
eqFilter.Show();

eqFilter.Explain(true);

```

::: zone-end

结果：

```console
eqFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|DeptName|
|--------|
|Research|
```

&nbsp; &nbsp;

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), EqualTo(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

第二个示例是针对部门记录的范围选择查询。 在 SQL 中，此查询类似于以下示例：

```sql
SELECT deptName
FROM departments
WHERE deptId > 20
```

与第一个示例类似，以下单元的输出显示 (两个部门的名称) 和查询计划的查询结果。 **FileScan** 运算符中的数据文件的位置显示 "deptIndex1" 用于运行该查询。

:::zone pivot = "programming-language-scala"

```scala
// Filter with range selection predicate

val rangeFilter: DataFrame = deptDFrame.filter("deptId > 20").select("deptName")
rangeFilter.show()

rangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with range selection predicate

rangeFilter = dept_DF.filter("""deptId > 20""").select("deptName")
rangeFilter.show()

rangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Filter with range selection predicate
DataFrame rangeFilter = deptDFrame.Filter("deptId > 20").Select("deptName");
rangeFilter.Show();

rangeFilter.Explain(true);

```

::: zone-end

结果：

```console
rangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|  DeptName|
|----------|
|Operations|
|     Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```
第三个示例是部门 ID 上的查询联接部门和员工记录。 等效的 SQL 语句如下所示：

```sql
SELECT employees.deptId, empName, departments.deptId, deptName
FROM   employees, departments
WHERE  employees.deptId = departments.deptId
```
运行下面的单元格的输出显示查询结果，这些结果是14名员工的姓名以及每位员工工作的部门的名称。 查询计划也包含在输出中。 请注意，两个 **FileScan** 运算符的文件位置如何显示 Spark 使用 "empIndex" 和 "deptIndex1" 索引来运行查询。

:::zone pivot = "programming-language-scala"

```scala
// Join

val eqJoin: DataFrame =
      empDFrame.
      join(deptDFrame, empDFrame("deptId") === deptDFrame("deptId")).
      select(empDFrame("empName"), deptDFrame("deptName"))

eqJoin.show()

eqJoin.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Join

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

eqJoin.show()

eqJoin.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Join
DataFrame eqJoin =
      empDFrame
      .Join(deptDFrame, empDFrame.Col("deptId") == deptDFrame.Col("deptId"))
      .Select(empDFrame.Col("empName"), deptDFrame.Col("deptName"));

eqJoin.Show();

eqJoin.Explain(true);

```

::: zone-end

结果：

```console
eqJoin: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534] parquet

== Physical Plan ==
*(3) Project [empName#528, deptName#534]
+- *(3) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>, SelectedBucketsCount: 200 out of 200
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>, SelectedBucketsCount: 200 out of 200
```

## <a name="support-for-sql-semantics"></a>支持 SQL 语义

索引用法对于使用数据帧 API 或 Spark SQL 是透明的。 下面的示例演示了与在 SQL 窗体中使用索引（如果适用）的相同联接示例。

:::zone pivot = "programming-language-scala"

```scala
empDFrame.createOrReplaceTempView("EMP")
deptDFrame.createOrReplaceTempView("DEPT")

val joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql import SparkSession

emp_DF.createOrReplaceTempView("EMP")
dept_DF.createOrReplaceTempView("DEPT")

joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

empDFrame.CreateOrReplaceTempView("EMP");
deptDFrame.CreateOrReplaceTempView("DEPT");

var joinQuery = spark.Sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId");

joinQuery.Show();
joinQuery.Explain(true);

```

::: zone-end

结果：

```console

joinQuery: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
'Project ['EMP.empName, 'DEPT.deptName]
+- 'Filter ('EMP.deptId = 'DEPT.deptId)
   +- 'Join Inner
      :- 'UnresolvedRelation `EMP`
      +- 'UnresolvedRelation `DEPT`

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Filter (deptId#529 = deptId#533)
   +- Join Inner
      :- SubqueryAlias `emp`
      :  +- Relation[empId#527,empName#528,deptId#529] parquet
      +- SubqueryAlias `dept`
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empId#527,empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(5) Project [empName#528, deptName#534]
+- *(5) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="explain-api"></a>说明 API

索引很好，但您如何知道它们是否正在使用？ 超空间允许用户在运行其查询前比较其原始计划与已更新索引相关计划。 您可以选择使用 HTML、纯文本或控制台模式来显示命令输出。

以下单元格显示了 HTML 的示例。 突出显示的部分表示原始计划和更新计划以及所使用的索引之间的差异。

:::zone pivot = "programming-language-scala"

```scala
spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin) { displayHTML }
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin, True, displayHTML)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

spark.Conf().Set("spark.hyperspace.explain.displayMode", "html");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.beginTag", "<b style=\"background:LightGreen\">");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.endTag", "</b>");

hyperspace.Explain(eqJoin, false, input => DisplayHTML(input));

```

::: zone-end

结果：

### <a name="plan-with-indexes"></a>规划索引

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="plan-without-indexes"></a>无索引计划

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [empName#528,deptId#529] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/employees.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="indexes-used"></a>使用的索引

```console
deptIndex1:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/deptIndex1/v__=0
empIndex:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/empIndex/v__=0
```

## <a name="refresh-indexes"></a>刷新索引

如果为其创建索引的原始数据发生更改，则该索引将不再捕获数据的最新状态。 可以使用 **refreshIndex** 命令刷新过时索引。 此命令将使索引完全重新生成，并根据最新的数据记录对其进行更新。 我们将向你展示如何以增量方式刷新其他笔记本中的索引。

以下两个单元显示了此方案的示例：

* 第一个单元格向原始部门数据添加另外两个部门。 它会读取和打印部门列表，以验证是否正确添加了新部门。 输出显示了六个部门，共四个：四个，两个是新的。 调用 **refreshIndex** 更新 "deptIndex1"，以便索引捕获新部门。
* 第二个单元格运行范围选择查询示例。 结果现在应包含四个部门：两个是在运行上述查询前看到的两个部门，另外两个是我们添加的新部门。

### <a name="specific-index-refresh"></a>特定索引刷新

:::zone pivot = "programming-language-scala"

```scala
val extraDepartments = Seq(
      (50, "Inovation", "Seattle"),
      (60, "Human Resources", "San Francisco"))

val extraDeptData: DataFrame = extraDepartments.toDF("deptId", "deptName", "location")
extraDeptData.write.mode("Append").parquet(deptLocation)

val deptDFrameUpdated: DataFrame = spark.read.parquet(deptLocation)

deptDFrameUpdated.show(10)

hyperspace.refreshIndex("deptIndex1")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

extra_Departments = [(50, "Inovation", "Seattle"), (60, "Human Resources", "San Francisco")]

extra_departments_df = spark.createDataFrame(extra_Departments, dept_schema)
extra_departments_df.write.mode("Append").parquet(dept_Location)


dept_DFrame_Updated = spark.read.parquet(dept_Location)

dept_DFrame_Updated.show(10)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

var extraDepartments = new List<GenericRow>()
{
    new GenericRow(new object[] {50, "Inovation", "Seattle"}),
    new GenericRow(new object[] {60, "Human Resources", "San Francisco"})
};
    
DataFrame extraDeptData = spark.CreateDataFrame(extraDepartments, departmentSchema);
extraDeptData.Write().Mode("Append").Parquet(deptLocation);

DataFrame deptDFrameUpdated = spark.Read().Parquet(deptLocation);

deptDFrameUpdated.Show(10);

hyperspace.RefreshIndex("deptIndex1");

```

::: zone-end

结果：

```console
extraDepartments: Seq[(Int, String, String)] = List((50,Inovation,Seattle), (60,Human Resources,San Francisco))  
extraDeptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
deptDFrameUpdated: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

&nbsp; &nbsp;

```console  
|deptId|       deptName|     location|
|------|---------------|-------------|
|    60|Human Resources|San Francisco|
|    10|     Accounting|     New York|
|    50|      Inovation|      Seattle|
|    40|     Operations|       Boston|
|    20|       Research|       Dallas|
|    30|          Sales|      Chicago|
```

### <a name="range-selection"></a>范围选择

:::zone pivot = "programming-language-scala"

```scala
val newRangeFilter: DataFrame = deptDFrameUpdated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

newRangeFilter = dept_DFrame_Updated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame newRangeFilter = deptDFrameUpdated.Filter("deptId > 20").Select("deptName");
newRangeFilter.Show();

newRangeFilter.Explain(true);

```

::: zone-end

结果：

```console
newRangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|       DeptName|
|---------------|
|Human Resources|
|      Inovation|
|     Operations|
|          Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#675]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Optimized Logical Plan ==
Project [deptName#675]
+- Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Physical Plan ==
*(1) Project [deptName#675]
+- *(1) Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- *(1) FileScan parquet [deptId#674,deptName#675] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="hybrid-scan-for-mutable-datasets"></a>可变数据集的混合扫描

通常，如果基础数据源数据中追加了一些新文件或删除了现有文件，则索引将会失效，而超空间决定不使用它。 不过，有时你只是想要使用索引，而不必每次都对其进行刷新。 执行此操作可能有多个原因：

- 您不希望持续刷新索引，而需要定期执行此操作，因为您了解工作负荷的最佳选择。
- 你添加/删除了几个文件，但不希望等待另一个刷新作业完成。

为了使你仍可以使用陈旧索引，超空间引入了混合扫描，这是一种 novel 的方法，它允许用户利用过时或过时的索引 (例如，基础数据源数据中追加了一些新文件，或者删除了现有文件) 不刷新索引。

若要实现此目的，当你将适当的配置设置为启用混合扫描时，超空间会修改查询计划，以利用如下所示的更改：
* 附加的文件可以通过使用 Union 或 BucketUnion (for join) 来合并索引数据。 如果需要，还可以在合并之前应用混排追加数据。
* 可以通过在索引数据的沿袭列上插入 Filter NOT IN 条件来处理已删除的文件，以便可以在查询时排除已删除文件中的索引行。

您可以在以下示例中检查查询计划的转换。

> [!NOTE]
> 目前，只有非分区数据支持混合扫描。

### <a name="hybrid-scan-for-appended-files---non-partitioned-data"></a>用于附加文件的混合扫描-非分区数据

在下面的示例中，将使用非分区数据。 在此示例中，我们预计联接索引可用于查询，并为追加的文件引入 BucketUnion。

:::zone pivot = "programming-language-scala"

```scala
val testData = Seq(
    ("orange", 3, "2020-10-01"),
    ("banana", 1, "2020-10-01"),
    ("carrot", 5, "2020-10-02"),
    ("beetroot", 12, "2020-10-02"),
    ("orange", 2, "2020-10-03"),
    ("banana", 11, "2020-10-03"),
    ("carrot", 3, "2020-10-03"),
    ("beetroot", 2, "2020-10-04"),
    ("cucumber", 7, "2020-10-05"),
    ("pepper", 20, "2020-10-06")
    ).toDF("name", "qty", "date")

val testDataLocation = s"$dataPath/productTable"

testData.write.mode("overwrite").parquet(testDataLocation)
val testDF = spark.read.parquet(testDataLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
testdata = [
    ("orange", 3, "2020-10-01"),
    ("banana", 1, "2020-10-01"),
    ("carrot", 5, "2020-10-02"),
    ("beetroot", 12, "2020-10-02"),
    ("orange", 2, "2020-10-03"),
    ("banana", 11, "2020-10-03"),
    ("carrot", 3, "2020-10-03"),
    ("beetroot", 2, "2020-10-04"),
    ("cucumber", 7, "2020-10-05"),
    ("pepper", 20, "2020-10-06")
]

testdata_location = data_path + "/productTable"
from pyspark.sql.types import StructField, StructType, StringType, IntegerType
testdata_schema = StructType([
    StructField('name', StringType(), True),
    StructField('qty', IntegerType(), True),
    StructField('date', StringType(), True)])

test_df = spark.createDataFrame(testdata, testdata_schema)
test_df.write.mode("overwrite").parquet(testdata_location)
test_df = spark.read.parquet(testdata_location)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Sql.Types;

var products = new List<GenericRow>() {
    new GenericRow(new object[] {"orange", 3, "2020-10-01"}),
    new GenericRow(new object[] {"banana", 1, "2020-10-01"}),
    new GenericRow(new object[] {"carrot", 5, "2020-10-02"}),
    new GenericRow(new object[] {"beetroot", 12, "2020-10-02"}),
    new GenericRow(new object[] {"orange", 2, "2020-10-03"}),
    new GenericRow(new object[] {"banana", 11, "2020-10-03"}),
    new GenericRow(new object[] {"carrot", 3, "2020-10-03"}),
    new GenericRow(new object[] {"beetroot", 2, "2020-10-04"}),
    new GenericRow(new object[] {"cucumber", 7, "2020-10-05"}),
    new GenericRow(new object[] {"pepper", 20, "2020-10-06"})
};
var productsSchema = new StructType(new List<StructField>()
{
    new StructField("name", new StringType()),
    new StructField("qty", new IntegerType()),
    new StructField("date", new StringType())
});

DataFrame testData = spark.CreateDataFrame(products, productsSchema); 
string testDataLocation = $"{dataPath}/productTable";
testData.Write().Mode("overwrite").Parquet(testDataLocation);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
// CREATE INDEX
hyperspace.createIndex(testDF, IndexConfig("productIndex2", Seq("name"), Seq("date", "qty")))

spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)
val filter1 = testDF.filter("name = 'banana'")
val filter2 = testDF.filter("qty > 10")
val query = filter1.join(filter2, "name")

// Check Join index rule is applied properly.
hyperspace.explain(query)(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# CREATE INDEX
hyperspace.createIndex(test_df, IndexConfig("productIndex2", ["name"], ["date", "qty"]))

spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)
filter1 = test_df.filter("name = 'banana'")
filter2 = test_df.filter("qty > 10")
query = filter1.join(filter2, "name")

# Check Join index rule is applied properly.
hyperspace.explain(query, True, displayHTML)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// CREATE INDEX
DataFrame testDF = spark.Read().Parquet(testDataLocation);
var productIndex2Config = new IndexConfig("productIndex", new string[] {"name"}, new string[] {"date", "qty"});
hyperspace.CreateIndex(testDF, productIndex2Config);

// Check Join index rule is applied properly.
DataFrame filter1 = testDF.Filter("name = 'banana'");
DataFrame filter2 = testDF.Filter("qty > 10");
DataFrame query = filter1.Join(filter2, filter1.Col("name") == filter2.Col("name"));

query.Show();

hyperspace.Explain(query, true, input => DisplayHTML(input));
```

::: zone-end

结果为：

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#607, qty#608, date#609, qty#632, date#633]
+- SortMergeJoin [name#607], [name#631], Inner
   :- *(1) Project [name#607, qty#608, date#609]
   :  +- *(1) Filter (isnotnull(name#607) && (name#607 = banana))
   :     +- *(1) FileScan parquet [name#607,date#609,qty#608] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Project [name#631, qty#632, date#633]
      +- *(2) Filter (((isnotnull(qty#632) && (qty#632 > 10)) && isnotnull(name#631)) && (name#631 = banana))
         +- *(2) FileScan parquet [name#631,date#633,qty#632] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#607, qty#608, date#609, qty#632, date#633]
+- SortMergeJoin [name#607], [name#631], Inner
   :- *(2) Sort [name#607 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#607, 200), [id=#453]
   :     +- *(1) Project [name#607, qty#608, date#609]
   :        +- *(1) Filter (isnotnull(name#607) && (name#607 = banana))
   :           +- *(1) FileScan parquet [name#607,qty#608,date#609] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#631 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#631, 200), [id=#459]
         +- *(3) Project [name#631, qty#632, date#633]
            +- *(3) Filter (((isnotnull(qty#632) && (qty#632 > 10)) && isnotnull(name#631)) && (name#631 = banana))
               +- *(3) FileScan parquet [name#631,qty#632,date#633] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=0

```

:::zone pivot = "programming-language-scala"

```scala
// Append new files.
val appendData = Seq(
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")).toDF("name", "qty", "date")
appendData.write.mode("append").parquet(testDataLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Append new files.
append_data = [
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")
]
append_df = spark.createDataFrame(append_data, testdata_schema)
append_df.write.mode("append").parquet(testdata_location)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Append new files.
var appendProducts = new List<GenericRow>()
{
    new GenericRow(new object[] {"orange", 13, "2020-11-01"}),
    new GenericRow(new object[] {"banana", 5, "2020-11-01"})
};
    
DataFrame appendData = spark.CreateDataFrame(appendProducts, productsSchema);
appendData.Write().Mode("Append").Parquet(testDataLocation);

```

::: zone-end

默认情况下，混合扫描处于禁用状态。 因此，您会发现，由于我们追加了新数据，超空间将决定 *不* 使用索引。

在输出中，你将看不到任何计划差异 (因此，不会突出显示) 。

:::zone pivot = "programming-language-scala"

```scala
// Hybrid Scan configs are false by default.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "false")
spark.conf.set("spark.hyperspace.index.hybridscan.delete.enabled", "false")

val testDFWithAppend = spark.read.parquet(testDataLocation)
val filter1 = testDFWithAppend.filter("name = 'banana'")
val filter2 = testDFWithAppend.filter("qty > 10")
val query = filter1.join(filter2, "name")
hyperspace.explain(query)(displayHTML(_))
query.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Hybrid Scan configs are false by default.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "false")
spark.conf.set("spark.hyperspace.index.hybridscan.delete.enabled", "false")

test_df_with_append = spark.read.parquet(testdata_location)
filter1 = test_df_with_append.filter("name = 'banana'")
filter2 = test_df_with_append.filter("qty > 10")
query = filter1.join(filter2, "name")
hyperspace.explain(query, True, displayHTML)
query.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Hybrid Scan configs are false by default.
spark.Conf().Set("spark.hyperspace.index.hybridscan.enabled", "false");
spark.Conf().Set("spark.hyperspace.index.hybridscan.delete.enabled", "false");

DataFrame testDFWithAppend = spark.Read().Parquet(testDataLocation);
DataFrame filter1 = testDFWithAppend.Filter("name = 'banana'");
DataFrame filter2 = testDFWithAppend.Filter("qty > 10");
DataFrame query = filter1.Join(filter2, filter1.Col("name") == filter2.Col("name"));

query.Show();

hyperspace.Explain(query, true, input => DisplayHTML(input));
```

::: zone-end

结果为：

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#685, date#686]
+- SortMergeJoin [name#678], [name#684], Inner
   :- *(2) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#678, 200), [id=#589]
   :     +- *(1) Project [name#678, qty#679, date#680]
   :        +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :           +- *(1) FileScan parquet [name#678,qty#679,date#680] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#684 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#684, 200), [id=#595]
         +- *(3) Project [name#684, qty#685, date#686]
            +- *(3) Filter (((isnotnull(qty#685) && (qty#685 > 10)) && (name#684 = banana)) && isnotnull(name#684))
               +- *(3) FileScan parquet [name#684,qty#685,date#686] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct

=============================================================
Plan without indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#685, date#686]
+- SortMergeJoin [name#678], [name#684], Inner
   :- *(2) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#678, 200), [id=#536]
   :     +- *(1) Project [name#678, qty#679, date#680]
   :        +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :           +- *(1) FileScan parquet [name#678,qty#679,date#680] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#684 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#684, 200), [id=#542]
         +- *(3) Project [name#684, qty#685, date#686]
            +- *(3) Filter (((isnotnull(qty#685) && (qty#685 > 10)) && (name#684 = banana)) && isnotnull(name#684))
               +- *(3) FileScan parquet [name#684,qty#685,date#686] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct

+------+---+----------+---+----------+
|  name|qty|      date|qty|      date|
+------+---+----------+---+----------+
|banana| 11|2020-10-03| 11|2020-10-03|
|banana|  5|2020-11-01| 11|2020-10-03|
|banana|  1|2020-10-01| 11|2020-10-03|
+------+---+----------+---+----------
```

### <a name="enable-hybrid-scan"></a>启用混合扫描

在规划索引的计划中，你可以看到仅为追加的文件所需的 Exchange 哈希分区，以便我们仍可以利用追加文件的 "无序" 索引数据。 BucketUnion 用于将 "无序" 追加的文件与索引数据合并在一起。

:::zone pivot = "programming-language-scala"

```scala
// Enable Hybrid Scan config. "delete" config is not necessary since we only appended data.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "true")
spark.enableHyperspace
// Need to redefine query to recalculate the query plan.
val query = filter1.join(filter2, "name")
hyperspace.explain(query)(displayHTML(_))
query.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Enable Hybrid Scan config. "delete" config is not necessary.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "true")

# Need to redefine query to recalculate the query plan.
query = filter1.join(filter2, "name")
hyperspace.explain(query, True, displayHTML)
query.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Enable Hybrid Scan config. "delete" config is not necessary.
spark.Conf().Set("spark.hyperspace.index.hybridscan.enabled", "true");
spark.EnableHyperspace();
// Need to redefine query to recalculate the query plan.
DataFrame query = filter1.Join(filter2, filter1.Col("name") == filter2.Col("name"));

query.Show();

hyperspace.Explain(query, true, input => DisplayHTML(input));
```

::: zone-end

结果为：

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#732, date#733]
+- SortMergeJoin [name#678], [name#731], Inner
   :- *(3) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- BucketUnion 200 buckets, bucket columns: [name]
   :     :- *(1) Project [name#678, qty#679, date#680]
   :     :  +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :     :     +- *(1) FileScan parquet [name#678,date#680,qty#679] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   :     +- Exchange hashpartitioning(name#678, 200), [id=#775]
   :        +- *(2) Project [name#678, qty#679, date#680]
   :           +- *(2) Filter (isnotnull(name#678) && (name#678 = banana))
   :              +- *(2) FileScan parquet [name#678,date#680,qty#679] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(6) Sort [name#731 ASC NULLS FIRST], false, 0
      +- BucketUnion 200 buckets, bucket columns: [name]
         :- *(4) Project [name#731, qty#732, date#733]
         :  +- *(4) Filter (((isnotnull(qty#732) && (qty#732 > 10)) && isnotnull(name#731)) && (name#731 = banana))
         :     +- *(4) FileScan parquet [name#731,date#733,qty#732] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
         +- Exchange hashpartitioning(name#731, 200), [id=#783]
            +- *(5) Project [name#731, qty#732, date#733]
               +- *(5) Filter (((isnotnull(qty#732) && (qty#732 > 10)) && isnotnull(name#731)) && (name#731 = banana))
                  +- *(5) FileScan parquet [name#731,date#733,qty#732] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Plan without indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#732, date#733]
+- SortMergeJoin [name#678], [name#731], Inner
   :- *(2) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#678, 200), [id=#701]
   :     +- *(1) Project [name#678, qty#679, date#680]
   :        +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :           +- *(1) FileScan parquet [name#678,qty#679,date#680] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#731 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#731, 200), [id=#707]
         +- *(3) Project [name#731, qty#732, date#733]
            +- *(3) Filter (((isnotnull(qty#732) && (qty#732 > 10)) && isnotnull(name#731)) && (name#731 = banana))
               +- *(3) FileScan parquet [name#731,qty#732,date#733] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=0


+------+---+----------+---+----------+
|  name|qty|      date|qty|      date|
+------+---+----------+---+----------+
|banana|  1|2020-10-01| 11|2020-10-03|
|banana| 11|2020-10-03| 11|2020-10-03|
|banana|  5|2020-11-01| 11|2020-10-03|
+------+---+----------+---+----------+
```

## <a name="incremental-index-refresh"></a>增量索引刷新

如果已准备好更新索引，但不希望重新生成整个索引，超空间支持使用 API 以增量方式更新索引 `hs.refreshIndex("name", "incremental")` 。 这样，便无需从头开始完全重新生成索引，而是利用以前创建的索引文件，以及仅更新新添加的数据的索引。

当然，应定期使用以下) 的互补 `optimizeIndex` API (，以确保不会看到性能回归。 `refreshIndex(..., "incremental")`假设你添加/删除的数据是原始数据集的 10% <，则建议每10次调用至少一次。 例如，如果原始数据集为 100 GB，并且以 1 GB 的增量/减量增量添加/删除数据，则可以在 `refreshIndex` 调用之前调用10次 `optimizeIndex` 。 请注意，此示例仅用于说明，你必须将其调整为工作负荷。

在下面的示例中，请注意在使用索引时在查询计划中添加了排序节点。 这是因为在追加的数据文件上创建了部分索引，从而导致 Spark 引入 `Sort` 。 另请注意，这种 `Shuffle` 情况下，Exchange 仍会从该计划中消除，为你提供相应的加速。

:::zone pivot = "programming-language-scala"

```scala
def query(): DataFrame = {
    val testDFWithAppend = spark.read.parquet(testDataLocation)
    val filter1 = testDFWithAppend.filter("name = 'banana'")
    val filter2 = testDFWithAppend.filter("qty > 10")
    filter1.join(filter2, "name")
}

hyperspace.refreshIndex("productIndex2", "incremental")

hyperspace.explain(query())(displayHTML(_))
query().show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
def query():
    test_df_with_append = spark.read.parquet(testdata_location)
    filter1 = test_df_with_append.filter("name = 'banana'")
    filter2 = test_df_with_append.filter("qty > 10")
    return filter1.join(filter2, "name")

hyperspace.refreshIndex("productIndex2", "incremental")

hyperspace.explain(query(), True, displayHTML)
query().show()
```

::: zone-end

结果为：

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#820, qty#821, date#822, qty#827, date#828]
+- SortMergeJoin [name#820], [name#826], Inner
   :- *(1) Sort [name#820 ASC NULLS FIRST], false, 0
   :  +- *(1) Project [name#820, qty#821, date#822]
   :     +- *(1) Filter (isnotnull(name#820) && (name#820 = banana))
   :        +- *(1) FileScan parquet [name#820,date#822,qty#821] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Sort [name#826 ASC NULLS FIRST], false, 0
      +- *(2) Project [name#826, qty#827, date#828]
         +- *(2) Filter (((isnotnull(qty#827) && (qty#827 > 10)) && (name#826 = banana)) && isnotnull(name#826))
            +- *(2) FileScan parquet [name#826,date#828,qty#827] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#820, qty#821, date#822, qty#827, date#828]
+- SortMergeJoin [name#820], [name#826], Inner
   :- *(2) Sort [name#820 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#820, 200), [id=#927]
   :     +- *(1) Project [name#820, qty#821, date#822]
   :        +- *(1) Filter (isnotnull(name#820) && (name#820 = banana))
   :           +- *(1) FileScan parquet [name#820,qty#821,date#822] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#826 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#826, 200), [id=#933]
         +- *(3) Project [name#826, qty#827, date#828]
            +- *(3) Filter (((isnotnull(qty#827) && (qty#827 > 10)) && (name#826 = banana)) && isnotnull(name#826))
               +- *(3) FileScan parquet [name#826,qty#827,date#828] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct

+------+---+----------+---+----------+
|  name|qty|      date|qty|      date|
+------+---+----------+---+----------+
|banana|  1|2020-10-01| 11|2020-10-03|
|banana| 11|2020-10-03| 11|2020-10-03|
|banana|  5|2020-11-01| 11|2020-10-03|
+------+---+----------+---+----------+
```

## <a name="optimize-index-layout"></a>优化索引布局

在对新追加的数据多次调用增量刷新后 (例如，如果用户以较小的批次写入数据或) 流式处理情况下写入数据，则索引文件数往往会变得很大影响索引的性能 (大量的小文件问题) 。 超空间提供 `hyperspace.optimizeIndex("indexName")` API 来优化索引布局，并减少大文件问题。

请注意，在下面的计划中，超空间已删除查询计划中的 "其他排序" 节点。 优化有助于避免对任何只包含一个文件的索引存储桶进行排序。 但是，如果所有索引 bucket 每个存储桶每个存储桶最多包含1个文件，则此值将仅为 true `optimizeIndex` 。

:::zone pivot = "programming-language-scala"

```scala
// Append some more data and call refresh again.
val appendData = Seq(
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")).toDF("name", "qty", "date")
appendData.write.mode("append").parquet(testDataLocation)

hyperspace.refreshIndex("productIndex2", "incremental")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Append some more data and call refresh again.
append_data = [
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")
]
append_df = spark.createDataFrame(append_data, testdata_schema)
append_df.write.mode("append").parquet(testdata_location)

hyperspace.refreshIndex("productIndex2", "incremental"
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
// Call optimize. Ensure that Sort is removed after optimization (This is possible here because after optimize, in this case, every bucket contains only 1 file.).
hyperspace.optimizeIndex("productIndex2")

hyperspace.explain(query())(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Call optimize. Ensure that Sort is removed after optimization (This is possible here because after optimize, in this case, every bucket contains only 1 file.).
hyperspace.optimizeIndex("productIndex2")

hyperspace.explain(query(), True, displayHTML)
```

::: zone-end

结果为：

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#954, qty#955, date#956, qty#961, date#962]
+- SortMergeJoin [name#954], [name#960], Inner
   :- *(1) Project [name#954, qty#955, date#956]
   :  +- *(1) Filter (isnotnull(name#954) && (name#954 = banana))
   :     +- *(1) FileScan parquet [name#954,date#956,qty#955] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Project [name#960, qty#961, date#962]
      +- *(2) Filter (((isnotnull(qty#961) && (qty#961 > 10)) && isnotnull(name#960)) && (name#960 = banana))
         +- *(2) FileScan parquet [name#960,date#962,qty#961] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#954, qty#955, date#956, qty#961, date#962]
+- SortMergeJoin [name#954], [name#960], Inner
   :- *(2) Sort [name#954 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#954, 200), [id=#1070]
   :     +- *(1) Project [name#954, qty#955, date#956]
   :        +- *(1) Filter (isnotnull(name#954) && (name#954 = banana))
   :           +- *(1) FileScan parquet [name#954,qty#955,date#956] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#960 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#960, 200), [id=#1076]
         +- *(3) Project [name#960, qty#961, date#962]
            +- *(3) Filter (((isnotnull(qty#961) && (qty#961 > 10)) && isnotnull(name#960)) && (name#960 = banana))
               +- *(3) FileScan parquet [name#960,qty#961,date#962] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=3
```

### <a name="optimize-modes"></a>优化模式

优化的默认模式是 "快速" 模式，在此模式下，会选取小于预定义阈值的文件进行优化。 若要最大程度地提高优化效果，超空间允许其他优化模式 "完全"，如下所示。 此模式将选取优化的所有索引文件，而不考虑其文件大小并创建最可能的索引布局。 这还比默认的优化模式慢，因为在此处处理的数据更多。

:::zone pivot = "programming-language-scala"

```scala
hyperspace.optimizeIndex("productIndex2", "full")

hyperspace.explain(query())(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
hyperspace.optimizeIndex("productIndex2", "full")

hyperspace.explain(query(), True, displayHTML)
```

::: zone-end

结果为：

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#1000, qty#1001, date#1002, qty#1007, date#1008]
+- SortMergeJoin [name#1000], [name#1006], Inner
   :- *(1) Project [name#1000, qty#1001, date#1002]
   :  +- *(1) Filter (isnotnull(name#1000) && (name#1000 = banana))
   :     +- *(1) FileScan parquet [name#1000,date#1002,qty#1001] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Project [name#1006, qty#1007, date#1008]
      +- *(2) Filter (((isnotnull(qty#1007) && (qty#1007 > 10)) && isnotnull(name#1006)) && (name#1006 = banana))
         +- *(2) FileScan parquet [name#1006,date#1008,qty#1007] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#1000, qty#1001, date#1002, qty#1007, date#1008]
+- SortMergeJoin [name#1000], [name#1006], Inner
   :- *(2) Sort [name#1000 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#1000, 200), [id=#1160]
   :     +- *(1) Project [name#1000, qty#1001, date#1002]
   :        +- *(1) Filter (isnotnull(name#1000) && (name#1000 = banana))
   :           +- *(1) FileScan parquet [name#1000,qty#1001,date#1002] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#1006 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#1006, 200), [id=#1166]
         +- *(3) Project [name#1006, qty#1007, date#1008]
            +- *(3) Filter (((isnotnull(qty#1007) && (qty#1007 > 10)) && isnotnull(name#1006)) && (name#1006 = banana))
               +- *(3) FileScan parquet [name#1006,qty#1007,date#1008] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=4
```

## <a name="next-steps"></a>后续步骤

* [项目超空间](https://microsoft.github.io/hyperspace/)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
