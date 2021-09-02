---
title: 在模型转换期间替代材料
description: 介绍转换期间的材料替代工作流
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: f99439ae71f8a66b9fc36b32603681e62f2224a9
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968243"
---
# <a name="override-materials-during-model-conversion"></a>在模型转换期间替代材料

源模型中的材料设置用于定义渲染器所使用的 [PBR 材料](../../overview/features/pbr-materials.md)。
有时[默认转换](../../reference/material-mapping.md)提供不了所需的结果，需要进行更改。
为便于在 Azure 远程渲染中使用而对模型进行转换时，可以提供材料替代文件，从而自定义每种材料的材料转换方式。
如果在输入容器的输入模型 `<modelName>.<ext>` 旁边找到一个名为 `<modelName>.MaterialOverrides.json` 的文件，则该文件将用作材料替代文件。

## <a name="the-override-file-used-during-conversion"></a>转换期间使用的替代文件

举个简单的例子，假设方框模型只含一个称为“默认”的材料。
此外，假设需要调整它的反照率颜色以供在 ARR 中使用。
在这种情况下，可以创建一个 `box.MaterialOverrides.json` 文件，如下所示：

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

将 `box.MaterialOverrides.json` 文件放在输入容器的 `box.fbx` 旁边，指示转换服务应用新的设置。

### <a name="color-materials"></a>颜色材料

[颜色材料](../../overview/features/color-materials.md)模型介绍了无照明情况下持续着色的表面。
例如，颜色材料适用于摄影测量算法创建的资产。
在材料替代文件中，通过将 `unlit` 设置为 `true`，可以将材料声明为颜色材料。

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>忽略特定纹理映射

有时可能需要转换过程忽略特定纹理映射。 当借助某种工具生成模型，且该工具生成渲染器无法正确理解的特定映射时，这种情况就可能发生。 例如，用于定义不透明度以外属性的 "OpacityMap"，或其中 "NormalMap" 已存储为 "BumpMap" 的模型。 （对于后一种情况，需要忽略 "NormalMap"，因为它会导致转换器将 "BumpMap" 用作 "NormalMap"。）

原理很简单。 只需添加一个名为 `ignoreTextureMaps` 的属性，并添加要忽略的纹理映射：

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

有关可忽略的纹理映射的完整列表，请参阅下面的 JSON 架构。

### <a name="applying-the-same-overrides-to-multiple-materials"></a>将相同替代应用于多种材料

默认情况下，当材料替代文件中的条目名与材料名完全匹配时，将应用该条目。
由于经常需要将相同替代应用于多种材料，因此，可以根据需要提供一个正则表达式作为条目名。
字段 `nameMatching` 具有默认值 `exact`，但可以设置为 `regex`，以表明该条目会应用于每种匹配的材料。
所使用的语法与用于 JavaScript 的语法相同。 下面的示例演示了应用于材料名如 "Material2"、"Material01" 和 "Material999" 的一个替代。

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

在材料替代文件中，一个条目最多只能应用于一种材料。
如果材料名得以精确匹配（例如 `nameMatching` 无或结果为 `exact`），则选择该条目。
否则，将选择文件中与材料名匹配的第一个 regex 条目。

### <a name="getting-information-about-which-entries-applied"></a>获取有关应用条目的信息

写入到输出容器的[信息文件](get-information.md#information-about-a-converted-model-the-info-file)提供了有关所提供的替代数以及已替代的材料数的信息。

## <a name="json-schema"></a>JSON 架构

此处提供了材料文件的完整 JSON 架构。 除了 `unlit` 和 `ignoreTextureMaps` 之外，可用属性均为[颜色材料](../../overview/features/color-materials.md)和 [PBR材料](../../overview/features/pbr-materials.md)模型部分中描述的属性的子集。

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" },
            "transparencyWriteDepth": {"type" : "boolean" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>后续步骤

* [有色材料](../../overview/features/color-materials.md)
* [PBR 材料](../../overview/features/pbr-materials.md)
