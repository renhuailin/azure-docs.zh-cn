---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/19/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0914cf9515930e23e4134181ffe8332e36eacffe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612949"
---
Azure Blob 存储是 Microsoft 提供的适用于云的对象存储解决方案。 Blob 存储最适合存储巨量的非结构化数据。 非结构化数据是不遵循特定数据模型或定义的数据（如文本或二进制数据）。

## <a name="about-blob-storage"></a>关于 Blob 存储

Blob 存储用于：

* 直接向浏览器提供图像或文档。
* 存储文件以供分布式访问。
* 对视频和音频进行流式处理。
* 向日志文件进行写入。
* 存储用于备份和还原、灾难恢复及存档的数据。
* 存储数据以供本地或 Azure 托管服务执行分析。

用户或客户端应用程序通过 HTTP/HTTPS 可以从世界任何地方访问 Blob 存储中的对象。 Blob 存储中的对象可以通过 [Azure 存储 REST API](/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](/powershell/module/az.storage)、[Azure CLI](/cli/azure/storage) 或 Azure 存储客户端库访问。 提供了不同语言的客户端库，包括：

* [.NET](/dotnet/api/overview/azure/storage)
* [Java](/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>关于 Azure Data Lake Storage Gen2

Blob 存储支持 Azure Data Lake storage Gen2，即 Microsoft 适用于云的企业大数据分析解决方案。 Azure Data Lake Storage Gen2 提供了分层文件系统以及 Blob 存储的优势，包括：

* 低成本分层存储
* 高可用性
* 强一致性
* 灾难恢复功能

有关 Data Lake Storage Gen2 的详细信息，请参阅 [Azure Data Lake Storage Gen2 简介](../articles/storage/blobs/data-lake-storage-introduction.md)。