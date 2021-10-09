---
title: 配置模型转换
description: 所有模型转换参数的说明
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 263531d24d50c27309163f0671a41ff7aacd36c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601361"
---
# <a name="configure-the-model-conversion"></a>配置模型转换

本章介绍模型转换的选项。

## <a name="settings-file"></a>“设置”文件

如果在输入模型 `<modelName>.<ext>` 旁边的输入容器中找到名为 `<modelName>.ConversionSettings.json` 的文件，则该文件将用于为模型转换过程提供额外配置。
例如，在转换 `box.gltf` 时会使用 `box.ConversionSettings.json`。

该文件的内容应满足以下 json 架构：

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

`box.ConversionSettings.json` 文件的一个示例可能是：

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>几何参数

* `scaling` - 此参数统一缩放模型。 缩放可用于增大或缩小模型，例如用于在桌面上显示建筑模型。
当模型以米之外的单位定义时，缩放也很重要，因为呈现引擎预期的单位为米。
例如，如果模型是以厘米定义的，则应用比例 0.01 应会以正确的大小呈现模型。
一些源数据格式（例如 .fbx）提供单位缩放提示，在这种情况下，转换会将模型隐式缩放为以米为单位。 将在缩放参数的基础上应用源格式提供的隐式缩放。
最终缩放因子应用于场景图形节点的几何顶点和局部转换。 根实体转换的缩放比例保持不变。

* `recenterToOrigin` - 指出应对模型进行转换，使其边界框以原点为中心。
如果源模型离原点较远，则浮点精度问题可能导致呈现噪点。
在这种情况下，将模型居中可以解决问题。

* `opaqueMaterialDefaultSidedness` - 呈现引擎假定不透明材料是双面的。
如果特定模型不符合该假定，则应将此参数设置为 "SingleSided"。 有关详细信息，请参阅 [:::no-loc text="single sided":::呈现](../../overview/features/single-sided-rendering.md)。

### <a name="material-de-duplication"></a>材料去重

* `deduplicateMaterials` - 此参数可允许或禁止对共享相同属性和纹理的材料进行自动去重。 在进行材料覆盖后进行去重。 默认启用。

* 如果去重后，模型的材料还超过 65,535 个，则该服务会尝试合并具有相似属性的材料。 超过该限制的所有材料将替换为红色错误材料，这是最终手段。

![图片显示了两个由 68,921 个彩色三角形组成的多维数据集。](media/mat-dedup.png?raw=true)

两个由 68,921 个彩色三角形组成的多维数据集。 左侧：去重前具有 68,921 个颜色材料。 右侧：去重后具有 64,000 个颜色材料。 材料数量限制为 65,535 个。 （请参阅[限制](../../reference/limits.md)。）

### <a name="color-space-parameters"></a>颜色空间参数

呈现引擎期待颜色值处于线性空间内。
如果模型是使用伽玛空间定义的，则应将这些选项设置为 true。

* `gammaToLinearMaterial` - 将材料颜色从伽玛空间转换为线性空间
* `gammaToLinearVertex` - 将 :::no-loc text="vertex"::: 颜色从伽玛空间转换为线性空间

> [!NOTE]
> 对于 FBX 文件，这些设置默认设置为 `true`。 对于所有其他文件类型，默认值为 `false`。

### <a name="scene-parameters"></a>场景参数

* `sceneGraphMode` - 定义如何转换源文件中的场景图：
  * `dynamic`（默认值）：文件中的所有对象都作为 API 中的[实体](../../concepts/entities.md)公开，并可任意转换和重新设置父级。 运行时的节点层次结构与源文件中的结构相同。
  * `static`：与 `dynamic` 类似，但场景图中的对象不能在运行时动态地重新成为其他对象的父级。 对于具有许多移动部件的动态模型（例如“爆炸视图”），`dynamic`选项会生成一个更高效的模型来呈现，但`static`模式仍允许单个部件转换。 如果不需要动态重设父级，则`static`选项最适合具有多个单独部件的模型。
  * `none`：场景图折叠为一个对象。

每个模式具有不同的运行时性能。 在 `dynamic` 模式下，即使没有移动部件，性能开销也会随图形中[实体](../../concepts/entities.md)的数量线性缩放。 仅当同时移动多个部件或大型子图（例如“炸视图”动画）时，才使用`dynamic`模式。

`static`模式还会导出完整的场景图。 [空间查询](../../overview/features/spatial-queries.md)将返回各个部件，并且可以通过[状态重写](../../overview/features/override-hierarchical-state.md)来修改每个部件。 在此模式下，每个对象的运行时开销可忽略不计。 它适用于下面的大型场景：需要按对象检查，偶尔对单个部件进行转换更改，但不重新设置对象父级。

