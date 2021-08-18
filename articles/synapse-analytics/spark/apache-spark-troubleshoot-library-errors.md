---
title: 排查库安装错误
description: 本教程概述了如何排查库安装错误。
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 1a5e665636e725252b18c2086471b272f40466cd
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291830"
---
# <a name="troubleshoot-library-installation-errors"></a>排查库安装错误 
要使第三方或本地生成的代码可用于你的应用程序，可将库安装到某个无服务器 Apache Spark 池上。 当池启动时，将从 PyPi 下载 requirements.txt 文件中列出的包。 每当通过该 Spark 池创建 Spark 实例时，都会使用此要求文件。 为 Spark 池安装库后，它就可用于使用同一池的所有会话。 

在某些情况下，你可能会发现库未显示在 Apache Spark 池中。 当提供的 requirements.txt 或指定的库中存在错误时，通常会发生这种情况。 当库安装过程中出现错误时，Apache Spark 池会恢复为 Synapse 基本运行时中指定的库。

本文档的目的是提供常见问题并帮助你调试库安装错误。

## <a name="force-update-your-apache-spark-pool"></a>强制更新 Apache Spark 池
更新 Apache Spark 池中的库时，这些更改会在重新启动池后恢复原样。 如果有活动的作业，这些作业将继续在 spark 池的原始版本上运行。

可以通过选择“强制使用新设置”选项来强制应用更改。 此设置将结束所选 Spark 池的所有当前会话。 会话结束后，需要等待池重启。 

![添加 Python 库](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "添加 Python 库")

## <a name="track-installation-progress"></a>跟踪安装进度
每次为池更新一组新的库时，都会启动系统预留的 Spark 作业。 此 Spark 作业有助于监视库的安装状态。 如果由于库冲突或其他问题而导致安装失败，Spark 池将恢复为以前的状态或默认状态。 

此外，用户还可以检查安装日志来识别依赖项冲突，或查看在池更新过程中安装了哪些库。

查看安装日志：
1. 导航到“监视”选项卡中的 Spark 应用程序列表。 
2. 选择与池更新相对应的系统 Spark 应用程序作业。 这些系统作业在 SystemReservedJob-LibraryManagement 标题下运行。
   ![突出显示系统预留的库作业的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "查看系统库作业")
3. 切换查看“驱动程序”和 stdout 日志。 
4. 在结果中，你将看到与自己的包安装相关的日志。
    ![突出显示系统预留的库作业结果的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "查看系统库作业进度")

## <a name="validate-your-permissions"></a>验证你的权限
若要安装和更新库，必须在关联到 Azure Synapse Analytics 工作区的主 Azure Data Lake Storage Gen2 存储帐户上拥有“存储 Blob 数据参与者”或“存储 Blob 数据所有者”权限 。

若要验证是否具有这些权限，可以运行以下代码：

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
如果收到错误，则你可能缺少所需的权限。 若要了解如何获取所需的权限，请访问此文档：[分配存储 Blob 数据参与者或存储 Blob 数据所有者权限](../../storage/blobs/assign-azure-role-data-access.md)。

此外，如果你运行的是管道，则工作区 MSI 也必须具有存储 Blob 数据所有者或存储 Blob 数据参与者权限。 若要了解如何向工作区标识授予此权限，请访问：[向工作区托管标识授予权限](../security/how-to-grant-workspace-managed-identity-permissions.md)。

## <a name="check-the-environment-configuration-file"></a>检查环境配置文件
环境配置文件可用于升级 Conda 环境。 [此处](./apache-spark-manage-python-packages.md)列出了 Python 池管理可接受的文件格式。

务必注意以下限制：
   -  要求文件的内容不能包含额外的空白行或字符。 
   -  [Synapse 运行时](apache-spark-version-support.md)包含一组预先安装在每个无服务器 Apache Spark 池上的库。 不能删除或卸载预先安装到基本运行时上的包。
   -  不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。
   -  Python 会话范围内的库仅接受具有 YML 扩展名的文件。

## <a name="validate-wheel-files"></a>验证 wheel 文件
Synapse 无服务器 Apache Spark 池基于 Linux 分发。 直接从 PyPI 下载和安装 Wheel 文件时，请确保选择基于 Linux 构建的版本，并在与 Spark 池相同的 Python 版本上运行。

>[!IMPORTANT]
>你可以在会话之间添加或修改自定义包。 但需要等待池和会话重启才能看到更新的包。

## <a name="check-for-dependency-conflicts"></a>检查依赖关系冲突
 一般来说，Python 依赖关系的解析很难管理。 为了帮助在本地调试依赖关系冲突，你可以创建自己的基于 Synapse 运行时的虚拟环境并验证所做的更改。

若要重新创建环境并验证更新，请进行以下操作：
 1. [下载](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml)模板以在本地重新创建 Synapse 运行时。 模板与实际的 Synapse 环境之间可能存在细微的差异。
   
 2. 按照[以下说明](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)创建虚拟环境。 此环境让你能够使用指定的库列表创建独立的 Python 安装。 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. 通过 ``pip install -r <provide your req.txt file>`` 使用指定的包更新虚拟环境。 如果安装导致错误，则 Synapse 基本运行时中预安装的内容与提供的要求文件中指定的内容之间可能存在冲突。 必须解决这些依赖关系冲突，才能在无服务器 Apache Spark 池上获取更新后的库。

>[!IMPORTANT]
>结合使用 pip 和 conda 可能会引发问题。 如果结合使用 pip 和 conda，最好遵循这些[建议的最佳做法](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)。

## <a name="next-steps"></a>后续步骤
- 查看默认库：[Apache Spark 版本支持](apache-spark-version-support.md)