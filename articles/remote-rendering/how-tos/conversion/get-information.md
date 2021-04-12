---
title: 获取转换的相关信息
description: 获取转换的相关信息
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "91576619"
---
# <a name="get-information-about-conversions"></a>获取转换的相关信息

## <a name="information-about-a-conversion-the-result-file"></a>转换的相关信息：结果文件

转换服务在转换资产时，会将所有问题的摘要写入“结果文件”。 例如，如果转换 `buggy.gltf` 文件，则输出容器将包含一个名为 `buggy.result.json` 的文件。

结果文件列出转换期间发生的所有错误和警告，并提供结果摘要，显示 `succeeded`、`failed` 或 `succeeded with warnings`。
结果文件的结构为对象 JSON 阵列，每个对象均具有一个字符串属性，即 `warning`、`error`、`internal warning`、`internal error` 或 `result`。 最多只会有一个错误（要么 `error`，要么 `internal error`），且始终只会有一个 `result`。

## <a name="example-result-file"></a>结果文件示例

下面的示例描述了已成功生成 arrAsset 的转换。 但是，由于缺少纹理，生成的 arrAsset 可能并不适用。

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>转换后的模型的有关信息：信息文件

转换服务生成的 arrAsset 文件仅适用于渲染服务。 但有时，你可能希望在不启动渲染会话的情况下访问有关模型的信息。 为了支持此工作流，转换服务将一个 JSON 文件放在输出容器中的 arrAsset 文件旁边。 例如，如果转换 `buggy.gltf` 文件，则输出容器将包含一个名为 `buggy.info.json` 的文件，该文件就在转换后的资产 `buggy.arrAsset` 旁边。 它包含有关源模型、转换后的模型以及转换本身的信息。

## <a name="example-info-file"></a>信息文件示例

下面是通过转换名为 `buggy.gltf` 的文件而生成的示例信息文件：

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>信息文件中的信息

### <a name="the-files-section"></a>files 部分

此部分包含提供的文件名。

* `input`：源文件名。
* `output`：输出文件名（在用户已指定非默认名的情况下）。

### <a name="the-conversionsettings-section"></a>ConversionSettings 部分

此部分保存模型转换时指定的 [ConversionSettings](configure-model-conversion.md#settings-file) 的副本。

### <a name="the-inputinfo-section"></a>InputInfo 部分

此部分记录有关源文件格式的信息。

* `sourceAssetExtension`：源文件的文件扩展名。
* `sourceAssetFormat`：源文件格式的说明。
* `sourceAssetFormatVersion`：源文件格式的版本。
* `sourceAssetGenerator`：生成源文件的工具的名称（如果可用）。

### <a name="the-materialoverrides-section"></a>MaterialOverrides 部分

此部分提供有关在向转换服务提供材料替代文件时的[材料替代](override-materials.md)的信息。
其中包含以下信息：
* `numOverrides`：从材料替代文件中读取的替代条目数。
* `numOverriddenMaterials`：已替代的材料数。

### <a name="the-inputstatistics-section"></a>InputStatistics 部分

此部分提供有关源场景的信息。 此部分中的值与源模型创建工具中的等效值之间通常会有差异。 这种差异在意料之中，因为在导出和转换步骤中修改了模型。

* `numMeshes`：网格部件的数量，其中每个部件都可以引用单个材料。
* `numFaces`：整个模型中的三角形总数。 请注意，在转换期间会对网格进行三角形化处理。 此数量有助于确定[标准渲染服务器大小](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons)中的多边形限制。
* `numVertices`：整个模型中的顶点总数。
* `numMaterial`：整个模型中的材料总数。
* `numFacesSmallestMesh`：模型最小网格中的三角形数。
* `numFacesBiggestMesh`：模型最大网格中的三角形数。
* `numNodes`：模型场景图中的节点数。
* `numMeshUsagesInScene`：节点引用网格的次数。 多个节点可能引用同一个网格。
* `maxNodeDepth`：场景图中节点的最大深度。

### <a name="the-outputinfo-section"></a>OutputInfo 部分

此部分记录有关生成的输出的一般信息。

* `conversionToolVersion`：模型转换器的版本。
* `conversionHash`：ArrAsset 中可用于渲染的数据哈希。 可用于了解在对同一文件上重新运行转换服务时，两次转换是否生成了不同的结果。

### <a name="the-outputstatistics-section"></a>outputStatistics 部分

此部分记录通过转换后的资产计算的信息。

* `numMeshPartsCreated`：arrAsset 中的网格数。 它可能与 `inputStatistics` 部分中的 `numMeshes` 不同，因为转换过程会影响实例化。
* `numMeshPartsInstanced`：arrAsset 中重用的网格数。
* `recenteringOffset`：在 [ConversionSettings](configure-model-conversion.md) 中启用 `recenterToOrigin` 选项时，会根据此值将转换后的模型移回其原始位置。
* `boundingBox`：模型的边界。

## <a name="deprecated-features"></a>已弃用的功能

转换服务将文件 `stdout.txt` 和 `stderr.txt` 写入到输出容器，之前一直使用这些文件查看警告和错误。
现在已弃用它们。 为此，请改用[结果文件](#information-about-a-conversion-the-result-file)。

## <a name="next-steps"></a>后续步骤

* [模型转换](model-conversion.md)
* [配置模型转换](configure-model-conversion.md)
