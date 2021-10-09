---
title: 使用 Databricks Jar 转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂或 Synapse Analytics 管道中运行 Databricks Jar 来处理或转换数据。
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 09/09/2021
ms.openlocfilehash: d4e1daad02e46f5921754665a44e57b42dabed8c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798496"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>通过运行 Azure Databricks 中的 Jar 活动转换数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[管道](concepts-pipelines-activities.md)中的 Azure Databricks Jar 活动在 Azure Databricks 群集中运行 Spark Jar。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。

有关此功能的十一分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks Jar 活动定义

下面是 Databricks Jar 活动的示例 JSON 定义：

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Databricks Jar 活动属性

下表描述了 JSON 定义中使用的 JSON 属性：

|属性|说明|必需|
|:--|---|:-:|
|name|管道中活动的名称。|是|
|description|描述活动用途的文本。|否|
|type|对于 Databricks Jar 活动，活动类型是 DatabricksSparkJar。|是|
|linkedServiceName|Databricks 链接服务的名称，Jar 活动在其上运行。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。|是|
|mainClassName|类的全名，包含要执行的主要方法。 此类必须包含在作为库提供的 JAR 中。 一个 JAR 文件中可以包含多个类。 每个类都可以包含一种主要方法。|是|
|parameters|将传递到主要方法的参数。 该属性是一个字符串数组。|否|
|库|要安装在将执行作业的群集上的库列表。 它可以是 <string, object> 数组|是（至少有一个包含 mainClassName 方法）|

> [!NOTE]
> **已知问题** - 当使用同一 [交互式群集](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks)来运行并发的 Databricks Jar 活动（没有重启群集）时，在 Databricks 中存在一个已知问题，即第一个活动的输入参数也会被后续活动使用。 这会导致将不正确的参数传递给后续作业。 若要缓解此问题，请改用[作业群集](compute-linked-services.md#example---using-new-job-cluster-in-databricks)。

## <a name="supported-libraries-for-databricks-activities"></a>databricks 活动支持的库

在前面的 Databricks 活动定义中，已指定以下库类型：`jar`、`egg`、`maven`、`pypi` 和 `cran`。

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

有关详细信息，请参阅针对库类型的 [Databricks 文档](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary)。

## <a name="how-to-upload-a-library-in-databricks"></a>如何上传 Databricks 中的库

### <a name="you-can-use-the-workspace-ui"></a>可以使用工作区 UI：

1. [使用 Databricks 工作区 UI](/azure/databricks/libraries/#create-a-library)

2. 若要获取使用 UI 添加的库的 dbfs 路径，可以使用 [Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli)。

   使用 UI 时，Jar 库通常存储在 dbfs:/FileStore/jars 下。 可以通过 CLI 列出所有库：databricks fs ls dbfs:/FileStore/job-jars

### <a name="or-you-can-use-the-databricks-cli"></a>或者，可以使用 Databricks CLI：

1. 按照[使用 Databricks CLI 复制库](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)操作

2. 使用 Databricks CLI[（安装步骤）](/azure/databricks/dev-tools/cli/#install-the-cli)

   例如，将 JAR 复制到 dbfs：`dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>后续步骤

有关此功能的十一分钟介绍和演示，请观看[视频](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player)。
