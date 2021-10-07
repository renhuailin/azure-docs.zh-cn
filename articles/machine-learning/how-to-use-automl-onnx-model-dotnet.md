---
title: 在 .NET 中使用 AutoML ONNX 模型进行预测
description: 了解如何在 .NET 中通过 ML.NET 使用 AutoML ONNX 模型进行预测
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: how-to
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.custom: automl
ms.openlocfilehash: e37f1a26b348772acbf3af0154bb5eb04fb8436a
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428837"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>在 .NET 中使用 AutoML ONNX 模型进行预测

在本文中，你将学习如何使用自动化 ML (AutoML) Open Neural Network Exchange (ONNX) 模型在 C# .NET Core 控制台应用程序中通过 ML.NET 进行预测。

[ML.NET](/dotnet/machine-learning/) 是一种面向 .NET 生态系统的跨平台的开源机器学习框架，借助它可通过 C# 或 F# 的代码优先方法以及通过 [Model Builder](/dotnet/machine-learning/automate-training-with-model-builder) 和 [ML.NET CLI](/dotnet/machine-learning/automate-training-with-cli) 等低代码工具来训练和使用自定义机器学习模型。 此框架还可扩展，使你能够利用其他常见的机器学习框架，如 TensorFlow 和 ONNX。

ONNX 是 AI 模型的开源格式。 ONNX 支持框架之间的互操作性。 这意味着你可在众多常见的机器学习框架（如 PyTorch）中训练模型，将其转换为 ONNX 格式，并在 ML.NET 等不同框架中使用 ONNX 模型。 有关详细信息，请参阅 [ONNX 网站](https://onnx.ai/)。

## <a name="prerequisites"></a>先决条件

- [.NET Core SDK 3.1 或更高版本](https://dotnet.microsoft.com/download)
- 文本编辑器或 IDE（如 [Visual Studio](https://visualstudio.microsoft.com/vs/) 或 [Visual Studio Code](https://code.visualstudio.com/Download)）
- ONNX 模型。 若要了解如何训练 AutoML ONNX 模型，请参阅以下[银行营销分类笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)。
- [Netron](https://github.com/lutzroeder/netron)（可选）

## <a name="create-a-c-console-application"></a>创建 C# 控制台应用程序

在此示例中，你将使用 .NET Core CLI 生成应用程序，但可使用 Visual Studio 执行相同的任务。 详细了解 [.NET Core CLI](/dotnet/core/tools/)。

1. 打开终端并创建新的 C# .NET Core 控制台应用程序。 在本例中，应用程序的名称是 `AutoMLONNXConsoleApp`。 目录将使用与应用程序的内容相同的名称创建。

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. 在终端中，导航到 AutoMLONNXConsoleApp 目录。

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>添加软件包

1. 使用 .NET Core CLI 安装 Microsoft.ML、Microsoft.ML.OnnxRuntime 和 Microsoft.ML.OnnxTransformer NuGet 包  。

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    这些包中有在 .NET 应用程序中使用 ONNX 模型所需的依赖项。 ML.NET 提供一个使用 [ONNX 运行时](https://github.com/Microsoft/onnxruntime)进行预测的 API。

1. 打开 Program.cs 文件，并在顶部添加以下 `using` 语句来引用相应的包。

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>添加对 ONNX 模型的引用

控制台应用程序访问 ONNX 模型的一种方法是将其添加到生成输出目录。  若要详细了解 MSBuild 常见项，请参阅 [MSBuild 指南](/visualstudio/msbuild/common-msbuild-project-items)。

在应用程序添加对 ONNX 模型文件的引用

1. 将 ONNX 模型复制到应用程序的 AutoMLONNXConsoleApp 根目录。
1. 打开 AutoMLONNXConsoleApp.csproj 文件，并在 `Project` 节点中添加以下内容。

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    在本例中，ONNX 模型文件的名称为 automl-model.onnx。

1. 打开 Program.cs 文件，并在 `Program` 类中添加以下行。

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>初始化 MLContext

在 `Program` 类的 `Main` 方法中，创建 [`MLContext`](xref:Microsoft.ML.MLContext) 的新实例。

```csharp
MLContext mlContext = new MLContext();
```

[`MLContext`](xref:Microsoft.ML.MLContext) 类是所有 ML.NET 操作的起点，而初始化 `mlContext` 会创建一个新的 ML.NET 环境，该环境可在模型生命周期内共享。 在概念上，它类似于实体框架中的 DbContext。

## <a name="define-the-model-data-schema"></a>定义模型数据架构

你的模型需要特定格式的输入和输出数据。 使用 ML.NET，可通过类定义数据的格式。 有时，你可能已经知道该格式是怎样的。 如果不知道数据格式，可使用 Netron 之类的工具来检查 ONNX 模型。

本示例中所用的模型使用来自“纽约 TLC 出租车行程”数据集的数据。 下面是数据的示例：

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3.75|CRD|15.5|
|VTS|1|1|480|2.72|CRD|10.0|
|VTS|1|1|1680|7.8|CSH|26.5|

### <a name="inspect-the-onnx-model-optional"></a>检查 ONNX 模型（可选）

使用 Netron 之类的工具来检查模型的输入和输出。

1. 打开 Netron。
1. 在顶部菜单栏中选择“文件”>“打开”，然后使用文件浏览器选择模型。
1. 这将打开你的模型。 例如，automl-model.onnx 模型的结构如下所示：

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX 模型":::

1. 选择图形底部的最后一个节点（在本例中为 `variable_out1`），显示模型的元数据。 侧栏中的输入和输出显示模型的预期输入、输出和数据类型。 使用此信息可定义模型的输入和输出架构。

### <a name="define-model-input-schema"></a>定义模型输入架构

在 Program.cs 文件中使用以下属性创建一个名为 `OnnxInput` 的新类。

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

每个属性都将映射到数据集中的一个列。 属性将用特性进一步批注。

利用 [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 特性，可指定在操作数据时 ML.NET 应如何引用该列。 例如，尽管 `TripDistance` 属性遵循标准的 .NET 命名约定，但该模型只知道名为 `trip_distance` 的列或功能。 为了解决这种命名差异，[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 特性会按名称 `trip_distance` 将 `TripDistance` 属性映射到列或功能。
  
对于数值，ML.NET 仅对 [`Single`](xref:System.Single) 值类型进行操作。 但是，某些列的原始数据类型为整数。 [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute) 特性在 ONNX 和 ML.NET 之间映射类型。

若要详细了解数据特性，请参阅 [ML.NET 加载数据指南](/dotnet/machine-learning/how-to-guides/load-data-ml-net)。

### <a name="define-model-output-schema"></a>定义模型输出架构

数据被处理后，它将生成特定格式的输出。 定义数据输出架构。 在 Program.cs 文件中使用以下属性创建一个名为 `OnnxOutput` 的新类。

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

与 `OnnxInput` 类似，使用 [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 特性可将 `variable_out1` 输出映射到更具描述性的名称 `PredictedFare`。

## <a name="define-a-prediction-pipeline"></a>定义预测管道

ML.NET 中的管道通常是一系列链式转换，它们对输入数据进行操作以生成输出。 若要详细了解数据转换，请参阅 [ML.NET 数据转换指南](/dotnet/machine-learning/resources/transforms)。

1. 在 `Program` 类中创建名为 `GetPredictionPipeline` 的新方法

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. 定义输入列和输出列的名称。 将以下代码添加到 `GetPredictionPipeline` 方法中。

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. 定义管道。 [`IEstimator`](xref:Microsoft.ML.IEstimator%601) 提供管道的操作、输入和输出架构的蓝图。

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    在本例中，[`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) 是管道中的唯一转换，它采用输入列和输出列的名称以及 ONNX 模型文件的路径。

1. [`IEstimator`](xref:Microsoft.ML.IEstimator%601) 仅定义要应用于数据的操作集。 对数据进行的操作称为 [`ITransformer`](xref:Microsoft.ML.ITransformer)。 使用 `Fit` 方法从 `onnxPredictionPipeline` 创建一个。

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A) 方法需要 [`IDataView`](xref:Microsoft.ML.IDataView) 作为输入以对其执行操作。 [`IDataView`](xref:Microsoft.ML.IDataView) 是一种使用表格格式在 ML.NET 中表示数据的方法。 在本例中，管道仅用于预测，因此你可提供一个空的 [`IDataView`](xref:Microsoft.ML.IDataView) 来为 [`ITransformer`](xref:Microsoft.ML.ITransformer) 提供必要的输入和输出架构信息。 然后，系统会返回合适的 [`ITransformer`](xref:Microsoft.ML.ITransformer)，以供今后在应用程序中使用。

    > [!TIP]
    > 在此示例中，管道在同一应用程序中定义和使用。 但建议分别使用单独的应用程序来定义和使用管道进行预测。 在 ML.NET 中，可将管道序列化并进行保存，以供今后在其他 .NET 最终用户应用程序中使用。 ML.NET 支持各种部署目标，例如桌面应用程序、Web 服务、WebAssembly 应用程序*等。 若要详细了解如何保存管道，请参阅[“ML.NET 保存和加载经过训练的模型”指南](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net)。
    >
    > *WebAssembly 仅在 .NET Core 5 或更高版本中受支持

1. 在 `Main` 方法中，用所需的参数调用 `GetPredictionPipeline` 方法。

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>使用模型进行预测

现在你已有一个管道，可用它来进行预测。 ML.NET 提供了一个方便的 API，用于在名为 [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) 的单个数据实例上进行预测。

1. 在 `Main` 方法中，使用 [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) 方法创建 [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602)。

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. 创建测试数据输入。

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. 通过 [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) 方法基于新的 `testInput` 数据，使用 `predictionEngine` 进行预测。

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. 将预测结果输出到控制台。

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. 使用 .NET Core CLI 运行你的应用程序。

    ```dotnetcli
    dotnet run
    ```

    结果应类似于以下输出：

    ```text
    Predicted Fare: 15.621523
    ```

若要详细了解如何在 ML.NET 中进行预测，请参阅[“使用模型进行预测”指南](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net)。

## <a name="next-steps"></a>后续步骤

- [将模型部署为 ASP.NET Core Web API](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [将模型部署为无服务器 .NET Azure 函数](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)