`none` 模式的运行时开销最小，并且加载时间也稍微快一些。 在此模式下无法检查或转换单个对象。 用例可以是一开始没有有意义场景图的摄影测量模型。

> [!TIP]
> 许多应用程序将加载多个模型。 应根据每个模型的使用方式优化转换参数。 例如，如果想要显示汽车模型，让用户能够详细剖析和检查，则需要以 `dynamic` 模式转换它。 但是，如果还想将该汽车放在展厅环境中，则可在将 `sceneGraphMode` 设置为 `static` 甚至 `none` 的情况下转换该模型。

### <a name="physics-parameters"></a>物理学参数

* `generateCollisionMesh` - 如果需要支持在模型上进行[空间查询](../../overview/features/spatial-queries.md)，则必须启用此选项。 在最糟的情况下，创建冲突网格可能使转换时间翻倍。 具有冲突网格的模型需要更长的加载时间，并且当使用 `dynamic` 场景图时，其运行时性能开销也更高。 为了获得总体而言最佳的性能，应在不需要空间查询的所有模型上禁用此选项。

### <a name="unlit-materials"></a>未打光材料

* `unlitMaterials` - 转换默认倾向于创建 [PBR 材料](../../overview/features/pbr-materials.md)。 此选项告知转换器改为将所有材料视为[有色材料](../../overview/features/color-materials.md)。 如果你拥有已包含灯光的数据（如通过摄影测量创建的模型），则此选项允许快速对所有材料强制执行正确的转换，而无需单独[重写每种材料](override-materials.md)。

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>使用 Phong 材料模型从较旧的 FBX 格式进行转换

* `fbxAssumeMetallic` - 较旧版本的 FBX 格式使用 Phong 材料模型定义其材料。 转换过程必须推断这些材料如何映射到呈现器的 [PBR 模型](../../overview/features/pbr-materials.md)。 这种做法的效果通常很好，但当材料没有纹理、高反射值和非灰返照色时，可能会出现模棱两可的情况。 在这种情况下，转换必须选择优先考虑高反射值，定义反射性强的金属材料使返照色消失，或者优先考虑返照色，定义类似于光泽有色塑料的材料。 默认情况下，如果存在歧义，则转换过程假定高反射值表示金属材料。 可以将此参数设置为 `false` 以切换到另一种情况。

### <a name="coordinate-system-overriding"></a>坐标系统替代

* `axis` - 替代坐标系统单位矢量。 默认值有 `["+x", "+y", "+z"]`。 理论上，FBX 格式具有标头，其中定义了这些矢量以及使用该信息来转换场景的转换。 glTF 格式也定义固定坐标系统。 在实践中，某些资产的标头中包含不正确的信息，或者是使用不同的坐标系统约定保存的。 此选项可用于替代坐标系统以进行补偿。 例如：`"axis" : ["+x", "+z", "-y"]` 将交换 Z 轴和 Y 轴，并通过反转 Y 轴方向来保持坐标系统的旋向。

### <a name="node-meta-data"></a>节点元数据

* `metadataKeys` - 用于指定要保留在转换结果中的节点元数据属性的键。 可以指定精确键或通配符键。 通配符键的格式为 "ABC*"，可与任何以 "ABC" 开头的键匹配。 支持的元数据值类型为 `bool`、`int`、`float` 和 `string`。

    对于 GLTF 文件，此数据来自[节点上的 extras 对象](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras)。 对于 FBX 文件，此数据来自 `Properties70` 上的 `Model nodes` 数据。 有关更多详细信息，请参阅 3D 资产工具的文档。

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: 格式

可以调整网格的 :::no-loc text="vertex"::: 格式，损失精度以节省内存。 占用内存较低，可以加载更大的模型或获得更好的性能。 但是，根据具体数据，格式错误可能会显著影响呈现质量。

> [!CAUTION]
> 更改 :::no-loc text="vertex"::: 格式应该是一种最终手段，用于内存不足以处理模型时，或进行优化以实现最佳性能时。 更改很容易引入呈现噪点，包括明显和细微的噪点。 除非你知道应该注意什么，否则不应更改默认值。

可以进行以下调整：

* 可以显式包括或排除特定数据流。
* 可以减少数据流的准确性以减少内存占用量。

`.json` 文件中的以下 `vertex` 部分是可选的。 对于未显式指定的每个部分，转换服务将回退到其默认设置。

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

通过将组件强制为 `NONE`，可以保证输出网格没有各自的流。

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>每个 :::no-loc text="vertex"::: 流的组件格式

允许对各自的组件使用以下格式：

| :::no-loc text="Vertex"::: 组件 | 支持的格式（粗体 = 默认值） |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, NONE |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **NONE**|
|一般| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|tangent| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NONE |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, NONE |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, NONE |

#### <a name="supported-component-formats"></a>支持的组件格式

各种格式的内存占用量如下：

