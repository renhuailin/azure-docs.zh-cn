---
title: 安装并运行异常检测器 API 的 Docker 容器
titleSuffix: Azure Cognitive Services
description: 使用异常探测器 API 算法，通过 Docker 容器查找本地数据中的异常。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbullwin
ms.custom: cog-serv-seo-aug-2020
keywords: 本地、Docker、容器、流式处理、算法
ms.openlocfilehash: 9a2481705ef1bed5a4b6d20bdfcbe68022c5f9e2
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057372"
---
# <a name="install-and-run-docker-containers-for-the-anomaly-detector-api"></a>安装并运行异常检测器 API 的 Docker 容器 

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

通过容器可以在自己的环境中使用异常检测器 API。 容器非常适合用于满足特定的安全性和数据管理要求。 本文介绍如何下载、安装以及运行异常检测器容器。

异常检测器提供单个 Docker 容器以在本地使用 API。 容器用途：
* 针对数据使用异常检测器的算法
* 监视流数据，并在实时发生异常时对其进行检测。
* 以批的形式检测整个数据集中的异常。 
* 以批的形式检测数据集中的走向更改点。
* 调整异常情况检测算法的敏感程度，以更好地适应你的数据。

有关 API 的详细信息，请参阅：
* [详细了解异常检测器 API 服务](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

使用异常检测器容器之前，必须满足以下先决条件：

|必须|目的|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。|
|异常检测器资源 |若要使用这些容器，必须具有：<br><br>一个用于获取关联 API 密钥和终结点 URI 的 Azure 异常检测器资源。 这两个值都可以从 Azure 门户中的异常检测器“概述”和“密钥”页获取；必须获取这两个值才能启动该容器。<br><br>**{API_KEY}** ：“密钥”页上提供的两个可用资源密钥中的一个<br><br>**{ENDPOINT_URI}** ：“概述”页上提供的终结点|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](../../iot-edge/index.yml). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表显示了为每个异常检测器容器分配的最小和建议的 CPU 核心数和内存。

| QPS（每秒查询次数） | 最小值 | 建议 |
|-----------|---------|-------------|
| 10 QPS | 4 个内核，1 GB 内存 | 8 个内核，2 GB 内存 |
| 20 QPS | 8 个内核，2 GB 内存 | 16 个内核，4 GB 内存 |

每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下载容器映像。

| 容器 | 存储库 |
|-----------|------------|
| cognitive-services-anomaly-detector | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>适用于异常检测器容器的 Docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](anomaly-detector-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{ENDPOINT_URI}` 和 `{API_KEY}` 值的详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。

`docker run` 命令的[示例](anomaly-detector-container-configuration.md#example-docker-run-commands)可用。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行异常检测器容器
* 分配一个 CPU 核心和 4 GB 内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

### <a name="running-multiple-containers-on-the-same-host"></a>在同一主机上运行多个容器

若要运行多个公开端口的容器，请确保在运行每个容器时使用不同的端口。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

请将 `<container-registry>` 和 `<container-name>` 替换为所用容器的值。 这些容器不需要是同一容器。 可以在 HOST 上同时运行异常检测器和 LUIS 容器，也可以运行多个异常检测器容器。

在主机端口 5000 上运行第一个容器。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

在主机端口 5001 上运行第二个容器。


```bash
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

每个后续容器应位于不同的端口上。

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。

为容器 API 使用主机 http://localhost:5000。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](anomaly-detector-container-configuration.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

异常检测器容器使用 Azure 帐户上的异常检测器资源将帐单信息发送到 Azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](anomaly-detector-container-configuration.md)。

## <a name="summary"></a>摘要

本文介绍了与下载、安装和运行表单异常检测器容器相关的概念与工作流。 综上所述：

* 异常检测器为 Docker、以批形式和流形式封装的异常检测、预期的范围推理和敏感度优化提供了一个 Linux 容器。
* 从专用于容器的专用 Azure 容器注册表下载容器映像。
* 容器映像在 Docker 中运行。
* 可使用 REST API 或 SDK 通过指定容器的主机 URI 来调用异常检测器容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的时间序列数据）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](anomaly-detector-container-configuration.md)了解配置设置
* [向 Azure 容器实例中部署异常检测器容器](how-to/deploy-anomaly-detection-on-container-instances.md)
* [详细了解异常检测器 API 服务](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
