---
title: 资产转换 REST API
description: 描述如何通过 REST API 转换资产
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: f33e5717cd5556e72d996e7e943867c16805e71b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705171"
---
# <a name="use-the-model-conversion-rest-api"></a>使用模型转换 REST API

[模型转换](model-conversion.md)服务通过[REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)来控制。 此 API 可用于创建转换、获取转换属性以及列出现有转换。

## <a name="rest-api-reference"></a>REST API 参考

可在 [此处](/rest/api/mixedreality/2021-01-01preview/remoterendering)找到 REST API 参考文档的远程呈现，并在 [此处](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)找到 swagger 定义。

我们在 "*脚本*" 文件夹（称为 *Conversion.ps1*）中的 " [ARR 示例" 存储库](https://github.com/Azure/azure-remote-rendering)中提供了一个 PowerShell 脚本，它演示了如何使用我们的服务。 此脚本及其配置如下所述： [PowerShell 脚本示例](../../samples/powershell-example-scripts.md)。 我们还提供适用于 [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering)、Java 和 Python 的 sdk。

## <a name="next-steps"></a>后续步骤

- [将 Azure Blob 存储用于模型转换](blob-storage.md)
- [模型转换](model-conversion.md)