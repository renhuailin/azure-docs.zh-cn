---
title: 资产转换 REST API
description: 介绍如何通过 REST API 转换资产
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e3db4b9c9b4a05142f1327f681b067748cb1a2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951634"
---
# <a name="use-the-model-conversion-rest-api"></a>使用模型转换 REST API

[模型转换](model-conversion.md)服务通过 [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer) 进行控制。 此 API 可用于创建转换、获取转换属性以及列出现有转换。

## <a name="rest-api-reference"></a>REST API 参考

远程渲染 REST API 参考文档可以在[此处](/rest/api/mixedreality/2021-01-01/remoterendering)找到，swagger 定义可以在[此处](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)找到。

我们在 [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)的“Scripts”文件夹中提供了一个名为“Conversion.ps1”的 PowerShell 脚本，该脚本演示了我们的服务的用法。 该脚本及其配置如下所述：[示例 PowerShell 脚本](../../samples/powershell-example-scripts.md)。 我们还提供适用于 [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) 和 [Java]( https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md) 的 SDK。

## <a name="next-steps"></a>后续步骤

- [将 Azure Blob 存储用于模型转换](blob-storage.md)
- [模型转换](model-conversion.md)