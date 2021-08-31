---
title: 安装并运行文本分析 API 的 Docker 容器
titleSuffix: Azure Cognitive Services
description: 使用文本分析 API 的 Docker 容器在本地执行自然语言处理，如情绪分析。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020, devx-track-azurecli
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/21/2021
ms.author: aahi
keywords: 本地, Docker, 容器, 情绪分析, 自然语言处理
ms.openlocfilehash: 82e247d51351417a987205b2c65ea26be737ec5c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473415"
---
# <a name="install-and-run-text-analytics-containers"></a>安装和运行文本分析容器

容器使你能够在自己的环境中运行文本分析 API，最适合特定安全性和数据管理要求。 以下文本分析容器可用：

* 情绪分析
* 语言检测
* 关键短语提取（预览版）
* 运行状况文本分析 

> [!NOTE]
> * 实体链接和 NER 当前不可用作容器。
> * 容器映像位置最近可能已更改。 阅读本文以查看此容器的更新位置。
> * 免费帐户限制为每月 5000 个文本记录，并且只有“免费”和“标准”[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics)才对容器有效 。 有关事务请求费率的更多信息，请参阅[数据限制](../concepts/data-limits.md)。

容器使你能够在自己的环境中运行文本分析 API，最适合特定安全性和数据管理要求。 文本分析容器提供对原始文本的高级自然语言处理，并且包含三项主要功能：情绪分析、关键短语提取和语言检测。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

使用文本分析容器之前，必须满足以下先决条件。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

* 在主机上安装 [Docker](https://docs.docker.com/)。 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 
    * 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。
    * 应该基本了解 [Docker 概念](https://docs.docker.com/get-started/overview/)。 
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="创建使用免费 (F0) 或标准 (S) [定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)的文本分析资源"  target="_blank">文本分析资源</a>。

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>主机要求和建议

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

下表显示了可用文本分析容器的最小和建议规范。 每个 CPU 核心必须至少为 2.6 千兆赫 (GHz) 或更快。 还列出了允许的每秒事务数 (TPS)。

|  | 最小主机规格 | 推荐主机规格 | 最小 TPS | 最大 TPS|
|---|---------|-------------|--|--|
| **语言检测**   | 1 核，2GB 内存 | 1 核，4GB 内存 |15 | 30| 
| **关键短语提取（预览版）**   | 1 核，2GB 内存 | 1 核，4GB 内存 |15 | 30| 
| **情绪分析**   | 1 核，2GB 内存 | 4 核，8GB 内存 |15 | 30|
| **运行状况文本分析 - 1 文档/请求**   |  4 核，10GB 内存 | 6 核，12GB 内存 |15 | 30|
| **运行状况文本分析 - 10 文档/请求**   |  6 核，16GB 内存 | 8 核，20GB 内存 |15 | 30|

CPU 核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

# <a name="sentiment-analysis"></a>[情绪分析](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[关键短语提取（预览版）](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[语言检测](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[运行状况文本分析](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

一旦容器位于主机上，请使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 容器将继续运行，直到停止它。

> [!IMPORTANT]
> * 以下各节中的 docker 命令使用反斜杠 `\` 作为行继续符。 根据主机操作系统的要求替换或删除字符。 
> * 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。
>   * 如果使用的是运行状况容器的文本分析，则[负责的 AI](/legal/cognitive-services/text-analytics/transparency-note-health) (RAI) 确认的值也必须为 `accept`。
> * 情绪分析和语言检测容器使用该 API 的 v3 版本，并且已正式发布。 关键短语提取容器使用该 API 的 v2 版本，并且处于预览阶段。

# <a name="sentiment-analysis"></a>[情绪分析](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[关键短语提取（预览版）](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[语言检测](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[运行状况文本分析](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。

为容器 API 使用主机 `http://localhost:5000`。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](../text-analytics-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

文本分析容器使用 Azure 帐户中的 _文本分析_ 资源向 Azure 发送账单信息。 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](../text-analytics-resource-container-config.md)。

## <a name="summary"></a>摘要

在本文中，我们已学习相关的概念，以及文本分析容器的下载、安装和运行工作流。 综上所述：

* 文本分析为 Docker 提供了三个 Linux 容器，其中封装了各种功能：
   * *情绪分析*
   * *关键短语提取（预览版）* 
   * *语言检测*
   * *运行状况文本分析*
* 从 Microsoft 容器注册表 (MCR) 下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用文本分析容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会向 Microsoft 发送客户数据（例如正在分析的文本）。

## <a name="next-steps"></a>后续步骤

* 请参阅[配置容器](../text-analytics-resource-container-config.md)了解配置设置。
