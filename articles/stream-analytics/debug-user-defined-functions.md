---
title: 在 Azure 流分析中调试用户定义的函数
description: 本文介绍如何在 Azure 流分析中调试用户定义的函数。
author: jenssuessmeyer
ms.author: jenss
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7f5a24e4d351d7c133251900a80c70094b84cc4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019425"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>在 Azure 流分析中调试用户定义的函数 

如果用户定义的函数 (UDF) 不按预期工作，则需要对其进行调试以找出问题。 当使用 [Visual Studio Code](visual-studio-code-local-run-live-input.md) 或 [Visual Studio](stream-analytics-vs-tools-local-run.md) 在本地运行作业时，可以调试流分析作业的 UDF。

当你在本地对实时输入流运行作业时，它仅模拟在一个节点上执行云 Azure 流分析引擎的操作。 实时数据本地测试不能取代可在云中执行的性能和可伸缩性测试，但此测试可帮助在功能测试期间节省时间，因为无需在每次想要测试时都将作业提交到云中。 此外，对于本地或示例数据的本地运行，将禁用时间策略，但实时数据测试支持时间策略。

## <a name="pick-your-language"></a>选择语言

可以使用 .NET (C#) 或 JavaScript 为 Azure 流分析编写 UDF。 

### <a name="functions-in-c"></a>C# 中的函数 

[使用 Visual Studio 编写 .NET UDF](stream-analytics-edge-csharp-udf-methods.md) 时，可以获得与任何 .NET 类项目相同的支持级别。 此支持包括：

* 编译支持，如语法检查和编译器支持。

* 在流分析解决方案中添加、生成和引用 C# 项目和工件的功能。 

* 轻松重复使用封装在可共享项目中的代码。 

* 直接在 Visual Studio 中调试支持。 将流分析项目设置为启动项目，并在 C# 代码中设置断点。 然后，按 F5 调试 C# 代码，就像对任何其他 C# 项目一样。 

### <a name="functions-in-javascript"></a>JavaScript 中的函数

JavaScript 是用于在流分析中创建函数的另一个选项。 JavaScript 代码直接置于流分析项目的函数区域中，这使得跨项目的共享更加困难。

编译或执行流分析项目时，会进行编译。 仅在运行时发现问题的机会更高。 对于流分析中的 JavaScript 函数，不直接提供调试支持。

## <a name="debug-options-for-javascript"></a>JavaScript 的调试选项

因为对于流分析中的 JavaScript 函数不直接提供调试支持，因此可以通过在 HTML 站点中封装该函数并从该位置获取输出来进行调试。

以下示例显示了如何在 [Visual Studio Code](quick-create-visual-studio-code.md) 中的集成运行时环境中调试 JavaScript UDF，但有一些限制。

### <a name="prerequisites"></a>先决条件

在开始之前，请确保 Azure 流分析项目包含以下各项：

* 输入 
* 输出 
* 流分析查询 (.asaql) 
* 流分析作业配置 (JobConfig.json)
* JavaScript UDF

### <a name="prepare-files"></a>准备文件

在下图中，.asaql 查询文件仅包含对 UDF“fxcharCount”的调用。 此更改可确保在进行更改后仍能够编译项目。

> [!div class="mx-imgBorder"]
> ![流分析测试查询文件](./media/debug-user-defined-functions/asaql-file.png)

在“测试”中创建另一个文件夹来承载测试文件，该文件将被调用以使用 JavaScript 函数执行测试。 在此示例中，文件夹的名称为 fxcharCount，并且测试的名称为 Test_UDF.js。 

下图显示了测试文件中的代码，该代码可加载功能文件并执行功能。 此示例很简单，但你可以加载其他测试数据文件，并通过其他测试循环以获取输出。 函数调用的表示法与常见调用略有不同，因为该文件被引用且未加载到运行时中，可以进行调试。 

> [!div class="mx-imgBorder"]
> ![流分析测试文件](./media/debug-user-defined-functions/test-file.png)

在函数中，将以下代码行添加到文件中以公开方法。 它们不会影响在 Visual Studio Code 中编译代码的能力。

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![流分析 JavaScript UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>安装调试支持

若要调试，必须[下载](https://nodejs.org/en/download/)并安装 node.js。 根据所使用的平台安装正确的版本。 安装 node.js 运行时后，重启 Visual Studio Code 以实现更改。 

选择“运行和调试”，或按 CTRL + SHIFT + D 开始调试。 此时会出现一个组合框，你可以在其中选择 node.js 作为运行时。 如果只安装 node.js，则默认会使用它。 如果需要，可以使用 F11 逐句通过代码和附属文件。 

> [!div class="mx-imgBorder"]
> ![流分析运行和调试 UDF](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>调试用户定义的聚合 

可以使用 JavaScript UDF 的调试方法来调试用户定义的聚合 (UDA)。 在此示例中，将 UDA 添加到 .asaql 查询文件和测试文件。

与 UDF 一样，可以包括对 UDA 的调用，以确保在进行更改后将编译项目。 

> [!div class="mx-imgBorder"]
> ![将 UDA 添加到 asaql](./media/debug-user-defined-functions/asaql-uda.png)

在 Test_UDA.js 文件中，将引用 UDA 文件，就像对 UDF 执行的操作一样。 此外，还可以调用 `main()`、`init()` 和 `accumulate()`。 在循环中调用 `accumulate()` 方法，以将值放入状态堆栈。 调用 `computeresult()` 方法以编写最终查询。 

> [!div class="mx-imgBorder"]
> ![UDA 测试文件](./media/debug-user-defined-functions/uda-test.png)

如 UDF 示例中所示，一些代码需要添加到 UDA 本身才能公开相关方法。

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![添加到 UDA 的代码](./media/debug-user-defined-functions/uda-expose-methods.png)

选择“运行和调试”，或按 CTRL + SHIFT + D 开始调试。 此时会出现一个组合框，你可以在其中选择 node.js 作为运行时。 如果只安装 node.js，则默认会使用它。 如果需要，可以使用 F11 逐句通过代码和附属文件。

> [!div class="mx-imgBorder"]
> ![流分析运行和调试 UDA](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>后续步骤

* [在本地开发和调试 Azure 流分析作业](develop-locally.md)
* [在 Visual Studio 中使用作业关系图在本地调试 Azure 流分析查询](debug-locally-using-job-diagram.md)
* [Azure 流分析中的用户定义函数](functions-overview.md)
 
