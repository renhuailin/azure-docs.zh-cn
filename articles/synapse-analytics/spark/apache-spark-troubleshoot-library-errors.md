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
ms.openlocfilehash: 57e9d0c584600a8fac90499d72cfac1620052603
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694914"
---
# <a name="troubleshoot-library-installation-errors"></a>排查库安装错误 
要使第三方或本地生成的代码可用于你的应用程序，你可以将库安装到你的无服务器 Apache Spark 池之一。 在启动池时，将从 PyPi 下载 requirements.txt 文件中列出的包。 每次从该 Spark 池中创建 Spark 实例时，都会使用此要求文件。 为 Spark 池安装库后，将可用于使用同一池的所有会话。 

在某些情况下，你可能会发现 Apache Spark 池中未显示库。 当提供的 requirements.txt 或指定的库中存在错误时，通常会发生这种情况。 当库安装过程中出现错误时，Apache Spark 池会恢复为 Synapse 基本运行时中指定的库。

本文的目的是提供常见问题并帮助你调试库安装错误。

## <a name="force-update-your-apache-spark-pool"></a>强制更新 Apache Spark 池
更新 Apache Spark 池中的库时，将在重新启动池后选取这些更改。 如果有活动的作业，这些作业将继续在 spark 池的原始版本上运行。

可以通过选择 **强制新设置** 的选项来强制应用更改。 此设置将结束所选 Spark 池的所有当前会话。 会话结束后，需要等待池重新启动。 

![添加 Python 库](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "添加 Python 库")

## <a name="track-installation-progress"></a>跟踪安装进度
每次使用一组新的库更新池时，都会启动系统保留的 Spark 作业。 此 Spark 作业有助于监视库安装的状态。 如果由于库冲突或其他问题而导致安装失败，Spark 池会恢复为其以前的状态或默认状态。 

此外，用户还可以检查安装日志以确定依赖关系冲突，或查看在更新池的过程中安装了哪些库。

查看这些日志：
1. 导航到 " **监视** " 选项卡中的 "Spark 应用程序" 列表。 
2. 选择与池更新相对应的系统 Spark 应用程序作业。 这些系统作业在 *SystemReservedJob-LibraryManagement* 标题下运行。
   ![突出显示系统保留库作业的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "查看系统库作业")
3. 切换以查看 **驱动程序** 和 **stdout** 日志。 
4. 在结果中，你将看到与安装包相关的日志。
    ![突出显示系统保留库作业结果的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "查看系统库作业进度")

## <a name="validate-your-permissions"></a>验证你的权限
若要安装和更新库，必须在链接到 Azure Synapse Analytics 工作区的主要 Azure Data Lake Storage Gen2 存储帐户上具有 **存储 Blob 数据参与者** 或 **存储 blob 数据所有者** 权限。

若要验证你是否具有这些权限，你可以运行以下代码：

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
如果收到错误，则可能缺少所需的权限。 若要了解如何获取所需的权限，请访问此文档： [分配存储 Blob 数据参与者或存储 Blob 数据所有者权限](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)。

此外，如果您运行的是管道，则工作区 MSI 也必须具有存储 Blob 数据所有者或存储 Blob 数据参与者权限。 若要了解如何向工作区标识授予此权限，请访问：向 [工作区托管标识授予权限](../security/how-to-grant-workspace-managed-identity-permissions.md)。

## <a name="check-the-environment-configuration-file"></a>检查环境配置文件
环境配置文件可用于升级 Conda 环境。 [此处](./apache-spark-manage-python-packages.md)列出了适用于 Python 池管理的可接受的文件格式。

务必注意以下限制：
   -  要求文件的内容不能包含额外的空白行或字符。 
   -  [Synapse 运行时](apache-spark-version-support.md)包括一组在每个无服务器 Apache Spark 池上预先安装的库。 不能删除或卸载预安装到基本运行时中的包。
   -  不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。
   -  Python 会话范围内的库仅接受具有 DOCKER-COMPOSE.OVERRIDE.YML 扩展名的文件。

## <a name="validate-wheel-files"></a>验证轮文件
Synapse 无服务器 Apache Spark 池基于 Linux 分发。 直接从 PyPI 下载和安装滚轮文件时，请确保选择在 Linux 上构建的版本，并在与 Spark 池相同的 Python 版本上运行。

>[!IMPORTANT]
>可以在会话之间添加或修改自定义包。 但是，需要等待池和会话重新启动才能看到更新的包。

## <a name="check-for-dependency-conflicts"></a>检查依赖关系冲突
 通常，Python 依赖项解析可能比较棘手。 若要帮助本地调试依赖项冲突，你可以创建自己的基于 Synapse 运行时的虚拟环境并验证所做的更改。

重新创建环境并验证更新：
 1. [下载](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) 模板以在本地重新创建 Synapse 运行时。 模板与实际的 Synapse 环境之间可能存在细微的差异。
   
 2. 按照 [以下说明](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html)创建虚拟环境。 此环境允许您使用指定的库列表创建独立的 Python 安装。 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. 用于使用 ``pip install -r <provide your req.txt file>`` 指定的包更新虚拟环境。 如果安装导致错误，则在 Synapse 基本运行时中预安装的内容与提供的要求文件中指定的内容之间可能存在冲突。 必须解决这些依赖关系冲突，才能在无服务器 Apache Spark 池上获取更新的库。

>[!IMPORTANT]
>同时使用 pip 和 conda 时，可能会 arrise 问题。 组合 pip 和 conda 时，最好遵循以下 [建议的最佳实践](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#using-pip-in-an-environment)。

## <a name="next-steps"></a>后续步骤
- 查看默认库： [Apache Spark 版本支持](apache-spark-version-support.md)