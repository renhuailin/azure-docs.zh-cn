---
title: 会话管理 REST API
description: 描述如何管理会话
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 9eb1bb87792dbc61e7c85dbc20c136499f23f67c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530206"
---
# <a name="use-the-session-management-rest-api"></a>使用会话管理 REST API

若要使用 Azure 远程呈现功能，需要创建一个 *会话*。 每个会话对应于要在 Azure 中分配的、客户端设备可以连接到的服务器。 当设备连接时，服务器将呈现请求的数据，并将结果作为视频流提供。 在会话创建过程中，您选择了要在哪 [种服务器](../reference/vm-sizes.md) 上运行，这会确定定价。 会话不再需要，应停止。 如果未手动停止，它将在会话的 *租约时间* 到期时自动关闭。

## <a name="rest-api-reference"></a>REST API 参考

可在 [此处](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering) 找到 REST API 引用，并在 [此处](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)找到 swagger 定义。
我们在 "*脚本*" 文件夹（称为 *RenderingSession.ps1*）中的 " [ARR 示例" 存储库](https://github.com/Azure/azure-remote-rendering)中提供了一个 PowerShell 脚本，它演示了如何使用我们的服务。 此脚本及其配置如下所述： [PowerShell 脚本示例](../samples/powershell-example-scripts.md)。
我们还提供适用于 [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering)、Java 和 Python 的 sdk。

> [!IMPORTANT]
> 延迟是使用远程呈现时的一个重要因素。 为了获得最佳体验，请在最接近你的区域中创建会话。 可以使用 [Azure 延迟测试](https://www.azurespeed.com/Azure/Latency) 来确定离您最近的区域。

> [!IMPORTANT]
> 要使客户端设备能够连接到呈现会话，需要使用 ARR 运行时 SDK。 这些 Sdk 在 [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering?view=remoterendering) 和 [c + +](https://docs.microsoft.com/cpp/api/remote-rendering/)中可用。 除了连接到服务以外，这些 Sdk 还可用于启动和停止会话。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 前端 API 进行身份验证](frontend-apis.md)
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
