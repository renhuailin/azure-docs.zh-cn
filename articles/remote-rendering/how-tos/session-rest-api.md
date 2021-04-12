---
title: 会话管理 REST API
description: 介绍如何管理会话
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4323884b2dee3eeccfe71ec7817d92467450e88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950019"
---
# <a name="use-the-session-management-rest-api"></a>使用会话管理 REST API

要使用 Azure 远程渲染功能，需要创建一个会话。 每个会话对应 Azure 中要分配的一个服务器，客户端设备可以连接到该服务器。 当设备连接时，服务器将渲染请求的数据，并以视频流形式提供结果。 在创建会话的过程中，选择要运行的[服务器种类](../reference/vm-sizes.md)，它们决定价格。 不再需要会话后，应将其停止。 如果未手动停止，它将在会话的租用时间到期后自动关闭。

## <a name="rest-api-reference"></a>REST API 参考

REST API 参考文件位于[此处](/rest/api/mixedreality/2021-01-01preview/remoterendering)，而 swagger 定义位于[此处](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)。
我们在 [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)的“Scripts”文件夹中提供了一个名为“RenderingSession.ps1”的 PowerShell 脚本，该脚本演示了我们服务的用法。 该脚本及其配置如下所述：[示例 PowerShell 脚本](../samples/powershell-example-scripts.md)。
我们还提供适用于 [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) 和 [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md) 的 SDK。

> [!IMPORTANT]
> 延迟是使用远程渲染时的一个重要因素。 为了获得最佳体验，请在离你最近的区域创建会话。 可以使用 [Azure 延迟测试](https://www.azurespeed.com/Azure/Latency)来确定离你最近的区域。

> [!IMPORTANT]
> 要将客户端设备连接到渲染会话，需要使用 ARR 运行时 SDK。 这些 SDK 可用于 [.NET](/dotnet/api/microsoft.azure.remoterendering) 和 [C++](/cpp/api/remote-rendering/)。 除连接到服务之外，这些 SDK 还可用来启动和停止会话。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 前端 API 进行身份验证](frontend-apis.md)
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)