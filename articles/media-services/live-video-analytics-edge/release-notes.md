---
title: IoT Edge 上的 Azure 实时视频分析发行说明 - Azure
description: 本主题提供 IoT Edge 上的 Azure 实时视频分析版本的发行说明、改进、bug 修复和已知问题。
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 67b30cfbcb374dbe8773023f67b77ef43b35f3ba
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372039"
---
# <a name="azure-live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge 上的 Azure 实时视频分析发行说明

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

本文提供以下事项的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

<hr width=100%>

## <a name="january-12-2021"></a>2021 年 1 月 12 日

该模块 2021 年 1 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 2 (live-video-analytics:2)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。
### <a name="bug-fixes"></a>Bug 修复 

* 信号门处理器中的 `ActivationSignalOffset`、`MinimumActivationTime` 和 `MaximumActivationTime` 字段被错误地设置为必需属性。 它们现在是可选属性。
* 修复了一个用法 bug，该 bug 会导致 IoT Edge 模块上的实时视频分析在部署到某些区域时崩溃。

<hr width=100%>

## <a name="december-14-2020"></a>2020 年 12 月 14 日
此版本是 IoT Edge 上实时视频分析的公共预览刷新版本。 发行标记为

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>模块更新
* 添加了对每个图形拓扑使用多个 HTTP 扩展处理器和 gRPC 扩展处理器的支持。
* 添加了对[接收器节点的磁盘空间管理](upgrading-lva-module.md#disk-space-management-with-sink-nodes)的支持。
* `MediaGraphGrpcExtension` 节点现在支持 [extensionConfiguration](grpc-extension-protocol.md) 属性，允许在单个 gRPC 服务器中使用多个 AI 模型。
* 添加了对以 [Prometheus 格式](https://prometheus.io/docs/practices/naming/)收集实时视频分析模块指标的支持。 详细了解如何[收集指标并在 Azure Monitor 中查看。](monitoring-logging.md#azure-monitor-collection-via-telegraf) 
* 添加了筛选输出选择的功能。 在 `outputSelectors` 的帮助下，可以将单纯的音频、单纯的视频或音频和视频一起传递给任何图节点  。 
* 帧速率筛选器处理器已弃用。  
    * 帧速率管理现在在图形扩展处理器节点本身中可用。

### <a name="visual-studio-code-extension"></a>Visual Studio Code 扩展
* 发布了 [IoT Edge 上的实时视频分析](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)（一个 Visual Studio Code 扩展），用于帮助你管理 LVA 媒体图。 此扩展与 LVA 2.0 模块一起使用，并提供了一个精美且易用的图形界面来编辑和管理媒体图。
## <a name="september-22-2020"></a>2020 年 9 月 22 日

此模块 2020 年 9 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 新的图形扩展节点 [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) 可以与认知服务中的[空间分析](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)（预览版）模块集成。
* 添加了对 Linux ARM64 设备的支持 - 使用[手动步骤](deploy-iot-edge-device.md)部署到此类设备。

### <a name="documentation-updates"></a>文档更新

* 若要了解如何在 Azure Stack Edge 设备上使用 IoT Edge 上的实时视频分析，请参阅[此处说明](deploy-azure-stack-edge-how-to.md)。
* 使用[自定义视觉服务](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)开发特定于方案的计算机视觉模型并使用它来实时[分析实时视频](custom-vision-tutorial.md)的新教程。

<hr width=100%>

## <a name="august-19-2020"></a>2020 年 8 月 19 日

此模块 2020 年 8 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 现在，你可以使用 gRPC 框架在 IoT Edge 的实时视频分析和自定义扩展之间获得高数据内容传输性能。 请参阅[快速入门](analyze-live-video-use-your-grpc-model-quickstart.md)的说明开始操作。
* 实时视频分析的区域部署更广泛，且只更新了云服务。  
* 实时视频分析现已在全球超过 25 个区域推出。 下面是所有可用区域的[列表](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。  
* 快速入门的[设置](https://aka.ms/lva-edge/setup-resources-for-samples)已更新，并支持新区域。
    * 对于已设置资源的用户，不需要进行任何操作

### <a name="bug-fixes"></a>Bug 修复 

* 在设置脚本中不再使用已弃用的 Azure 扩展

<hr width=100%>

## <a name="july-13-2020"></a>2020 年 7 月 13 日

此模块 2020 年 7 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 现在，你可以创建在信号门处理器节点下游具有资产接收节点和文件接收节点的图拓扑。 有关示例，请参阅此[拓扑](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files)。

### <a name="bug-fixes"></a>Bug 修复

* 对所需属性验证的改进

<hr width=100%>

## <a name="june-1-2020"></a>2020 年 6 月 1 日

此版本是 IoT Edge 上的实时视频分析的第一个公共预览版本。 发行标记为

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>支持的功能

* 使用所选的 AI 模块分析实时视频流，还可以选择在边缘设备上或云中录制视频
* 在 IoT Edge [支持](../../iot-edge/support.md)的 Linux AMD64 操作系统上使用
* 使用 Azure 门户或 Visual Studio Code 通过 IoT 中心部署和配置模块
* 通过以下直接方法调用来远程或本地管理[图形拓扑和图形实例](media-graph-concept.md#media-graph-topologies-and-instances)

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>后续步骤

[概述](overview.md)
