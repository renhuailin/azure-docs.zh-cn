---
title: 快速入门：用于 C++ (Linux) 的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 根据本指南可设置配合使用 Linux 上的 C++ 和语音服务 SDK 的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 10cee7f5b0deff37a9b1df1937fe8f6ed8431daa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188160"
---
本指南介绍如何安装用于 Linux 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>系统要求

Linux：请参阅[受支持的 Linux 分发和目标体系结构](~/articles/cognitive-services/speech-service/speech-sdk.md)的列表。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* 支持的 Linux 平台会要求安装某些库（要求安装 `libssl` 以获取安全套接字层支持；要求安装 `libasound2` 以获取声音支持）。 请参阅下面的发行版，了解安装这些库的正确版本所需的命令。

   * 在 Ubuntu/Debian 上：

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     如果 libssl1.0.0 不可用，则改为安装 libssl1.0.x（其中 x 大于 0）或 libssl1.1。

   * 在 RHEL/CentOS 上：

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - 在 RHEL/CentOS 7 上，按照[如何为语音 SDK 配置 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的说明进行操作。
> - 在 RHEL/CentOS 8 上，按照[如何配置 OpenSSL for Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的说明进行操作。

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
