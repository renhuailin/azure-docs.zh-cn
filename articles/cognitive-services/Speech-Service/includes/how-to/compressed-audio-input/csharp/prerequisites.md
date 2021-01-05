---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 3d4c908e0caf1cf84159df49d98603fd13b75994
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821547"
---
处理压缩音频是使用 [GStreamer](https://gstreamer.freedesktop.org) 实现的。 出于许可原因，GStreamer 二进制文件未编译，也未与语音 SDK 链接。 开发人员需要安装几个依赖项和插件，请参阅 [在 Windows 上安装](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) 或 [在 Linux 上安装](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)。 适用 gstreamer 二进制文件需要位于系统路径中，以便语音 SDK 可在运行时加载二进制文件。 例如，在 Windows 上，如果语音 SDK 能够在 `libgstreamer-1.0-0.dll` 运行时中找到，则表示适用 gstreamer 二进制文件在系统路径中。

