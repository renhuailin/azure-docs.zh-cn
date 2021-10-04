---
title: 使用 Databricks Notebook 转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂和 Synapse Analytics 管道中运行 Databricks Notebook 来处理或转换数据。
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 9f82c1f2e39261ba4ba1072f5da9f807f23a180e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798458"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>通过运行 Databricks Notebook 转换数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[管道](concepts-pipelines-activities.md)中的 Azure Databricks Notebook 活动在 Azure Databricks 工作区中运行 Databricks Notebook。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。

## <a name="databricks-notebook-activity-definition"></a>Databricks Notebook 活动定义

下面是 Databricks Notebook 活动的示例 JSON 定义：

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks Notebook 活动属性

下表描述了 JSON 定义中使用的 JSON 属性：

|属性|说明|必需|
|---|---|---|
|name|管道中活动的名称。|是|
|description|描述活动用途的文本。|否|
|type|对于 Databricks Notebook 活动，活动类型是 DatabricksNotebook。|是|
|linkedServiceName|Databricks 链接服务的名称，Databricks Notebook 在其上运行。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。|是|
|notebookPath|要在 Databricks 工作区中运行的 Notebook 的绝对路径。 此路径必须以斜杠开头。|是|
|baseParameters|一个键/值对的数组。 基参数可用于运行每个活动。 如果 Notebook 采用的参数未指定，则将使用 Notebook 中的默认值。 有关参数的更多信息，请参阅 [Databricks Notebook](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)。|否|
|库|要安装在将执行作业的群集上的库列表。 它可以是 \<string, object> 的数组。|否|

## <a name="supported-libraries-for-databricks-activities"></a>Databricks 活动支持的库

在以上 Databricks 活动定义中，指定这些库类型：jar、egg、whl、maven、pypi、cran     。

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

有关详细信息，请参阅库类型的 [Databricks 文档](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary)。

## <a name="passing-parameters-between-notebooks-and-pipelines"></a>在笔记本和管道之间传递参数

可以使用 Databricks 活动中的 baseParameters 属性将参数传递给笔记本。

在某些情况下，你可能需要将某些值从笔记本传回服务，这些值可用于服务中的控制流（条件检查）或由下游活动使用（大小限制为 2MB）。

1. 在笔记本中，可以调用 [dbutils.notebook.exit("returnValue")](/azure/databricks/notebooks/notebook-workflows#notebook-workflows-exit)，相应的“returnValue”将会返回到服务。

2. 可以使用表达式（如 `@{activity('databricks notebook activity name').output.runOutput}`）在服务中使用该输出。 

   > [!IMPORTANT]
   > 如果要传递 JSON 对象，可以通过追加属性名称来检索值。 示例：`@{activity('databricks notebook activity name').output.runOutput.PropertyName}`

## <a name="how-to-upload-a-library-in-databricks"></a>如何上传 Databricks 中的库

### <a name="you-can-use-the-workspace-ui"></a>可以使用工作区 UI：

1. [使用 Databricks 工作区 UI](/azure/databricks/libraries/#create-a-library)

2. 若要获取使用 UI 添加的库的 dbfs 路径，可以使用 [Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli)。

   使用 UI 时，Jar 库通常存储在 dbfs:/FileStore/jars 下。 可以通过 CLI 列出所有库：databricks fs ls dbfs:/FileStore/job-jars

### <a name="or-you-can-use-the-databricks-cli"></a>或者，可以使用 Databricks CLI：

1. 按照[使用 Databricks CLI 复制库](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)操作

2. 使用 Databricks CLI[（安装步骤）](/azure/databricks/dev-tools/cli/#install-the-cli)

   例如，将 JAR 复制到 dbfs：`dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
