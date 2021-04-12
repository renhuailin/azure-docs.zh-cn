---
title: 颜色材料
description: 介绍颜色材料类型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: fb26a669229ac140aba262032f8ce84ef9f37727
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593375"
---
# <a name="color-materials"></a>颜色材料

颜色材料是 Azure 远程渲染支持的[材料类型](../../concepts/materials.md)之一。 它们用于不应获得任何类型照明但应始终保持最大亮度的[网格](../../concepts/meshes.md)。 这可能适用于“发光”材料，如汽车仪表板、电灯泡或已引入静态照明的数据（例如通过[摄影测量](https://en.wikipedia.org/wiki/Photogrammetry)获取的模型）。

颜色材料比 [PBR 材料](pbr-materials.md)能够更加有效地渲染，因为它的底纹模型更为简单。 它们还支持不同的透明模式。

## <a name="common-material-properties"></a>常用材料属性

这些属性是所有材料共有的：

* **albedoColor：** 将此颜色与其他颜色（如 albedoMap 或 :::no-loc text="vertex"::: 颜色）叠加。 如果对材料启用了透明度，则可以使用 alpha 通道调整不透明度，`1` 表示完全不透明，`0` 则表示完全透明。 默认值为白色。

  > [!NOTE]
  > 由于颜色材料不反映环境，因此完全透明的颜色材料是不可见的。 这与 [PBR 材料](pbr-materials.md)不同。

* **albedoMap：** 与每个像素反照率值对应的 [2D 纹理](../../concepts/textures.md)。

* **alphaClipEnabled** 和 **AlphaClipThreshold：** 如果 alphaClipEnabled 为 true，则不会绘制反照率 alpha 值小于 alphaClipThreshold 的所有像素。 即使不启用透明度，也可以使用 alpha 裁剪，并且渲染速度更快。 但是 alpha 剪裁材料的渲染速度仍比完全不透明材料要慢。 默认情况下，禁用 alpha 剪裁。

* **textureCoordinateScale** 和 **textureCoordinateOffset：** 将缩放叠加到 UV 纹理坐标，并向其添加偏移量。 可用于拉伸和移动纹理。 默认缩放为 (1, 1)，默认偏移量为 (0, 0)。

* **useVertexColor：** 如果网格包含 :::no-loc text="vertex"::: 颜色且已启用此选项，则会将网格的 :::no-loc text="vertex"::: 颜色叠加到 albedoColor 和 albedoMap 中。 默认情况下，禁用 useVertexColor。

* **isDoubleSided：** 如果将双面设置为 true，则即使相机正对背景面，也会渲染使用此材料的三角形。 默认情况下，禁用此选项。 另请参阅[:::no-loc text="Single-sided":::渲染](single-sided-rendering.md)。

* **TransparencyWritesDepth：** 如果对材料设置了 TransparencyWritesDepth 标志，且材料是透明的，则使用此材料的对象也会纳入到最终深度缓冲区。 请参阅下一节中的颜色材料属性 transparencyMode。 如果用例中需要为完全透明的场景采用更加真实的[后期重投影](late-stage-reprojection.md)，则建议启用此功能。 对于不透明/透明的混合场景，此设置可能会引入不太真实的投影行为或投影项目。 出于此原因，对于一般用例，默认推荐设置是禁用此标志。 写入的深度值取自离相机最近的对象的每像素深度层。

* **FresnelEffect：** 此材料标志为对应材料启用附加的[菲涅尔透镜效果](../../overview/features/fresnel-effect.md)。 此效果的外观由以下介绍的其他菲涅尔透镜参数控制。 

* **FresnelEffectColor：** 材料使用的菲涅尔透镜颜色。 仅在为材料设置了菲涅尔透镜效果位时，它才变得重要（请参阅上述属性）。 此属性控制菲涅尔透镜光照的基础颜色（请参阅[菲涅尔透镜效果](../../overview/features/fresnel-effect.md)以获取完整说明）。 目前仅 rgb 通道值是重要值，忽略 alpha 值。

* **FresnelEffectExponent：** 用于材料的菲涅尔透镜指数。 仅在为材料设置了菲涅尔透镜效果位时，它才变得重要（请参阅上述属性）。 此属性控制菲涅尔透镜光照的传播。 最小值 0.01 将使光照遍布整个对象。 最大值 10.0 会将光照限制为仅最亮的边缘可见。

## <a name="color-material-properties"></a>颜色材料属性

以下属性特定于颜色材料：

* **vertexMix：** 此值介于 `0` 和 `1` 之间，指定在[网格](../../concepts/meshes.md)最终颜色中 :::no-loc text="vertex"::: 颜色的使用程度。 默认值为 1，将 :::no-loc text="vertex"::: 颜色完全叠加到反照率颜色。 将值设置为 0，将完全忽略 :::no-loc text="vertex"::: 颜色。

* **transparencyMode：** 与 [PBR 材料](pbr-materials.md)相反，颜色材料区分不同的透明模式：

  1. **Opaque：** 此默认模式禁用透明度。 但仍然可以使用 alpha 剪裁，且应将其作为首选（如果空间足够）。
  
  1. **AlphaBlended：** 此模式类似于 PBR 材料的透明模式。 它应该用于透明材料（例如玻璃）。

  1. **Additive：** 此模式是最简单、最有效的透明模式。 向已渲染的图像添加材料效果。 此模式可用于模拟发光（但仍透明）对象，例如用于突出显示重要对象的标记。

## <a name="api-documentation"></a>API 文档

* [C# ColorMaterial 类](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RenderingConnection.CreateMaterial()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ ColorMaterial 类](/cpp/api/remote-rendering/colormaterial)
* [C++ RenderingConnection::CreateMaterial()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>后续步骤

* [PBR 材料](pbr-materials.md)
* [纹理](../../concepts/textures.md)
* [网格](../../concepts/meshes.md)