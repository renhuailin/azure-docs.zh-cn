---
title: 使用 C++ 的 Azure 存储示例 | Microsoft Docs
description: 查看、下载和运行 Azure 存储的示例代码和应用程序 使用 C++ 存储客户端库发现 Blob、队列、表和文件的入门示例。
author: normesta
ms.author: normesta
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 46f34dfc6a841bf465aed527907ee81e8fb56e22
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633709"
---
# <a name="azure-storage-samples-using-v12-c-client-libraries"></a>使用 v12 C++ 客户端库的 Azure 存储示例

下表概述了示例存储库和每个示例中涉及的场景。 单击链接可查看 GitHub 中相应的示例代码。

> [!NOTE]
> 这些示例使用最新的 Azure 存储 C++ v12 库。

## <a name="blob-samples"></a>Blob 示例

:::row:::
   :::column:::
        [使用连接字符串进行身份验证](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L18)
   :::column-end:::
   :::column:::
        [创建一个 blob 容器](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L20)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [获取 Blob 客户端](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [上传 Blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L32)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [在 blob 上设置元数据](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L34)
   :::column-end:::
   :::column:::
        [获取 Blob 属性](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
        [下载 Blob](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/sample/blob_getting_started.cpp#L44)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2 示例

:::row:::
   :::column:::
        [使用连接字符串创建服务客户端](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L22)
   :::column-end:::
   :::column:::
        [使用连接字符串创建文件系统客户端](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L25)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [创建文件系统](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [创建目录](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L48)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [创建文件](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L52)
   :::column-end:::
   :::column:::
        [将数据追加到文件](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [刷新文件数据](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L77)
   :::column-end:::
   :::column:::
        [读取文件](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [列出所有文件系统](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L88)
   :::column-end:::
   :::column:::
        [删除文件系统](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/sample/datalake_getting_started.cpp#L102)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure 文件存储示例

:::row:::
    :::column:::
        [使用连接字符串创建共享客户端](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L18)
    :::column-end:::
    :::column:::
        [创建文件共享](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L21)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [获取文件客户端](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L29)
    :::column-end:::
    :::column:::
        [上传文件](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L31)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [在文件上设置元数据](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L33)
    :::column-end:::
    :::column:::
        [获取文件属性](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L36)
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        [下载文件](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/sample/file_share_getting_started.cpp#L43)
    :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Azure 代码示例库

要查看完整的 C++ 示例库，请转到：

- [Azure Blob 代码示例](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
- [Azure Data Lake 代码示例](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-datalake/sample)
- [Azure 文件存储代码示例](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-shares/sample)

可以浏览并克隆每个库的 GitHub 存储库。

## <a name="getting-started-guides"></a>入门指南

如果要查找有关如何安装和开始使用 Azure 存储客户端库的说明，请查看以下指南。

- [快速入门：Azure Blob 存储库 v12 - C++](../blobs/quickstart-blobs-c-plus-plus.md)

## <a name="next-steps"></a>后续步骤

了解有关其他语言的示例的信息：

- .NET:[使用 .NET 的 Azure 存储示例](storage-samples-dotnet.md)
- Java:[使用 Java 的 Azure 存储示例](storage-samples-java.md)
- Python:[使用 Python 的 Azure 存储示例](storage-samples-python.md)
- JavaScript/Node.js：[使用 JavaScript 的 Azure 存储示例](storage-samples-javascript.md)
- 所有其他语言：[Azure 存储示例](storage-samples.md)
