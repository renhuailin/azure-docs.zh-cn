---
title: 使用 Databricks Python 转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过在 Azure 数据工厂管道中运行 Databricks Python 活动来处理或转换数据。
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 03/15/2018
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-python, synapse
ms.openlocfilehash: 24d1ab1860f875201c255e775af151af88585731
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182069"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>通过运行 Azure Databricks 中的 Python 活动转换数据
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[数据工厂管道](concepts-pipelines-activities.md)中的 Azure Databricks Python 活动在 Azure Databricks 群集中运行 Python 文件。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。

有关此功能的十一分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python 活动定义

下面是 Databricks Python 活动的示例 JSON 定义：

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Databricks Python 活动属性

下表描述了 JSON 定义中使用的 JSON 属性：

|属性|说明|必需|
|---|---|---|
|name|管道中活动的名称。|是|
|description|描述活动用途的文本。|否|
|type|对于 Databricks Python 活动，活动类型是 DatabricksSparkPython。|是|
|linkedServiceName|Databricks 链接服务的名称，Python 活动在其上运行。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。|是|
|pythonFile|要执行的 Python 文件的 URI。 仅支持 DBFS 路径。|是|
|parameters|将传递到 Python 文件的命令行参数。 这是一个字符串数组。|否|
|库|要安装在将执行作业的群集上的库列表。 它可以是 <string, object> 数组|否|

## <a name="supported-libraries-for-databricks-activities"></a>databricks 活动支持的库

在以上 Databricks 活动定义中，指定这些库类型：jar、egg、maven、pypi、cran。

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

有关详细信息，请参阅库类型的 [Databricks 文档](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary)。

## <a name="how-to-upload-a-library-in-databricks"></a>如何上传 Databricks 中的库

### <a name="you-can-use-the-workspace-ui"></a>可以使用工作区 UI：

1. [使用 Databricks 工作区 UI](/azure/databricks/libraries/#create-a-library)

2. 若要获取使用 UI 添加的库的 dbfs 路径，可以使用 [Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli)。

   使用 UI 时，Jar 库通常存储在 dbfs:/FileStore/jars 下。 可以通过 CLI 列出所有库：databricks fs ls dbfs:/FileStore/job-jars

### <a name="or-you-can-use-the-databricks-cli"></a>或者，可以使用 Databricks CLI：

1. 按照[使用 Databricks CLI 复制库](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)操作

2. 使用 Databricks CLI[（安装步骤）](/azure/databricks/dev-tools/cli/#install-the-cli)

   例如，将 JAR 复制到 dbfs：`dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
