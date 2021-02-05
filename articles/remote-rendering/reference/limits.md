---
title: 限制
description: SDK 功能的代码限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 68c0c04feba2779598a500c84b2ba4a9086b104d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593937"
---
# <a name="limitations"></a>限制

许多功能具有大小、数量或其他限制。

## <a name="azure-frontend"></a>Azure 前端

以下限制适用于前端 API (c + + 和 c # ) ：
* 每个进程的 [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) 实例总数：16。
* 每个[RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient)的[RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession)实例总数：16。

## <a name="objects"></a>对象

* 单个类型 ([实体](../concepts/entities.md)、 [CutPlaneComponent](../overview/features/cut-planes.md)等的允许对象总数 ) ：16777215。
* 允许的活动剪切平面总数：8.

## <a name="geometry"></a>几何图形

* **动画：** 动画仅限于对 [游戏对象](../concepts/entities.md)的各个转换进行动画处理。 不支持具有外观动画或顶点动画的框架式动画。 不保留源资产文件中的动画跟踪。 相反，必须由客户端代码驱动对象转换动画。
* **自定义着色器：** 不支持创作自定义着色器。 只有内置 [颜色材料](../overview/features/color-materials.md) 或 [.pbr 材料](../overview/features/pbr-materials.md) 才能使用。
* 资产中 **的最大不同材料数**：65535。 有关自动材料计数缩减的详细信息，请参阅 " [材料消除](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) 重复" 一章。
* **单个纹理的最大尺寸**： 16384 x 16384。 转换过程会减小更大的源纹理大小。

### <a name="overall-number-of-polygons"></a>多边形总数

所有已加载模型允许的多边形数量取决于传递给[会话管理 REST API](../how-tos/session-rest-api.md#create-a-session) 的 VM 大小：

| 服务器大小 | 最大多边形数量 |
|:--------|:------------------|
|standard| 2000 万 |
|高级| 无限制 |

有关此限制的详细信息，请参阅 " [服务器大小](../reference/vm-sizes.md) " 一章。

## <a name="platform-limitations"></a>平台限制

**Windows 10 桌面**

* Win32/x64 是唯一受支持的 Win32 平台。 Win32/x86 不受支持。

**HoloLens 2**

* 不支持[从 PV 相机进行呈现](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)功能。