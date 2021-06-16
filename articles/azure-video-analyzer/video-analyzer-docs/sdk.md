---
title: Azure 视频分析器 SDK 和资源
description: 了解 Azure 视频分析器 SDK
author: bennage
ms.author: christb
ms.topic: reference
ms.date: 05/14/2021
ms.openlocfilehash: 25e94b06fbc45d16ef198e2d77a653a407fcf1c9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954438"
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