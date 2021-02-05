---
title: 菲涅尔透镜效果
description: 菲涅尔衰减材料效果的功能说明页
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594106"
---
# <a name="fresnel-effect"></a>菲涅尔透镜效果

菲涅尔衰减效果材料功能是一个非物理的、即席的副作用。 此功能基于对象的物理观察，它们会越来越多地以这些角度反射。 菲涅尔衰减 reflectance 本身已在物理上并入 Azure 远程呈现中使用的 [.pbr 材料模型](../../overview/features/pbr-materials.md) 。 与此相反，菲涅尔衰减效果材料功能只是一种增加的颜色效果，不依赖于 [光源](../../overview/features/lights.md) 或 [天空环境](../../overview/features/sky.md)。

菲涅尔衰减效果可使受影响的对象围绕其边缘进行彩色的照射。 有关效果自定义的信息和呈现结果的示例，请参阅以下各节。

## <a name="enabling-the-fresnel-effect"></a>启用菲涅尔衰减效果

若要使用菲涅尔衰减效果功能，需要对相关材料启用此功能。 可以通过在[.pbr 材料](../../overview/features/pbr-materials.md)上设置[PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)的 FresnelEffect 位来启用它。 同一模式适用于 [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) 和 [颜色材料](../../overview/features/color-materials.md)。 有关用法演示，请参阅代码示例部分。

启用后，菲涅尔衰减效果将立即可见。 默认情况下，闪光将为白色 (1，1，1，1) 并且指数为1。 可以使用下面的参数 setter 自定义这些设置。

## <a name="customizing-the-effect-appearance"></a>自定义效果外观

目前，可以使用以下属性针对每个材料自定义菲涅尔衰减效果：

| 材料属性 | 类型 | 说明 |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | 最多添加为菲涅尔衰减的颜色。 Alpha 通道当前被忽略。 |
| FresnelEffectExponent | float | 菲涅尔衰减的传播。 介于 0.01 (范围内的所有对象) 到 10 (只) 最 gracing 角。 |

实际上，不同的颜色和指数设置将如下所示：

![菲涅尔衰减效果示例](./media/fresnel-effect-examples.png)

对于每个颜色行，菲涅尔衰减效果的指数逐渐增加1到10。 这样做会逐渐将菲涅尔衰减的照射到所查看对象的边缘。 菲涅尔衰减效果还不受透明度影响，如以下示例中所示：

![菲涅尔衰减效果透明度示例](./media/fresnel-effect-transparent-examples.png)

正如图中所示，对角线上的对象是完全透明的，但菲涅尔衰减的闪光会保持不变。 此效果模拟基于物理的菲涅尔衰减，这也是在这些屏幕截图中提供的。

## <a name="code-samples"></a>代码示例

下面的代码示例演示了如何为 [.pbr 材料](../../overview/features/pbr-materials.md) 和 [颜色材料](../../overview/features/color-materials.md)启用和自定义菲涅尔衰减效果：

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

* [C # PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C + + PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C # ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C + + ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>后续步骤

* [材料](../../concepts/materials.md)
* [.PBR 材料](../../overview/features/pbr-materials.md)
* [颜色材料](../../overview/features/color-materials.md)