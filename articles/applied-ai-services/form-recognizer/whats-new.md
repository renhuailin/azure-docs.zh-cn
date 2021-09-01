---
title: 表单识别器的新增功能
titleSuffix: Azure Applied AI Services
description: 了解表单识别器 API 的最新更改。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.openlocfilehash: 2efc4d2c546cff172164b9416cbe5a0772167631
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326333"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-azure-form-recognizer"></a>Azure 表单识别器的新增功能

表单识别器服务会持续更新。 将此页添加为书签，随时了解发行说明、功能增强和文档更新。

## <a name="july-2021"></a>2021 年 7 月

### <a name="system-assigned-managed-identity-support"></a>系统分配的托管标识支持 

 你现在可以启用系统分配的托管标识，以授予表单识别器对专用存储帐户的有限访问权限，包括那些受虚拟网络 (VNet) 或防火墙保护的帐户，或启用了自带存储 (BYOS) 的帐户。 请参阅[为表单识别器资源创建和使用托管标识](managed-identity-byos.md)以了解详细信息。

## <a name="june-2021"></a>2021 年 6 月

### <a name="form-recognizer-containers-v21-released-in-gated-preview"></a>以门控式预览版形式发布的表单识别器容器 v2.1

表单识别器功能现在由六个功能容器支持：布局、名片、ID 文档、回执、发票、自定义     。 若要使用这些容器，必须提交[在线请求](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)并获得批准。

请参阅[为表单识别器资源安装和运行 Docker 容器](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout)和[配置表单识别器容器](containers/form-recognizer-container-configuration.md?branch=main) 

### <a name="form-recognizer-connector-released-in-preview"></a>以预览版发布的表单识别器连接器

  [表单识别器连接器](/connectors/formrecognizer)与 [Azure 逻辑应用](../../logic-apps/logic-apps-overview.md)、[Microsoft Power Automate](/power-automate/getting-started) 和 [Microsoft Power Apps](/powerapps/powerapps-overview) 集成。 连接器支持工作流操作和触发器，可从自定义和预生成的表单、发票、回执、名片和 ID 文档提取并分析文档数据和结构。

### <a name="form-recognizer-sdk-v310-patched-to-v311-for-c-java-and-python"></a>表单识别器 SDK v3.1.0 修补为 v3.1.1，适用于 C#、Java 和 Python

补丁可处理未检测到子行项字段的发票，例如具有 `Text` 但没有 `BoundingBox` 或 `Page` 信息的 `FormField`。

### <a name="c"></a>[**C#**](#tab/csharp)

