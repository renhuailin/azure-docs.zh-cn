---
title: 安装来自计算机视觉的“读取 OCR”Docker 容器
titleSuffix: Azure Cognitive Services
description: 使用来自计算机视觉的“读取 OCR”Docker 容器可从本地图像和文档中提取文本。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: 本地, OCR, Docker, 容器
ms.openlocfilehash: c2a12a675b2a169847cbe11c8cdf3d6aa468fcc8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438275"
---
# <a name="install-read-ocr-docker-containers"></a>安装 Read OCR Docker 容器

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

借助容器，你可以在自己的环境中运行计算机视觉 API。 容器非常适合用于满足特定的安全性和数据管理要求。 在本文中，你将了解如何下载、安装和运行计算机视觉容器。

“读取 OCR”容器用于从图像和文档中提取打印文本和手写文本，并支持 JPEG、PNG、BMP、PDF 和 TIFF 文件格式。 有关详细信息，请参阅[读取 API 操作指南](Vision-API-How-to-Topics/call-read-api.md)。

## <a name="read-32-container"></a>Read 3.2 容器

Read 3.2 OCR 容器提供的功能：
* 用于增强准确度的新模型。
* 对同一文档中的多种语言的支持。
* 对总共 73 种语言的支持。 请参阅 [OCR 支持的语言](./language-support.md#optical-character-recognition-ocr)完整列表。
* 既适用于文档又适用于图像的单个操作。
* 对较大文档和图像的支持。
* 置信度分数。
* 对同时包含打印文本和手写文本的文档的支持。
* 仅从文档中的选定页面提取文本的功能。
* 用于将默认文本行输出顺序更改为更自然的语言阅读顺序（仅限拉丁语）的选择。
* 作为手写样式或并非仅适用于拉丁语的文本行分类。

如果目前使用的是读取 2.0 容器，请参阅[迁移指南](read-container-migration-guide.md)，了解新版本中的更改。

## <a name="prerequisites"></a>先决条件

使用容器前，必须满足以下先决条件：

|必须|目的|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/install/#server) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|计算机视觉资源 |若要使用容器，必须具有：<br><br>Azure 计算机视觉资源和关联的 API 密钥及终结点 URI。 这两个值都可以在资源的“概述”和“密钥”页上找到，并且是启动容器所必需的。<br><br>**{API_KEY}** ：“密钥”页上提供的两个可用资源密钥中的一个<br><br>**{ENDPOINT_URI}** ：“概述”页上提供的终结点|

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="request-approval-to-run-the-container"></a>请求批准运行容器

填写并提交[请求表单](https://aka.ms/csgate)，以请求批准运行容器。 

[!INCLUDE [Request access to run the container](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>高级矢量扩展支持

主计算机是运行 docker 容器的计算机。 主机必须支持[高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2)。 可使用以下命令检查 Linux 主机是否提供 AVX2 支持：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> 需要主计算机来支持 AVX2。 如果没有 AVX2 支持，容器将无法正常运行。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

提供适用于读取的容器映像。

| 容器 | 容器注册表/存储库/映像名称 |
|-----------|------------|
| 读取 2.0-preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Read 3.2 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2` |

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下载容器映像。

### <a name="docker-pull-for-the-read-ocr-container"></a>适用于读取 OCR 容器的 Docker 拉取

# <a name="version-32"></a>[版本 3.2](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[版本 2.0-preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](computer-vision-resource-container-config.md)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{ENDPOINT_URI}` 和 `{API_KEY}` 值的详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。

`docker run` 命令的[示例](computer-vision-resource-container-config.md#example-docker-run-commands)可用。

# <a name="version-32"></a>[版本 3.2](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行读取 OCR 容器。
* 分配 8 个 CPU 核心和 18 千兆字节 (GB) 的内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

你也可以使用环境变量运行容器：

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
--env Eula=accept \
--env Billing={ENDPOINT_URI} \
--env ApiKey={API_KEY} \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[版本 2.0-preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行读取 OCR 容器。
* 分配 8 个 CPU 核心和 16 千兆字节 (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

你也可以使用环境变量运行容器：

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
--env Eula=accept \
--env Billing={ENDPOINT_URI} \
--env ApiKey={API_KEY} \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```
---


提供 `docker run` 命令的多个[示例](./computer-vision-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

如果需要更高的吞吐量（例如，在处理多页文件时），请考虑使用 [Azure 存储](../../storage/common/storage-account-create.md)和 [Azure 队列](../../storage/queues/storage-queues-introduction.md)[在 Kubernetes 群集上](deploy-computer-vision-on-premises.md)部署多个容器。

如果使用 Azure 存储来存储图像以进行处理，则可以创建在调用容器时要使用的[连接字符串](../../storage/common/storage-configure-connection-string.md)。

若要查找连接字符串，请执行以下操作：

1. 导航到 Azure 门户上的“存储帐户”，然后找到你的帐户。
2. 单击左侧导航列表中的“访问密钥”。
3. 连接字符串将位于“连接字符串”下面

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

# <a name="version-32"></a>[版本 3.2](#tab/version-3-2)

为容器 API 使用主机 `http://localhost:5000`。 可以在以下位置查看 Swagger 路径：`http://localhost:5000/swagger/vision-v3.2-read/swagger.json`。

# <a name="version-20-preview"></a>[版本 2.0-preview](#tab/version-2)

为容器 API 使用主机 `http://localhost:5000`。 可以在以下位置查看 Swagger 路径：`http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json`。

---

### <a name="asynchronous-read"></a>异步读取


# <a name="version-32"></a>[版本 3.2](#tab/version-3-2)

可以同时使用 `POST /vision/v3.2/read/analyze` 和 `GET /vision/v3.2/read/operations/{operationId}` 操作来异步读取图像，类似于计算机视觉服务使用相应 REST 操作的方式。 异步 POST 方法将返回一个 `operationId`，它用作 HTTP GET 请求的标识符。


在 Swagger UI 中，选择 `Analyze` 以在浏览器中将其展开。 然后选择“试用” > “选择文件”。 在本示例中，我们将使用以下图像：

![制表符与空格](media/tabs-vs-spaces.png)

异步 POST 成功运行后，它会返回 HTTP 202 状态代码。 作为响应的一部分，有一个 `operation-location` 标头，其中包含请求的结果终结点。

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` 是完全限定的 URL，可通过 HTTP GET 访问。 以下是从上图执行 `operation-location` URL 的 JSON 响应：

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[版本 2.0-preview](#tab/version-2)

可以同时使用 `POST /vision/v2.0/read/core/asyncBatchAnalyze` 和 `GET /vision/v2.0/read/operations/{operationId}` 操作来异步读取图像，类似于计算机视觉服务使用相应 REST 操作的方式。 异步 POST 方法将返回一个 `operationId`，它用作 HTTP GET 请求的标识符。

在 Swagger UI 中，选择 `asyncBatchAnalyze` 以在浏览器中将其展开。 然后选择“试用” > “选择文件”。 在本示例中，我们将使用以下图像：

![制表符与空格](media/tabs-vs-spaces.png)

异步 POST 成功运行后，它会返回 HTTP 202 状态代码。 作为响应的一部分，有一个 `operation-location` 标头，其中包含请求的结果终结点。

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` 是完全限定的 URL，可通过 HTTP GET 访问。 以下是从上图执行 `operation-location` URL 的 JSON 响应：

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> 如果将多个 Read OCR 容器部署在负载均衡器之后（例如，在 Docker Compose 或 Kubernetes 下面），则必须有外部缓存。 由于进行处理的容器和 GET 请求容器可能不是同一个容器，因此外部缓存会存储结果并在容器之间共享这些结果。 有关缓存设置的详细信息，请参阅[配置计算机视觉 Docker 容器](./computer-vision-resource-container-config.md)。

### <a name="synchronous-read"></a>同步读取

可以使用以下操作来同步读取图像。 

# <a name="version-32"></a>[版本 3.2](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[版本 2.0-preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

完整读取图像后，API 才会返回 JSON 响应。 唯一的例外是发生错误的情况。 发生错误时，将返回以下 JSON：

```json
{
    "status": "Failed"
}
```

JSON 响应对象具有与异步版本相同的对象图。 如果你是 JavaScript 用户并且需要类型安全性，则可以使用 TypeScript 强制转换 JSON 响应。

有关示例用例，请在此处查看 <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">TypeScript 沙盒</a> ，然后选择 **运行** 以直观显示其易用性。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](./computer-vision-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

认知服务容器使用 Azure 帐户中的相应资源向 Azure 发送计费信息。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./computer-vision-resource-container-config.md)。 

## <a name="summary"></a>摘要

在本文中，你已学习相关概念，以及计算机视觉容器的下载、安装和运行工作流。 综上所述：

* 计算机视觉为 Docker 提供 Linux 容器，用于封装读取。
* 读取容器映像需要应用程序才能运行。 
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用读取 OCR 容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](computer-vision-resource-container-config.md)了解配置设置
* 查看 [OCR 概述](overview-ocr.md)，了解有关识别印刷文本和手写文本的详细信息
* 如需详细了解该容器支持的方法，请参阅[读取 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)。
* 参阅[常见问题解答 (FAQ)](FAQ.yml)，以解决与计算机视觉功能相关的问题。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