| 格式 | 说明 | 每 :::no-loc text="vertex"::: 的字节数 |
|:-------|:------------|:---------------|
|32_32_FLOAT|双组件全浮点精度|8
|16_16_FLOAT|双组件半浮点精度|4
|32_32_32_FLOAT|三组件全浮点精度|12
|16_16_16_16_FLOAT|四组件半浮点精度|8
|8_8_8_8_UNSIGNED_NORMALIZED|四组件字节，规范化为 `[0; 1]` 范围|4
|8_8_8_8_SIGNED_NORMALIZED|四组件字节，规范化为 `[-1; 1]` 范围|4

#### <a name="best-practices-for-component-format-changes"></a>组件格式更改的最佳做法

* `position`：降低的准确度够用的情况很罕见。 **16_16_16_16_FLOAT** 会引入明显的量化噪点，即使对于小型模型也是如此。
* `normal`、`tangent`、`binormal`：通常，要一起更改这些值。 除非存在由正常量化而导致的明显发光点，否则没有理由提高其准确度。 但在某些情况下，可将这些组件设置为“NONE”：
  * 仅当应为模型中至少一种材料打光时，才需要 `normal`、`tangent` 和 `binormal`。 在 ARR 中，只要将 [PBR 材料](../../overview/features/pbr-materials.md)用于模型，就符合这种情况。
  * 仅当有已打光的材料使用普通映射纹理时，才需要 `tangent` 和 `binormal`。
* `texcoord0`、`texcoord1`：如果纹理坐标的值保持在 `[0; 1]` 范围内，并且被强调纹理的最大大小为 2048 x 2048 像素，则纹理坐标可以使用降低的准确度 (16_16_FLOAT)。 如果超出这些限制，纹理映射的质量就会降低。

#### <a name="example"></a>示例

假设你有一个摄影测量模型，其纹理中融入了照明。 要呈现该模型，只需要 :::no-loc text="vertex"::: 位置和纹理坐标。

默认情况下，转换器必须假设你在某个时间会想在模型上使用 PBR 材料，因此它将为你生成 `normal`、`tangent` 和 `binormal` 数据。 因此，每个顶点的内存使用量为 `position`（12 字节）+ `texcoord0`（8 字节）+ `normal`（4 字节）+ `tangent`（4 字节）+ `binormal`（4 字节）= 32 字节。 这种类型的大型模型很容易拥有数百万个 :::no-loc text="vertices":::，导致模型可能占用数 GB 的内存。 如此大量的数据会影响性能，甚至可能耗尽内存。

如果知道永远不需要对模型进行动态照明，并且知道所有纹理坐标都在 `[0; 1]` 范围内，就可以将 `normal`、`tangent` 和 `binormal` 设置为 `NONE`，并将 `texcoord0` 设置为半精度 (`16_16_FLOAT`)，从而使每个 :::no-loc text="vertex"::: 仅占用 16 个字节。 将网格数据一分为二，可以加载更大的模型，并可能提高性能。

## <a name="memory-optimizations"></a>内存优化

已加载内容对内存的消耗可能会成为呈现系统的瓶颈。 如果内存负载过大，则可能影响呈现性能或导致模型完全不加载。 本段落讨论了一些用于降低内存占用量的重要策略。

### <a name="instancing"></a>实例化

与引用自身唯一几何图形的部件相反，实例化概念是指具有不同空间转换的部件会重用网格。 实例化对内存占用量有重大影响。
实例化示例用例可以是引擎模型中的螺丝，也可以是体系结构模型中的椅子。

> [!NOTE]
> 实例化可以显著降低内存消耗（因此缩短加载时间），但对渲染性能的提升作用微乎其微。

如果源文件中已相应标记了部件，则转换服务不会遵循实例化。 但是，转换不会对网格数据进行其他深入分析来识别可重用的部件。 因此，内容创建工具及其导出管道是正确进行实例化设置的决定性条件。

