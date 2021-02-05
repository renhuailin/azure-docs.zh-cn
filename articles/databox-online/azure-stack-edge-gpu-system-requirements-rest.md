---
title: Microsoft Azure Stack 边缘 Blob 存储要求 |Microsoft Docs
description: 了解适用于 Azure Stack Edge Blob 存储的 Api、Sdk 和客户端库的支持版本
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 887aaf32592c74a5884916bc7ad63d79544fb554
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575849"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Azure Stack 边缘 Blob 存储要求

本文列出了 Azure Api、Azure 客户端库和 Azure Stack Edge Blob 存储支持的工具的版本。 Azure Stack Edge Blob 存储提供了具有 Azure 一致性语义的 Blob 管理功能。 本文还汇总了 Azure 存储服务的已知 Azure Stack Edge Blob 存储差异。

建议在连接到 Azure Stack Edge Blob 存储之前仔细查看信息，并在必要时返回到该存储。

## <a name="storage-differences"></a>存储差异

|     功能                                             |     Azure 存储                                     |     Azure Stack 边缘 Blob 存储 |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure 文件存储                                   |    支持基于云的 SMB 文件共享              |    不支持      |
|    存储帐户类型                                 |    常规用途和 Azure Blob 存储帐户    |    仅常规用途 v1|
|    Blob 名称                                            |    1024 个字符（2048 个字节）                     |    880 个字符（1760 个字节）|
|    块 blob 最大大小                              |    4.75 TB（100 MB X 50,000 块）                   |    4.75 TB (100 MB x 50000 块) 用于 Azure Stack 边缘|
|    页 blob 最大大小                               |    8 TB                                               |    1 TB                   |
|    页 Blob 页面大小                                  |    512 字节                                          |    4 KB                   |

## <a name="supported-api-versions"></a>支持的 API 版本

Azure Stack Edge Blob 存储支持以下版本的 Azure 存储服务 Api。

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack 边缘2.1.1377.2170 向前

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>支持的 Azure 客户端库

对于 Azure Stack Edge Blob 存储，有特定的客户端库和特定的终结点后缀要求。 Azure Stack 边缘 Blob 存储终结点不具有最新版本的 Azure Blob 存储的完整奇偶校验 REST API;请参阅 [Azure Stack Edge 支持的 API 版本](#supported-api-versions)。 对于存储客户端库，需要了解与 REST API 兼容的版本。

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack 边缘2.1.1377.2170 向前

以下 Azure 客户端库版本支持 Azure Stack Edge Blob 存储。

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>通过编辑器安装 PHP 客户端-最新

通过编辑器安装 PHP 客户端：

1. 使用以下代码，在项目的根目录中创建一个名为 composer.js的文件 (示例使用 Azure 存储 Blob 服务) 。

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. 将 `composer.phar` 下载到项目根目录。

3. 运行：php composer.phar 安装。


## <a name="endpoint-declaration"></a>终结点声明

在 Azure Stack Edge Blob 存储 SDK 中，终结点后缀- `<device serial number>.microsoftdatabox.com` 标识 Azure Stack 的边缘域。 有关 blob 服务终结点的详细信息，请转到 [通过包含 Azure Stack Edge PRO GPU 的存储帐户传输数据](azure-stack-edge-j-series-deploy-add-storage-accounts.md)。


## <a name="examples"></a>示例

### <a name="net"></a>.NET

对于 Azure Stack Edge Blob 存储，将在文件中指定终结点后缀 `app.config` ：

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

对于 Azure Stack Edge Blob 存储，将在连接字符串的设置中指定终结点后缀：

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

对于 Azure Stack Edge Blob 存储，将在声明实例中指定终结点后缀：

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

对于 Azure Stack Edge Blob 存储，将在连接字符串的设置中指定终结点后缀：

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

对于 Azure Stack Edge Blob 存储，将在连接字符串的设置中指定终结点后缀：

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

对于 Azure Stack Edge Blob 存储，将在声明实例中指定终结点后缀：

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

对于 Azure Stack Edge Blob 存储，将在连接字符串的设置中指定终结点后缀：

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>后续步骤

* [准备部署带 GPU Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
