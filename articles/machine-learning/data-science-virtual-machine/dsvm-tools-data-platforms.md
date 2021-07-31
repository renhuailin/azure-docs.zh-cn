---
title: 支持的数据平台
titleSuffix: Azure Data Science Virtual Machine
description: 了解 Azure Data Science Virtual Machine 支持的数据平台和工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 04/29/2021
ms.openlocfilehash: 8e8920d2a2ebbf326c9d5d0aba100ad5352ca6c4
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110071193"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine 支持的数据平台

使用 Data Science Virtual Machine (DSVM)，可生成针对各种数据平台的分析。 除远程数据平台接口外，DSVM 还提供用于快速开发和原型制作的本地实例。

DSVM 支持以下数据平台工具。

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 本地关系数据库实例      |
| 支持的 DSVM 版本      | Windows 2019、Ubuntu 18.04 (SQL Server 2019)   |
| 典型用途      | <ul><li>使用小型数据集在本地进行快速开发</li><li>运行数据库内 R</li></ul> |
| 指向示例的链接      | <ul><li>将 New York City 数据集加载进 SQL 数据库的小型示例：<br/>  `nyctaxi`</li><li>可在以下位置找到显示 Microsoft Machine Learning Server 和数据库内分析的 Jupyter 示例：<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| DSVM 上的相关工具       | <ul><li>SQL Server Management Studio</li><li>ODBC/JDBC 驱动程序</li><li>pyodbc, RODBC</li></ul> |

> [!NOTE]
> SQL Server Developer Edition 只能用于开发和测试。 需要许可证或一个 SQL Server VM 才能在生产中运行。

> [!NOTE]
> 对 Machine Learning Server 独立版的支持将于 2021 年 7 月 1 日结束。 我们将在 6 月 30 日之后从 DSVM 映像中删除它。 现有部署可继续访问该软件，但由于已到达支持结束日期，因此在 2021 年 7 月 1 日之后将不再为其提供支持。


### <a name="windows"></a>Windows

#### <a name="setup"></a>设置

数据库服务器已预先配置，与 SQL Server 相关的 Windows 服务（例如 `SQL Server (MSSQLSERVER)`）设置为自动运行。 唯一的手动步骤涉及使用 Microsoft Machine Learning Server 启用数据库内分析。 要启用分析，可在 SQL Server Management Studio (SSMS) 中一次性运行以下命令。 先以计算机管理员身份登录，然后运行此命令，在 SSMS 中打开一个新查询，并确保选择的是 `master` 数据库：

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

（将 %COMPUTERNAME% 替换为你的 VM 名称。）

若要运行 SQL Server Management Studio，可在程序列表中搜索“SQL Server Management Studio”，或使用 Windows 搜索来查找并运行它。 系统提示输入凭据时，请选择“Windows 身份验证”，然后使用计算机名称或 SQL Server 名称字段中的 ```localhost``` 。

#### <a name="how-to-use-and-run-it"></a>如何使用和运行它

默认情况下，具有默认数据库实例的数据库服务器会自动运行。 可在 VM 上使用 SQL Server Management Studio 等工具在本地访问 SQL Server 数据库。 本地管理员帐户在数据库中具有管理员访问权限。

此外，ODBC 驱动程序和 JDBC 驱动程序随附的 DSVM 还会通过使用多种语言（包括 Python 和 Machine Learning Server）编写的应用程序与 SQL Server、Azure SQL 数据库和 Azure Synapse Analytics 通信。

#### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>如何在 DSVM 上配置和安装它？ 

 SQL Server 采用标准方式安装。 可在 `C:\Program Files\Microsoft SQL Server` 中找到它。 可在 `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` 找到数据库内 Machine Learning Server 实例。 DSVM 还具有单独的独立 Machine Learning Server 实例，该实例安装在 `C:\Program Files\Microsoft\R Server\R_SERVER` 中。 这两个 Machine Learning Server 实例不共享库。


### <a name="ubuntu"></a>Ubuntu

若要在 Ubuntu DSVM 上使用 SQL Server Developer Edition，需要先安装它。 [快速入门：在 Ubuntu 上安装 SQL Server 并创建数据库](/sql/linux/quickstart-install-connect-ubuntu)告诉你如何操作。



## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (Standalone)

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 它是流行的 Apache Spark 平台的独立（单个进程内节点）实例，是快速进行大规模数据处理和机器学习的系统     |
| 支持的 DSVM 版本      | Linux     |
| 典型用途      | <ul><li>使用较小型的数据集在本地快速开发 Spark/PySpark 应用程序，然后在大型 Spark 群集（例如 Azure HDInsight）上进行部署</li><li>测试 Microsoft Machine Learning Server Spark 上下文</li><li>使用 SparkML 或 Microsoft 的开放源代码 [MMLSpark](https://github.com/Azure/mmlspark) 库来生成 ML 应用程序</li></ul> |
| 指向示例的链接      |    Jupyter 示例：<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server（Spark 上下文）：/dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| DSVM 上的相关工具       | <ul><li>PySpark、Scala</li><li>Jupyter（Spark/PySpark 内核）</li><li>Microsoft Machine Learning Server、SparkR、Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>如何使用
可以通过运行 `spark-submit` 或 `pyspark` 命令在命令行上提交 Spark 作业。 还可通过使用 Spark 内核新建笔记本来创建 Jupyter 笔记本。

使用 DSVM 上提供的 SparkR、Sparklyr 和 Microsoft Machine Learning Server 库，可通过 R 使用 Spark。 请参阅上表中的示例链接。

### <a name="setup"></a>设置
在 Ubuntu Linux DSVM 版本的 Microsoft Machine Learning Server 的 Spark 上下文中运行前，必须执行一次性设置步骤来启用本地单节点 Hadoop HDFS 和 Yarn 实例。 默认情况下，Hadoop 服务已安装但在 DSVM 上禁用。 若要启用它们，需要首次以 root 身份运行以下命令：

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

不再需要 Hadoop 相关服务时，可以通过运行 ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` 来停止这些服务。

可在 `/dsvm/samples/MRS` 目录中找到演示如何在远程 Spark 上下文（DSVM 上的独立 Spark 实例）中开发和测试 MRS 的示例。


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>如何在 DSVM 上配置和安装它？ 
|平台|安装位置 ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


用于通过 Azure Blob 存储或 Azure Data Lake Storage 访问数据（使用 Microsoft MMLSpark 机器学习库）的库已在 $SPARK_HOME/jars 中预先安装。 Spark 启动时，这些 JAR 会自动加载。 默认情况下，Spark 使用本地磁盘上的数据。 

DSVM 上的 Spark 实例若要访问存储在 Blob 存储或 Azure Data Lake Storage 中的数据，必须根据 $SPARK_HOME/conf/core-site.xml.template 中找到的模板来创建和配置 `core-site.xml` 文件。 必须具有相应的凭据，才能访问 Blob 存储和 Azure Data Lake Storage。 （请注意，这些模板文件使用占位符表示 Blob 存储和 Azure Data Lake Storage 配置。）

若要详细了解如何创建 Azure Data Lake Storage 服务凭据，请参阅 [Azure Data Lake Storage Gen1 身份验证](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)。 在 core-site.xml 文件中输入 Blob 存储或 Azure Data Lake Storage 的凭据后，可使用 wasb:// 和 adl:// 这两个 URI 前缀引用存储在这些源中的数据。