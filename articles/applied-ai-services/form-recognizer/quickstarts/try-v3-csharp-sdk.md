---
title: 快速入门：表单识别器 C# SDK v3.0 | 预览版
titleSuffix: Azure Applied AI Services
description: 使用表单识别器 C# 客户端库 SDK v3.0（预览版）进行表单和文档处理、数据提取和分析
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f0ed9d9b7574f6d8d1856c263b22dfa66e1207ca
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809822"
---
# <a name="quickstart-form-recognizer-c-client-library-sdks-v30--preview"></a>快速入门：表单识别器 C# 客户端库 SDK v3.0 | 预览版

通过 C# 编程语言实现 Azure 表单识别器入门。 Azure 表单识别器是一种 [Azure 应用的 AI 服务](../../../applied-ai-services/index.yml)云服务，你可通过它使用机器学习技术构建自动化数据处理软件。 可通过 REST API 或 SDK 使用表单识别器。 我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。

>[!NOTE]
> 表单识别器 v3.0 当前为公共预览版。 某些功能可能不受支持或者受限。 

[参考文档](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet&preserve-view=true ) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [包 (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [示例](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

Azure 认知服务表单识别器是一种云服务，它使用机器学习从文档中提取和分析表单域、文本和表。 可通过将客户端库 SDK 集成到工作流和应用程序，轻松调用表单识别器模型。

### <a name="form-recognizer-models"></a>表单识别器模型

C# SDK 支持以下模型和功能：

* 🆕通用文档 - 分析和提取文本、表、结构、键值对和命名实体。|
* 布局 - 分析和提取表、行、字词和选择标记（如表单文档中的单选按钮和复选框），而无需训练模型。
* 自定义 - 通过使用自己的表单类型训练的模型，分析和提取自定义表单中的表单域和其他内容。
* 发票 - 使用预先训练的发票模型分析和提取发票中的常见字段。
* 收据 - 使用预先训练的收据模型分析和提取收据中的常见字段。
* ID 文档 - 使用预先训练的 ID 文档模型从 ID 文档分析和提取常见字段（如护照或驾驶证）。
* 名片 - 使用预先训练的名片模型分析和提取名片中的常见字段。

在本快速入门中，你将使用以下功能来分析和提取表单及文档中的数据和值：

* [**常规文档**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**预生成的发票**](#try-it-prebuilt-invoice-model)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)

* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或 [.NET Core](https://dotnet.microsoft.com/download) 的当前版本。

* 认知服务或表单识别器资源。 拥有 Azure 订阅后，在 Azure 门户中创建[单服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)或[多服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)表单识别器资源来获取密钥和终结点。 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

> [!TIP] 
> 如果计划在一个终结点/密钥下访问多个认知服务，请创建认知服务资源。 仅针对表单识别器访问，创建表单识别器资源。 请注意，若要使用 [Azure Active Directory 身份验证](/azure/active-directory/authentication/overview-authentication)，则需要单一服务资源。

* 部署资源后，单击“转到资源”。 需要从创建的资源获取密钥和终结点，以便将应用程序连接到表单识别器 API。 稍后在快速入门中将密钥和终结点粘贴到下方的代码中：

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户中的密钥和终结点位置。":::

## <a name="set-up"></a>设置

### <a name="option-1-net-command-line-interface-cli"></a>[选项 1：.NET 命令行接口 (CLI)](#tab/cli)

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `formrecognizer-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*Program.cs*。

```console
dotnet new console -n formrecognizer-quickstart
```

打开命令行并切换到包含项目文件的目录。 通过以下方式生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library-with-nuget"></a>使用 NuGet 安装客户端库

在包含项目的目录中，使用以下命令安装适用于 .NET 的表单识别器客户端库：

```console
dotnet add package Azure.AI.FormRecognizer
```

此版本的客户端库默认为服务的 2021-09-30-preview 版本。

### <a name="option-2-visual-studio"></a>[选项 2：Visual Studio](#tab/vs)

1. 启动 Visual Studio 2019。

1. 在“开始”页上，选择“创建新项目”。

    :::image type="content" source="../media/quickstarts/start-window.png" alt-text="屏幕截图：Visual Studio 开始窗口。":::

1. 在“创建新项目”页面上，在搜索框中输入“控制台” 。 选择“控制台应用程序”模板，然后选择“下一步” 。

    :::image type="content" source="../media/quickstarts/create-new-project.png" alt-text="屏幕截图：Visual Studio 的“新建项目”页。":::

1. 在“配置新项目”对话框中，在项目名称框中输入 `formRecognizer_quickstart`。 然后选择“下一步”  。

    :::image type="content" source="../media/quickstarts/configure-new-project.png" alt-text="屏幕截图：Visual Studio 的“配置新项目”对话框窗口。":::

1. 在“附加信息”对话框窗口中，选择“.NET 5.0 (当前版)”，然后选择“创建”  。

    :::image type="content" source="../media/quickstarts/additional-information.png" alt-text="屏幕截图：Visual Studio 的“其他信息”对话框窗口。":::

### <a name="install-the-client-library-with-nuget"></a>使用 NuGet 安装客户端库

 1. 右键单击 formRecognizer_quickstart 项目，然后选择“管理 NuGet 包...” 。

    :::image type="content" source="../media/quickstarts/select-nuget-package.png" alt-text="屏幕截图：select-nuget-package.png":::

 1. 选择“浏览”选项卡，并键入 Azure.AI.FormRecognizer。

     :::image type="content" source="../media/quickstarts/azure-nuget-package.png" alt-text="屏幕截图：select-form-recognizer-package.png":::

 1. 从下拉菜单中选择所需的版本，然后选择“安装”。

     此版本的客户端库默认为服务的 2021-09-30-preview 版本。

---

## <a name="build-your-application"></a>生成应用程序

若要与表单识别器服务交互，需要创建 `DocumentAnalysisClient` 类的实例。 为此，你将使用 apiKey 创建一个 `AzureKeyCredential`，使用 `AzureKeyCredential` 和表单识别器 `endpoint` 创建一个 `DocumentAnalysisClient` 实例。

1. 打开 **Program.cs** 文件。

1. 包括以下 using 指令：

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.AI.FormRecognizer.DocumentAnalysis;
    ```

1. 设置 `endpoint` 和 `apiKey` 环境变量，并创建 `AzureKeyCredential` 和 `DocumentAnalysisClient` 实例：

    ```csharp
    string endpoint = "<your-endpoint>";
    string apiKey = "<your-apiKey>";
    var credential = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), credential);
    ```

1. 删除行 `Console.Writeline("Hello World!");`，并将“试用”代码添加到 Program.cs 文件中的 Main 方法  ：

    :::image type="content" source="../media/quickstarts/add-code-here.png" alt-text="屏幕截图：将示例代码添加到 Main 方法。":::

## <a name="try-it-general-document-model"></a>**试用**：常规文档模型

> [!div class="checklist"]
>
> * 对于此示例，需要 URI 中的一个表单文档文件。 在本快速入门中，可使用[示例表单文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)。
> * 将文件 URI 值添加到 Main 方法顶部的 `string fileUri` 变量。
> * 若要分析 URI 中的特定文件，将使用 `StartAnalyzeDocumentFromUri` 方法。 返回的值是一个 `AnalyzeResult` 对象，其中包含已提交文档的相关数据。

### <a name="add-the-following-code-to-your-general-document-application-main-method"></a>将以下代码添加到常规文档应用程序的 Main 方法

```csharp
// sample form document
string fileUri = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentFromUriAsync("prebuilt-document", fileUri);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

Console.WriteLine("Detected entities:");

foreach (DocumentEntity entity in result.Entities)
{
    if (entity.SubCategory == null)
    {
        Console.WriteLine($"  Found entity '{entity.Content}' with category '{entity.Category}'.");
    }
    else
    {
        Console.WriteLine($"  Found entity '{entity.Content}' with category '{entity.Category}' and sub-category '{entity.SubCategory}'.");
    }
}

Console.WriteLine("Detected key-value pairs:");

foreach (DocumentKeyValuePair kvp in result.KeyValuePairs)
{
    if (kvp.Value.Content == null)
    {
        Console.WriteLine($"  Found key with no value: '{kvp.Key.Content}'");
    }
    else
    {
        Console.WriteLine($"  Found key-value pair: '{kvp.Key.Content}' and '{kvp.Value.Content}'");
    }
}

foreach (DocumentPage page in result.Pages)
{
    Console.WriteLine($"Document Page {page.PageNumber} has {page.Lines.Count} line(s), {page.Words.Count} word(s),");
    Console.WriteLine($"and {page.SelectionMarks.Count} selection mark(s).");

    for (int i = 0; i < page.Lines.Count; i++)
    {
        DocumentLine line = page.Lines[i];
        Console.WriteLine($"  Line {i} has content: '{line.Content}'.");

        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {line.BoundingBox[0].X}, Y= {line.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {line.BoundingBox[1].X}, Y= {line.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {line.BoundingBox[2].X}, Y= {line.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {line.BoundingBox[3].X}, Y= {line.BoundingBox[3].Y}");
    }

    for (int i = 0; i < page.SelectionMarks.Count; i++)
    {
        DocumentSelectionMark selectionMark = page.SelectionMarks[i];

        Console.WriteLine($"  Selection Mark {i} is {selectionMark.State}.");
        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {selectionMark.BoundingBox[0].X}, Y= {selectionMark.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {selectionMark.BoundingBox[1].X}, Y= {selectionMark.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {selectionMark.BoundingBox[2].X}, Y= {selectionMark.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {selectionMark.BoundingBox[3].X}, Y= {selectionMark.BoundingBox[3].Y}");
    }
}

foreach (DocumentStyle style in result.Styles)
{
    // Check the style and style confidence to see if text is handwritten.
    // Note that value '0.8' is used as an example.

    bool isHandwritten = style.IsHandwritten.HasValue && style.IsHandwritten == true;

    if (isHandwritten && style.Confidence > 0.8)
    {
        Console.WriteLine($"Handwritten content found:");

        foreach (DocumentSpan span in style.Spans)
        {
            Console.WriteLine($"  Content: {result.Content.Substring(span.Offset, span.Length)}");
        }
    }
}

Console.WriteLine("The following tables were extracted:");

for (int i = 0; i < result.Tables.Count; i++)
{
    DocumentTable table = result.Tables[i];
    Console.WriteLine($"  Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");

    foreach (DocumentTableCell cell in table.Cells)
    {
        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) has kind '{cell.Kind}' and content: '{cell.Content}'.");
    }
}

```

## <a name="try-it-layout-model"></a>**试用**：布局模型

从文档中提取文本、选择标记、文本类型和表结构及其边界区域坐标。

> [!div class="checklist"]
>
> * 对于此示例，需要 URI 中的一个表单文档文件。 在本快速入门中，可使用[示例表单文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)。
> * 将文件 URI 值添加到 Main 方法顶部的 `string fileUri` 变量。
> * 若要从 URI 中的特定文件中提取布局，请使用 `StartAnalyzeDocumentFromUri` 方法，并传递 `prebuilt-layout` 作为模型 ID。 返回的值是一个 `AnalyzeResult` 对象，其中包含来自已提交文档的数据。

### <a name="add-the-following-code-to-your-layout-application-main-method"></a>将以下代码添加到布局应用程序的 Main 方法

```csharp
// sample form document
string fileUri = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentFromUriAsync("prebuilt-layout", fileUri);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

foreach (DocumentPage page in result.Pages)
{
    Console.WriteLine($"Document Page {page.PageNumber} has {page.Lines.Count} line(s), {page.Words.Count} word(s),");
    Console.WriteLine($"and {page.SelectionMarks.Count} selection mark(s).");

    for (int i = 0; i < page.Lines.Count; i++)
    {
        DocumentLine line = page.Lines[i];
        Console.WriteLine($"  Line {i} has content: '{line.Content}'.");

        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {line.BoundingBox[0].X}, Y= {line.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {line.BoundingBox[1].X}, Y= {line.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {line.BoundingBox[2].X}, Y= {line.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {line.BoundingBox[3].X}, Y= {line.BoundingBox[3].Y}");
    }

    for (int i = 0; i < page.SelectionMarks.Count; i++)
    {
        DocumentSelectionMark selectionMark = page.SelectionMarks[i];

        Console.WriteLine($"  Selection Mark {i} is {selectionMark.State}.");
        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {selectionMark.BoundingBox[0].X}, Y= {selectionMark.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {selectionMark.BoundingBox[1].X}, Y= {selectionMark.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {selectionMark.BoundingBox[2].X}, Y= {selectionMark.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {selectionMark.BoundingBox[3].X}, Y= {selectionMark.BoundingBox[3].Y}");
    }
}

foreach (DocumentStyle style in result.Styles)
{
    // Check the style and style confidence to see if text is handwritten.
    // Note that value '0.8' is used as an example.

    bool isHandwritten = style.IsHandwritten.HasValue && style.IsHandwritten == true;

    if (isHandwritten && style.Confidence > 0.8)
    {
        Console.WriteLine($"Handwritten content found:");

        foreach (DocumentSpan span in style.Spans)
        {
            Console.WriteLine($"  Content: {result.Content.Substring(span.Offset, span.Length)}");
        }
    }
}

Console.WriteLine("The following tables were extracted:");

for (int i = 0; i < result.Tables.Count; i++)
{
    DocumentTable table = result.Tables[i];
    Console.WriteLine($"  Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");

    foreach (DocumentTableCell cell in table.Cells)
    {
        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) has kind '{cell.Kind}' and content: '{cell.Content}'.");
    }
}

```

## <a name="try-it-prebuilt-invoice-model"></a>**试用**：预生成的发票模型

此示例演示如何以发票为例，使用预先训练的模型来分析某些类型的常见文档中的数据。

> [!div class="checklist"]
>
> * 对于此示例，需要 URI 中的一个发票文档文件。 在本快速入门中，可使用[示例发票文档](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)。
> * 将文件 URI 值添加到 Main 方法顶部的 `string fileUri` 变量。
> * 若要分析 URI 中的特定文件，请使用 `StartAnalyzeDocumentFromUri` 方法，并传递 `prebuilt-invoice` 作为模型 ID。 返回的值是一个 `AnalyzeResult` 对象，其中包含来自已提交文档的数据。

### <a name="choose-the-invoice-prebuilt-model-id"></a>选择发票预生成模型 ID

并不局限于发票 - 也可从几个预生成模型中选择，每个模型都有自己的一组支持字段。 用于分析操作的模型由要分析的文档类型确定。 下面是表单识别器服务当前支持的预生成模型的模型 ID：

* **prebuilt-invoice**：从发票中提取文本、选择标记、表、键值对和密钥信息。
* **prebuilt-businessCard**：从名片中提取文本和密钥信息。
* **prebuilt-idDocument**：从驾驶证和国际护照中提取文本和密钥信息。
* **prebuilt-receipt**：从发票中提取文本和密钥信息。

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-main-method"></a>将以下代码添加到预生成发票应用程序的 Main 方法

```csharp
// sample invoice document
string filePath = "(https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

using var stream = new FileStream(filePath, FileMode.Open);

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentAsync("prebuilt-invoice", stream);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

for (int i = 0; i < result.Documents.Count; i++)
{
    Console.WriteLine($"Document {i}:");

    AnalyzedDocument document = result.Documents[i];

    if (document.Fields.TryGetValue("VendorName", out DocumentField vendorNameField))
    {
        if (vendorNameField.ValueType == DocumentFieldType.String)
        {
            string vendorName = vendorNameField.AsString();
            Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("CustomerName", out DocumentField customerNameField))
    {
        if (customerNameField.ValueType == DocumentFieldType.String)
        {
            string customerName = customerNameField.AsString();
            Console.WriteLine($"Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("Items", out DocumentField itemsField))
    {
        if (itemsField.ValueType == DocumentFieldType.List)
        {
            foreach (DocumentField itemField in itemsField.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.ValueType == DocumentFieldType.Dictionary)
                {
                    IReadOnlyDictionary<string, DocumentField> itemFields = itemField.AsDictionary();

                    if (itemFields.TryGetValue("Description", out DocumentField itemDescriptionField))
                    {
                        if (itemDescriptionField.ValueType == DocumentFieldType.String)
                        {
                            string itemDescription = itemDescriptionField.AsString();

                            Console.WriteLine($"  Description: '{itemDescription}', with confidence {itemDescriptionField.Confidence}");
                        }
                    }

                    if (itemFields.TryGetValue("Amount", out DocumentField itemAmountField))
                    {
                        if (itemAmountField.ValueType == DocumentFieldType.Double)
                        {
                            double itemAmount = itemAmountField.AsDouble();

                            Console.WriteLine($"  Amount: '{itemAmount}', with confidence {itemAmountField.Confidence}");
                        }
                    }
                }
            }
        }
    }

    if (document.Fields.TryGetValue("SubTotal", out DocumentField subTotalField))
    {
        if (subTotalField.ValueType == DocumentFieldType.Double)
        {
            double subTotal = subTotalField.AsDouble();
            Console.WriteLine($"Sub Total: '{subTotal}', with confidence {subTotalField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("TotalTax", out DocumentField totalTaxField))
    {
        if (totalTaxField.ValueType == DocumentFieldType.Double)
        {
            double totalTax = totalTaxField.AsDouble();
            Console.WriteLine($"Total Tax: '{totalTax}', with confidence {totalTaxField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("InvoiceTotal", out DocumentField invoiceTotalField))
    {
        if (invoiceTotalField.ValueType == DocumentFieldType.Double)
        {
            double invoiceTotal = invoiceTotalField.AsDouble();
            Console.WriteLine($"Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
        }
    }
}

```

## <a name="run-your-application"></a>运行应用程序

### <a name="net-command-line-interface-cli"></a>[.NET 命令行接口 (CLI)](#tab/cli)

打开命令提示符，转到包含项目的目录并键入以下内容：

```console
dotnet run formrecognizer-quickstart.dll
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

选择 formRecognizer_quickstart 旁的绿色“启动”按钮，来生成并运行程序，或者按 F5 。

  :::image type="content" source="../media/quickstarts/run-visual-studio.png" alt-text="屏幕截图：运行 Visual Studio 程序。":::

---

恭喜！ 在本快速入门中，你使用了表单识别器 C# SDK 以不同方式分析各种表单和文档。 接下来，请浏览参考文档来深入了解表单识别器 API。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API v3.0 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)