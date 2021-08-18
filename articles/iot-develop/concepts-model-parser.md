---
title: 了解数字孪生体模型分析程序 | Microsoft Docs
description: 了解如何以开发人员身份使用 DTDL 分析程序来验证模型。
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: a4deb82ad3d9029483813e6aa1fd9813c7d815fa
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406318"
---
# <a name="understand-the-digital-twins-model-parser"></a>了解数字孪生模型分析程序

[DTDL 规范](https://github.com/Azure/opendigitaltwins-dtdl)中介绍了数字孪生体定义语言 (DTDL)。 用户可以使用数字孪生体模型分析程序 NuGet 包来验证和查询多个文件中定义的模型。

## <a name="install-the-dtdl-model-parser"></a>安装 DTDL 模型分析程序

NuGet.org 中提供了 ID 为 [Microsoft.Azure.DigitalTwins.Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser) 的分析程序。 若要安装该分析程序，请使用任何兼容的 NuGet 包管理器（如 Visual Studio 或 `dotnet` CLI 中的包管理器）。

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> 撰写本文时，分析程序版本为 `3.12.5`。

## <a name="use-the-parser-to-validate-a-model"></a>使用分析程序来验证模型

模型可由 JSON 文件中描述的一个或多个接口组成。 可以使用分析程序加载给定文件夹中的所有文件，并使用分析程序将所有文件（包括文件间的所有引用）作为一个整体进行验证：

1. 使用一个包含所有模型内容的列表来创建 `IEnumerable<string>`：

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. 实例化 `ModelParser` 并调用 `ParseAsync`：

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. 检查验证错误。 如果分析程序发现任何错误，则会引发 `ParsingException` 并显示错误列表：

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. 检查 `Model`。 如果验证成功，则可以使用模型分析程序 API 来检查模型。 以下代码片段演示如何循环访问已分析的所有模型，并显示现有属性：

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>后续步骤

本文介绍的模型分析程序 API 支持使用许多方案来自动执行或验证依赖于 DTDL 模型的任务。 例如，可以根据模型中的信息动态生成 UI。
