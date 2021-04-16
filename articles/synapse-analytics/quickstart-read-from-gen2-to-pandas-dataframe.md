---
title: 快速入门：将数据从 ADLS Gen2 读取到 Pandas 数据帧
description: 在 Azure Synapse Analytics 的 Synapse Studio 中使用 Python 将数据从 Azure Data Lake Storage Gen2 帐户读入到 Pandas 数据帧。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969566"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>快速入门：在 Azure Synapse Analytics 中将数据从 ADLS Gen2 读取到 Pandas 数据帧

本快速入门介绍如何在 Azure Synapse Analytics 中使用 Python 轻松将数据从 Azure Data Lake Storage (ADLS) Gen2 读入到 Pandas 数据帧。

你将通过 Synapse Studio 笔记本执行以下操作：

- 连接到 Data Lake Storage Gen2 中与 Azure Synapse Analytics 工作区相链接的容器
- 使用 `spark.read.load` 从 PySpark 笔记本中读取数据
- 使用 `.toPandas()` 将数据转换为 Pandas 数据帧

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
- 已将 Data Lake Storage Gen2 配置为默认存储的 Synapse Analytics 工作区 - 你需是所用 Data Lake Storage Gen2 文件系统的“存储 Blob 数据参与者”。 有关如何创建工作区的详细信息，请参阅[创建 Synapse 工作区](get-started-create-workspace.md)。
- 工作区中的 Apache Spark 池 - 请参阅[创建无服务器 Apache Spark 池](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="upload-sample-data-to-adls-gen2"></a>将示例数据上传到 ADLS Gen2

1. 在 Azure 门户中，在 Synapse Studio 所用的同一 Data Lake Storage Gen2 中创建容器。 如果你要在 Azure Synapse Analytics 工作区中使用默认的已链接存储帐户，则可以跳过此步骤。

1. 在 Synapse Studio 中单击“数据”，选择“已链接”选项卡，然后选择“Azure Data Lake Storage Gen2”下的容器  。

1. 下载示例文件 [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) 并将其上传到该容器。

1. 选择上传的文件，单击“属性”，然后复制“ABFSS 路径”值 。

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>将数据从 ADLS Gen2 读入到 Pandas 数据帧

1. 在左侧窗格中，单击“开发”。

1. 单击 **+** ，选择“笔记本”以创建新的笔记本。

1. 在“附加到”中，选择你的 Apache Spark 池。 如果你没有 Apache Spark 池，请单击“创建 Apache Spark 池”。

1. 在笔记本代码单元中粘贴以下 Python 代码，并插入前面复制的 ABFSS 路径：

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. 运行该单元。

几分钟后，显示的文本应类似于以下内容。

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Synapse Analytics？](overview-what-is.md)
- [Azure Synapse Analytics 入门](get-started.md)
- [创建无服务器 Apache Spark 池](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
