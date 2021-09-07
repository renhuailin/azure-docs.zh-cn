---
title: Microsoft Azure Stack Edge Blob 存储要求 | Microsoft Docs
description: 了解 Azure Stack Edge Blob 存储支持的 API、SDK 和客户端库版本
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 4740a4b4aac59ac2e0e57797f39c489c6beaf75c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252172"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Azure Stack Edge Blob 存储要求

本文列出了能够与 Azure Stack Edge Blob 存储配合使用的 Azure API、Azure 客户端库和工具的版本。 Azure Stack Edge Blob 存储提供了具有 Azure 一致语义的 blob 管理功能。 本文还总结了已知 Azure Stack Edge Blob 存储与 Azure 存储服务的区别。

建议在连接到 Azure Stack Edge Blob 存储之前仔细审阅这些信息，然后在必要时再回头查阅。

## <a name="storage-differences"></a>存储差异

|     功能                                             |     Azure 存储                                     |     Azure Stack Edge Blob 存储 |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure 文件                                   |    支持基于云的 SMB 和 NFS 文件共享              |    不支持      |
|    存储帐户类型                                 |    常规用途和 Azure Blob 存储帐户    |    仅常规用途 v1|
|    Blob 名称                                            |    1024 个字符（2048 个字节）                     |    880 个字符（1760 个字节）|
|    块 blob 最大大小                              |    4.75 TiB（100 MiB X 50,000 块）                   |    适用于 Azure Stack Edge 的 4.75 TiB（100 MiB x 50,000 块）|
|    页 blob 最大大小                               |    8 TiB                                               |    1 TiB                   |
|    页 Blob 页面大小                                  |    512 字节                                          |    4 KiB                   |

## <a name="supported-api-versions"></a>支持的 API 版本

以下版本的 Azure 存储服务 API 可以与 Azure Stack Edge Blob 存储配合使用。

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 及更高版本

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>支持的 Azure 客户端库

对于 Azure Stack Edge Blob 存储，有特定的客户端库和特定的终结点后缀要求。 Azure Stack Edge Blob 存储终结点与最新版 Azure Blob 存储 REST API 没有完全的奇偶一致性；请参阅 [Azure Stack Edge 支持的 API 版本](#supported-api-versions)。 对于存储客户端库，需要了解与 REST API 兼容的版本。

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 及更高版本

Azure Stack Edge Blob 存储支持以下 Azure 客户端库版本。

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>通过 Composer 安装 PHP 客户端 - 当前

若要通过 Composer 安装 PHP 客户端，请执行以下操作：

1. 使用以下代码在项目的根目录中创建名为 composer.json 的文件（示例使用 Azure 存储 Blob 服务）。

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. 将 `composer.phar` 下载到项目根目录。

3. 运行：php composer.phar 安装。


## <a name="endpoint-declaration"></a>终结点声明

在 Azure Stack Edge Blob 存储 SDK 中，终结点后缀 `<device serial number>.microsoftdatabox.com` 标识 Azure Stack Edge 域。 若要详细了解 blob 服务终结点，请转到[使用 Azure Stack Edge Pro GPU 通过存储帐户传输数据](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)。


## <a name="examples"></a>示例

### <a name="net"></a>.NET

对于 Azure Stack Edge Blob 存储，终结点后缀在 `app.config` 文件中指定：

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

对于 Azure Stack Edge Blob 存储，终结点后缀在连接字符串的设置中指定：

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

对于 Azure Stack Edge Blob 存储，终结点后缀在声明实例中指定：

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

对于 Azure Stack Edge Blob 存储，终结点后缀在连接字符串的设置中指定：

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

对于 Azure Stack Edge Blob 存储，终结点后缀在连接字符串的设置中指定：

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

对于 Azure Stack Edge Blob 存储，终结点后缀在声明实例中指定：

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

对于 Azure Stack Edge Blob 存储，终结点后缀在连接字符串的设置中指定：

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>后续步骤

* [准备部署 Azure Stack Edge Pro with GPU](azure-stack-edge-gpu-deploy-prep.md)