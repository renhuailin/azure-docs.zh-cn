---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: d555322c0fd4067a4232e53387d811de7b50a8f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417687"
---
处理压缩音频是使用 [GStreamer](https://gstreamer.freedesktop.org) 实现的。 出于许可原因，GStreamer 二进制文件未编译，也未与语音 SDK 链接。 开发人员需要安装几个依赖项和插件。

> [!NOTE]
> 请参阅[受支持的 Linux 发行版和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)的列表。

# <a name="ubuntudebian"></a>[Ubuntu/Debian](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rhelcentos"></a>[RHEL/CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> - 在 RHEL/CentOS 7 和 RHEL/CentOS 8 上，如果使用“任何”压缩格式，则需要安装更多 gstreamer 插件（如果流媒体格式插件不在上面安装的插件中）。 
> - 在 RHEL/CentOS 7 上，按照[如何为语音 SDK 配置 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的说明进行操作。
> - 在 RHEL/CentOS 8 上，按照[如何配置 OpenSSL for Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的说明进行操作。


---