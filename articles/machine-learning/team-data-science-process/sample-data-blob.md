---
title: Azure Blob 存储中的示例数据-团队数据科学流程
description: 通过以编程方式下载存储在 Azure Blob 存储中的数据，然后使用 Python 中编写的过程对其进行采样。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c5827a0e07e537b66684f852d8f3e1500cd9febb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788835"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Azure Blob 存储中的示例数据

本文介绍如何通过编程方式下载存储在 Azure Blob 存储中的数据，然后使用 Python 中编写的过程对其进行采样。

**为什么对数据进行采样？**
如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 采样有利于数据理解、探索和特征工程。 它在 Cortana Analytics 进程中的作用是能够快速建立数据处理函数和机器学习模型的快速原型。

此采样任务是[团队数据科学流程 (TDSP)](./index.yml) 中的一个步骤。

## <a name="download-and-down-sample-data"></a>下载和向下采样数据
1. 使用以下示例 Python 代码中的 Blob 服务从 Azure Blob 存储下载数据： 

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

2. 从以上下载的文件中将数据读入 Pandas 数据帧。

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. 使用 `numpy` 的 `random.choice` 向下采样数据，如下所示：

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

现在可以使用上述具有 1% 样本的数据帧，执行进一步的研究和特征生成。

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>上传数据并读入 Azure 机器学习
可以使用以下示例代码向下采样数据，并直接在 Azure 机器学习中使用：

1. 将数据帧写入本地文件

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. 使用以下示例代码将本地文件上传到 Azure blob：

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. 使用 Azure 机器学习[导入数据](/azure/machine-learning/studio-module-reference/import-data)从 Azure blob 中读取数据，如下图中所示：

![blob 读取器](./media/sample-data-blob/reader_blob.png)
