---
title: PBR 材料
description: 介绍 PBR 材料类型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: e9908c106e57801cb1b7def8b3353a983cc97de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99591933"
---
# <a name="pbr-materials"></a>PBR 材料

PBR 材料是 Azure 远程渲染支持的[材料类型](../../concepts/materials.md)之一。 它们用于应该接收真实照明的[网格](../../concepts/meshes.md)。

PBR 是 **P** hysically **B** ased **R** endering（基于物理学的渲染）的缩写，表示材料以物理上合理的方式描述表面的视觉属性，这样，在所有照明条件下均可获得逼真的效果。 大多数新式游戏引擎和内容创建工具都支持 PBR 材料，因为这些材料被认为最接近真实场景，因此最适合用于实时渲染。

![ARR 渲染的 Helmet glTF 示例模型](media/helmet.png)

不过，PBR 材料并非通用解决方案。 有些材料会根据视角以不同的方式反映颜色。 例如，在某些结构或汽车喷漆场景中。 此类材料不由标准 PBR 模型处理，目前不受 Azure 远程渲染的支持。 这包括 PBR 的扩展，例如薄膜（多层表面）和透明涂层（用于汽车喷漆）。 

## <a name="common-material-properties"></a>通用材料属性

这些属性是所有材料共有的：

* **albedoColor：** 此颜色与其他颜色（如 *albedoMap* 或 :::no-loc text="vertex "::: 颜色）叠加。 如果对材料启用了透明度，则可以使用 alpha 通道调整不透明度，`1` 表示完全不透明，`0` 则表示完全透明。 默认值为白色。

  > [!NOTE]
  > 当某个 PBR 材料完全透明（类似于一片完全透明的玻璃）时，它仍会反映环境。 类似于太阳的亮点仍在反射中可见。 [彩色材料](color-materials.md)则与此不同。

* **albedoMap：** 与每个像素反照率值对应的 [2D 纹理](../../concepts/textures.md)。

* **alphaClipEnabled** 和 **alphaClipThreshold：** 如果 *alphaClipEnabled* 为 true，则不会绘制反照率 alpha 值小于 *alphaClipThreshold* 的所有像素。 即使不启用透明度，也可以使用 alpha 裁剪，并且渲染速度更快。 但是 alpha 剪裁材料的渲染速度仍比完全不透明材料要慢。 默认已禁用 alpha 剪裁。

* **textureCoordinateScale** 和 **textureCoordinateOffset：** 将缩放叠加到 UV 纹理坐标，并向其添加偏移量。 可用于拉伸和移动纹理。 默认缩放为 (1, 1)，默认偏移量为 (0, 0)。

* **useVertexColor：** 如果网格包含 :::no-loc text="vertex"::: 颜色且已启用此选项，则会将网格的 :::no-loc text="vertex"::: 颜色叠加到 *albedoColor* 和 *albedoMap* 中。 默认已禁用 *useVertexColor*。

* **isDoubleSided：** 如果将双面性设置为 true，则即使相机正对背景面，也会渲染使用此材料的三角形。 对于 PBR 材料，还会正确计算背面的照明。 默认已禁用此选项。 另请参阅[:::no-loc text="Single-sided"::: 渲染](single-sided-rendering.md)。

* **TransparencyWritesDepth：** 如果对材料设置了 TransparencyWritesDepth 标志，且材料是透明的，则使用此材料的对象也会纳入到最终深度缓冲区。 请参阅下一部分中的 PBR 材料标志 *transparent*。 如果用例中需要为完全透明的场景采用更加真实的[后期重投影](late-stage-reprojection.md)，则建议启用此功能。 对于不透明/透明的混合场景，此设置可能会引入不太真实的投影行为或投影项目。 出于此原因，对于一般用例，默认推荐设置是禁用此标志。 写入的深度值取自离相机最近的对象的每像素深度层。

* **FresnelEffect：** 此材料标志为对应材料启用附加的[菲涅尔透镜效果](../../overview/features/fresnel-effect.md)。 此效果的外观由以下介绍的其他菲涅尔透镜参数控制。 

* **FresnelEffectColor：** 材料使用的菲涅尔透镜颜色。 仅在为材料设置了菲涅尔透镜效果位时，它才变得重要（请参阅上述属性）。 此属性控制菲涅尔透镜光照的基础颜色（请参阅[菲涅尔透镜效果](../../overview/features/fresnel-effect.md)以获取完整说明）。 目前仅 rgb 通道值是重要值，忽略 alpha 值。

* **FresnelEffectExponent：** 用于材料的菲涅尔透镜指数。 仅在为材料设置了菲涅尔透镜效果位时，它才变得重要（请参阅上述属性）。 此属性控制菲涅尔透镜光照的传播。 最小值 0.01 将使光照遍布整个对象。 最大值 10.0 会将光照限制为仅最亮的边缘可见。

## <a name="pbr-material-properties"></a>PBR 材料属性

