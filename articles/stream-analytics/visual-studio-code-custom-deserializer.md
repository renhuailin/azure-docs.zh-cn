---
title: 使用 Visual Studio Code 为 Azure 流分析云作业创建自定义 .NET 反
description: 本教程演示如何使用 Visual Studio Code 为 Azure 流分析云作业创建自定义 .NET 反序列化程序。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 0f041910d1b02cc6d1fd48bfd773711c3361c0f5
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822451"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>在 Visual Studio Code 中创建 Azure 流分析的自定义 .NET 反

Azure 流分析[内置了对三种数据格式的支持](stream-analytics-parsing-json.md)：JSON、CSV 和 Avro。 通过自定义 .NET 反，你可以从其他格式（如 [协议缓冲区](https://developers.google.com/protocol-buffers/)、 [绑定](https://github.com/Microsoft/bond) 和云作业的其他用户定义格式）读取数据。

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Visual Studio Code 中的自定义 .NET 反

可以使用 Visual Studio Code 创建、测试和调试 Azure 流分析云作业的自定义 .NET 反序列化程序。

### <a name="prerequisites"></a>先决条件

* 安装 [.NET core SDK](https://dotnet.microsoft.com/download) 并重启 Visual Studio Code。

* 使用此[快速入门](quick-create-visual-studio-code.md)了解如何使用 Visual Studio Code 创建流分析作业。

### <a name="create-a-custom-deserializer"></a>创建自定义反序列化程序

1. 打开终端并运行以下命令，在 Visual Studio Code 中创建名为 **ProtobufDeserializer** 的自定义反序列化的 .net 类库。

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. 中转到 **ProtobufDeserializer** 项目目录，并安装 [Streamanalytics-default-central-us](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) 和 [Protobuf](https://www.nuget.org/packages/Google.Protobuf/) NuGet 包。

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. 将 [MessageBodyProto 类](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) 和 [MessageBodyDeserializer 类](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs)添加到项目。

4. 生成 **ProtobufDeserializer** 项目。

### <a name="add-an-azure-stream-analytics-project"></a>添加 Azure 流分析项目

1. 打开 Visual Studio Code，然后选择 **Ctrl + Shift + P** 以打开命令面板。 然后输入 ASA，并选择 **asa：创建新项目**。 将其命名为 **ProtobufCloudDeserializer**。

### <a name="configure-a-stream-analytics-job"></a>配置流分析作业

1. 双击“JobConfig.json”。 使用默认配置，但以下设置除外：

   |设置|建议的值|
   |-------|---------------|
   |全局存储设置资源|选择当前帐户中的数据源|
   |全局存储设置订阅| <你的订阅>|
   |全局存储设置存储帐户| <你的存储帐户>|
   |CustomCodeStorage 设置存储帐户|<你的存储帐户>|
   |CustomCodeStorage 设置容器|<你的存储容器>|

2. 在 " **输入** 文件夹" 下打开 **input.js**。 选择 " **添加实时输入** "，然后从 Azure Data Lake Storage Gen2/Blob 存储添加输入，选择 **Azure 订阅中** 的 "选择"。 使用默认配置，但以下设置除外：

   |设置|建议的值|
   |-------|---------------|
   |名称|输入|
   |订阅|<你的订阅>|
   |存储帐户|<你的存储帐户>|
   |容器|<你的存储容器>|
   |序列化类型|选择 **自定义**|
   |SerializationProjectPath|选择 "从 CodeLens **选择库项目路径** "，然后选择在上一部分中创建的 **ProtobufDeserializer** 库项目。 选择 **生成项目** 以生成项目|
   |SerializationClassName|选择 "从 CodeLens 中 **选择反序列化类** " 以自动填充类名称和 DLL 路径|
   |类名|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="添加自定义反序列化输入。":::

3. 将以下查询添加到 **ProtobufCloudDeserializer** 文件。

   ```sql
   SELECT * FROM Input
   ```

4. 下载[示例 protobuf 输入文件](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)。 在 "**输入**" 文件夹中，右键单击input.js，然后选择 "**添加本地输入** **"** 。 然后，双击 " **local_input1.js** "，并使用默认配置，但以下设置除外。

   |设置|建议的值|
   |-------|---------------|
   |选择本地文件路径|单击 "CodeLens" 以选择 < 下载的示例 protobuf 输入文件的文件路径>|

### <a name="execute-the-stream-analytics-job"></a>执行流分析作业

1. 打开 **ProtobufCloudDeserializer script.asaql** ，选择 "从 CodeLens **本地运行** "，然后从下拉列表中选择 " **使用本地输入** "。

2. 在右侧的 "作业关系图" 视图中的 **"结果" 选项卡** 中查看结果。 您还可以单击作业关系图中的步骤以查看中间结果。 更多详细信息，请参阅 [使用作业关系图本地调试](debug-locally-using-job-diagram-vs-code.md)。

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="检查本地运行结果。":::

已成功为流分析作业实现自定义反序列化程序！ 在本教程中，已在本地测试了本地输入数据的自定义反序列化程序。 你还可以 [在云中使用实时数据输入对](visual-studio-code-local-run-live-input.md)其进行测试。 若要在云中运行作业，应正确配置输入和输出。 然后从 Visual Studio Code 将作业提交到 Azure，以使用刚刚实现的自定义反序列化程序在云中运行作业。

### <a name="debug-your-deserializer"></a>调试反序列化程序

可以在本地采用调试标准 .NET 代码的方式调试 .NET 反序列化程序。 

1. 在 .NET 函数中添加断点。

2. 单击 Visual Studio Code 活动栏中的 " **运行** "，然后选择 **"在文件上创建 launch.js"**。
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="创建启动文件。":::

   从下拉列表中选择 " **ProtobufCloudDeserializer** "，然后选择 " **Azure 流分析** "。
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="创建启动文件2。":::

   编辑文件 **上的launch.js** ，将 <ASAScript> Script.asaql 替换为 ProtobufCloudDeserializer. script.asaql。
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="配置启动文件。":::

3. 按 **F5** 开始调试。 程序按预期在断点处停止。 这适用于本地输入和实时输入数据。

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="调试自定义反序列化程序。":::

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、流式处理作业以及所有相关资源，请将其删除。 删除作业可避免对作业使用的流单元进行计费。 如果计划在将来使用该作业，可以先停止它，等到以后需要时再重启它。 如果你不打算继续使用此作业，请使用以下步骤删除本教程中创建的所有资源：

1. 在 Azure 门户的左侧菜单中选择“资源组”，然后选择已创建资源的名称。  

2. 在资源组页上选择“删除”，在文本框中键入要删除的资源的名称，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何为协议缓冲区输入序列化实现自定义 .NET 反序列化程序。 若要了解有关创建自定义反序列化程序的详细信息，请继续阅读以下文章：

> [!div class="nextstepaction"]
> * [为 Azure 流分析作业创建不同的 .NET 反序列化程序](custom-deserializer-examples.md)
> * [通过 Visual Studio Code，使用实时输入在本地测试 Azure 流分析作业](visual-studio-code-local-run-live-input.md)