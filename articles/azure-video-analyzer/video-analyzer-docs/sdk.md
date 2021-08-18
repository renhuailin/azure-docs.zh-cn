---
title: Azure 视频分析器 SDK 和资源
description: 了解 Azure 视频分析器 SDK
author: bennage
ms.author: christb
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: 63f0ad6642dca449479a36d6e7503f9eaa2219fc
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601887"
---
# <a name="azure-video-analyzer-sdks"></a>Azure 视频分析器 SDK

Azure 视频分析器包含两组 SDK。 管理 SDK 用于管理 Azure 资源，客户端 SDK 用于与边缘模块交互。

## <a name="management-sdks"></a>管理 SDK

使用管理 SDK，你可以与 Azure 资源管理器公开的资源进行交互。 可以创建视频分析器帐户、为边缘模块生成预配令牌、管理视频的访问策略等。 SDK 基于基础 [REST API](/rest/api/videoanalyzer/?branch=video) 进行构建。

支持的平台如下：

- [.NET](https://aka.ms/ava/sdk/mgt/net)
- [Java](https://aka.ms/ava/sdk/mgt/java)
- [Python](https://aka.ms/ava/sdk/mgt/python)

## <a name="client-sdks"></a>客户端 SDK

使用客户端 SDK，你可以与视频分析器边缘模块的[直接方法][docs-direct-methods]进行交互。 这些 SDK 设计为用于 [Azure IoT 中心 SDK][docs-iot-hub-sdks]。 它们支持构造表示直接方法的对象，然后可以使用 IoT 中心 SDK 将其发送到边缘模块。

支持的平台如下：

- [.NET](https://aka.ms/ava/sdk/client/net)
- [Python](https://aka.ms/ava/sdk/client/python)
- [Java](https://aka.ms/ava/sdk/client/java)

<!-- links -->
[docs-direct-methods]: direct-methods.md
[docs-iot-hub-sdks]: ../../iot-hub/iot-hub-devguide-sdks.md

[REST API]: https://aka.ms/ava/api/rest
