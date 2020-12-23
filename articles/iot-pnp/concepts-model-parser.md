---
title: 了解数字孪生模型分析器 |Microsoft Docs
description: 作为开发人员，了解如何使用 DTDL 分析器来验证模型。
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d68abe8548dac3306228683e4b6ce8935a248ebc
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331781"
---
# <a name="understand-the-digital-twins-model-parser"></a>了解数字孪生模型分析程序

[DTDL 规范](https://github.com/Azure/opendigitaltwins-dtdl)中介绍了数字孪生定义语言 (DTDL) 。 用户可以使用 _数字孪生模型分析器_ NuGet 包来验证和查询在多个文件中定义的模型。

## <a name="install-the-dtdl-model-parser"></a>安装 DTDL 模型分析器

使用 ID： [DigitalTwins](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)的 NuGet.org 中提供了该分析器。 若要安装分析器，请使用任何兼容的 NuGet 包管理器，例如 Visual Studio 或 CLI 中的任何一个 `dotnet` 。

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> 编写时，分析器的版本为 `3.12.5` 。

## <a name="use-the-parser-to-validate-a-model"></a>使用分析器验证模型

模型可由 JSON 文件中描述的一个或多个接口组成。 您可以使用分析器加载给定文件夹中的所有文件，并使用分析器来验证所有文件，包括文件之间的所有引用：

1. `IEnumerable<string>`使用所有模型内容的列表创建：

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

1. 实例化 `ModelParser` 并调用 `ParseAsync` ：

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. 检查验证错误。 如果分析器发现任何错误，则会引发 `ParsingException` 并出现错误列表：

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

1. 检查 `Model` 。 如果验证成功，则可以使用模型分析器 API 来检查模型。 下面的代码段演示如何循环访问所有分析的模型，并显示现有属性：

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>后续步骤

本文中所述的模型分析器 API 启用了许多方案来自动或验证依赖于 DTDL 模型的任务。 例如，您可以从模型中的信息动态构建一个 UI。
