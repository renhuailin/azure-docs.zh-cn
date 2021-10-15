---
title: 升级具有 Azure Data Lake Storage Gen2 功能的 Azure Blob 存储 | Microsoft Docs
description: 演示如何使用资源管理器模板从 Azure Blob 存储升级到 Data Lake Storage。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: b1f612ee189d47529033fdd9e6c7e7df0f523df9
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129459015"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>升级具有 Azure Data Lake Storage Gen2 功能的 Azure Blob 存储

本文可帮助你解锁文件和目录级安全性和更快的操作等功能。 这些功能广泛用于大数据分析工作负荷，并统称为 Azure Data Lake Storage Gen2。 

若要详细了解这些功能并评估此升级对工作负荷、应用程序、成本、服务集成、工具、功能和文档的影响，请参阅[升级具有 Azure Data Lake Storage Gen2 功能的 Azure Blob 存储](upgrade-to-data-lake-storage-gen2.md)。

> [!IMPORTANT]
> 升级是单向的。 执行升级后，无法还原帐户。 建议在非生产环境中验证升级。

## <a name="perform-the-upgrade"></a>执行升级

1. 登录到 [Azure 门户](https://portal.azure.com/)即可开始操作。

2. 找到存储帐户并显示帐户概览。

3. 选择“Data Lake Gen2 迁移”。

   此时将出现“升级到具有 Azure Data Lake Gen2 功能的存储帐户”配置页。

   > [!div class="mx-imgBorder"]
   > ![配置页](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. 展开“步骤 1: 在升级之前查看帐户更改”部分，然后单击“查看并同意更改”。

5. 在“查看帐户更改”页上，选中相应的复选框，然后单击“同意更改”。

6. 展开“步骤 2: 升级之前验证帐户”部分，然后单击“开始验证”。

   如果验证失败，请选择“查看错误”链接

   > [!div class="mx-imgBorder"]
   > ![查看错误链接](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   然后，从“error.json”文件的上下文菜单中选择“下载”。

   > [!div class="mx-imgBorder"]
   > ![错误 json 页面](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   打开下载的文件，确定帐户未通过验证步骤的原因。 

   下面的 JSON 指示在该帐户上启用了不兼容的功能。 在这种情况下，您将禁用该功能，然后重新启动验证过程。

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. 成功验证你的帐户后，展开“步骤 3: 升级帐户”部分，然后单击“开始升级”。

   > [!IMPORTANT]
   > 帐户升级时，将禁用写入操作。 读取操作未被禁用，但我们强烈建议你挂起读取操作，因为这些操作可能会使升级过程不稳定。

   成功完成迁移后，将显示类似于以下内容的消息。 

   > [!div class="mx-imgBorder"]
   > ![迁移已完成页](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

## <a name="migrate-data-workloads-and-applications"></a>迁移数据、工作负载和应用程序 

1. 将[工作负荷中的服务](data-lake-storage-integrate-with-azure-services.md)配置为指向 Blob 服务终结点或 Data Lake storage 终结点。

   > [!div class="mx-imgBorder"]
   > ![帐户终结点](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. 对于使用 Windows Azure 存储 Blob 驱动程序或 [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) 驱动程序的 Hadoop 工作负荷，请确保修改它们以使用 [Azure Blob 文件系统 (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) 驱动程序。 不同于对 Blob 服务终结点发出请求的 WASB 驱动程序，ABFS 驱动程序将向帐户的 Data Lake Storage 终结点发出请求。

2. 测试自定义应用程序，以确保在你的升级帐户中正常工作。 

   [Data Lake Storage 上的多协议访问](data-lake-storage-multi-protocol-access.md)允许大多数应用程序继续使用 Blob API，而无需修改。 如果遇到问题或想要使用 API 来处理目录操作和 ACL，请考虑移动一些代码以使用 Data Lake Storage Gen2 API。 请参阅适用于 [.NET](data-lake-storage-directory-file-acl-dotnet.md)、[Java](data-lake-storage-directory-file-acl-java.md)、[Python](data-lake-storage-directory-file-acl-python.md)、[Node.js](data-lake-storage-acl-javascript.md) 和 [REST](/rest/api/storageservices/data-lake-storage-gen2) 的指南。 

3. 测试任何自定义脚本，以确保在你的升级帐户中正常工作。 

   与 Blob API 一样，许多脚本可能不需要修改即可工作。 但是，如果需要，可以升级脚本文件以使用 Data Lake Storage Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md) 和 [Azure CLI 命令](data-lake-storage-directory-file-acl-cli.md)。
 

## <a name="see-also"></a>另请参阅

[Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)