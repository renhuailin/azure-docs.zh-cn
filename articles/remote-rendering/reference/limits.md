---
title: 限制
description: SDK 功能的代码限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: f62a07ef7109c669c6abc90d4e1b4a2ee83ece23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "100530240"
---
# <a name="limitations"></a>限制

许多功能具有大小、数量或其他限制。

## <a name="azure-frontend"></a>Azure 前端

前端 API（C++ 和 C#）具有以下限制：
* 每个进程的 [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) 实例总数：16 个。
* 每个 [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) 的 [RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession) 实例总数：16 个。

## <a name="objects"></a>对象

* 单个类型允许的对象总数（[Entity](../concepts/entities.md)、[CutPlaneComponent](../overview/features/cut-planes.md) 等）：16,777,215 个。
* 允许的活动剪切平面总数：8.

## <a name="geometry"></a>几何图形

* **动画：** 动画仅限于对[游戏对象](../concepts/entities.md)的各个转换进行动画处理。 不支持包含外观动画或顶点动画的框架式动画。 不保留从源资产文件中执行的动画跟踪。 改由客户端代码驱动对象转换动画。
* **自定义着色器：** 不支持创作自定义着色器。 只能使用内置的[颜色材料](../overview/features/color-materials.md)或 [PBR 材料](../overview/features/pbr-materials.md)。
* **最大非重复材料数**（一个资产中）：65,535 个。 有关自动材料计数减少的详细信息，请参阅[材料去重](../how-tos/conversion/configure-model-conversion.md#material-de-duplication)一章。
* **单个纹理的最大尺寸**：16,384 x 16,384。 转换过程会减小较大源纹理的大小。

### <a name="overall-number-of-polygons"></a>多边形总数

所有已加载模型允许的多边形数量取决于传递给[会话管理 REST API](../how-tos/session-rest-api.md) 的 VM 大小：

| 服务器大小 | 最大多边形数量 |
|:--------|:------------------|
|standard| 2000 万 |
|高级| 无限制 |

有关此限制的详细信息，请参阅[服务器大小](../reference/vm-sizes.md)一章。

## <a name="platform-limitations"></a>平台限制

**Windows 10 桌面**

* Win32/x64 是唯一受支持的 Win32 平台。 不支持 Win32/x86。

**HoloLens 2**

* 不支持[从 PV 相机进行呈现](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)功能。