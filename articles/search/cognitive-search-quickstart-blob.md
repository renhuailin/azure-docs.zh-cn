---
title: 快速入门：文本翻译和实体识别
titleSuffix: Azure Cognitive Search
description: 使用导入数据向导和 AI 认知技能检测语言、翻译文本和识别实体。 通过 AI 创建的新字段将成为 Azure 认知搜索索引中的可搜索文本。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/07/2021
ms.openlocfilehash: 01024a829c4e4447cfa7dc642469384170bc99f4
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708217"
---
# <a name="quickstart-translate-text-and-recognize-entities-using-the-import-data-wizard"></a>快速入门：使用导入数据向导翻译文本和识别实体

了解如何通过 Azure 认知搜索中的 AI 扩充，支持语言检测、文本翻译和实体识别功能，在搜索索引中创建可搜索内容。 

在本快速入门中，你将运行“导入数据”向导，分析有关西班牙多座国家博物馆的法语和西班牙语说明。 输出是包含已翻译的文本和实体的可搜索索引，可使用[搜索资源管理器](search-explorer.md)在门户中进行查询。 

为做好准备，在运行向导之前需要创建一些资源并上传示例文件。

想要从代码开始吗？ 请尝试改用 [.NET 教程](cognitive-search-tutorial-blob-dotnet.md)、[Python 教程](cognitive-search-tutorial-blob-python.md)或 [REST 教程](cognitive-search-tutorial-blob-dotnet.md)。

## <a name="prerequisites"></a>先决条件

开始之前，必须具备以下先决条件：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。