测试转换过程中是否保留了实例化信息的一种简单方法是查看[输出统计信息](get-information.md#example-info-file)，具体来说是 `numMeshPartsInstanced` 成员。 如果 `numMeshPartsInstanced` 的值大于零，则表示多个实例共享网格。

#### <a name="example-instancing-setup-in-3ds-max"></a>示例：3DS Max 中的实例化设置

[Autodesk 3DS Max](https://www.autodesk.de/products/3ds-max) 提供不同的对象克隆模式，分别为 **`Copy`** 、 **`Instance`** 和 **`Reference`** ，这些模式在 `.fbx` 导出文件中的实例化行为有所不同。

![在 3ds Max 中克隆](./media/3dsmax-clone-object.png)

* **`Copy`** ：在此模式下，将克隆网格，因此不会使用实例化 (`numMeshPartsInstanced` = 0)。
* **`Instance`** ：两个对象共享同一个网格，因此会使用实例化 (`numMeshPartsInstanced` = 1)。
* **`Reference`** ：可将不同修饰符应用于几何图形，因此导出程序选择传统方法，而不使用实例化 (`numMeshPartsInstanced` = 0)。


### <a name="depth-based-composition-mode"></a>基于深度的组合模式

如果内存占用量较大，请使用[基于深度的组合模式](../../concepts/rendering-modes.md#depthbasedcomposition-mode)配置呈现器。 在此模式下，GPU 有效负载分布于多个 GPU。

### <a name="decrease-vertex-size"></a>减小顶点大小

如[组件格式更改的最佳做法](configure-model-conversion.md#best-practices-for-component-format-changes)一节所述，调整顶点格式可以减少内存占用量。 但是，此选项应该是最终手段。

### <a name="texture-sizes"></a>纹理大小

根据方案的类型，纹理数据量可能超出网格数据所用的内存量。 摄影测量模型是候选项。
转换配置不支持自动纵向缩减纹理。 如有必要，必须在客户端预处理步骤中完成纹理缩放。 但是，转换步骤会选取合适的[纹理压缩格式](/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)：

* `BC1` 表示不透明颜色纹理
* `BC7` 表示带有 alpha 通道的源颜色纹理

`BC7` 格式的内存占用量是 `BC1` 的两倍，因此请务必确保输入纹理不会提供多余的 alpha 通道。

## <a name="typical-use-cases"></a>典型用例

为给定用例找到适当的导入设置可能是一个枯燥的过程。 另一方面，转换设置可能对运行时性能产生严重影响。

某些类型的用例可以使用特定的优化。 下面给出了一些示例。

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>用例：体系结构可视化/大型户外地图

* 这种类型的场景通常是静态的，这意味着它们不需要可移动部件。 因此，可以将 `sceneGraphMode` 设置为 `static` 甚至 `none`，从而提高运行时性能。 在 `static` 模式下，仍可移动、旋转和缩放场景的根节点，以便进行在 1:1 缩放比例（用于第一人称视图）和桌面视图之间动态切换等操作。

* 如果需要移动部件，这通常还意味着需要支持打光或其他[空间查询](../../overview/features/spatial-queries.md)，以便在一开始就选中这些部件。 另一方面，如果不打算移动部件，则很有可能也不需要它参与空间查询，因此可以关闭 `generateCollisionMesh` 标志。 此开关对转换时间、加载时间以及运行时的每帧更新成本有重大影响。

* 如果应用程序不使用[剪切平面](../../overview/features/cut-planes.md)，则应关闭 `opaqueMaterialDefaultSidedness` 标志。 性能提升通常为 20%-30%。 剪切平面仍可使用，但在查看对象的内部部件时看不到背面，这与人的直觉相悖。 有关详细信息，请参阅 [:::no-loc text="single sided":::呈现](../../overview/features/single-sided-rendering.md)。

### <a name="use-case-photogrammetry-models"></a>用例：摄影测量模型

呈现摄影测量模型时，通常不需要场景图，因此可以将 `sceneGraphMode` 设置为 `none`。 但由于这些模型一开始极少包含复杂场景图，此选项的影响应该无关紧要。

由于已将光照融入纹理，因此不需要动态照明。 因此：

* 将 `unlitMaterials` 标志设置为 `true`以将所有材料转换为未打光[有色材料](../../overview/features/color-materials.md)。
* 从顶点格式中删除不需要的数据。 请参阅以上[示例](#example)。

### <a name="use-case-visualization-of-compact-machines-etc"></a>用例：小型机器的可视化等。

在这些用例中，模型通常很小但细节非常丰富。 呈现器进行了大量优化，可以很好地处理这种情况。 但是，以上用例中提到的大多数优化在这里并不适用：

* 各个部件应是可选择且可移动的，因此必须将 `sceneGraphMode` 保持为 `dynamic`。
* 打光通常是应用程序不可分割的一部分，因此必须生成冲突网格。
* 启用 `opaqueMaterialDefaultSidedness` 标志可美化剪切平面。

## <a name="deprecated-features"></a>已弃用的功能

仍支持使用非特定于模型的文件名 `conversionSettings.json` 来提供设置，但该功能已弃用。
请改用特定于模型的文件名 `<modelName>.ConversionSettings.json`。

仍支持使用 `material-override` 设置来标识转换设置文件中的[材料替代文件](override-materials.md)，但该功能已弃用。 请改用特定于模型的文件名 `<modelName>.MaterialOverrides.json`。

## <a name="next-steps"></a>后续步骤

* [模型转换](model-conversion.md)
* [有色材料](../../overview/features/color-materials.md)
* [PBR 材料](../../overview/features/pbr-materials.md)
* [在模型转换期间替代材料](override-materials.md)