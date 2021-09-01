---
title: Tutorial:使用调试会话修复技能组
titleSuffix: Azure Cognitive Search
description: 调试会话（预览版）是一种 Azure 门户工具，用于在技能组中查找、诊断和修复问题。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ab1be78570ef784da6a51eb27d9d18d5c902f092
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221659"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Tutorial:使用调试会话调试技能组

技能组协调一系列分析或转换内容的操作，其中一种技能的输出将变为另一种技能的输入。 当输入依赖于输出时，如果技能组定义和字段关联出现错误，可能会导致操作和数据丢失。

Azure 门户中的调试会话可提供技能组的整体可视化效果。 借助此工具，可深入了解特定步骤，以轻松查看操作可能会在哪里失败。

在本文中，你将使用“调试会话”查找并修复以下问题：1) 缺少输入，2) 输出字段映射。 本教程将包含所有这些方面。 它将为你提供要编制索引的数据（临床试验数据）、创建对象的 Postman 集合，以及使用调试会话查找和修复技能组问题的说明。

> [!Important]
> 调试会话是一项在提供时未附带服务级别协议的预览功能，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="prerequisites"></a>先决条件

开始之前，必须具备以下先决条件：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。

+ Azure 认知搜索服务。 [创建服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。 

+ 具有 [Blob 存储](../storage/blobs/index.yml)的 Azure 存储帐户，用于承载示例数据以及保存在调试会话期间创建的临时数据。

+ [Postman 桌面应用](https://www.getpostman.com/)和 [Postman 集合](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)，用于通过 REST API 创建对象。

+ [示例数据（临床试验）](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials/clinical-trials-pdf-19)。

> [!NOTE]
> 此快速入门还将 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/)用于 AI。 由于工作负荷很小，因此，认知服务在幕后会抽调一部分算力来免费处理事务（最多 20 个）。 这意味着，无需创建其他认知服务资源即可完成此练习。

## <a name="set-up-your-data"></a>设置数据

本部分在 Azure Blob 存储中创建示例数据集，让索引器和技能组有内容可使用。

1. [下载包含 19 个文件的示例数据 (clinical-trials-pdf-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials/clinical-trials-pdf-19)。

1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)或[查找现有帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 

   + 选择 Azure 认知搜索所在的同一区域，以避免带宽费用。

   + 选择 StorageV2（常规用途 V2）帐户类型。

1. 导航到门户中的 Azure 存储服务页，创建一个 Blob 容器。 最佳做法是将访问级别指定为“专用”。 将容器命名为 `clinicaltrialdataset`。

1. 在容器中，单击“上传”以上传在第一个步骤中下载并解压缩的示例文件。

1. 在门户中，获取并保存 Azure 存储的连接字符串。 REST API 对数据编制索引时需要使用该字符串。 可以从 Azure 门户的“设置” > “访问密钥”获取连接字符串 。

## <a name="get-a-key-and-url"></a>获取密钥和 URL

REST 调用需要在每个请求中使用服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 认知搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="获取 HTTP 终结点和访问密钥" border="false":::

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="create-data-source-skillset-index-and-indexer"></a>创建数据源、技能组、索引和索引器

本部分将使用 Postman 和提供的集合来创建认知搜索数据源、技能组、索引和索引器。 如果你不熟悉 Postman，请参阅[本快速入门](search-get-started-rest.md)。

需要为本教程创建 [Postman 集合](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)才能完成此任务。 

1. 启动 Postman 并导入集合。 在“文件” > “新建”下，选择要导入的集合。
1. 导入集合后，展开操作列表 (...)。
1. 单击 **“编辑”** 。
1. 在“当前值”下，输入 `searchService` 的名称（例如，如果终结点为 `https://mydemo.search.windows.net`，则服务名称为“`mydemo`”）。
1. 输入包含搜索服务主要密钥或辅助密钥的 `apiKey`。
1. 输入 Azure 存储帐户的密钥页中的 `storageConnectionString`。
1. 输入在存储帐户中创建的容器的 `containerName`，然后单击“更新”。

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="在 Postman 中编辑变量":::

该集合包含四个不同的 REST 调用，可使用这些调用创建本教程中使用的对象。

第一个调用创建数据源。 `clinical-trials-ds`. 第二个调用创建技能组 `clinical-trials-ss`。 第三个调用创建索引 `clinical-trials`。 第四个（也是最后一个）调用创建索引器 `clinical-trials-idxr`。

+ 依次打开每个请求，然后单击“发送”以将每个请求发送到搜索服务。 

完成集合中的所有调用后，关闭 Postman 并返回 Azure 门户。

## <a name="check-results-in-the-portal"></a>在门户中检查结果

由于技能组执行过程中发生了问题，示例代码特意创建了错误索引。 此问题是指缺少数据。 

1. 在 Azure 门户的搜索服务概述页上，选择“索引”选项卡。 
1. 选择 `clinical-trials` 索引。
1. 输入此查询字符串 `$select=metadata_storage_path, organizations, locations&$count=true` 以返回特定文档的字段（由唯一 `metadata_storage_path` 字段标识）。
1. 单击“搜索”以运行查询，返回所有 19 个文档并对“organizations”和“locations”显示空值。

这些字段应已通过技能组的[实体识别技能](cognitive-search-skill-entity-recognition-v3.md)进行了填充，此技能用于查找 blob 内容内任何位置的 organizations 和 locations。 在下一练习中，你将使用调试会话来确定发生了什么问题。

还可通过 Azure 门户调查错误和警告。

1. 打开“索引器”选项卡，然后选择 `clinical-trials-idxr`。
1. 请注意，虽然索引器作业全部成功完成，但有 57 个警告。
1. 单击“成功”以查看警告（如果大部分都是错误，详细信息链接将失败）。  你将看到索引器发出的每个警告都有一长串列表。

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="查看警告":::

## <a name="start-your-debug-session"></a>启动调试会话

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="启动新的调试会话":::

1. 在搜索概述页中，单击“调试会话”选项卡。
1. 选择“+ 新建调试会话”。
1. 为会话命名。 
1. 将会话连接到存储帐户。 
1. 在索引器模板中，提供索引器名称。 索引器具有对数据源、技能组和索引的引用。
1. 接受集合中第一个文档的默认文档选择。 
1. 保存会话。 保存会话将启动技能组定义的 AI 扩充管道。

> [!Important]
> 调试会话仅适用于单个文档。 可选择要调试的文档，也可以只使用第一个文档。

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

调试会话初始化完毕后，该会话默认显示“AI 扩充”选项卡，并突出显示“技能图” 。 技能图提供技能组的可视层次结构并按顺序并行显示其执行顺序。

## <a name="find-issues-with-the-skillset"></a>查找技能组问题

索引器报告的任何问题都可在相邻的“错误/警告”选项卡中找到。 

请注意，“错误/警告”选项卡提供的列表比之前显示的列表要小得多，因为此列表仅详细说明单个文档的错误。 和索引器显示的列表一样，你可以单击警告消息并查看此警告的详细信息。

选择“错误/警告”以查看通知。 应会看到三个通知：

   + “无法将输出字段“locations”映射到搜索索引。 请检查索引器的“outputFieldMappings”属性。
缺少值“/document/merged_content/locations”。”

   + “无法将输出字段“organizations”映射到搜索索引。 请检查索引器的“outputFieldMappings”属性。
缺少值“/document/merged_content/organizations”。”

   + “技能已执行，但可能会产生意外结果，因为一个或多个技能输入无效。
缺少可选的技能输入。 名称：languageCode，源：/document/languageCode。 表达式语言分析问题：缺少值“/document/languageCode”。

   许多技能都有一个 languageCode 参数。 通过检查该操作，可以看到 `Enrichment.NerSkillV2.#1` 缺少此语言代码输入，这与在“locations”和“organizations”输出方面遇到问题的实体识别技能相同。 

由于这三个通知全都是关于此技能的，因此下一步就是调试这项技能。 如果可能，请先解决输入问题，然后再解决 outputFieldMapping 问题。

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="新调试会话已启动":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>修复缺失的技能输入值

在“错误/警告”选项卡中，标记为 `Enrichment.NerSkillV2.#1` 的操作存在错误。 此错误的详细信息说明技能输入值“/document/languageCode”存在问题。 

1. 返回“AI 扩充”选项卡。
1. 单击“技能图”。
1. 单击标记为 #1 的技能，在右窗格中显示其详细信息。
1. 找到“languageCode”的输入。
1. 选择该行开头的 </> 符号，并打开表达式计算器。
1. 单击“求值”按钮以确认此表达式导致错误。 它将确认“languageCode”属性不是有效输入。

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="表达式计算器":::

可通过两种方法在会话中研究此错误。 第一种方法是查看输入来自何处，层次结构中的哪个技能应该会生成此结果？ “技能详细信息”窗格中的“执行”选项卡应显示输入源。 如果没有源，则表示存在字段映射错误。

1. 单击“执行”选项卡。
1. 查看输入并找到“languageCode”。 未列出此输入的源。 
1. 切换左窗格以显示“扩充数据结构”。 没有与“languageCode”对应的映射路径。

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="扩充数据结构":::

存在与“language”对应的映射路径。 因此，技能设置中存在拼写错误。 若要修复此问题，需要使用“/document/language”表达式更新技能 #1 中的表达式。

1. 为“language”路径打开表达式计算器 </>。
1. 复制表达式。 关闭“服务” 窗口。
1. 转到技能 #1 的技能设置，然后为“languageCode”输入打开表达式计算器 </>。
1. 将新值“/document/language”粘贴到“表达式”框中，然后单击“求值”。
1. 它应该显示正确输入“en”。 单击“应用”以更新表达式。
1. 在右侧的“技能详细信息”窗格中单击“保存”。
1. 在会话的窗口菜单中单击“运行”。 这将启动对使用文档的技能组的另一次执行。 

调试会话执行完毕后，单击“错误/警告”选项卡，它将显示标记为“Enrichment.NerSkillV2.#1”的错误已消失。 但是，还有两个警告，提示服务无法将 organizations 和 locations 的输出字段映射到搜索索引。 缺失的值为“/document/merged_content/organizations”和“/document/merged_content/locations”。

## <a name="fix-missing-skill-output-values"></a>修复缺失的技能输出值

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="错误和警告":::

某个技能缺失输出值。 若要确定出现错误的技能，请转到“扩充数据结构”，查找值名称并查看其“原始源”。 在缺失 organizations 和 locations 值的情况下，它们是技能 #1 的输出。 为每个路径打开表达式计算器 </> 时，列出的表达式将分别显示为“/document/content/organizations”和“/document/content/locations”。

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="表达式计算器 organizations 实体":::

这些实体的输出为空，但其输出不应为空。 哪些输入生成此结果？

1. 转到“技能图”并选择技能 #1。
1. 在右侧的“技能详细信息”窗格中选择“执行”选项卡。
1. 为输入“text”打开表达式计算器 </>。

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="text 技能的输入":::

此输入的显示结果看起来不像文本输入。 它看起来像是被新行包围的图像。 缺少文本意味着无法确定任何实体。 查看技能组的层次结构时，系统显示内容首先由技能 #6 (OCR) 处理，然后传递给技能 #5 (Merge)。 

1. 在“技能图”中选择技能 #5 (Merge)。
1. 在右侧的“技能详细信息”窗格中选择“执行”选项卡，然后为输出“mergedText”打开表达式计算器 </>。

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Merge 技能的输出":::

此处的文本与图像配对。 查看表达式“/document/merged_content”时，可以看到技能 #1 的“organizations”和“locations”路径中的错误。 它不应使用“/document/content”，而是应该使用“/document/merged_content”作为“text”输入。

1. 复制“mergedText”输出的表达式并关闭“表达式计算器”窗口。
1. 在“技能图”中选择技能 #1。
1. 在右侧的“技能详细信息”窗格中选择“技能设置”选项卡。
1. 为“text”输入打开表达式计算器 </>。
1. 将新表达式粘贴到框中。 单击“求值”。
1. 此时应显示包含所添加文本的正确输入。 单击“应用”以更新技能设置。
1. 在右侧的“技能详细信息”窗格中单击“保存”。
1. 在会话的窗口菜单中单击“运行”。 这将启动对使用文档的技能组的另一次执行。

索引器运行完毕后，错误仍然存在。 返回技能 #1 并进行调查。 该技能的输入已从“content”更正为“merged_content”。 这些实体在技能中的输出是什么？

1. 选择“AI 扩充”选项卡。
1. 选择“技能图”并单击技能 #1。
1. 浏览“技能设置”以找到“输出”。
1. 为“organizations”实体打开表达式计算器 </>。

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="organizations 实体的输出":::

计算表达式的结果可给出正确的结果。 该技能正在为“organizations”实体确定正确的值。 但是，实体路径中的输出映射仍在引发错误。 在将技能中的输出路径与错误中的输出路径进行比较时，该技能将 /document/content 节点下的输出、组织和位置作为父级。 而输出字段映射期望结果在 /document/merged_content 节点下成为父级。 在上一步中，输入从“/document/content”更改为“/document/merged_content”。 需要更改技能设置中的上下文，以确保在正确的上下文中生成输出。

1. 选择“AI 扩充”选项卡。
1. 选择“技能图”并单击技能 #1。
1. 浏览“技能设置”以找到“上下文”。
1. 双击“上下文”设置，然后将其编辑为读取“/document/merged_content”。
1. 在右侧的“技能详细信息”窗格中单击“保存”。
1. 在会话的窗口菜单中单击“运行”。 这将启动对使用文档的技能组的另一次执行。

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="技能设置中的上下文更正":::

所有错误均已解决。

## <a name="commit-changes-to-the-skillset"></a>提交对技能组的更改

启动调试会话后，搜索服务创建了技能组的副本。 这样做是为了保护搜索服务上的原始技能组。 现在，你已完成对技能组的调试，接下来可以提交修复（覆盖原始技能组）。 

或者，如果你尚未准备好提交更改，则可保存调试会话，稍后再重新打开它。

1. 在“调试会话”主菜单中，单击“提交更改”。
1. 单击“确定”以确认你希望更新技能组。
1. 关闭调试会话，然后选择“索引器”选项卡。
1. 打开“clinical-trials-idxr”。
1. 单击“重置”。
1. 单击 **“运行”** 。 单击“确定”以确认。

索引器运行完毕后，在“执行历史记录”选项卡中，最近一次运行的时间戳旁边应该有一个绿色复选标记和“成功”字样。若要确保已应用更改，请执行以下操作：

1. 在“搜索概述”页中，选择“索引”选项卡。
1. 打开“clinical-trials”索引，然后在“搜索资源管理器”选项卡中输入查询字符串 `$select=metadata_storage_path, organizations, locations&$count=true` 以返回特定文档的字段（由唯一 `metadata_storage_path` 字段标识）。
1. 单击“搜索”。 

结果应显示 organizations 和 locations 现在已填充预期值。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

本教程涉及技能组定义和处理的各个方面。 若要详细了解概念和工作流，请参阅以下文章：

+ [如何将技能组输出字段映射到搜索索引中的字段](cognitive-search-output-field-mapping.md)

+ [Azure 认知搜索中的技能组](cognitive-search-working-with-skillsets.md)

+ [如何为增量扩充配置缓存](cognitive-search-incremental-indexing-conceptual.md)