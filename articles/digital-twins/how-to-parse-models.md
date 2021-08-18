---
title: 分析和验证模型
titleSuffix: Azure Digital Twins
description: 了解如何使用分析程序库分析 DTDL 模型。
author: baanders
ms.author: baanders
ms.date: 8/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 52cec60dc771232fcb808184373b82f790f8a759
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252755"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>使用 DTDL 分析程序库分析和验证模型

Azure 数字孪生中的[模型](concepts-models.md)是使用基于 JSON-LD 的数字孪生定义语言 (DTDL) 定义的。 **建议先离线验证你的模型，然后再将其上传到你的 Azure 数字孪生实例。**

为帮助你执行此操作，NuGet 上提供了一个 .NET 客户端 DTDL 分析库：[Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)。 

你可以直接在 C# 代码中使用分析程序库，也可以使用基于分析程序库构建的与语言无关的代码示例项目：[DTDL 验证程序示例](/samples/azure-samples/dtdl-validator/dtdl-validator)。

## <a name="use-the-dtdl-validator-sample"></a>使用 DTDL 验证程序示例

[DTDL 验证程序](/samples/azure-samples/dtdl-validator/dtdl-validator)是一个示例项目，它可以对模型文档进行验证以确保 DTDL 有效。 它是基于 .NET 分析程序库构建的，与语言无关。 可以通过示例链接中的“下载 ZIP”按钮获取它。

源代码显示了有关如何使用分析程序库的示例。 可以将验证程序示例用作命令行实用工具来验证 DTDL 文件的目录树。 它还提供交互模式。

在 DTDL 验证程序示例的文件夹中查看 readme.md 文件，了解如何将示例打包为独立的可执行文件。

构建独立的程序包并将可执行文件添加到你的路径后，可以在计算机上的控制台中使用以下命令运行该验证程序：

```cmd/sh
DTDLValidator
```

示例将使用默认选项在当前目录和所有子目录中搜索 .json 文件。 你还可以添加以下选项，让示例在指定的目录和所有子目录中搜索扩展名为 .dtdl 的文件：

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

可以为示例添加 `-i` 选项以进入交互模式：

```cmd/sh
DTDLValidator -i
```

有关此示例的详细信息，请参阅源代码或运行 `DTDLValidator --help`。

## <a name="use-the-net-parser-library"></a>使用 .NET 分析程序库 

[Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) 库提供对 DTDL 定义的模型访问权限，本质上相当于 DTDL 的 C# 反射。 此库可独立于任何 [Azure 数字孪生 SDK](concepts-apis-sdks.md) 使用，尤其适合在可视化编辑器或文本编辑器中进行 DTDL 验证。 使用它，可以在尝试将模型定义文件上传到服务之前确保这些文件有效。

若要使用分析程序库，请向其提供一组 DTDL 文档。 通常情况下，你将从服务中检索这些模型文档，但如果你的客户端从一开始就负责将它们上传到服务中，则也可在本地获取它们。 

下面是使用分析程序的一般工作流：
1. 从服务中检索部分或全部 DTDL 文档。
2. 将返回的内存中 DTDL 文档传递到分析程序。
3. 分析程序会验证传递给它的文档集，并返回详细的错误信息。 这种功能在编辑器场景中很有用。
4. 使用分析程序 API 继续分析文档集中包括的模型。 

分析程序的功能包括：
* 获取所实现的所有模型接口（接口的 `extends` 节的内容）。
* 获取在模型中声明的所有属性、遥测、命令、组件和关系。 此命令还获取这些定义中包括的所有元数据，并且会考虑到继承（`extends` 节）。
* 获取所有复杂的模型定义。
* 确定模型是否可从另一个模型分配。

> [!NOTE]
> [IoT 即插即用 (PnP)](../iot-develop/overview-iot-plug-and-play.md) 设备使用小语法变体来描述其功能。 此语法变体是 Azure 数字孪生中使用的 DTDL 的一个子集，在语义上与其兼容。 使用分析程序库时，你不需要知道数字孪生体的 DTDL 是使用哪种语法变体创建的。 默认情况下，对于 PnP 语法和 Azure 数字孪生语法，分析程序会始终返回相同的模型。

### <a name="code-with-the-parser-library"></a>包含分析程序库的代码

你可以直接将分析程序库用于下述用途：在你自己的应用程序中验证模型，或者生成动态的、模型驱动的 UI、仪表板和报表。

若要支持下面的分析程序代码示例，请考虑 Azure 数字孪生实例中定义的多个模型：

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

以下代码显示的示例展示了如何在 C# 中使用分析程序库来反映这些定义：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>后续步骤

编写完模型后，请了解如何通过 DigitalTwinsModels API 上传它们（以及执行其他管理操作）：
* [管理 DTDL 模型](how-to-manage-model.md)