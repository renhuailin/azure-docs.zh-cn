---
title: 模型格式的材料映射
description: 介绍从模型源格式到 PBR 材料的默认转换过程
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96024038"
---
# <a name="material-mapping-for-model-formats"></a>模型格式的材料映射

将源资产[转换为模型](../how-tos/conversion/model-conversion.md)时，转换器将为每个[网格](../concepts/meshes.md)创建[材料](../concepts/materials.md)。 材料的创建方式是可以[替代的](../how-tos/conversion/override-materials.md)。 但是转换过程默认创建 [PBR 材料](../overview/features/pbr-materials.md)。 由于每个源文件格式（如 FBX）使用自己的约定来定义材料，因此必须将这些约定映射到 Azure 远程渲染的 PBR 材料参数。 

本文列出了用于将材料从源资产转换为运行时材料的确切映射。

## <a name="gltf"></a>glTF

除了 EmissiveFactor 和 EmissiveTexture以外，Azure 远程渲染几乎支持 glTF 2.0 规范中的所有映射。

下表显示了映射：

| glTF | Azure 远程渲染 |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   roughness                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   occlusion                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode.OPAQUE  |   alphaClipEnabled = false，isTransparent = false |
|   alphaMode.MASK    |   alphaClipEnabled = true，isTransparent = false  |
|   alphaMode.BLEND   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

glTF 中的每个纹理都包含 `texCoord` 值，也支持用于 Azure 远程渲染材料。

### <a name="embedded-textures"></a>嵌入的纹理

支持 \*.bin 或 \*.glb 文件中嵌入的纹理。

### <a name="supported-gltf-extension"></a>支持的 glTF 扩展

除了基础特征集之外，Azure 远程渲染还支持以下 glTF 扩展：

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md)：对应于[颜色材料](../overview/features/color-materials.md)。 对于放射材料，建议使用此扩展。
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md)：可以提供 diffuse-specular-glossiness 纹理，与 metallic-roughness 纹理相反。 Azure 远程渲染实现直接遵循此扩展中的转换公式。

## <a name="fbx"></a>FBX

FBX 格式采用封闭源代码，因此 FBX 材料通常与 PBR 材料不兼容。 FBX 使用包含很多唯一参数和属性的复杂表面描述，但仅有部分可以用于 Azure 远程渲染管道。

> [!IMPORTANT]
> Azure 远程渲染模型转换管道仅支持 FBX 2011 及以上版本。

FBX 格式定义了用于材料的传统方法，官方 FBX 规范中只提供两种类型：

* *Lambert* - 已经很长时间不常使用，但在转换到 Phong 的转换期间仍提供对它的支持。
* *Phong* - 几乎所有材料和大多数内容工具都使用此类型。

Phong 模型更为精准，它用作 FBX 材料的唯一模型。 下面的 FBX 材料将参考此模型。

> Maya 通过定义 PBR 的自定义属性和材料的 Stingray 类型，将两个自定义扩展用于 FBX。 这些详细信息未包含在 FBX 规范中，因此当前不受 Azure 远程渲染支持。

FBX 材料使用 Diffuse-Specular-SpecularLevel 概念，因此，若要从漫射纹理转换到反照率图，我们需要考虑其他参数并将它们从漫射中去除。

> FBX 中的所有颜色和纹理都在 sRGB 空间中（也称为 Gamma 空间），但 Azure 远程渲染在可视化期间使用线性空间，并在帧结束时将所有内容转换回 sRGB 空间。 Azure 远程渲染资产管道将所有内容转换到线性空间，并将其作为准备好的数据发送到渲染器。

此表显示了如何将纹理从 FBX 材料映射到 Azure 远程渲染材料。 其中一些纹理不会直接使用，而是与公式中的其他纹理组合使用（例如漫射纹理）：

| FBX | Azure 远程渲染 |
|:-----|:----|
| AmbientColor | 遮蔽图   |
| DiffuseColor | 用于反照率、金属度 |
| TransparentColor | 用于反照率的 alpha 通道 |
| TransparencyFactor | 用于反照率的 alpha 通道 |
| 不透明度 | 用于反照率的 alpha 通道 |
| SpecularColor | 用于反照率、金属度、粗糙度 |
| SpecularFactor| 用于反照率、金属度、粗糙度 |
| ShininessExponent | 用于反照率、金属度、粗糙度 |
| NormalMap | NormalMap |
| Bump | 转换为 NormalMap |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

以上映射是材料转换中最复杂的部分，因为需要进行很多假定。 下面将介绍这些假定情况。

