---
title: IoT Edge 上的实时视频分析发行说明 - Azure
description: 本主题提供 IoT Edge 上的实时视频分析版本的发行说明、改进、bug 修复和已知问题。
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 328fe97c4e03f039a1224d13ce6712ccff06b3b7
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629770"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge 上的实时视频分析发行说明

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

本文提供以下事项的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

<hr width=100%>

## <a name="january-12-2021"></a>2021 年 1 月 12 日

此版本标记适用于模块2021年1月的刷新：

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用2个 (实时视频分析： 2) 的标记。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。
### <a name="bug-fixes"></a>Bug 修复 

* `ActivationSignalOffset` `MinimumActivationTime` `MaximumActivationTime` 未正确地将字段和信号入口处理器设置为所需属性。 它们现在是 **可选** 属性。
* 修复了一个使用 bug，该 bug 会导致 IoT Edge 模块上的实时视频分析在特定区域中部署时崩溃。

<hr width=100%>

## <a name="december-14-2020"></a>2020 年 12 月 14 日
此版本是 IoT Edge 上实时视频分析的公共预览刷新版本。 发行标记为

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>模块更新
* 添加了对每个图形拓扑使用多个 HTTP 扩展处理器和 gRPC 扩展处理器的支持。
* 添加了对接收器节点的磁盘空间管理的支持。
* `MediaGraphGrpcExtension` 节点现在支持 [extensionConfiguration](grpc-extension-protocol.md) 属性，允许在单个 gRPC 服务器中使用多个 AI 模型。
* 添加了对以 [Prometheus 格式](https://prometheus.io/docs/practices/naming/)收集实时视频分析模块度量值的支持。 详细了解如何 [在 Azure Monitor 中收集指标和视图。](monitoring-logging.md#azure-monitor-collection-via-telegraf) 
* 添加了筛选输出选择的功能。 既可以将 **音频** 和 **视频、视频或****音频和视频** 传递 `outputSelectors` 到任何图形节点的帮助。 
* 帧速率筛选器处理器已弃用。  
    * 帧速率管理现在在图形扩展处理器节点本身中可用。

### <a name="visual-studio-code-extension"></a>Visual Studio Code 扩展
* [IoT Edge 上发布的实时视频分析](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)-一种 Visual Studio Code 扩展，可帮助你管理 LVA media graph。 此扩展适用于 **LVA 2.0 模块** ，提供了编辑和管理媒体图形的功能，并提供了一个精美易用的图形界面。
## <a name="september-22-2020"></a>2020 年 9 月 22 日

此模块 2020 年 9 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 新的 "图形扩展" 节点， [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) 可与 " [空间分析](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview) " (预览 ") 模块与认知服务集成。
* 添加了对 Linux ARM64 设备的支持 - 使用[手动步骤](deploy-iot-edge-device.md)部署到此类设备。

### <a name="documentation-updates"></a>文档更新

* 可在 Azure Stack 边缘设备上的 IoT Edge 上使用实时视频分析的[说明](deploy-azure-stack-edge-how-to.md)。
* 新教程介绍了如何使用 [自定义视觉服务](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) 开发方案特定的计算机视觉模型，以及如何使用它来实时 [分析实时视频](custom-vision-tutorial.md) 。

<hr width=100%>

## <a name="august-19-2020"></a>2020 年 8 月 19 日

此模块 2020 年 8 月刷新的此版本标记为：

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (live-video-analytics:1)。 因此，只需重新部署此类清单，即可在“边缘”>“设备”上更新该模块。

### <a name="module-updates"></a>模块更新

* 现在，你可以使用 gRPC 框架在 IoT Edge 的实时视频分析和自定义扩展之间获得高数据内容传输性能。 请参阅[此内容](analyze-live-video-use-your-grpc-model-quickstart.md)以开始使用。
* 实时视频分析的区域部署更广泛，且只更新了云服务。  
* 实时视频分析现已在全球25个其他区域推出。 下面是所有可用区域的[列表](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。  
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

* 现在，你可以创建具有资产接收器节点以及信号门处理器节点下游的文件接收器节点的图形拓扑。 请参阅[此内容](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files)获取示例。

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
