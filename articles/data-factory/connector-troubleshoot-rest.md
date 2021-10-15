---
title: 排查 REST 连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 REST 连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: dcf49b00450836aafdb5b9772744914b8e5cf0e2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390365"
---
# <a name="troubleshoot-the-rest-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂 和 Azure Synapse 中的 REST 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供了一些建议，用于排查 Azure 数据工厂 和 Azure Synapse 中常见的 REST 连接器问题。

## <a name="error-code-restsinkcallfailed"></a>错误代码：RestSinkCallFailed

- **消息**：`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **原因**：如果数据工厂或 Synapse 管道通过 HTTP 协议与 REST 终结点通信，并且请求操作失败，则会发生此错误。

- **建议**：检查错误消息中的 HTTP 状态代码或消息，并修复远程服务器问题。

## <a name="error-code-restsourcecallfailed"></a>错误代码：RestSourceCallFailed

- **消息**：`The HttpStatusCode %statusCode; indicates failure.&#xA;Request URL: %requestUri;&#xA;Response payload:%payload;`

- **原因**：当 Azure 数据工厂通过 HTTP 协议与 REST 终结点通信，并且请求操作失败时，发生此错误。

- **建议**：检查错误消息中的 HTTP 状态代码、请求 URL 或响应工作负载，并修复远程服务器问题。

## <a name="error-code-restsinkunsupportedcompressiontype"></a>错误代码：RestSinkUNSupportedCompressionType

- **消息**：`User Configured CompressionType is Not Supported By Azure Data Factory：%message;`

- **建议**：检查 REST 接收器支持的压缩类型。

## <a name="unexpected-network-response-from-the-rest-connector"></a>来自 REST 连接器的意外网络响应

- **故障描述**：终结点有时从 REST 连接器收到意外响应（400、401、403、500）。

- **原因**：构造 HTTP 请求时，REST 源连接器使用链接服务/数据集/复制源的 URL 和 HTTP 方法/标头/正文作为参数。 此问题很可能是由一个或多个指定参数中的某些错误引起的。

- **解决方法**： 
    - 在命令提示窗口中使用“curl”查看该问题是否是参数导致的（应始终包含“接受”和“用户代理”标头） ）：
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      如果命令返回相同的意外响应，请使用“curl”修复前面的参数，直到它返回预期的响应。 

      也可使用“curl--help”来更高级地使用命令。

    - 如果只有 REST 连接器返回意外响应，请联系 Microsoft 支持人员进行进一步的故障排除。
    
    - 请注意，“curl”可能不适合重现 SSL 证书验证问题。 在某些情况下，“curl”命令成功执行，没有遇到任何 SSL 证书验证问题。 但是，当在浏览器中执行相同的 URL 时，实际上不会返回任何 SSL 证书以供客户端与服务器建立信任。

      对于上述情况，建议使用 Postman 和 Fiddler 之类的工具 。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
