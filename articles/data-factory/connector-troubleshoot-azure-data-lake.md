---
title: 排查 Azure Data Lake Storage 连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 Azure Data Lake Storage Gen1 和 Gen2 连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 1c36fa5295acafb96e57484cf34429091dd634e9
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390371"
---
# <a name="troubleshoot-the-azure-data-lake-storage-connectors-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的 Azure Data Lake Storage 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供有关排查 Azure 数据工厂和 Azure Synapse 中常见的 Azure Data Lake Storage Gen1 和 Gen2 连接器问题的建议。

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>错误消息：基础连接已关闭:无法建立 SSL/TLS 安全通道的信任关系。

- **症状**：复制活动失败，出现以下错误： 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **原因**：在 TLS 握手期间，证书验证失败。

- **解决方法**：要解决此问题，请使用暂存复制跳过对 Azure Data Lake Storage Gen1 的传输层安全性 (TLS) 验证。 你需要重现此问题并收集网络监视器 (netmon) 的跟踪数据，然后与网络团队合作来检查本地网络配置。

    :::image type="content" source="./media/connector-troubleshoot-guide/adls-troubleshoot.png" alt-text="用于解决问题的 Azure Data Lake Storage Gen1 连接关系图。":::


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>错误消息：远程服务器返回了错误：(403)已禁止

- **症状**：复制活动失败，出现以下错误： 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **原因**：一个可能原因是，你使用的服务主体或托管身份无权访问某个文件夹或文件。

- **解决方法**：授予对要复制的所有文件夹和子文件夹的相应权限。 有关详细信息，请参阅[向/从 Azure Data Lake Storage Gen1 复制数据](connector-azure-data-lake-store.md#linked-service-properties)。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>错误消息：无法使用服务主体来获取访问令牌。 ADAL 错误: service_unavailable

- **症状**：复制活动失败，出现以下错误：

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **原因**：如果 Azure Active Directory 拥有的 Service Token Server (STS) 不可用（即太忙而无法处理请求），则会返回 HTTP 错误 503。 

- **解决方法**：数分钟后重新运行复制活动。

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>错误代码：ADLSGen2OperationFailed

- 消息：`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

  | 原因分析                                               | 建议                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 如果该错误是由 Azure Data Lake Storage Gen2 引发的，则表明某个操作失败。| 请查看 Azure Data Lake Storage Gen2 引发的错误的详细消息。 如果错误是暂时性故障，请重试该操作。 如需更多帮助，请联系 Azure 存储支持部门并提供错误消息中的请求 ID。 |
  | 如果错误消息包含“禁止”这一字符串，则你使用的服务主体或托管标识可能没有足够的权限访问 Azure Data Lake Storage Gen2。 | 若要排查此错误，请参阅[在 Azure Data Lake Storage Gen2 中复制和转换数据](./connector-azure-data-lake-storage.md#service-principal-authentication)。 |
  | 如果错误消息包含字符串“InternalServerError”，则该错误是由 Azure Data Lake Storage Gen2 返回的。 | 此错误可能是由暂时性故障导致的。 如果是，请重试操作； 如果问题仍然存在，请联系 Azure 存储支持部门并提供错误消息中的请求 ID。 |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>“请求 Azure Data Lake Storage Gen2 帐户导致超时”错误

- **消息**： 
  * 错误代码 = `UserErrorFailedBlobFSOperation`
  * 错误消息 = `BlobFS operation failed for: A task was canceled.`

- **原因**：此问题是由 Azure Data Lake Storage Gen2 接收器超时错误引起的，此错误通常发生在自承载集成运行时 (IR) 计算机上。

- **建议**： 

    - 如果可能，请将自承载 IR 计算机和目标 Azure Data Lake Storage Gen2 帐户置于同一区域中。 这可避免随机超时错误，还可提高性能。

    - 检查是否有特殊的网络设置（例如 ExpressRoute），并确保网络具有足够的带宽。 建议在总体带宽较低时调低自承载 IR 并发作业设置。 这样做有助于避免跨多个并发作业的网络资源竞争。

    - 如果文件大小适中或较小，请为非二进制复制使用较小的块大小，以缓解此类超时错误。 有关详细信息，请参阅 [Blob 存储放置块](/rest/api/storageservices/put-block)。

       若要指定自定义块大小，请在 JSON 文件编辑器中编辑属性，如下所示：
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
