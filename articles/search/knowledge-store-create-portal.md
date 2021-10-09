---
title: 快速入门：在 Azure 门户中创建知识存储
titleSuffix: Azure Cognitive Search
description: 使用“导入数据”向导创建用于保存扩充内容的知识存储。 连接到知识存储以便从其他应用进行分析，或将扩充内容发送到下游流程。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/02/2021
ms.openlocfilehash: f80a4a5961c0506f423da4d4f1578b8cf8999b51
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755237"
---
# <a name="quickstart-create-a-knowledge-store-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建知识存储

[知识存储](knowledge-store-concept-intro.md)是 Azure 认知搜索的一项功能，它可以将输出从 [AI 扩充管道](cognitive-search-concept-intro.md)发送到 Azure 存储。 管道创建的扩充内容（例如已翻译的文本、OCR 文本、已识别的实体和其他扩充内容）投影到表或 Blob 中，任何连接到 Azure 存储的应用或工作负载都可以在其中访问这些内容。

在本快速入门中，你将设置数据，然后运行 **导入数据** 向导来创建一个同样会生成知识存储的扩充管道。 知识存储将包含从源（客户对酒店的评论）提取的原始文本内容，以及 AI 生成的内容，包括情绪标签、关键短语提取和非英语客户评论的文本翻译。

> [!NOTE]
> 本快速入门展示了在 Azure 存储中完成知识存储的最快途径。 有关每个步骤更详细的说明，请改为参阅[在 REST 中创建知识存储](knowledge-store-create-rest.md)。

## <a name="prerequisites"></a>必备条件

本快速入门使用以下服务：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。

