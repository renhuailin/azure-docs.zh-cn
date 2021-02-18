---
title: Azure 前门标准/高级版中的文件压缩疑难解答
description: 了解如何对 Azure 前门中的文件压缩问题进行故障排除。 本文介绍几种可能的原因。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098798"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>排查 Azure 前门标准/高级文件压缩问题

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

本文将帮助你排查 Azure 前门标准/高级文件压缩问题。

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="symptom"></a>症状

已启用路由的压缩，但未压缩返回文件。

> [!TIP]
> 要检查返回的文件是否已压缩，需要使用 [Fiddler](https://www.telerik.com/fiddler) 这类工具或浏览器的[开发人员工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)。  检查随缓存的 CDN 内容一起返回的 HTTP 响应标头。  如果存在名为 `Content-Encoding` 的标头，且其值为 **gzip**、**bzip2** 或 **deflate**，则内容已压缩。
> 
> ![Content-Encoding 标头](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>原因

有若干个可能的原因，包括：

* 请求的内容不适用于压缩。
* 没有为所请求的文件类型启用压缩。
* HTTP 请求未包含请求有效压缩类型的标头。
* 源正在发送分块内容。

## <a name="troubleshooting-steps"></a>疑难解答步骤

> [!TIP]
> 与部署新的终结点一样，AFD 配置更改需要一些时间才能通过网络传播。  通常情况下会在 90 分钟内应用更改。  如果这是首次为 CDN 终结点设置压缩，应考虑等待 1-2 个小时，以确保压缩设置传播到 POP。 
> 

### <a name="verify-the-request"></a>验证请求

首先，应仔细检查请求。 您可以使用浏览器的 **[开发人员工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** 来查看发出的请求。

* 验证请求是否发送到你的终结点 URL， `<endpointname>.z01.azurefd.net` 而不是源。
* 验证该请求包含 **Accept-encoding** 标头，并且该标头的值包含 **gzip**、**deflate** 或 **bzip2**。

![CDN 请求标头](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>验证压缩设置

在 [Azure 门户](https://portal.azure.com) 中导航到终结点，并在 "路由" 面板中选择 " **配置** " 按钮。 验证是否 **启用了** 压缩。

![CDN 压缩设置](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>在源服务器上检查请求是否包含 **Via** 标头

**Via** HTTP 标头指明了由代理服务器正在将请求传递到的 web 服务器。  默认情况下，当请求包含 **Via** 标头时，Microsoft IIS web 服务器不会压缩响应。  若要重写此行为，请执行以下操作：

* **Iis 6**：在 Iis 元数据库属性中设置 HcNoCompressionForProxies = "FALSE"。 有关信息，请参阅 [IIS 6 压缩](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))。
* **IIS 7 和更高**：在服务器配置中将 **noCompressionForHttp10** 和 **noCompressionForProxies** 都设置为 *False* 。 有关详细信息，请参阅 [HTTP 压缩](https://www.iis.net/configreference/system.webserver/httpcompression)。
