---
title: 菲涅尔透镜效果
description: 菲涅尔透镜材料效果的功能说明页面
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594106"
---
# <a name="fresnel-effect"></a>菲涅尔透镜效果

菲涅尔透镜效果材料功能是一种非物理修正的特别效果。 该功能基于对物体在这些角度反射性更强的物理观察而构建。 菲涅尔反射率实际上已融入 Azure 远程渲染所使用的 [PBR 材料模型](../../overview/features/pbr-materials.md)中。 相比之下，菲涅尔透镜效果材料功能只是一种不依赖于[光线](../../overview/features/lights.md)或[天空环境](../../overview/features/sky.md)的加色效果。

菲涅尔透镜效果会围绕涉及的物体边缘显示彩色光照。 有关效果自定义的信息和渲染结果的示例，请参阅以下部分。

## <a name="enabling-the-fresnel-effect"></a>启用菲涅尔透镜效果

要使用菲涅尔透镜效果功能，需要对相关材料启用此功能。 通过在 [PBR 材料](../../overview/features/pbr-materials.md)上设置 [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) 的 FresnelEffect 位，可启用该功能。 这一模式也适用于 [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) 和[有色材料](../../overview/features/color-materials.md)。 有关用法演示，请参阅代码示例部分。

启用后，菲涅尔透镜效果将立即可见。 默认情况下，光照为白色 (1, 1, 1, 1)，指数为 1。 可以使用下面的参数 setters 自定义这些设置。

## <a name="customizing-the-effect-appearance"></a>自定义效果显示

目前，可以根据材料使用以下属性自定义菲涅尔透镜效果：

| 材料属性 | 类型 | 说明 |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | 菲涅尔光照中添加最多的颜色。 Alpha 通道当前被忽略。 |
| FresnelEffectExponent | FLOAT | 菲涅尔光照的传播。 范围在 0.01（遍布整个物体）到 10（只有最大掠射角）之间。 |

实际上，不同颜色和指数的设置将如下所示：

![菲涅尔透镜效果示例](./media/fresnel-effect-examples.png)

对于每个颜色行，菲涅尔透镜效果的指数将在 1 至 10 之间逐渐增加。 这样会将菲涅尔光照逐渐传送到所查看物体的边缘。 此外，菲涅尔透镜效果不受透明度影响，如以下示例中所示：

![菲涅尔透镜效果透明度示例](./media/fresnel-effect-transparent-examples.png)

如图所示，对角线上的物体是完全透明的，但菲涅尔光照依然存在。 在这方面，该效果模仿基于物理学的菲涅尔透镜，如屏幕截图中所示。

## <a name="code-samples"></a>代码示例

以下代码示例演示了如何为 [PBR 材料](../../overview/features/pbr-materials.md)和[有色材料](../../overview/features/color-materials.md)启用和自定义菲涅尔透镜效果：

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>API 文档

* [C# PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C# ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>后续步骤

* [材料](../../concepts/materials.md)
* [PBR 材料](../../overview/features/pbr-materials.md)
* [有色材料](../../overview/features/color-materials.md)