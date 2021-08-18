---
title: 引入客户端 - 语音服务
titleSuffix: Azure Cognitive Services
description: 本文介绍 GitHub 上发布的一款工具，该工具使客户能够轻松快速地将音频文件推送到语音服务
services: cognitive-services
author: panosperiorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 069c0b587fd3ff69ca8e883850d44b84ab12486f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743575"
---
# <a name="ingestion-client-for-the-speech-service"></a>用于语音服务的引入客户端

引入客户端是 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/ingestion) 上发布的一款工具，该工具使客户能通过语音服务快速转录音频文件，并且只需少量或根本无需开发工作。 它的工作方式是将专用的 [Azure 存储](https://azure.microsoft.com/product-categories/storage/)帐户绑定到自定义 [Azure Functions](https://azure.microsoft.com/services/functions/)，该自定义 Azure Functions 以无服务器方式使用 [REST API](rest-speech-to-text.md) 或 [SDK](speech-sdk.md) 来将转录请求传递给服务。  

## <a name="architecture"></a>体系结构

该工具可帮助客户了解转录文件的质量，他们无需事先进行开发投资。 该工具连接一些资源以转录位于专用 [Azure 存储容器](https://azure.microsoft.com/product-categories/storage/)中的音频文件。

该工具在内部使用 3.0 Batch API 或 SDK，并遵循最佳做法来处理向上扩展、重试和故障转移。 下面的示意图展示了相关资源和连接。

:::image type="content" source="media/ingestion-client/architecture-1.png" alt-text="引入客户端体系结构。":::

[引入客户端的入门指南](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/ingestion/ingestion-client/Setup/guide.md)介绍了如何设置和使用该工具。

> [!IMPORTANT]
> 定价取决于操作模式（批处理和实时），以及所选的 Azure Function SKU。 默认情况下，该工具会创建一个高级 Azure Function SKU 来处理大量音频。 有关详细信息，请访问[定价](https://azure.microsoft.com/pricing/details/functions/)页面。

Microsoft [语音 SDK](speech-sdk.md) 和[语音转文本 REST API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) 都可用于获取转录文件。 如指南中所述，该选择会影响总体成本。 

> [!TIP]
> 可以使用工具和生产中生成的解决方案来处理大量音频。

## <a name="tool-customization"></a>工具自定义

该工具可为客户快速显示结果。 可以将该工具自定义为你首选的 SKU 和设置。 可以在 [Azure 门户](https://portal.azure.com)中编辑 SKU，[GitHub 上提供相关代码](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)。

> [!NOTE]
> 建议在同一专用资源组中创建资源，以更轻松地了解和跟踪成本。

## <a name="next-steps"></a>后续步骤

* [获取语音服务试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [详细了解语音 SDK](./speech-sdk.md)
* [了解语音 CLI 工具](./spx-overview.md)
