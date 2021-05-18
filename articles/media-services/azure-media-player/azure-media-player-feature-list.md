---
title: Azure Media Player 功能列表
description: Azure Media Player 的功能参考。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 88048c3328114f17b30859efb41bb9f059b71439
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91296359"
---
# <a name="feature-list"></a>功能列表 #
下面是已测试的功能和不支持的功能的列表：

| 功能 | 已测试 | 已部分测试 | 未测试 | 不支持 | 注释 |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| **播放**                                |        |                  |          |             |                                                                                                                      |
| 基本点播播放                | X      |                  |          |             | 只支持来自 Azure 媒体服务的流                                                                      |
| 基本实时播放                     | X      |                  |          |             | 只支持来自 Azure 媒体服务的流                                                                      |
| AES                                     | X      |                  |          |             | 支持 Azure 媒体服务密钥传递服务                                                                   |
| 多 DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | 支持 Azure 媒体服务密钥传递服务                                                                   |
| Widevine                                |        | X                |          |             | 支持清单中列出的 Widevine PSSH 框                                                                    |
| FairPlay                                |        | X                |          |             | 支持 Azure 媒体服务密钥传递服务                                                                   |
| 技术                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| 闪存回退 (FlashSS)                | X      |                  |          |             | 并非所有有关此技术的功能都可用。                                                                         |
| Silverlight 回退 SilverlightSS      | X      |                  |          |             | 并非所有有关此技术的功能都可用。                                                                         |
| 原生 HLS 直通 (Html5)         |        | X                |          |             | 由于平台限制，并非所有有关此技术的功能都可用。                                            |
| **功能**                                |        |                  |          |             |                                                                                                                      |
| API 支持                             | X      |                  |          |             | 请参阅已知问题列表                                                                                                |
| 基本 UI                                | X      |                  |          |                                                                                                                                    |
| 通过 JavaScript 进行的初始化       | X      |                  |          |             |                                                                                                                      |
| 通过视频标记进行的初始化        |        | X                |          |             |                                                                                                                      |
| 分段寻址 - 基于时间         | X      |                  |          |             |                                                                                                                      |
| 分段寻址 - 基于索引        |        |                  |          | X           |                                                                                                                      |
| 分段寻址 - 基于字节         |        |                  |          | X           |                                                                                                                      |
| Azure 媒体服务 URL 重写工具       |        | X                |          |             |                                                                                                                      |
| 辅助功能 - 隐藏式字幕和字幕  | X      |                 |          |             |  WebVTT（点播）、CEA 708（点播和实时）和 IMSC1（点播和实时）                                                       |
| 辅助功能 - 热键                 | X      |                  |          |             |                                                                                                                      |
| 辅助功能 - 高对比度           |        | X                |          |             |                                                                                                                      |
| 辅助功能 - 选项卡焦点               |        | X                |          |             |                                                                                                                      |
| 错误消息传送                         |        | X                |          |             | 不同技术中的错误消息不一致                                                                         |
| 事件触发                        | X      |                  |          |             |                                                                                                                      |
| 诊断                             |        | X                |          |             | 诊断信息只适用于 AzureHtml5JS 技术，在 SilverlightSS 技术上部分可用。 |
| 可自定义的技术命令                 |        | X                |          |             |                                                                                                                      |
| 启发 - 基本                      | X      |                  |          |             |                                                                                                                      |
| 启发 - 自定义              |        |                  | X        |             | 自定义只适用于 AzureHtml5JS 技术。                                                          |
| 中断                         | X      |                  |          |             |                                                                                                                      |
| 选择比特率                          | X      |                  |          |             | 此 API 只适用于 AzureHtml5JS 和 FlashSS 技术。                                                    |
| 多音频流                      |        | X                |          |             | 编程式音频切换在 AzureHtml5JS 和 FlashSS 技术上受支持，并可通过 AzureHtml5JS、FlashSS 和原生 Html5（在 Safari 中）上的 UI 选择来使用。  大多数平台都需要采用相同的编解码器专用数据来切换音频流（相同的编解码器、频道、采样率，等等）。 |
| UI 本地化                         |        | X                |          |             |                                                                                                                      |
| 多实例播放                 |        |                  |          | X           | 此方案可能适用于某些技术，但目前不受支持且未经测试。 你也可以使用 iframe 来实现它 |
| 广告支持                             |        | X                |          |             | AMP 支持为采用 AzureHtml5JS 技术的点播视频插入来自符合 VAST 的广告服务器的正片前、中和后线性广告 |
| 分析                               |        | X                |          |             | AMP 提供侦听分析和诊断事件的功能，以便将事件发送到所选的 Analytics 后端。  由于平台限制，并非所有的事件和属性都在不同的技术中可用。                                                                            |
| 自定义外观                            |        |                  | X        |             | 可以通过在 AMP 中将设置控件切换为 false 并使用你自己的 HTML 和 CSS 来实现此方案。           |
| 搜索栏清理                      |        |                  |          | X           |                                                                                                                      |
| 特技播放                              |        |                  |          | X           |                                                                                                                      |
| 仅音频                              | X      |                  |          |           | 在 AzureHtml5JS 中受支持。 如果平台支持，渐进式 MP3 播放可以使用 HTML5 技术。                                                                                                        |
| 仅视频                              | X      |                  |          |           | 在 AzureHtml5JS 中受支持。                                                                                                        |
| 多周期呈现               |        |                  |          | X                                                                                                                                  |
| 多摄像角度                  |        |                  |          | X           |                                                                                                                      |
| 播放速度                          |        | X                |          |             | 大多数情况下支持播放速度，使用移动设备时除外（由于 Chrome 中存在某个部分修复的 bug）                 |

## <a name="next-steps"></a>后续步骤 ##
- [Azure Media Player 快速入门](azure-media-player-quickstart.md)