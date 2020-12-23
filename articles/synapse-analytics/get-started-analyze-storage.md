---
title: 教程：开始分析存储帐户中的数据
description: 本教程介绍如何分析位于存储帐户中的数据。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5e3fbd1868cc1216cb7b9d02b2aa8e690af33952
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917675"
---
# <a name="analyze-data-in-a-storage-account"></a>分析存储帐户中的数据

本教程介绍如何分析位于存储帐户中的数据。

## <a name="overview"></a>概述

到目前为止，我们介绍了数据驻留在工作区的数据库中的各种情况。 现在，我们将介绍如何使用存储帐户中的文件。 对于这一情况，我们将使用在创建工作区时所指定的工作区和容器的主存储帐户。

* 存储帐户的名称为：contosolake
* 存储帐户中的容器的名称：users

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>在存储帐户中创建 CSV 和 Parquet 文件

在笔记本中运行以下代码。 这会在存储帐户中创建 CSV 文件和 parquet 文件。

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>分析存储帐户中的数据

可以在工作区默认 ADLS Gen2 帐户中分析数据，也可以通过“管理”>“链接服务”>“新建”  （以下步骤将引用主 ADLS Gen2 帐户）将 ADLS Gen2 或 Blob 存储帐户链接到工作区。

1. 在 Synapse Studio 中，转到“数据”中心，然后选择“已关联” 。
1. 转到“存储帐户” > “myworkspace (主 - contosolake)” 。
1. 选择“用户(主)”。 应会看到 NYCTaxi 文件夹。 在内部应会看到名为 PassengerCountStats_csvformat 和 PassengerCountStats_parquetformat 的两个文件夹 。
1. 打开 PassengerCountStats_parquetformat 文件夹。 在文件夹内，你将看到名称类似于 `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` 的 Parquet 文件。
1. 右键单击“.parquet”，然后选择“新建笔记本” 。 它会创建一个包含如下所示的单元的笔记本：

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. 运行该单元。
1. 右键单击内部的 parquet 文件，然后选择“新建 SQL 脚本” > “选择前 100 行” 。 它会创建如下所示的 SQL 脚本：

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    在脚本窗口中，“连接到”字段将设置为“无服务器 SQL 池” 。

1. 运行该脚本。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用管道安排活动](get-started-pipelines.md)
