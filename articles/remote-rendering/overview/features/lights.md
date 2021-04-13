---
title: 场景照明
description: 光源说明和属性
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 49027899d66a2192cc311fb4dba66e441155b527
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "92206843"
---
# <a name="scene-lighting"></a>场景照明

默认情况下，远程渲染的对象将使用[天空光](sky.md)照明。 对于大多数应用程序而言，这已经足够了，但你可以将更多的光源添加到场景中。

> [!IMPORTANT]
> 只有 [PBR 材料](pbr-materials.md)会受光源影响。 [有色材料](color-materials.md)总是全亮度显示。

> [!NOTE]
> 当前不支持投射阴影。 Azure 远程渲染经过优化，可渲染大量几何体，必要时可使用多个 GPU。 在这种情况下，用于投影的传统方法效果不佳。

## <a name="common-light-component-properties"></a>常用的光组件属性

所有光类型都派生自抽象基类 `LightComponent` 并且都具有以下属性：

* **Color：** [伽马空间](https://en.wikipedia.org/wiki/SRGB)中光的颜色。 将忽略阿尔法。

* **Intensity：** 光的亮度。 对于点光和投射光，强度还定义光照射的距离。

## <a name="point-light"></a>点光

在 Azure 远程渲染中，`PointLightComponent` 不仅可以从单个点发出光，还可以从小球或小管道发出光，以模拟较柔和的光源。

### <a name="pointlightcomponent-properties"></a>PointLightComponent 属性

* **Radius：** 默认半径为零，在这种情况下，光充当点光。 如果半径大于零，它将充当球形光源，从而更改镜面高光的外观。

* **Length：** 如果 `Length` 和 `Radius` 都不是零，则光将充当管光。 这可用来模拟霓虹管。

* **AttenuationCutoff：** 如果位于 (0,0) 的左侧，则光的衰减仅取决于其 `Intensity`。 但是，你可以提供自定义的最小/最大距离，让光的强度在该距离内线性缩减为 0。 此功能可用于强迫特定的光影响较小的范围。

* **ProjectedCubemap：** 如果设置为有效的[立方图](../../concepts/textures.md)，则纹理将投射到光的周围几何体上。 立方图的颜色通过光的颜色进行调制。

## <a name="spot-light"></a>投射光

`SpotLightComponent` 类似于 `PointLightComponent`，但光被约束成圆锥体的形状。 圆锥体的方向由所有者实体的负 z 轴定义。

### <a name="spotlightcomponent-properties"></a>SpotLightComponent 属性

* **Radius：** 与用于 `PointLightComponent` 的值相同。

* **SpotAngleDeg：** 此间隔定义圆锥体的内角和外角（以度为单位）。 内角中的一切都以全亮度照亮。 将沿生成半影状效果的外角应用衰减。

* **FalloffExponent：** 定义内锥角与外锥角之间的衰减过渡锐度。 值越高，过渡越尖锐。 默认值为 1.0，会导致线性过渡。

* **AttenuationCutoff：** 与用于 `PointLightComponent` 的值相同。

* **Projected2dTexture：** 如果设置为有效的 [2D 纹理](../../concepts/textures.md)，则图像会投射到光照射的几何体上。 纹理的颜色通过光的颜色进行调制。

## <a name="directional-light"></a>定向光

`DirectionalLightComponent` 模拟无限远的光源。 光照向所有者实体的负 z 轴方向。 实体的位置会被忽略。

没有其他属性。

## <a name="performance-considerations"></a>性能注意事项

光源对渲染性能有明显的影响。 请谨慎使用它们，仅在应用程序需要时使用。 任何静态全局照明条件（包括静态方向分量）都可以通过[自定义天空纹理](sky.md)实现，不需要额外的渲染成本。

## <a name="api-documentation"></a>API 文档

* [C# LightComponentBase 类](/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [C# PointLightComponent 类](/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [C# SpotLightComponent 类](/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [C# DirectionalLightComponent 类](/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [C++ LightComponentBase 类](/cpp/api/remote-rendering/lightcomponentbase)
* [C++ PointLightComponent 类](/cpp/api/remote-rendering/pointlightcomponent)
* [C++ SpotLightComponent 类](/cpp/api/remote-rendering/spotlightcomponent)
* [C++ DirectionalLightComponent 类](/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>后续步骤

* [材料](../../concepts/materials.md)
* [天空](sky.md)