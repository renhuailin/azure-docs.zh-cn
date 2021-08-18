---
title: Azure 视频分析器发行说明 - Azure
description: 本主题提供 Azure 视频分析器版本的发行说明、改进、bug 修复和已知问题。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: f7189e6bda42ad77cb4bb6f426bf73209f46c363
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601924"
---
# <a name="azure-video-analyzer-release-notes"></a>Azure 视频分析器发行说明

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Azure+Video+Analyzer+on+IoT+Edge+release+notes%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

本文提供以下事项的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

<hr width=100%>

## <a name="june-3-2021"></a>2021 年 6 月 3 日

该模块 2021 年 6 月刷新的版本标记为：

```
     mcr.microsoft.com/media/video-analyzer:1.0.1
```
> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (video-analyzer:1)。 因此，只需重新部署此类清单，即可在更新的标记发布时在边缘设备上更新该模块。

### <a name="module-updates"></a>模块更新
* 支持用于连接到 RTSP 相机的凭据中的 unicode 字符
* 在调试模式下启用详细日志的更新

<hr width=100%>

## <a name="may-25-2021"></a>2021 年 5 月 25 日

此版本是 Azure 视频分析器的第一个公共预览版本。 发行标记为

```
mcr.microsoft.com/media/video-analyzer:1.0.0
```

> [!NOTE]
> 在快速入门和教程中，部署清单使用标记 1 (video-analyzer:1)。 因此，只需重新部署此类清单，即可在更新的标记发布时在边缘设备上更新该模块。

### <a name="supported-functionalities"></a>支持的功能

* 使用所选的 AI 模块分析实时视频流，还可以选择在边缘设备上或云中录制视频
* 在云中记录视频和推理元数据
* 当对象使用你自己的对象检测模型跨行时触发事件
* 跟踪自己的检测模型检测到的对象 
* 使用视频分析器播放器小组件（Web 组件）播放录制的视频和推理元数据
* 使用 Azure 门户或 Visual Studio Code 通过 IoT 中心部署和配置模块
* 使用[直接方法](direct-methods.md)调用从远程或在本地管理[管道拓扑](pipeline.md#pipeline-topologies)

## <a name="next-steps"></a>后续步骤

[概述](overview.md)
