---
title: 用于语音容器的批处理工具包
titleSuffix: Azure Cognitive Services
description: 使用批处理工具包缩放语音容器请求。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: 0ecdb23eddcb47f8a50c2d10cab601787354a81b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745652"
---
# <a name="batch-processing-kit-for-speech-containers"></a>用于语音容器的批处理工具包

使用批处理工具包来补充和横向扩展语音容器上的工作负载。 此开放源代码实用程序以容器的形式提供，有助于跨任意数量的本地和基于云的语音容器终结点对大量音频文件进行批量听录。 

:::image type="content" source="media/containers/general-diagram.png" alt-text="显示示例批处理工具包容器工作流的关系图。":::

可在 [GitHub](https://github.com/microsoft/batch-processing-kit) 和 [Docker 中心](https://hub.docker.com/r/batchkit/speech-batch-kit/tags)免费使用批处理工具包容器。 你只需要为使用的语音容器[付费](speech-container-howto.md#billing)。

| 功能  | 说明  |
|---------|---------|
| 批处理音频文件分发     | 自动将大量文件分发到本地或基于云的语音容器终结点。 文件可以位于任何兼容 POSIX 的卷上，包括网络文件系统。       |
| 语音 SDK 集成 | 将常见标记传递到语音 SDK，这些标记包括：n-best 假说、说话人分类、语言、猥亵屏蔽。  |
|运行模式     | 在后台连续运行一次批处理客户端，或为音频文件创建 HTTP 终结点。         |
| 容错 | 在不丢失进度的情况下自动重试并继续听录，并区分哪些错误可以重试，哪些错误不可以重试。 |
| 终结点可用性检测 | 如果某个终结点变得不可用，批处理客户端将继续使用其他容器终结点进行听录。 终结点再次变得可用之后，客户端将自动开始使用该终结点。   |
| 终结点热交换 | 在运行时添加、删除或修改语音容器终结点，而不中断批处理进度。 更新是即时的。 |
| 实时记录 | 实时记录尝试的请求、时间戳和失败原因，以及每个音频文件的语音 SDK 日志文件。 |

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令下载最新的批处理工具包容器。

[!INCLUDE [pull-image-include](../../../includes/pull-image-include.md)]

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>终结点配置

批处理客户端采用指定本地容器终结点的 yaml 配置文件。 下面的示例可以写入 `/mnt/my_nfs/config.yaml` 中，下文的示例中使用了该文件。 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

此 yaml 示例在三台主机上指定了三个语音容器。 第一个主机由 IPv4 地址指定，第二个主机在与批处理客户端所在的同一 VM 上运行，第三个容器由另一个 VM 的 DNS 主机名指定。 `concurrency` 值指定可以在同一容器上运行的最大并发文件听录数。 `rtf`（实时因子）值是可选的，可用于优化性能。
 
批处理客户端可以动态检测终结点是否变为不可用（例如，由于容器重启或网络问题）以及何时再次变为可用。 听录请求将不会发送到不可用的容器，并且客户端将继续使用其他可用的容器。 你可以随时添加、删除或编辑终结点，而不会中断批处理的进度。


## <a name="run-the-batch-processing-container"></a>运行批处理容器
  
> [!NOTE] 
> * 此示例为配置文件和输入、输出以及日志目录使用相同的目录 (`/my_nfs`)。 可以对这些文件夹使用托管或 NFS 装载的目录。
> * 使用 `–h` 运行客户端将列出可用的命令行参数及其默认值。 
> * 仅 Linux 支持批处理容器。

使用 Docker `run` 命令启动容器。 这会在容器中启动一个交互式 shell。

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

运行批处理客户端：  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

以单一命令运行批处理客户端和容器：

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


客户端将开始运行。 如果某个音频文件在之前的运行中已听录，客户端将自动跳过该文件。 如果发生暂时性错误，则会发送文件进行自动重试，并且你可以区分希望客户端重试哪些错误。 出现听录错误时，客户端将继续进行听录，并且可以重试而不会丢失进度。  

## <a name="run-modes"></a>运行模式 

批处理工具包使用 `--run-mode` 参数提供三种模式。

#### <a name="oneshot"></a>[一次性](#tab/oneshot)

`ONESHOT` 模式可将单一批次的音频文件（来自输入目录和可选文件列表）听录到输出文件夹。

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="显示批处理工具包容器以一次性模式处理文件的关系图。":::

1. 定义批处理客户端将在 `config.yaml` 文件中使用的语音容器终结点。 
2. 将要进行听录的音频文件放入输入目录。  
3. 调用目录中的容器，该容器将开始处理这些文件。 如果音频文件已在具有相同输出目录的先前运行中进行听录，客户端将跳过该文件。 
4. 文件将从步骤 1 调度到容器终结点。
5. 日志和语音容器输出将返回到指定的输出目录。 

#### <a name="daemon"></a>[守护程序](#tab/daemon)

> [!TIP]
> 如果同时向输入目录添加了多个文件，则可以通过定期添加它们来提高性能。

`DAEMON` 模式听录指定文件夹中的现有文件，并在添加新的音频文件时连续转录这些文件。          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="显示批处理工具包容器以守护程序模式处理文件的关系图。":::

1. 定义批处理客户端将在 `config.yaml` 文件中使用的语音容器终结点。 
2. 调用输入目录中的容器。 批处理客户端将开始监视传入文件的目录。 
3. 设置到输入目录的持续音频文件交付。 如果音频文件已在具有相同输出目录的先前运行中进行听录，客户端将跳过该文件。 
4. 检测到文件写入或 POSIX 信号后，将触发容器来进行响应。
5. 文件将从步骤 1 调度到容器终结点。
6. 日志和语音容器输出将返回到指定的输出目录。 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` 模式是一种 API 服务器模式，它提供了一组基本的 HTTP 终结点，用于音频文件批处理提交、状态检查和长时间轮询。 它还允许使用 Python 模块扩展（或导入为子模块）进行编程使用。

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="显示批处理工具包容器以 REST 模式处理文件的关系图。":::

1. 定义批处理客户端将在 `config.yaml` 文件中使用的语音容器终结点。 
2. 向 API 服务器的终结点之一发送 HTTP 请求。 
        
    |终结点  |说明  |
    |---------|---------|
    |`/submit`     | 用于创建新批处理请求的终结点。        |
    |`/status`     | 用于检查批处理请求状态的终结点。 此连接将一直保持打开状态，直至批处理完成。       |
    |`/watch`     | 用于在批处理完成之前使用 HTTP 长轮询的终结点。        |

3. 音频文件从输入目录上传。 如果音频文件已在具有相同输出目录的先前运行中进行听录，客户端将跳过该文件。 
4. 如果将请求发送到 `/submit` 终结点，则会将这些文件从步骤 1 调度到容器终结点。
5. 日志和语音容器输出将返回到指定的输出目录。 

---

## <a name="logging"></a>日志记录

> [!NOTE]
> 如果批处理客户端变得太大，则可能会定期覆盖 run.log 文件。

客户端在 docker `-log_folder` 命令中由 `run` 参数指定的目录中创建 run.log 文件。 默认情况下，系统在调试级别捕获日志。 相同的日志会发送到 `stdout/stderr`，并根据 `-file_log_level` 或 `console_log_level` 参数进行筛选。 此日志仅在调试或需要发送跟踪以获得支持的情况下才需要。 日志记录文件夹还包含每个音频文件的语音 SDK 日志。

`-output_folder` 指定的输出目录将包含一个 run_summary.json 文件，该文件每隔 30 秒或在完成新的听录时定期重写 。 可以使用此文件在批处理过程中检查进度。 它还将在批处理完成后包含每个文件的最终运行统计信息和最终状态。 进程干净地退出时，即表示批处理已完成。 

## <a name="next-steps"></a>后续步骤

* [如何安装和运行容器](speech-container-howto.md)