+ Azure 认知搜索服务。 [创建服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。 

+ 具有 Blob 存储的 Azure 存储帐户。 [创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)或[查找现有帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 

  + 如果希望向导查找存储帐户并设置连接，请选择同一订阅。
  + 选择 Azure 认知搜索所在的同一区域，以避免带宽费用。
  + 选择StorageV2（常规用途 V2）。

> [!NOTE]
> 此快速入门还将[认知服务](https://azure.microsoft.com/services/cognitive-services/)用于 AI。 由于工作负荷很小，因此，认知服务在幕后会抽调一部分算力来免费处理事务（最多 20 个）。 这意味着，无需创建其他认知服务资源即可完成此练习。

## <a name="set-up-your-data"></a>设置数据

在以下步骤中，在 Azure 存储中设置 blob 容器以存储异类内容文件。

1. 从 GitHub [下载示例数据](https://github.com/Azure-Samples/azure-search-sample-data)。 有多个数据集。 在本快速入门中，请使用 spanish-museums 文件夹中的文件。

1. 将示例数据上传到 Blob 容器。

   1. 登录到 [Azure 门户](https://portal.azure.com/)，并找到你的存储帐户。
   1. 在左侧导航窗格中，选择“容器”。
   1. [创建容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)，名为“spanish-museums”。 使用默认公共访问级别。
   1. 在“spanish-museums”容器中，选择“上传”，从本地 spanish-museums 文件夹上传文件 。

应有 10 个文件，其中包含位于西班牙的国家博物馆的法语和西班牙语说明。

   :::image type="content" source="media/cognitive-search-quickstart-blob/museums-container.png" alt-text="Blob 容器中的 docx 文件列表" border="true":::

现在可以在“导入数据”向导中转到下一步。

## <a name="run-the-import-data-wizard"></a>运行“导入数据”向导

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。

1. [查找搜索服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)，并在“概述”页中，单击命令栏上的“导入数据”，通过四个步骤设置认知扩充。

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="“导入数据”命令的屏幕截图" border="true":::

### <a name="step-1---create-a-data-source"></a>步骤 1 - 创建数据源

1. 在“连接到数据”中，选择“Azure Blob 存储” 。 选择与你创建的存储帐户和容器的现有连接。 为数据源命名，并对余下的设置使用默认值。 

   :::image type="content" source="media/cognitive-search-quickstart-blob/connect-to-spanish-museums.png" alt-text="Azure Blob 配置" border="true":::

### <a name="step-2---add-cognitive-skills"></a>步骤 2 - 添加认知技能

接下来，配置 AI 扩充以调用语言检测、文本翻译和实体识别。 

1. 本快速入门将使用 **免费** 的认知服务资源。 示例数据包括 10 个文件，因此，认知服务提供的每日每个索引器 20 个免费事务的配额足以完成本快速入门。 

   :::image type="content" source="media/cognitive-search-quickstart-blob/free-enrichments.png" alt-text="附加免费认知服务处理" border="true":::

1. 在同一页中，展开“添加扩充”并选择五个选项：

   选择实体识别（人员、组织、位置）

   选择语言检测和文本翻译

   :::image type="content" source="media/cognitive-search-quickstart-blob/select-entity-lang-enrichments.png" alt-text="附加认知服务 - 为技能组选择服务" border="true":::

   在 Blob 中，“内容”字段包含文件的内容。 在示例数据中，内容是有关给定博物馆的多个段落（法语或西班牙语）。 “粒度”即该字段本身。 某些技能在更小的文本块上效果更好，但对于本快速入门中的技能来说，字段粒度已足够。

### <a name="step-3---configure-the-index"></a>步骤 3 - 配置索引

索引包含可搜索的内容，“导入数据”向导通常可以通过对数据采样来推断架构。 在此步骤中查看生成的架构，并根据情况修改任何设置。 以下是为演示数据集创建的默认架构。

在本快速入门中，向导能够很好地设置合理的默认值：  

+ 默认字段基于现有 blob 的属性以及包含扩充输出的新字段（例如 `people`、`organizations`、`locations`）。 数据类型从元数据和数据采样推断。

+ 默认文档键是 *metadata_storage_path*（由于字段包含唯一值，因此选择了此键）。

+ 默认属性为 **可检索** 和 **可搜索**。 **可搜索** 允许对字段进行全文搜索。 **可检索** 意味着可以在结果中返回字段值。 向导假设你希望这些字段可检索且可搜索，因为它们是通过技能集创建的。

+ 选中“语言”的可筛选复选框。 向导不会设置文件夹，但鉴于存在多种语言，按语言进行的筛选功能在此演示中非常有用。

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields-lang-entities.png" alt-text="索引字段" border="true":::

将某个字段标记为 **Retrievable** 并不意味着该字段一定会出现在搜索结果中。 可以使用 **$select** 查询参数指定要包含的字段，来精确控制搜索结果的构成。 对于包含大量文本的字段（例如 `content`），可以使用 $select 参数针对应用程序的用户整理可管理的搜索结果，同时确保客户端代码可以通过 Retrievable 属性访问全部所需信息。

### <a name="step-4---configure-the-indexer"></a>步骤 4 - 配置索引器

索引器是推动索引过程的高级资源。 它指定数据源名称、目标索引和执行频率。 “导入数据”向导将创建多个对象，其中始终包括一个可以重复运行的索引器。

1. 在“索引器”页中，可以接受默认名称并单击“一次”计划选项来立即运行该索引器 。 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-spanish-museum.png" alt-text="索引器定义" border="true":::

1. 单击“提交”以创建并同时运行索引器。

## <a name="monitor-status"></a>监视状态

与典型的基于文本的索引相比，认知技能索引编制需要花费更长的时间才能完成。 若要监视进度，请转到“概述”页，然后选择页面中间的“索引器”选项卡。

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-status-spanish-museums.png" alt-text="索引器状态" border="true":::

若要查看有关执行状态的详细信息，请从列表中选择一个索引器。

## <a name="query-in-search-explorer"></a>搜索浏览器中的查询

创建索引后，可以运行查询以返回结果。 为完成此任务，请在门户中使用 **搜索浏览器**。 

1. 在搜索服务仪表板页上，单击命令栏上的“搜索浏览器”。

1. 选择顶部的“更改索引”，选择创建的索引。

1. 在“查询字符串”中，输入搜索字符串以查询索引，如 `search="picasso museum" &$select=people,organizations,locations,language,translated_text &$count=true &$filter=language eq 'fr'`，然后选择“搜索”。

   :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-query-string-spanish-museums.png" alt-text="在搜索资源管理器中查询字符串" border="true":::

随后会返回 JSON 格式的结果。这些结果可能非常冗长且难以阅读，尤其是出现在源自 Azure Blob 的大型文档中时。 在此工具中搜索时，可以借鉴一些提示，其中包括以下技术：

+ 追加 `$select`，以指定要包含在结果中的字段。 
+ 使用 CTRL-F 在 JSON 中搜索特定属性或术语。

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-results-spanish-museums.png" alt-text="搜索浏览器示例" border="true":::

查询字符串区分大小写，因此如果收到“未知字段”消息，请检查“字段”或“索引定义(JSON)”以验证名称和大小写。 

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

认知搜索具有可在导入数据向导中执行的其他内置技能。 下一步，尝试使用 OCR 和图像分析技能，从图像文件中创建文本可搜索的内容。

> [!div class="nextstepaction"]
> [快速入门：使用 OCR 和图像分析创建可搜索内容](cognitive-search-quickstart-ocr.md)