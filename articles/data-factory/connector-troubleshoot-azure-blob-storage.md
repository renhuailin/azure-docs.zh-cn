---
title: 排查 Azure Blob 存储连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 Azure Blob 存储连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e9a1c085e3d1d88d0897d46b924c5a9a21c747a2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390366"
---
# <a name="troubleshoot-the-azure-blob-storage-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的 Azure Blob 存储连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供有关排查 Azure 数据工厂和 Azure Synapse 中常见的 Azure Blob 存储连接器问题的建议。

## <a name="error-code-azurebloboperationfailed"></a>错误代码：AzureBlobOperationFailed

- **消息**：“Blob 操作失败。 ContainerName: %containerName;，path: %path;。”

- **原因**：Blob 存储操作存在问题。

- **建议**：若要查看错误详细信息，请参阅 [Blob 存储错误代码](/rest/api/storageservices/blob-service-error-codes)。 如需更多帮助，请联系 Blob 存储团队。


## <a name="invalid-property-during-copy-activity"></a>执行复制活动期间属性无效

- 消息：`Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **原因**：数据集中定义的类型与复制活动中定义的源或接收器类型不一致。

- **解决方法**：编辑数据集或管道 JSON 定义以使类型一致，然后重新运行部署。

## <a name="error-code-fipsmodeisnotsupport"></a>错误代码：FIPSModeIsNotSupport

- **消息**：`Fail to read data form Azure Blob Storage for Azure Blob connector needs MD5 algorithm which can't co-work with FIPS mode. Please change diawp.exe.config in self-hosted integration runtime install directory to disable FIPS policy following https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/runtime/enforcefipspolicy-element.`

- **原因**：在安装了自承载集成运行时的 VM 上启用了 FIPS 策略。

- **建议**：在安装了自承载集成运行时的 VM 上禁用 FIPS 模式。 Windows不建议使用 FIPS 模式。

## <a name="error-code-azureblobinvalidblocksize"></a>错误代码：AzureBlobInvalidBlockSize

- **消息**：`Block size should between %minSize; MB and 100 MB.`

- **原因**：块大小超过 Blob 限制。

## <a name="error-code-azurestorageoperationfailedconcurrentwrite"></a>错误代码：AzureStorageOperationFailedConcurrentWrite

- **消息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`

- **原因**：有多个并发复制活动运行或应用程序写入到同一文件。

## <a name="error-code-azureappendblobconcurrentwriteconflict"></a>错误代码：AzureAppendBlobConcurrentWriteConflict

- **消息**：`Detected concurrent write to the same append blob file, it's possible because you have multiple concurrent copy activities runs or applications writing to the same file '%name;'. Please check your ADF configuration and retry.`

- **原因**：存在多个并发写入请求，这会导致文件内容冲突。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
