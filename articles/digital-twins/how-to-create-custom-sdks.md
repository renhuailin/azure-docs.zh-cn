---
title: 使用 AutoRest 创建自定义语言 SDK
titleSuffix: Azure Digital Twins
description: 了解如何使用 AutoRest 生成自定义语言 SDK，用于以无已发布 SDK 的其他语言编写 Azure 数字孪生编码。
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 4e91bf5acc5290229afa8dc7a849e8953257bcfd
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751105"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>使用 AutoRest 为 Azure 数字孪生创建自定义语言 SDK

如果需要使用无[已发布 Azure 数字孪生 SDK](how-to-use-apis-sdks.md) 的语言处理 Azure 数字孪生，本文将介绍如何以所选语言使用 AutoRest 生成你自己的 SDK。 

本文中的示例演示了如何创建[数据平面 SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)，但这一过程也将适用于生成[控制平面 SDK](how-to-use-apis-sdks.md#overview-control-plane-apis)。

## <a name="prerequisites"></a>先决条件

若要生成 SDK，首先需要在本地计算机上完成以下设置：
* 安装版本为 2.0.4413 的 [**AutoRest**](https://github.com/Azure/autorest)（当前不支持版本 3）
* 安装 [**Node.js**](https://nodejs.org)，这是使用 AutoRest 的先决条件
* 安装 [**Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* [从数据平面 Swagger 文件夹](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)中下载最新的 Azure 数字孪生数据平面 Swagger (OpenAPI) 文件及其随附的示例文件夹。 Swagger 文件是一个名为 digitaltwins.json 的文件。

>[!TIP]
> 若要改为创建控制平面 SDK，请使用控制平面而不是数据平面中 Swagger 文件夹的最新[控制平面 Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) (OpenAPI) 文件完成本文中的步骤。

在计算机配备了上述列表中的所有内容之后，就可以使用 AutoRest 创建一个 SDK。

## <a name="create-the-sdk-using-autorest"></a>使用 AutoRest 创建 SDK 

安装 Node.js 后，可以运行此命令，以便确保安装了所需 AutoRest 版本：
```cmd/sh
npm install -g autorest@2.0.4413
```

若要使用 Azure 数字孪生 Swagger 文件运行 AutoRest，请执行以下步骤：
1. 将 Azure 数字孪生 Swagger 文件及其随附的示例文件夹复制到工作目录。
2. 使用命令提示符窗口切换到该工作目录。
3. 使用以下命令运行 AutoRest。 将 `<language>` 占位符替换为所选语言：`python`、`java` 和 `go` 等。 （可以在 [AutoRest README](https://github.com/Azure/autorest) 中找到选项的完整列表。）

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

因此，你将在工作目录中看到一个名为 DigitalTwinsApi 的新文件夹。 生成的 SDK 文件将具有命名空间 DigitalTwinsApi。 在本文的其他使用情况示例中，你将继续使用该名称空间。

AutoRest 支持多种语言代码生成器。

## <a name="make-the-sdk-into-a-class-library"></a>使 SDK 成为类库

可以将 AutoRest 生成的文件直接包括到 .NET 解决方案中。 不过，你可能想要将 Azure 数字孪生 SDK 包括在多个单独的项目（你的客户端应用、Azure Functions 应用等）中。 出于此原因，从生成的文件构建单独的项目（.NET 类库）可能会十分有用。 然后，可以将此类库项目作为项目引用包含到多个解决方案中。

本部分提供了有关如何将 SDK 构建为类库的说明，这是其自己的项目，可以包含在其他项目中。 这些步骤依赖于 Visual Studio。

下面是相关步骤：

1. 为类库创建新的 Visual Studio 解决方案
2. 使用 DigitalTwinsApi 作为项目名称
3. 在“解决方案资源管理器”中，右键单击选择生成的解决方案的 DigitalTwinsApi 项目，然后选择“添加”>“现有项...” 
4. 找到生成 SDK 的文件夹，然后选择根级别的文件
5. 按“确定”
6. 将文件夹添加到项目（在解决方案资源管理器中，右键单击选择项目，然后选择“添加”>“新文件夹”）
7. 将文件夹命名为“Models”
8. 在“解决方案资源管理器”中，右键单击 Models 文件夹，然后选择“添加”>“现有项...” 
9. 选择生成的 SDK 的 Models 文件夹，然后按“确定”

若要成功生成 SDK，你的项目将需要以下参考：
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

要添加这些内容，请打开“工具”>“NuGet 包管理器”>“管理解决方案的 NuGet 包...”。

1. 在面板中，确保选中“浏览”选项卡
2. 搜索“Microsoft.Rest”
3. 选择 `ClientRuntime` 和 `ClientRuntime.Azure` 包，并将其添加到解决方案

你现在可以生成项目，并将其作为项目引用包含在你编写的任何 Azure 数字孪生应用程序中。

## <a name="tips-for-using-the-sdk"></a>使用 SDK 的提示

本部分包含有关使用生成的 SDK 的一般信息和指南。

### <a name="synchronous-and-asynchronous-calls"></a>同步和异步调用

所有 SDK 函数都提供同步和异步版本。

### <a name="typed-and-untyped-data"></a>类型化和非类型化数据

REST API 调用通常返回强类型对象。 但是，由于 Azure 数字孪生允许用户定义其自己的自定义孪生类型，因此无法为许多 Azure 数字孪生调用预定义的静态返回数据。 相反，API 会返回强类型包装器类型（如果适用），并且自定义类型的孪生数据在 Json.NET 对象中（在 API 签名中出现数据类型“对象”的情况下使用）。 可以在代码中适当地强制转换这些对象。

### <a name="error-handling"></a>错误处理。

每当 SDK 中发生错误时（包括 HTTP 错误，如 404），SDK 会引发异常。 出于此原因，请务必封装 try/catch 块内的所有 API 调用。

下面是一个代码片段，它尝试添加一个孪生体，并在此过程中捕获任何错误：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>分页

AutoRest 为 SDK 生成两种类型的分页模式：
* 一种是针对 Query API 以外的所有 API
* 一种是针对查询 API

在非查询分页模式中，此处有一个示例方法，显示了如何从 Azure 数字孪生检索传出关系的分页列表：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

第二种模式仅为查询 API 生成。 它显式使用 `continuationToken`。

>[!TIP]
> 获取页面的主要原因是为了计算查询 API 调用的[查询单位费用](concepts-query-units.md)。

下面是此模式的示例：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>后续步骤

逐步完成创建可使用 SDK 的客户端应用的步骤：
* [*教程：* 为客户端应用编写代码](tutorial-code.md)
