---
title: 资产规范化
description: 了解 Azure Purview 如何通过资产规范化来防止数据映射中出现重复资产
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 68092e55cfe53ecde6e6b8146608267e39df0557
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114670043"
---
# <a name="asset-normalization"></a>资产规范化

将资产引入 Azure Purview 数据映射时，更新同一数据资产的不同源可能会发送类似但略有不同的限定名称。 虽然这些限定名称代表同一个资产，但细微的差异（例如额外的字符或不同的大小写）可能会导致这些资产在表面上看似不同。 为了避免在使用数据目录时存储重复的条目并造成混淆，Azure Purview 将在引入过程中应用规范化，以确保同一实体类型的所有完全限定名称采用相同的格式。

例如，假设你在限定名称为 `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet` 的 Azure Blob 中进行扫描。 此 Blob 也由某个 Azure 数据工厂管道使用，而后者随之将在资产中添加世系信息。 ADF 管道可配置为将文件作为 `https://myAccount.file.core.windows.net//myshare/folderA/folderB/my-file.parquet` 读取。 虽然限定名称不同，但此 ADF 管道使用的是同一个数据片段。 规范化可确保 Azure Blob 存储和 Azure 数据工厂中的所有元数据均在单个资产 `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet` 上可见。

## <a name="normalization-rules"></a>规范化规则

下面是 Azure Purview 应用的规范化规则。

### <a name="encode-curly-brackets"></a>对大括号进行编码
适用于：所有资产

之前： `https://myaccount.file.core.windows.net/myshare/{folderA}/folder{B/`

之后：`https://myaccount.file.core.windows.net/myshare/%7BfolderA%7D/folder%7BB/`

### <a name="trim-section-spaces"></a>剪裁节中的空格
适用于：Azure Blob、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure 数据工厂、Azure SQL 数据库、Azure SQL 数据库托管实例、Azure SQL 池、Azure Cosmos DB、Azure 认知搜索、Azure 数据资源管理器、Azure 数据共享、Amazon S3

之前： `https://myaccount.file.core.windows.net/myshare/  folder A/folderB   /`

之后：`https://myaccount.file.core.windows.net/myshare/folder A/folderB/`

### <a name="remove-hostname-spaces"></a>删除主机名中的空格
适用于：Azure Blob、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库、Azure SQL 数据库托管实例、Azure SQL 池、Azure Cosmos DB、Azure 认知搜索、Azure 数据资源管理器、Azure 数据共享、Amazon S3

之前： `https://myaccount .file. core.win dows. net/myshare/folderA/folderB/`

之后：`https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="remove-square-brackets"></a>删除方括号 
适用于：Azure SQL 数据库、Azure SQL 数据库托管实例、Azure SQL 池

之前： `mssql://foo.database.windows.net/[bar]/dbo/[foo bar]`

之后：`mssql://foo.database.windows.net/bar/dbo/foo%20bar`

> [!NOTE]
> 将对两个方括号之间的空格进行编码

### <a name="lowercase-scheme"></a>小写方案
适用于：Azure Blob、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库、Azure SQL 数据库托管实例、Azure SQL 池、Azure Cosmos DB、Azure 认知搜索、Azure 数据资源管理器、Amazon S3

之前： `HTTPS://myaccount.file.core.windows.net/myshare/folderA/folderB/`

之后：`https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-hostname"></a>小写主机名
适用于：Azure Blob、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库、Azure SQL 数据库托管实例、Azure SQL 池、Azure Cosmos DB、Azure 认知搜索、Azure 数据资源管理器、Amazon S3

之前： `https://myAccount.file.Core.Windows.net/myshare/folderA/folderB/`

之后：`https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-file-extension"></a>小写文件扩展名
适用于：Azure Blob、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Amazon S3

之前： `https://myAccount.file.core.windows.net/myshare/folderA/data.TXT`

之后：`https://myaccount.file.core.windows.net/myshare/folderA/data.txt`

### <a name="remove-duplicate-slash"></a>删除重复的斜杠
适用于：Azure Blob、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure 数据工厂、Azure SQL 数据库、Azure SQL 数据库托管实例、Azure SQL 池、Azure Cosmos DB、Azure 认知搜索、Azure 数据资源管理器、Azure 数据共享、Amazon S3

之前： `https://myAccount.file.core.windows.net//myshare/folderA////folderB/`

之后：`https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-adf-sections"></a>小写 ADF 节
适用于：Azure 数据工厂

之前： `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/fooBar/providers/Microsoft.DataFactory/factories/fooFactory/pipelines/barPipeline/activities/barFoo`

之后：`/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/foobar/providers/microsoft.datafactory/factories/foofactory/pipelines/barpipeline/activities/barfoo`

### <a name="convert-to-adl-scheme"></a>转换为 ADL 方案
适用于：Azure Data Lake Storage Gen1

之前： `https://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

之后：`adl://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

## <a name="next-steps"></a>后续步骤

[在 Azure Purview 数据映射的 Azure Blob 存储帐户中扫描](register-scan-azure-blob-storage-source.md)。 