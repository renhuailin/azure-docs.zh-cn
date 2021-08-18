---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: lajanuar
ms.openlocfilehash: 81d6127e7329fd787665cc569177ef033b9c85f8
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122262252"
---
处理压缩音频是使用 [GStreamer](https://gstreamer.freedesktop.org) 实现的。 出于许可原因，GStreamer 二进制文件未编译，也未与语音 SDK 链接。 开发人员需要安装几个依赖项和插件，请参阅[在 Windows 上安装](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)或[在 Linux 上安装](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)。 GStreamer 二进制文件需要在系统路径中，以便语音 SDK 可以在运行时加载二进制文件。 例如，在 Windows 上，如果语音 SDK 能够在运行时找到 `libgstreamer-1.0-0.dll` 或 `gstreamer-1.0-0.dll`（对于最新的 GStreamer），这意味着 GStreamer 二进制文件在系统路径中。

