---
title: 系统要求
description: 列出 Azure 远程渲染的系统要求
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.custom: references_regions
ms.openlocfilehash: 320f9ef1285f16a3f7b38b3b4be3e5c51eff269f
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729788"
---
# <a name="system-requirements"></a>系统要求

本章列出了使用 Azure 远程渲染 (ARR) 的最低系统要求。

## <a name="development-pc"></a>开发电脑

* Windows 10 版本 1903 或更高版本。
* 最新的图形驱动程序。
* 可选：[H265 硬件视频解码器](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)（如果需要使用远程渲染内容的本地预览（例如在 Unity 中这样做））。

> [!IMPORTANT]
> Windows 更新不会始终提供最新的 GPU 驱动程序，请查看 GPU 制造商网站以获取最新驱动程序：
>
> * [AMD 驱动程序](https://www.amd.com/en/support)
> * [Intel 驱动程序](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA 驱动程序](https://www.nvidia.com/Download/index.aspx)

下表列出了哪些 GPU 支持 H265 硬件视频解码。

| GPU 制造商 | 支持的模型 |
|-----------|:-----------|
| NVIDIA | 请查看[此页底部](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix)的“NVDEC 支持矩阵”。 你的 GPU 要求在“H.265 4:2:0 8 位”列中为“YES”。 |
| AMD | 具有 AMD 的[统一视频解码器](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)（版本最低为 6）的 GPU。 |
| Intel | Skylake 及更高版本的 CPU |

尽管可能已经安装了正确的 H265 编解码器，但编解码器 DLL 上的安全属性可能还是会导致编解码器初始化失败。 [故障排除指南](../resources/troubleshoot.md#h265-codec-not-available)介绍了解决此问题的步骤。 只有在桌面应用程序中（例如在 Unity 中）使用该服务时，才会出现该 DLL 问题。

## <a name="devices"></a>设备

Azure 远程渲染目前只支持使用 HoloLens 2 和 Windows 桌面设备作为目标设备。 请参阅[平台限制](../reference/limits.md#platform-limitations)部分。

请务必使用最新的 HEVC 编解码器，因为更新的版本在延迟方面有显著改进。 若要检查在设备上安装的是哪个版本，请执行以下操作：

1. 启动“Microsoft Store”。
1. 单击右上的“...”按钮。
1. 选择“下载和更新”。
1. 在列表中搜索“设备制造商提供的 HEVC 视频扩展”。 如果更新下未列出此项，则表明已经安装了最新版本。
1. 请确保列出的编解码器的版本至少是“1.0.21821.0”。
1. 单击“获取更新”按钮，等待安装完成。

## <a name="network"></a>网络

若要获得良好的用户体验，稳定、低延迟的网络连接至关重要。

请参阅专门介绍[网络要求](../reference/network-requirements.md)的章节。

若要排查网络问题，请参阅[故障排除指南](../resources/troubleshoot.md#unstable-holograms)。

### <a name="network-firewall"></a>网络防火墙

### <a name="sdk-version--0176"></a>SDK 版本 0.1.76 或更高版本

远程渲染虚拟机使用以下 IP 范围中的共享 IP 地址：

| 名称             | 区域         | IP 前缀         |
|------------------|:---------------|:------------------|
| 澳大利亚东部   | australiaeast  | 20.53.44.240/28   |
| 美国东部          | eastus         | 20.62.129.224/28  |
| 美国东部 2        | eastus2        | 20.49.103.240/28  |
| Japan East       | japaneast      | 20.191.165.112/28 |
| 北欧     | northeurope    | 52.146.133.64/28  |
| 美国中南部 | southcentralus | 20.65.132.80/28   |
| Southeast Asia   | southeastasia  | 20.195.64.224/28  |
| 英国南部         | uksouth        | 51.143.209.144/28 |
| 西欧      | westeurope     | 20.61.99.112/28   |
| 美国西部 2        | westus2        | 20.51.9.64/28     |

请确保防火墙（设备上的、路由器内部的，等等）未阻止这些 IP 范围和以下端口范围：

| 端口              | 协议  | Allow    |
|-------------------|---------- |----------|
| 49152-65534       | TCP/UDP | 传出 |

#### <a name="sdk-version--0176"></a>SDK 版本低于 0.1.76

确保防火墙（在设备上、路由器内部等）未阻止以下端口：

| 端口              | 协议 | Allow    | 说明 |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | 传出 | 初始连接（HTTP 握手） |
| 8266              | UDP      | 传出 | 数据传输 |
| 5000、5433、8443  | TCP      | 传出 | [ArrInspector 工具](../resources/tools/arr-inspector.md)所需|

## <a name="software"></a>软件

必须安装以下软件：

* Visual Studio 2019 的最新版本[（下载）](https://visualstudio.microsoft.com/vs/older-downloads/)
* [适用于混合现实的 Visual Studio tools](/windows/mixed-reality/install-the-tools)。 具体来说，必须安装以下工作负载：
  * **使用 C++ 的桌面开发**
  * **通用 Windows 平台 (UWP) 开发**
* Windows SDK 10.0.18362.0[（下载）](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* GIT[（下载）](https://git-scm.com/downloads)
* 可选：若要在台式电脑上观看来自服务器的视频流，需要有 HEVC 视频扩展[（Microsoft Store 链接）](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)。 请通过检查该商店中的更新来确保安装了最新版本。

## <a name="unity"></a>Unity

若要使用 Unity 进行开发，请安装支持版本的 Unity[（下载）](https://unity3d.com/get-unity/download)。 建议使用 Unity Hub 来管理安装。
请确保在 Unity 安装中包括以下模块：
* **UWP** - 通用 Windows 平台生成支持
* **IL2CPP** - Windows 生成支持 (IL2CPP)

### <a name="unity-2019"></a>Unity 2019

支持 Unity 2019 版本 2019.3 或 2019.4 LTS。 但是，若要使用 OpenXR 版本的插件，需要基于 2020 的版本。

Unity 2019 ARR 既支持适用于 Windows 混合现实的旧版内置 XR 集成，也支持新的 XR SDK 插件框架。

### <a name="unity-2020"></a>Unity 2020

对于 Unity 2020，请使用最新版本的 Unity 2020.3。

> [!IMPORTANT]
> 使用 OpenXR 版本的插件时，必须验证通用呈现管道 (URP) 的版本是否为 10.5.1 或更高版本。 若要检查这一点，请从 Unity Windows 菜单中打开“包管理器”，并参阅“通用 RP”部分：![通用 RP 版本](./media/unity-universal-rp-version-10-5-1.png)

> [!IMPORTANT]
> 适用于 Unity 2020.3 的 WMR（Windows 混合现实）插件当前在 ARR 中性能降级。 为了获得更好的体验，建议继续使用 Unity 2019.X 或切换到 OpenXR 版本。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Unity 渲染模型](../quickstarts/render-model.md)