+ Azure 认知搜索服务。 在帐户中[创建服务](search-create-service-portal.md)或[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。 

+ 具有 [Blob 存储](../storage/blobs/index.yml)的 Azure 存储帐户。

此快速入门还将[认知服务](https://azure.microsoft.com/services/cognitive-services/)用于 AI。 由于工作负荷很小，因此，认知服务在幕后会抽调一部分算力来免费处理事务（最多 20 个）。 这意味着，无需创建其他认知服务资源即可完成此练习。

## <a name="set-up-your-data"></a>设置数据

在以下步骤中，在 Azure 存储中设置 blob 容器以存储异类内容文件。

1. [下载 HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)。 此数据是保存在某个 CSV 文件中的酒店评论数据（源自 Kaggle.com），其中包含客户对一家酒店的 19 条反馈。 

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)或[查找现有帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 需要使用 Azure 存储来存储要导入的原始内容，并使用知识存储来存储最终结果。

   选择“StorageV2 (常规用途 V2)”帐户类型。

1. 在 Azure 存储资源中，使用存储资源管理器创建一个名为 hotel-reviews 的 blob 容器 。

1. 选择页面顶部的“上传”，以加载从上一步骤下载的 HotelReviews-Free.csv 文件 。

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="包含上传的文件和左侧导航窗格的存储资源管理器屏幕截图" border="true":::

1. 你几乎完成了对此资源的操作，但在退出这些页面之前，请选择左侧导航窗格中的“访问密钥”以获取连接字符串，以便可以使用索引器检索此数据。

1. 在“访问密钥”中，选择页面顶部的“显示密钥”以取消隐藏连接字符串，然后复制 key1 或 key2 的连接字符串 。

   连接字符串具有以下格式：`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

现在可以在“导入数据”向导中转到下一步。

## <a name="start-the-wizard"></a>启动向导

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。

1. [查找搜索服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)，并在“概述”页中，单击命令栏上的“导入数据”，通过四个步骤创建知识存储。

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="“导入数据”命令的屏幕截图" border="true":::

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源

由于数据在一个 CSV 文件中包含多行，因此请设置分析模式，为每一行获取一个搜索文档。

1. 在“连接到数据”中，选择“Azure Blob 存储”，再选择创建的帐户和容器 。 

1. 对于“名称”，请输入 `hotel-reviews-ds`。

1. 对于“分析模式”，请选择“分隔文本”，然后选中“第一行包含标头”复选框。   确保“分隔符”是逗号 (,)。

1. 在“连接字符串”中，粘贴从 Azure 存储复制的连接字符串。

1. 在“容器”中，输入保存数据 (`hotel-reviews`) 的 blob 容器的名称。

    页面应类似于以下屏幕截图。

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ds.png" alt-text="数据源定义的屏幕截图" border="true":::

1. 继续转到下一页。

### <a name="step-2-add-skills"></a>步骤 2：添加技能

在此向导步骤中，添加 AI 扩充技能。 源数据包括用英文和法语撰写的客户评论。 与此数据集相关的技能包括关键短语提取、情绪检测和文本翻译。 在后续步骤中，这些扩充内容将以 Azure 表的形式“投影”到知识存储。

1. 展开“附加认知服务”。 默认已选择“免费(受限扩充)”。 之所以可以使用此资源，是因为 HotelReviews-Free.csv 中的记录数为 19 个，并且此免费资源每天最多允许 20 个事务。

1. 展开“添加扩充”。

1. 对于“技能集名称”，请输入 `hotel-reviews-ss`。

1. 对于“源数据字段”，请选择“reviews_text”。 

1. 对于“扩充粒度级别”，请选择“页面(5000 个字符区块)” 。

1. 对于“文本认知技能”，请选择以下技能：

    + **提取关键短语**
    + **翻译文本**
    + **语言检测**
    + **检测情绪**

   页面应类似于以下屏幕截图所示：

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ss.png" alt-text="技能组定义的屏幕截图" border="true":::

1. 向下滚动并展开“将扩充内容保存到知识存储”。

1. 选择以下“Azure 表投影”。 向导始终提供“文档”投影。 将根据你选择的技能（例如“关键短语”）或扩充粒度（“页面”）提供其他投影 ：

    + 文档
    + **页**
    + **关键短语**

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ks.png" alt-text="知识存储定义的屏幕截图" border="true":::

1. 输入在上一步骤中保存的 **存储帐户连接字符串**。

1. 继续转到下一页。

### <a name="step-3-configure-the-index"></a>步骤 3：配置索引

在此向导步骤中，为可选的全文搜索查询配置索引。 向导将对数据源进行采样，以推断字段和数据类型。 你只需为所需的行为选择属性。 例如，“可检索”属性将允许搜索服务返回一个字段值，而“可搜索”属性将对字段启用全文搜索。 

1. 对于“索引名称”，请输入 `hotel-reviews-idx`。

1. 对于属性，请接受默认选项：“可检索”和“可搜索”（对于管道正在创建的新字段） 。

    索引应与下图类似： 由于该列表很长，图像中未显示所有字段。

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-idx.png" alt-text="索引定义的屏幕截图" border="true":::

1. 继续转到下一页。

### <a name="step-4-configure-and-run-the-indexer"></a>步骤 4：配置和运行索引器

在此向导步骤中，配置一个索引器，用于统一提取前面向导步骤中定义的数据源、技能集和索引。

1. 对于“名称”，请输入 `hotel-reviews-idxr`。

1. 对于“计划”，请保留默认设置“一次”。 

1. 选择“提交”运行索引器。 数据提取、索引编制和应用认知技能的操作都在此步骤中发生。

## <a name="check-status"></a>查看状态

在“概述”页面中，打开页面中间的“索引器”选项卡，然后选择“hotels-reviews-ixr”  。 在一两分钟内，状态应从“正在进行”变为“成功”，且没有任何错误和警告。

## <a name="check-tables-in-storage-explorer"></a>使用存储资源管理器查看表

在 Azure 门户中，切换到 Azure 存储帐户并使用存储资源管理器查看新的表。 应会看到三个表，每个表对应于“添加扩充”页的“保存扩充”部分提供的每个投影。

+ `hotelReviewssDocument` 包含文档扩充树中所有不是集合的第一级节点。

+ `hotelReviewssPages` 包含在从文档拆分的每个页面上创建的扩充字段。 页面级扩充由情绪标签和已翻译的文本组成。 当你在技能集定义中选择“pages”粒度时，系统会创建一个 pages 表；如果指定 sentences 这个特定级别的粒度，则会创建一个 sentences 表。 在页面或句子级别执行的技能会将输出投影到此表。

+ `hotelReviewssKeyPhrases` 包含一长串从所有评论中提取的关键短语。 输出集合（数组）（例如关键短语和实体）的技能会将输出发送到一个独立的表。

所有这些表都包含 ID 列，以支持其他工具和应用中的表关系。 打开表时，滚动浏览这些字段可查看管道添加的内容字段。

本快速入门中的表应类似于以下屏幕截图：

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="存储资源管理器中生成的表的屏幕截图" border="true":::

## <a name="clean-up"></a>清理

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。

> [!TIP]
> 若要重复此练习或尝试其他 AI 扩充演练，请删除 hotel-reviews-idxr 索引器和相关对象以重新创建它们。 删除该索引器会将每日的免费事务计数器重置为零。

## <a name="next-steps"></a>后续步骤

现在，你已了解知识存储，请通过切换到 REST API 演练来仔细查看每个步骤。 REST 演练中介绍了向导在内部处理的任务。

> [!div class="nextstepaction"]
> [使用 REST 和 Postman 创建知识存储](knowledge-store-create-rest.md)
