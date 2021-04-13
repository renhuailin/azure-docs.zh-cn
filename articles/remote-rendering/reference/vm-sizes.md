---
title: 服务器大小
description: 描述可以分配的非重复的服务器大小
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e2687954fb05ce826e780ae0dbd3931d899885f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594394"
---
# <a name="server-sizes"></a>服务器大小

Azure 远程渲染有两种服务器配置：`Standard` 和 `Premium`。

## <a name="polygon-limits"></a>多边形限制

使用 `Standard` 大小服务器的远程渲染的最大场景大小为 2 千万个多边形。 使用 `Premium` 大小的远程渲染不会强制实施硬性最大值，但如果内容超出该服务的渲染能力，则性能可能会下降。

在“标准”服务器大小上的渲染器达到此限制时，它会将渲染切换为黑白方格图案背景：

![屏幕截图显示了具有“工具”菜单的黑白正方形网格。](media/checkerboard.png)

## <a name="specify-the-server-size"></a>指定服务器大小

必须在渲染会话初始化时指定所需服务器配置类型。 在正在运行的会话中无法更改该类型。 下面的代码示例显示了必须指定服务器大小的位置：

```cs
async void CreateRenderingSession(RemoteRenderingClient client)
{
    RenderingSessionCreationOptions sessionCreationOptions = default;
    sessionCreationOptions.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(sessionCreationOptions);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // do something with the session
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client)
{
    RenderingSessionCreationOptions sessionCreationOptions;
    sessionCreationOptions.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    client->CreateNewRenderingSessionAsync(sessionCreationOptions, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // do something with the session
        }
    });
}

```

对于[示例 PowerShell 脚本](../samples/powershell-example-scripts.md)，必须在 `arrconfig.json` 文件中指定所需服务器大小：

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>渲染器如何计算多边形数量

进行限制测试时考虑的多边形的数量是实际传递到渲染器的多边形的数量。 此几何图形通常是所有已实例化的模型的总和，但也有例外。 以下几何图形未包括在内：
* 完全在视锥外的已加载的模型实例。
* 使用[分层状态替代组件](../overview/features/override-hierarchical-state.md)切换为不可见的模型或模型部件。

因此，可以针对 `standard` 大小编写一个应用程序，用于加载多边形计数接近每一个模型的限制的多个模型。 在该应用程序一次只显示一个模型时，不会触发黑白方格。

### <a name="how-to-determine-the-number-of-polygons"></a>如何确定多边形的数量

对于会影响 `standard` 配置大小的预算限制的模型或场景，若要确定它们的多边形数量，可以使用两种方法：
* 在模型转换端，检索[转换输出 json 文件](../how-tos/conversion/get-information.md)，并检查 [inputStatistics 节](../how-tos/conversion/get-information.md#the-inputstatistics-section)中的 `numFaces` 条目
* 如果你的应用程序要处理动态内容，则可以在运行时动态查询渲染的多边形的数量。 使用[性能评估查询](../overview/features/performance-queries.md#performance-assessment-queries)并检查 `FrameStatistics` 结构中的 `polygonsRendered` 成员。 在渲染器达到多边形限制时，`PolygonsRendered` 字段会被设置为 `bad`。 黑白方格图案背景在淡入时始终会带有一些延迟，以确保可以在此异步查询后执行用户操作。 例如，用户操作可能会是隐藏或删除模型实例。

## <a name="pricing"></a>定价

若要详细了解每种配置类型的定价明细，请参考[远程渲染定价](https://azure.microsoft.com/pricing/details/remote-rendering)页。

## <a name="next-steps"></a>后续步骤
* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
* [模型转换](../how-tos/conversion/model-conversion.md)