使用下面一些定义：

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.Red ∗ 0.2125 +  `Specular`.Green ∗ 0.7154 + `Specular`.Blue ∗ 0.0721
* `DiffuseBrightness` = 0.299 * `Diffuse`.Red<sup>2</sup> + 0.587 * `Diffuse`.Green<sup>2</sup> + 0.114 * `Diffuse`.Blue<sup>2</sup>
* `SpecularBrightness` = 0.299 * `Specular`.Red<sup>2</sup> + 0.587 * `Specular`.Green<sup>2</sup> + 0.114 * `Specular`.Blue<sup>2</sup>
* `SpecularStrength` = max(`Specular`.Red, `Specular`.Green, `Specular`.Blue)

请从[此处](https://en.wikipedia.org/wiki/Luma_(video))获取 SpecularIntensity 公式。
有关亮度公式的说明，请参阅此[规范](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)。

### <a name="roughness"></a>粗糙度

在[公式](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)中使用 `Specular` 和 `ShininessExponent` 计算得出 `Roughness`。 该公式使用 Phong 反射指数计算得出粗糙度的近似值：

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>金属度

在 [glTF 规范提供的公式](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)中使用 `Diffuse` 和 `Specular` 计算得出 `Metalness`。

此处思路在于求解方程：Ax<sup>2</sup> + Bx + C = 0。
大体上，非传导性表面以反射方式反射约 4% 的光线，而其余光线则以漫射方式反射。 金属表面不会以漫射方式反射光线，而是全部以反射方式反射光线。
此公式有一些缺点，它无法区分有光泽的塑料表面和有光泽的金属表面。 假定大多数情况下表面具有金属属性，结果，有光泽的塑料/橡胶表面看起来可能并不正常。

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>反照率

使用 `Diffuse`、`Specular` 和 `Metalness` 计算得出 `Albedo`。

如“金属度”部分中所述，非传导性表面反射约 4% 的光线。  
此处思路是使用 `Metalness` 值作为因子，以线性方式在 `Dielectric` 和 `Metal` 颜色之间进行内插。 如果金属度为 `0.0`，则根据反射情况，要么是暗色（当反射值很大时），要么漫射不会变化（当没有反射时）。 如果金属度的值很大，则漫射颜色将会消失，反射颜色会加重。

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

已根据以上公式计算得出 `AlbedoRGB`，但 alpha 通道还需要进行其他计算。 FBX 格式对透明度没有明确规定，有多种方法可以定义它。 不同的内容工具使用不同的方法。 此处思路是将它们统一为一个公式。 但如果不是以常见方式创建资产，则此公式会将它们错误地显示为透明。

使用 `TransparentColor`、`TransparencyFactor`、`Opacity` 进行计算。

如果定义了 `Opacity`，则直接使用它：`AlbedoAlpha`  =  `Opacity` else  
如果定义了 `TransparencyColor`，则 `AlbedoAlpha` = 1.0 - ((`TransparentColor`.Red + `TransparentColor`.Green + `TransparentColor`.Blue) / 3.0) else  
如果 `TransparencyFactor`，则 `AlbedoAlpha` = 1.0 - `TransparencyFactor`

最终 `Albedo` 颜色具有四个通道，并将 `AlbedoRGB` 与 `AlbedoAlpha` 组合在一起。

### <a name="summary"></a>摘要

总之，如果 `Specular` 接近于零，则 `Albedo` 将非常接近于原始的 `Diffuse`。 否则，表面将类似于金属表面，且失去漫射颜色。 如果 `ShininessExponent` 足够大且 `Specular` 很明亮，则表面更类似于抛光且反射光。 否则，表面将会很粗糙，且几乎不反射环境中的光线。

### <a name="known-issues"></a>已知问题

* 当前公式不适用于简单的彩色几何图形。 如果 `Specular` 足够明亮，则所有几何图形都将成为没有任何颜色的反光金属表面。 解决方法是将 `Specular` 从原始值调低到 30%，或者使用转换设置 [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)。
* `Maya` 和 `3DS Max` 内容创建工具中最近添加了 PBR 材料。 它们使用用户定义的自定义黑盒属性将其传递到 FBX。 Azure 远程渲染不会读取这些附加属性，因为没有记录这些属性且其格式采用封闭源代码。

## <a name="next-steps"></a>后续步骤

* [模型转换](../how-tos/conversion/model-conversion.md)
* [在模型转换期间替代材料](../how-tos/conversion/override-materials.md)
