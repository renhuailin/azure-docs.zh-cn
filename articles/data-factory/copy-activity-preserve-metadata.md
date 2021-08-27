---
title: 使用 Azure 数据工厂中的复制活动保留元数据和 ACL
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure 数据工厂中的复制活动在复制期间保留元数据和 ACL。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jianleishen
ms.openlocfilehash: 1883d1192a1908611e3440ea2d39e770b4599bc6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638618"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>使用 Azure 数据工厂中的复制活动保留元数据和 ACL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

当使用 Azure 数据工厂复制活动将数据从源复制到接收器时，在以下情况下，还可以保留元数据和 ACL。

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> 保留数据湖迁移的元数据

在将数据从一个数据湖迁移到另一个数据湖（包括 [Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) 和 [Azure文件存储](connector-azure-file-storage.md)）时，可选择保留文件元数据和数据。

复制活动支持在数据复制期间保留以下属性：

- **所有客户指定的元数据** 
- 以及以下五个数据存储内置系统属性： **、** （Amazon S3 除外）、`contentType`、`contentLanguage`、`contentEncoding``contentDisposition``cacheControl`。

**处理元数据的差异：** Amazon S3 和 Azure 存储允许在客户指定的元数据键中使用不同的字符集。 选择使用复制活动保留元数据时，ADF 会自动将无效字符替换为“_”。

按原样将文件从 Amazon S3/Azure Data Lake Storage Gen2/Azure Blob/Azure 文件存储复制到二进制格式的 Azure Data Lake Storage Gen2/Azure Blob/Azure 文件存储时，可在用于活动创作的“复制活动” > “设置”选项卡中或在复制数据工具的“设置”页面上找到“保留”选项   。

![复制活动保留元数据](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

下面是复制活动 JSON 配置的示例（请参阅 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> 将 ACL 从 Data Lake Storage Gen1/Gen2 保留到 Gen2

在从 Azure Data Lake Storage Gen1 升级到 Gen2 或在 ADLS Gen2 之间复制数据时，可以选择保留 POSIX 访问控制列表 (ACL) 以及数据文件。 有关访问控制的详细信息，请参阅 [Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)和 [Azure Data Lake Storage Gen2 中的访问控制](../storage/blobs/data-lake-storage-access-control.md)。

复制活动支持在数据复制期间保留以下 ACL 类型。 可以选择一个或多个类型：

- **ACL**：复制并保留文件和目录上的 POSIX 访问控制列表。 它将完整的现有 ACL 从源复制到接收器。 
- **所有者**：复制并保留文件和目录的拥有用户。 需要对接收器 Data Lake Storage Gen2 的超级用户访问权限。
- **组**：复制并保留文件和目录的拥有组。 需要对接收器 Data Lake Storage Gen2 或拥有用户（如果拥有用户也是目标组的成员）的超级用户访问权限。

如果指定从文件夹复制，则 `recursive` 设为 true 时，数据工厂将复制该指定文件夹及其下方的文件和目录中的 ACL。 如果指定从单一文件复制，则复制该文件中的 ACL。

>[!NOTE]
>使用 ADF 将 ACL 从 Data Lake Storage Gen1/Gen2 保留到 Gen2 时，将覆盖接收器 Gen2 的相应文件夹/文件中的现有 ACL。

>[!IMPORTANT]
>选择保留 ACL 时，请确保为数据工厂授予足够高的权限，以针对接收器 Data Lake Storage Gen2 帐户进行操作。 例如，使用帐户密钥身份验证，或将“存储 Blob 数据所有者”角色分配给服务主体或托管标识。

将源配置为具有二进制格式或二进制复制选项的 Data Lake Storage Gen1/Gen2，并将接收器配置为具有二进制格式或二进制复制选项的 Data Lake Storage Gen2 时，可在“复制数据工具”的“设置”页面上或在用于活动创作的“复制活动” > “设置”选项卡中找到“保留”选项   。

![将 ACL 从 Data Lake Storage Gen1/Gen2 保留到 Gen2](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

下面是复制活动 JSON 配置的示例（请参阅 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>后续步骤

请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能](copy-activity-performance.md)
