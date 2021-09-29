---
title: 参考：表单识别器客户端库 3.0.0 和 REST API v2.0
titleSuffix: Azure Cognitive Services
description: 使用表单识别器客户端库 v3.0.0 或 REST API v2.0 创建一个表单处理应用，该应用从自定义文档中提取键值对和表数据。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 05/25/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp
keywords: 表单处理, 自动化数据处理
ms.openlocfilehash: 32dcfb1ba1e413e666e6a3d27ef9f03dbce81978
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641724"
---
# <a name="reference-azure-form-recognizer-client-library-v300-and-rest-api-v20"></a>参考：Azure 表单识别器客户端库 v3.0.0 和 REST API v2.0

>[!IMPORTANT]
>
> * 本指南基于 Azure 表单识别器 SDK v3.0.0 和 REST API v2.0。 可以在我们的[快速入门](../quickstarts/client-library.md)文章中找到适用于最新版本的文档和代码示例。
>
>* 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产环境，请使用安全方法来存储和访问凭据。 有关详细信息，请参阅认知服务[安全性](../../../cognitive-services/cognitive-services-security.md)文章。

开始通过你选择的编程语言来使用 Azure 表单识别器。 Azure 表单识别器是一种 [Azure 应用的 AI 服务](../../../applied-ai-services/index.yml)，可让用户使用机器学习技术构建自动化数据处理软件。 从表单文档中识别并提取文本、键/值对、选中标记、表数据等内容 &mdash; 该服务将输出结构化数据（其中包括原始文件中的关系）。 可通过 REST API 或 SDK 使用表单识别器。 请遵循以下步骤安装 SDK 包并试用基本任务的示例代码。

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK v3.0.0](includes/csharp-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK v3.0.0](includes/java-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK v3.0.0](includes/javascript-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK v3.0.0](includes/python-v3-0-0.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API v2.0](includes/rest-api-v2-0.md)]

::: zone-end