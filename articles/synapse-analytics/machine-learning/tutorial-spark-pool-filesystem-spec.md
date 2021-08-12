---
title: 教程：使用 FSSPEC 在 Synapse Analytics Spark 池中读取/写入 ADLS 数据
description: 有关如何使用适用于机器学习工作负载的 FSSPEC 在专用 Spark 池中读取/写入 ADLS 数据的教程。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 06/07/2021
author: AjAgr
ms.author: ajagarw
ms.openlocfilehash: ecd0b923939ab1da724b46272dbaf60c44389d3d
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904298"
---
# <a name="tutorial-use-fsspec-to-readwrite-adls-data-in-synapse-analytics-spark-pool"></a>教程：使用 FSSPEC 在 Synapse Analytics Spark 池中读取/写入 ADLS 数据

了解如何使用文件系统规范 (FSSPEC)，通过链接服务在 Azure Synapse Analytics 的专用 Spark 池中从/向 Azure Data Lake Storage (ADLS) 读取/写入数据。

本教程介绍以下操作：

> [!div class="checklist"]
> - 在专用 Spark 会话中读取/写入 ADLS 数据。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- [Azure Synapse Analytics 工作区](../get-started-create-workspace.md)，其中 Azure Data Lake Storage Gen2 存储帐户配置为默认存储。 你需要成为所使用的 Data Lake Storage Gen2 文件系统的存储 Blob 数据参与者。
- Azure Synapse Analytics 工作区中的 Spark 池。 有关详细信息，请参阅[在 Azure Synapse 中创建 Spark 池](../get-started-analyze-spark.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-linked-services"></a>创建链接服务

在 Azure Synapse Analytics 中，链接服务是定义到其他服务的连接信息的一个位置。 在本部分中，你将添加 Azure Synapse Analytics 和 Azure Data Lake Storage Gen2 链接服务。

1. 打开 Azure Synapse Studio 并选择“管理”选项卡。
1. 在“外部连接”下，选择“链接服务”。
1. 若要添加链接服务，请选择“新建”。
1. 从列表中选择“Azure Data Lake Storage Gen2”磁贴，然后选择“继续”。
1. 输入你的身份验证凭据。 帐户密钥目前是支持的身份验证类型。 选择“测试连接”以验证你的凭据是否正确。 选择“创建”。

   :::image type="content" source="media/tutorial-spark-pool-filesystem-spec/create-adls-linked-service.png" alt-text="使用 ADLS Gen2 存储访问密钥创建链接服务。":::


## <a name="readwrite-data-using-storage-account-name-and-key"></a>使用存储帐户名称和密钥读取/写入数据

FSSPEC 可以通过直接指定存储帐户名称和密钥来读取/写入 ADLS 数据。

1. 在 Synapse Studio 中，打开“数据” > “已链接” > “Azure Data Lake Storage Gen2”  。 将数据上传到默认存储帐户。

1. 运行以下代码。

   > [!NOTE]
   > 在运行此脚本之前，先在其中更新文件 URL、ADLS Gen2 存储名称和密钥。

   ```PYSPARK
   # To read data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name
   adls_account_key = '' #Provide exact ADLS account key

   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name, account_key=adls_account_key)

   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas

   adls_account_name = '' #Provide exact ADLS account name 
   adls_account_key = '' #Provide exact ADLS account key 
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name=adls_account_name,    account_key=adls_account_key, mode="wt")
   
   with fsspec_handle.open() as f:
    data.to_csv(f)
   ```

## <a name="readwrite-data-using-linked-service"></a>使用链接服务读取/写入数据

FSSPEC 可以通过指定链接服务名称来读取/写入 ADLS 数据。


1. 在 Synapse Studio 中，打开“数据” > “已链接” > “Azure Data Lake Storage Gen2”  。 将数据上传到默认存储帐户。

1. 运行以下代码。

   > [!NOTE]
   > 在运行此脚本之前，先在其中更新文件 URL、链接服务名称和 ADLS Gen2 存储名称。

   ```PYSPARK
   # To read data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>)
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key)
   
   with fsspec_handle.open() as f:
       df = pandas.read_csv(f)

   # To write data
   import fsspec
   import pandas
   
   adls_account_name = '' #Provide exact ADLS account name
   
   data = pandas.DataFrame({'Name':['Tom', 'nick', 'krish', 'jack'], 'Age':[20, 21, 19, 18]})
   sas_key = TokenLibrary.getConnectionString(<LinkedServiceName>) 
   
   fsspec_handle = fsspec.open('abfs://<container>/<path-to-file>', account_name =    adls_account_name, sas_token=sas_key, mode="wt") 
   
   with fsspec_handle.open() as f:
       data.to_csv(f) 
   ```

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics 中的机器学习功能](what-is-machine-learning.md)