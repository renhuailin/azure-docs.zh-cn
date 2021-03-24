---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97821513"
---
处理压缩音频是使用 [GStreamer](https://gstreamer.freedesktop.org) 实现的。 出于许可原因，GStreamer 二进制文件未编译，也未与语音 SDK 链接。 开发人员需要安装几个依赖项和插件，请参阅[在 Windows 上安装](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)或[在 Linux 上安装](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)。 GStreamer 二进制文件需要在系统路径中，以便语音 SDK 可以在运行时加载二进制文件。 例如，在 Windows 上，如果语音 SDK 能够在运行时找到 `libgstreamer-1.0-0.dll`，这意味着 gstreamer 二进制文件在系统路径中。