基于物理学的渲染的核心概念是使用 *BaseColor*、*Metalness* 和 *Roughness* 属性来仿真各种真实材料。 本文不会详细介绍 PBR。 有关 PBR 的详细信息，请参阅[其他资源](http://www.pbr-book.org)。 以下属性特定于 PBR 材料：

* **baseColor：** 在 PBR 材料中，albedo 颜色称为基础颜色。  在 Azure 远程渲染中，albedo 颜色属性已通过通用材料属性提供，因此没有其他基础颜色属性。

* **roughness** 和 **roughnessMap：** Roughness 定义表面的粗糙或光滑程度。 与光滑表面相比，粗糙表面可朝更多方向散射光线，使反射变得更模糊而不是更清晰。 值的范围为 `0.0` 到 `1.0`。 当 `roughness` 等于 `0.0` 时，反射将变得清晰。 当 `roughness` 等于 `0.5` 时，反射将变得模糊。

  如果同时提供了粗糙度值和粗糙度贴图，最终的值将是这两个值的积。

* **metalness** 和 **metalnessMap：** 在物理学中，此属性对应于表面是导电性还是介电性的。 导电材料具有不同的反射属性，它们往往是在无 albedo 颜色的情况下具有反射性。 在 PBR 材料中，此属性会影响表面反映周围环境的程度。 值的范围为 `0.0` 到 `1.0`。 当 metalness 为 `0.0` 时，albedo 颜色将完全可见，材料看起来像是塑料或陶瓷。 当 metalness 为 `0.5` 时，材料看起来像是喷了漆的金属。 当 metalness 为 `1.0` 时，表面将几乎完全失去其 albedo 颜色，只反映周围环境。 例如，如果 `metalness` 为 `1.0`，且 `roughness` 为 `0.0`，则表面看起来像是真实的镜子。

  如果同时提供了金属度值和金属度贴图，最终的值将是这两个值的积。

  ![使用不同金属度和粗糙度值渲染的球体](./media/metalness-roughness.png)

  在上图中，右下角的球体看起来像是真实的金属材料，左下角的球体看起来像是陶瓷或塑料。 albedo 颜色还会根据物理属性而变化。 随着粗糙度的增加，材料将失去反射清晰度。

* **normalMap：** 若要精细地模拟细节，可以提供[法线图](https://en.wikipedia.org/wiki/Normal_mapping)。

* **occlusionMap** 和 **aoScale：** [环境遮蔽](https://en.wikipedia.org/wiki/Ambient_occlusion)可在遮挡的区域中添加阴影，使存在裂隙的对象看起来更逼真。 遮蔽值的范围为 `0.0` 到 `1.0`，其中，`0.0` 表示黑暗（遮挡），`1.0` 表示无遮挡物。 如果提供 2D 纹理作为遮蔽贴图，则会启用该效果，*aoScale* 将充当乘数。

  ![在启用和不启用环境遮蔽的情况下渲染的对象](./media/boom-box-ao2.gif)

* **transparent：** 对于 PBR 材料，只有一种透明度设置：是否启用透明度。 不透明度由 albedo 颜色的 alpha 通道定义的。 启用后，调用更复杂的渲染管道来绘制半透明表面。 Azure 远程渲染实现真正的[顺序独立透明度](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT)。

  渲染透明几何体的资源开销很大。 如果只需在表面上绘制小孔（例如绘制树叶），则最好是改用 alpha 裁剪。

  ![使用零透明度到完全透明度渲染的球体](./media/transparency.png) 在上图中可以看到，最右侧的球体是完全透明的，但反射仍然可见。

  > [!IMPORTANT]
  > 如果在运行时应将任何材料从不透明切换为透明，则渲染器必须使用 *TileBasedComposition* [渲染模式](../../concepts/rendering-modes.md)。 此项限制不适用于一开始就转换为透明材料的材料。

## <a name="technical-details"></a>技术详细信息

Azure 远程渲染使用了 Cook-Torrance 微面 BRDF、GGX NDF、Schlick Fresnel 和 GGX Smith 相关可见性术语以及 Lambert 漫射术语。 目前，此模型是事实上的行业标准。 有关更深入的详细信息，请参阅以下文章：[基于物理学的渲染 - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 可以在 Azure 远程渲染中使用“镜面反射-光泽度”PBR 模型来替代“金属度-粗糙度”PBR 模型。  此模型能够表示各种不同的材料。 不过，它的开销更大，且通常不适用于实时案例。
并非始终可以从“镜面反射-光泽度”转换为“金属度-粗糙度”，因为某些 *(Diffuse, Specular)* 值对无法转换为 *(BaseColor, Metalness)* 。  其他方向的转换更简单且更精确，因为所有 *(BaseColor, Metalness)* 对都对应于妥善定义的 *(Diffuse, Specular)* 对。

## <a name="api-documentation"></a>API 文档

* [C# PbrMaterial 类](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection.CreateMaterial()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ PbrMaterial 类](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection::CreateMaterial()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>后续步骤

* [颜色材料](color-materials.md)
* [纹理](../../concepts/textures.md)
* [网格](../../concepts/meshes.md)