| [参考文档](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [NuGet 包版本 3.1.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

### <a name="java"></a>[**Java**](#tab/java)

 | [参考文档](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Maven 项目包依赖项版本 3.1.1](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer/3.1.1) |

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

> [!NOTE]
> JavaScript SDK v3.1.0 没有更新。

| [参考文档](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm 包依赖项表单识别器 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer) |

### <a name="python"></a>[**Python**](#tab/python)

| [参考文档](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.1](https://pypi.org/project/azure-ai-formrecognizer/) |

---

## <a name="may-2021"></a>2021 年 5 月

### <a name="form-recognizer-21-api-generally-available-ga-release"></a>表单识别器 2.1 API 正式发布 (GA) 版本

* 表单识别器 2.1 现已正式发布。 此正式发布 (GA) 版本标志着先前 2.1 预览版包版本中引入的更改的稳定性。 利用此版本，你能够检测和提取以下内容中的信息和数据：

* 文档
* [Receipts](concept-receipts.md)
* [名片](concept-business-cards.md)
* [发票](concept-invoices.md)
* [标识文档](concept-identification-cards.md)
* [自定义窗体](concept-custom.md)

#### <a name="get-started"></a>入门 

请转到[表单识别器示例工具](https://fott-2-1.azurewebsites.net/)并按照[快速入门](quickstarts/get-started-with-form-recognizer.md)进行操作 

### <a name="layout-adds-table-headers"></a>布局中添加了表头

更新后的布局 API 表功能添加了标头识别，其中列标题可跨越多行。 每个表单元格都有一个属性，指示其是否为标头的一部分。 这可用于确定构成表头的行。

#### <a name="sdk-updates"></a>SDK 更新

### <a name="c"></a>[**C#**](#tab/csharp)

| [参考文档](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [NuGet 包版本 3.0.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

#### <a name="non-breaking-changes"></a>**非重大变化**

* FormRecognizerModelFactory 类现在支持更新 TextAppearance 和 ReadingOrder 以及删除 TextStyle 模型   。 请参阅[中断性变更](#breaking-changes-may)

#### <a name="breaking-changes-may"></a>**中断性变更（5 月）**

* 客户端默认为受支持的最新服务版本，当前为 v2.1。 可在 FormRecognizerClientOptions 对象的“版本”属性中指定版本 2.0 。

* StartRecognizeIdentityDocuments。 通过使用“标识”替换所有相关标识文档识别 API 功能的“ID”关键字重命名了方法和方法参数。

* FormReadingOrder。 ReadingOrder 已重命名为 FormReadingOrder。

* AsCountryRegion。 AsCountryCode 已重命名为 AsCountryRegion。

* TextAppearance 现包括 StyleName 和 StyleConfidence 属性（以前属于 TextStyle 对象）   。

* FieldValueType。  已从模型中删除值 Gender。

* 已删除 TextStyle 模型。

* 已删除 FieldValueGender 类型。

### <a name="java"></a>[**Java**](#tab/java)

  | [参考文档](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Maven 项目包依赖项版本 3.1.0](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) |

#### <a name="non-breaking-changes"></a>**非重大变化**

* FormRecognizerClientBuilder 和 FormTrainingClientBuilder 。 添加了 clientOptions 和 getDefaultLogOptions 方法 。

* FormRecognizerLanguage。  添加了更多语言字段。

#### <a name="breaking-changes-may"></a>**中断性变更（5 月）**

* 客户端默认为受支持的最新服务版本，当前为 v2.1。 可在 FormRecognizerClientBuilder 对象的 serviceVersion 方法中指定版本 2.0 。

* 已删除 v2.1-preview.1 和 v2.1-preview.2 支持。

* beginRecognizeIdentityDocuments。  通过使用“标识”替换所有相关标识文档识别 API 功能的“ID”关键字重命名了方法和方法参数。

* FormReadingOrder。 ReadingOrder 已重命名为 FormReadingOrder，并将该类重构为可展开的字符串类。

* asCountryRegion。 asCountry 已重命名为 asCountryRegion 方法。

* FieldValueType。 字段值 COUNTRY 已重命名为 COUNTRY_REGION。

* TextAppearance 类现包括 styleName 和 styleConfidence 属性（以前属于 TextStyle 对象）   。

* FieldValueType。 已从模型中删除值 Gender。

* 已删除 TextStyle 模型。

* 已删除 FieldValueGender 类类型。

* pollInterval。 从 RecognizeBusinessCardsOptions、RecognizeContentOptions、RecognizeCustomFormsOptions、RecognizeIdentityDocumentOptions、RecognizeInvoicesOptions 和 RecognizeReceiptsOptions 类中删除了 pollInterval 方法     。 可分别使用 Azure Core [SyncPoller setPollInterval](/java/api/com.azure.core.util.polling.syncpoller.setpollinterval?view=azure-java-stable&preserve-view=true) 或 [PollerFlux setPollInterval](/java/api/com.azure.core.util.polling.pollerflux.setpollinterval?view=azure-java-stable&preserve-view=true) 方法同步或异步更新轮询间隔 。

* FormLine、FormPage、FormTable、FormSelectionMark、TextAppearance、CustomFormModel、CustomFormModelInfo、CustomFormModelProperties、CustomFormSubmodel 和 TrainingDocumentInfo 现为不可变模型类          。

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

| [参考文档](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm 包依赖项表单识别器 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |

#### <a name="non-breaking-changes"></a>**非重大变化**

* 所有 REST API 调用都迁移到 v2.1 终结点。

* 添加了 KnownFormLocale 枚举，用于访问表单区域设置的可能值。

* beginRecognizeIdDocuments...。通过使用“标识”替换所有相关标识文档识别 API 功能的“ID”关键字重命名了方法和方法参数。

* FormReadingOrder 和 FormLanguage 。 ReadingOrder 已重命名为 FormReadingOrder 。 Language 已重命名为 FormLanguage。

* FormCountryRegionField 和 countryRegion 。 FormCountryField 类型已重命名为 FormCountryRegionField，valueType country 已重命名为 countryRegion。

* TextAppearance 接口现包括 styleName 和 styleConfidence 属性（以前的 name 和 confidence 属性位于 TextStyle 接口中）   。

* 已删除 KnownStyleName、KnownSelectionMarkState 和 KnownKeyValueType 枚举  。

* 已删除 FormGenderField 类型。 任何以前作为 FormGenderField 生成的可识别值现在都将作为 FormStringField 类型返回，并且该值将保持不变。

* 已删除 TextStyle 类型。

#### <a name="breaking-changes-may"></a>**中断性变更（5 月）**

**无中断性变更**

### <a name="python"></a>[**Python**](#tab/python)

| [参考文档](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.0](https://pypi.org/project/azure-ai-formrecognizer/) |

#### <a name="non-breaking-changes"></a>**非重大变化**

* to_dict 和 from_dict 方法已添加到所有模型 。

#### <a name="breaking-changes-may"></a>**中断性变更（5 月）**

* begin_recognize_identity_documents 和 begin_recognize_identity_documents_from_url 。 通过使用“标识”替换“Id”关键字重命名了方法和方法参数。

* FieldValueType。 值类型 country 已重命名为 countryRegion。  已删除值类型 gender。

* TextAppearance 模型现包括 style_name 和 style_confidence 属性（以前的 name 和 confidence 属性位于 TextStyle 对象中）   。

* 已删除 TextStyle 模型。

---

## <a name="april-2021"></a>2021 年 4 月
<!-- markdownlint-disable MD029 -->

### <a name="sdk-preview-updates-for-api--version-21-preview3"></a>API 版本 2.1 的 SDK 预览版更新 - 预览版 3

### <a name="c"></a>[**C#**](#tab/csharp)

NuGet 包版本 3.1.0 - beta.4

* **用于分析标识文档中数据的新方法**：

   **StartRecognizeIdDocumentsFromUriAsync**

   **StartRecognizeIdDocumentsAsync**

   有关字段值的列表，请参阅表单识别器文档中的[提取的字段](concept-identification-cards.md#fields-extracted)。

* 扩展了可提供给 [StartRecognizeContent](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true) 方法的文档语言集。

* **以下类支持的新属性 `Pages`** ：

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   `Pages` 属性允许选择多页 PDF 和 TIFF 文档的单个页面或特定范围的页面。 对于单个页面，请输入页码，例如 `3`。 对于页面范围（如第 2 页和第 5 页到第 7 页），请输入用逗号分隔的页码和范围：`2, 5-7`。

* **以下类支持的新属性 `ReadingOrder`** ：

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  `ReadingOrder` 属性是一个可选参数，可用于指定应该应用哪一种读取顺序算法（`basic` 或 `natural`）对文本元素的提取进行排序。 如果未指定，默认值为 `basic`。

#### <a name="breaking-changes-april"></a>中断性变更（4 月）

* 客户端默认为支持的最新服务版本，目前为 2.1-preview.3。

* [StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_) 方法现在会在传递无效文件时引发 `RequestFailedException()`。

### <a name="java"></a>[**Java**](#tab/java)

Maven 项目包依赖项版本 3.1.0 - beta 3

* **用于分析标识文档中数据的新方法**：

  [beginRecognizeIdDocumentsFromUrl]

  [beginRecognizeIdDocuments]

   有关字段值的列表，请参阅表单识别器文档中的[提取的字段](concept-identification-cards.md#fields-extracted)。

* **位图图像文件 (.bmp) 支持 `FormContentType` 枚举中的自定义表单和训练方法**：

* `image/bmp`

* **以下类支持的新属性 `Pages`** ：

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  `Pages` 属性允许选择多页 PDF 和 TIFF 文档的单个页面或特定范围的页面。 对于单个页面，请输入页码，例如 `3`。 对于页面范围（如第 2 页和第 5 页到第 7 页），请输入用逗号分隔的页码和范围：`2, 5-7`。

* **位图图像文件 (.bmp) 支持 [FormContentType](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields) 字段中的自定义表单和训练方法**：

  `image/bmp`

* **以下方法支持的新关键字参数 `ReadingOrder`** ：

* **[beginRecognizeContent](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?preserve-view=true&view=azure-java-preview)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   `ReadingOrder` 关键字参数是一个可选参数，可用于指定应该应用哪一种读取顺序算法（`basic` 或 `natural`）对文本元素的提取进行排序。 如果未指定，默认值为 `basic`。

* 客户端默认为支持的最新服务版本，目前为 2.1-preview.3。

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

npm 包版本 3.1.0 - beta.3

* **用于分析标识文档中数据的新方法**：

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    有关字段值的列表，请参阅表单识别器文档中的[提取的字段](concept-identification-cards.md#fields-extracted)。

* **为 FieldValue 接口添加了新字段值**：

    `gender` - 可能的值为 `M`、`F` 或 `X`。</br>
   `country` - 可能的值遵循 [ISO alpha-3](https://www.iso.org/obp/ui/#search) 三个字母的国家/地区代码字符串。

* 所有表单识别方法（自定义表单和所有预生成模型）都支持的新选项 `pages`。 该参数允许选择多页 PDF 和 TIFF 文档的单个页面或特定范围的页面。 对于单个页面，请输入页码，例如 `3`。 对于页面范围（如第 2 页和第 5 页到第 7 页），请输入用逗号分隔的页码和范围：`2, 5-7`。

* 向内容识别方法添加了对 **[ReadingOrder](/javascript/api/@azure/ai-form-recognizer/formreadingorder?view=azure-node-latest&preserve-view=true to the URL)** 类型的支持。 使用此选项可控制服务用于确定应如何对已识别的文本行进行排序的算法。 可指定应该应用哪一种读取顺序算法（`basic` 或 `natural`）对文本元素的提取进行排序。 如果未指定，默认值为 `basic`。

* 将 [FormField](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true) 类型拆分为多个不同的接口。 此更新不会导致任何 API 兼容性问题，除了某些边缘情况（未定义 valueType）。

* 已迁移到 2.1-preview.3 表单识别器服务终结点来进行所有 REST API 调用。

### <a name="python"></a>[**Python**](#tab/python)

pip 包版本 3.1.0b4

* **用于分析标识文档中数据的新方法**：

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  有关字段值的列表，请参阅表单识别器文档中的[提取的字段](concept-identification-cards.md#fields-extracted)。

* **为 [FieldValueType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true) 枚举添加了新字段值**：

   gender - 可能的值为 `M`、`F` 或 `X`。

  country - 可能的值遵循 [ISO alpha-3 国家/地区代码](https://www.iso.org/obp/ui/#search)。

* **位图图像文件 (.bmp) 支持 [FormContentType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true) 枚举中的自定义表单和训练方法**：

    image/bmp

* **以下方法支持的新关键字参数 `pages`** ：

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   `pages` 关键字参数允许选择多页 PDF 和 TIFF 文档的单个页面或特定范围的页面。 对于单个页面，请输入页码，例如 `3`。 对于页面范围（如第 2 页和第 5 页到第 7 页），请输入用逗号分隔的页码和范围：`2, 5-7`。

* **以下方法支持的新关键字参数 `readingOrder`** ：

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   `readingOrder` 关键字参数是一个可选参数，可用于指定应该应用哪一种读取顺序算法（`basic` 或 `natural`）对文本元素的提取进行排序。 如果未指定，默认值为 `basic`。

---

## <a name="march-2021"></a>2021 年 3 月

窗体识别器 2.1 公共预览版 3 现已推出。 2\.1 预览版 3 已发布，其中包括以下功能：

* 新的预生成 ID 模型 新的预生成 ID 模型使客户能够创建 ID，并返回结构化数据以进行自动处理。 它结合了强大的光学字符识别 (OCR) 功能与 ID 理解模型，可从护照和美国驾照中提取关键信息（如姓名、出生日期、颁发日期、到期日期等）。

  [了解有关预生成 ID 模型的更多信息](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="护照示例" lightbox="./media/id-canada-passport-example.png":::

* 预生成发票模型的行项提取 - 预生成发票模型现在支持行项提取；现在，它将提取所有项及其部件：说明、金额、数量、产品 ID、日期等。 使用简单的 API/SDK 调用，可以从发票中提取有用数据：文本、表、键值对和行项。

   [了解有关预生成发票模型的更多信息](concept-invoices.md)

* 监督式表标记和训练，空值标记 - 除了表单识别器的[先进深度学习自动表提取功能](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)，现在还允许客户对表进行标记和训练。 此新版本包括对行项/表进行标记和训练（动态和固定），并训练自定义模型来提取键值对和行项。 对模型进行训练后，模型将提取行项作为 JSON 输出中 documentResults 部分的一部分。

    :::image type="content" source="./media/table-labeling.png" alt-text="表标记" lightbox="./media/table-labeling.png":::

    除了标记表，你现在还可以标记空值和区域；如果训练集中的某些文档没有为某些字段提供值，则可以标记它们，以便模型知道正确地从分析后的文档中提取值。

* 支持 66 种新语言 - 表单识别器的布局 API 和自定义模型现在支持 73 种语言。

  [了解有关表单识别器的语言支持的更多信息](language-support.md)

* 自然阅读顺序、手写分类和页面选择 - 通过此更新，你可以选择以自然阅读顺序（而不是从左到右和从上到下的顺序）获取文本行输出。 使用新的 readingOrder 查询参数，并将其设置为“natural”值，以获得更友好的阅读顺序输出。 此外，对于拉丁语语言，表单识别器会将文本行归类为手写样式，并提供置信度分数。

* 预生成收据模型质量改进 此更新包括预生成收据模型的许多质量改进，尤其是有关行项提取的改进。

## <a name="november-2020"></a>2020 年 11 月

### <a name="new-features"></a>新增功能

窗体识别器 2.1 公共预览版 2 现已推出。 2\.1 预览版 2 已发布，其中包括以下功能：

* 新的预生成发票模型 - 新的预生成发票模型使客户能够以各种格式创建发票，并返回结构化数据以自动处理发票。 它结合了强大的光学字符识别 (OCR) 功能与发票理解深度学习模型，可从英语发票中提取重要信息。 可提取重要文本、表和信息，如客户、供应商、发票 ID、发票到期日期、总计、应付金额、税金、送货地址和帐单地址。

  > [了解有关预生成发票模型的更多信息](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="发票示例" lightbox="./media/invoice-example.jpg":::

* 增强的表提取 - 表单识别器现在提供了增强的表提取功能，该功能结合了强大的光学字符识别 (OCR) 功能与深度学习表提取模型。 表单识别器可以从表中提取数据，包括包含合并列、行、无边框等的复杂表。

  :::image type="content" source="./media/tables-example.jpg" alt-text="表示例" lightbox="./media/tables-example.jpg":::

  > [了解有关布局提取的更多信息](concept-layout.md)

* 客户端库更新 - 适用于 .NET、Python、Java 和 JavaScript 的[客户端库](quickstarts/client-library.md)的最新版本支持表单识别器 2.1 API。
* 支持的新语言：日语 - 现在支持以下新语言：对于 `AnalyzeLayout` 和 `AnalyzeCustomForm`：日语 (`ja`)。 [语言支持](language-support.md)
* 文本行样式指示（手写/其他）（仅拉丁语）- 格式识别器现在会输出一个 `appearance` 对象（该对象会分类每个文本行是否为手写样式）以及置信度分数。 此功能仅支持拉丁语。
* 质量改进 - 提取改进（包括一位数提取改进）。
* 表单识别器示例和标记工具中的新试用功能 - 可以使用表单识别器示例标记工具试用预生成的发票、收据和名片模型以及布局 API。 查看如何不编写任何代码即可提取数据。

  [试用表单识别器示例标记工具](https://fott-2-1.azurewebsites.net)

  ![屏幕截图：示例标记工具。](./media/ui-preview.jpg)

* 反馈循环 - 通过示例标记工具分析文件时，现在还可以将其添加到训练集，根据需要调整标记，并训练以改进模型。
* 自动标记文档 - 根据项目中以前标记的文档自动标记其他文档。

## <a name="august-2020"></a>2020 年 8 月

### <a name="new-features"></a>新增功能

表单识别器 2.1 公共预览版现已推出。 2\.1 预览版 1 已发布，其中包括以下功能：

* REST API 参考可用 - 请参阅 [2.1 预览版 1 参考](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
* 除了英语以外受支持的新语言，以下[语言](language-support.md)现在受支持：对于 `Layout` 和 `Train Custom Model`：英语 (`en`)、简体中文 (`zh-Hans`)、荷兰语 (`nl`)、法语 (`fr`)、德语 (`de`)、意大利语 (`it`)、葡萄牙语 (`pt`) 和西班牙语 (`es`)。
* 复选框/选择标记检测 - 表单识别器支持检测和提取选择标记（如复选框和单选按钮）。 选择标记是在 `Layout` 中提取的，你现在还可以在 `Train Custom Model` - “使用标签进行训练”中进行标记和训练，以便提取选择标记的键值对。
* 模型组合 - 允许组合多个模型并使用单个模型 ID 调用。 提交要使用组合模型 ID 分析的文档时，首先会执行分类步骤以将其路由到正确的自定义模型。 模型组合可用于 `Train Custom Model` - “使用标签进行训练”。
* 模型名称 - 将友好名称添加到自定义模型，以便更轻松地进行管理和跟踪。
* [新的名片预生成模型](concept-business-cards.md)，用于提取英语、语言名片中的公共字段。
* **[预生成收据的新区域设置](concept-receipts.md)** 除 EN-US 外，现在还支持 EN-AU、EN-CA、EN-GB、EN-IN
* 针对 `Layout`、`Train Custom Model` - “在没有标签的情况下进行训练”和“使用标签进行训练”的质量改进。

2.0 版包括以下更新：

* NET、Python、Java 和 JavaScript 的[客户端库](quickstarts/client-library.md)已正式发布。

可在 GitHub 上找到新示例。

* [知识提取方案 - 表单操作手册](https://github.com/microsoft/knowledge-extraction-recipes-forms)从真实的表单识别器客户互动中收集最佳做法，并提供用于开发这些项目的可用代码示例、清单和示例管道。
* [示例标记工具](https://github.com/microsoft/OCR-Form-Tools)已更新，以支持新的 2.1 版功能。 请参阅此[快速入门](label-tool.md)，了解如何开始使用此工具。
* [智能展台](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md)表单识别器示例显示了如何集成 `Analyze Receipt` 和 `Train Custom Model` - “在没有标签的情况下进行训练”。

## <a name="july-2020"></a>2020 年 7 月

### <a name="new-features"></a>新增功能
<!-- markdownlint-disable MD004 -->
* 2\.0 版参考可用 - 查看 [2.0 版 API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)和适用于 [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme)、[Python](/python/api/overview/azure/)、[Java](/java/api/overview/azure/ai-formrecognizer-readme) 和 [JavaScript](/javascript/api/overview/azure/) 的更新的 SDK。
* 表增强功能和提取增强功能 - 包括准确性改进和表提取增强功能，具体是指在“在没有标签的情况下自定义训练”中了解表头和结构的功能。

* 货币支持 - 检测和提取全球货币符号。
* Azure Gov - 表单识别器现已在 Azure Gov 提供。
* 增强的安全性功能：
  * 自带密钥 - 表单识别器会在保存到云时自动加密数据，以保护数据，并帮助你满足组织的安全性和合规性承诺。 默认情况下，订阅使用 Microsoft 托管的加密密钥。 你现在也可以使用自己的加密密钥来管理订阅。 [客户管理的密钥（也称为自带密钥，BYOK）](./encrypt-data-at-rest.md)，在创建、轮换、禁用和撤销访问控制方面可提供更大的灵活性。 此外，你还可以审核用于保护数据的加密密钥。
  * 专用终结点 - 使你能够在虚拟网络中[通过专用链接安全访问数据](../../private-link/private-link-overview.md)。

## <a name="june-2020"></a>2020 年 6 月

### <a name="new-features"></a>新增功能

* 添加到客户端 SDK 的 CopyModel API - 现在可以使用客户端 SDK 将模型从一个订阅复制到另一个订阅。 有关此功能的一般信息，请参阅[备份和恢复模型](./disaster-recovery.md)。
* Azure Active Directory 集成 - 你现在可以使用你的 Azure AD 凭据对 SDK 中的表单识别器客户端对象进行身份验证。
* SDK 特定更改 - 此更改包括添加的次要功能和重大更改。 有关更多信息，请参阅 SDK 更改日志。
  * [C# SDK 预览版 3 更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK 预览版 3 更改日志](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK 预览版 3 更改日志](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK 预览版 3 更改日志](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-features"></a>新增功能

* SDK 对表单识别器 API 2.0 公共预览版的支持 - 本月，我们扩展了我们的服务支持，以包括适用于表单识别器 2.0 预览版的预览版 SDK。 使用以下链接开始使用你选择的语言：
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  新的 SDK 支持适用于表单识别器的 v2.0 REST API 的所有功能。 例如，你可以在具有或没有标签的情况下训练模型，并从表单提取文本、键值对和表，使用预生成的收据服务从收据中提取数据，以及使用布局服务从文档中提取文本和表。 可以通过 [SDK 反馈表单](https://aka.ms/FR_SDK_v1_feedback)分享有关 SDK 的反馈。

* 复制自定义模型 你现在可以使用新的复制自定义模型功能在区域和订阅之间复制模型。 在调用复制自定义模型 API 之前，必须首先通过对目标资源终结点调用复制授权操作，来获取要复制到目标资源的授权。

  * [生成复制授权](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [复制自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API

### <a name="security-improvements"></a>安全性改进

* 客户管理的密钥现在可用于 FormRecognizer。 有关更多信息，请参阅[表单识别器的静态数据加密](./encrypt-data-at-rest.md)。
* 使用托管标识通过 Azure Active Directory 访问 Azure 资源。 有关更多信息，请参阅[授权访问托管标识](../../cognitive-services/authentication.md#authorize-access-to-managed-identities)。

## <a name="march-2020"></a>2020 年 3 月

### <a name="new-features"></a>新增功能

* 用于标记的值类型 你现在可以指定使用表单识别器示例标记工具标记的值类型。 目前支持以下值类型和变体：
  * `string`
    * default、`no-whitespaces`、`alphanumeric`
  * `number`
    * default、`currency`
  * `date`
    * default、`dmy`、`mdy`、`ymd`
  * `time`
  * `integer`

  若要了解如何使用此功能，请参阅[示例标记工具](label-tool.md#specify-tag-value-types)指南。

* 表可视化 示例标记工具现在会显示在文档中识别的表。 此功能使你可以在标记和分析之前查看已在文档中识别和提取的表。 可以使用“层”选项开启/关闭此功能。

  下图是如何识别和提取表的示例：

  > [!div class="mx-imgBorder"]
  > ![使用示例标记工具的表可视化](./media/whats-new/table-viz.png)

    提取的表在 JSON 输出中的 `"pageResults"` 下可用。

  > [!IMPORTANT]
  > 标记表不受支持。 如果无法识别并自动提取表，则只能将它们标记为键/值对。 如果将表标记为键/值对，请将每个单元标记为唯一值。

### <a name="extraction-enhancements"></a>提取增强功能

此版本包括提取增强功能和准确性改进，具体是指在同一文本行中标记和提取多个键/值对的功能。

### <a name="sample-labeling-tool-is-now-open-source"></a>示例标记工具现在为开源

表单识别器示例标记工具现在可作为开源项目提供。 你可以将其集成到你的解决方案中，并根据需要进行特定于客户的更改。

有关表单识别器示例标记工具的更多信息，请查看 [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md) 上提供的文档。

### <a name="tls-12-enforcement"></a>强制执行 TLS 1.2

现在，将对此服务的所有 HTTP 请求强制执行 TLS 1.2。 有关详细信息，请参阅 [Azure 认知服务安全性](../../cognitive-services/cognitive-services-security.md)。

## <a name="january-2020"></a>2020 年 1 月

此版本引入了表单识别器 2.0（预览版）。 在以下部分，你将找到有关新功能、增强功能和更改的更多信息。

### <a name="new-features"></a>新增功能

* **自定义模型**
  * 使用标签进行训练 你现在可以使用手动标记的数据来训练自定义模型。 此方法会生成表现更好的模型，并且可以生成处理复杂表单或所含值没有键的表单的模型。
  * 异步 API 可以使用异步 API 调用来训练和分析大型数据集和文件。
  * TIFF 文件支持 你现在可以训练和提取 TIFF 文档中的数据。
  * 提取准确性改进

* 预生成收据模型
  * 小费金额 你现在可以提取小费金额和其他手写值。
  * 行项提取 你可以从收据中提取行项值。
  * 置信度值 你可以查看模型对每个提取值的置信度。
  * 提取准确性改进

* 布局提取 你现在可以使用布局 API 从表单中提取文本数据和表数据。

### <a name="custom-model-api-changes"></a>自定义模型 API 更改

所有用于训练和使用自定义模型的 API 都已重命名，并且一些同步方法现在是异步的。 以下是主要更改：

* 模型训练过程现在是异步的。 通过 /custom/model API 调用发起训练。 此调用将返回一个操作 ID，你可以将该 ID 传递到 custom/models/{modelID} 以返回训练结果。
* 键/值提取现在由 /custom/models/{modelID}/analyze API 调用发起。 此调用将返回一个操作 ID，你可以将该 ID 传递到 custom/models/{modelID}/analyzeResults/{resultID} 以返回提取结果。
* 训练操作的操作 ID 现在位于 HTTP 响应的 Location 标头中，而非 Operation-Location 标头中。

### <a name="receipt-api-changes"></a>收据 API 更改

用于读取销售收据的 API 已重命名。

* 收据数据提取现在由 /prebuilt/receipt/analyze API 调用发起。 此调用将返回一个操作 ID，你可以将该 ID 传递到 /prebuilt/receipt/analyzeResults/{resultID} 以返回提取结果。

### <a name="output-format-changes"></a>输出格式更改

所有 API 调用的 JSON 响应都具有新格式。 已添加、删除或重命名某些键和值。 有关当前 JSON 格式的示例，请参阅快速入门。

## <a name="next-steps"></a>后续步骤

完成[快速入门](quickstarts/client-library.md)，开始使用表单识别器以你选择的开发语言编写表单处理应用。

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)