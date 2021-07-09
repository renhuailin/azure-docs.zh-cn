---
title: 疑难解答 - QnA Maker
description: 关于 QnA Maker 服务的最常见问题解答特选列表有助于快速利用此服务，并获得更理想的结果。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: d9050b0b414ef6ca49f9376e20498b14a8952480
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072570"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker 疑难解答

关于 QnA Maker 服务的最常见问题解答特选列表有助于快速利用此服务，并获得更理想的结果。

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>管理预测

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

<details>
<summary><b>如何提高查询预测的吞吐量性能？</b></summary>

答：吞吐量性能问题表明你需要同时扩展应用服务和认知搜索。 请考虑将副本添加到认知搜索以提高性能。

了解有关[定价层](Concepts/azure-resources.md)的更多信息。
</details>

<details>
<summary><b>如何获取 QnAMaker 服务终结点</b></summary>

答：在联系 QnAMaker 客户支持部门或 UserVoice 时，QnAMaker 服务终结点对调试工作非常有用。 此终结点是采用以下格式的 URL：`https://your-resource-name.azurewebsites.net`。

1. 在 [Azure 门户](https://portal.azure.com)中转到 QnAMaker 服务（资源组）

    ![Azure 门户中的 QnAMaker Azure 资源组](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 选择与 QnA Maker 资源关联的应用服务。 通常，名称是相同的。

     ![选择“QnAMaker 应用服务”](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 在“概述”部分可以找到终结点 URL

    ![QnAMaker 终结点](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

<details>
<summary><b>如何提高查询预测的吞吐量性能？</b></summary>

答：吞吐量性能问题表明你需要扩展认知搜索。 请考虑将副本添加到认知搜索以提高性能。

了解有关[定价层](Concepts/azure-resources.md)的更多信息。
</details>

---

## <a name="manage-the-knowledge-base"></a>管理知识库

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

<details>
<summary><b>我不小心删除了一部分 QnA Maker，我该怎么办？</b></summary>

答：不要删除与 QnA Maker 资源一起创建的任何 Azure 服务，例如搜索或 Web 应用。 这些是 QnA Maker 工作所必需的，一旦删除某项 Azure 服务，QnA Maker 将停止正常工作。

所有删除操作都是永久性的，包括问题和答案对、文件、URL、自定义问题和答案、知识库或 Azure 资源。 在删除知识库的任何部分之前，请务必从“设置”页导出知识库。

</details>

<details>
<summary><b>为什么我的 URL/文件没有提取问答对？</b></summary>

答：QnA Maker 可能无法从有效的常见问题解答 URL 中自动提取某些问答 (QnA) 内容。 在这种情况下，可以将 QnA 内容粘贴到 .txt 文件中，并查看该工具是否可以引入它。 或者，也可以通过 [QnA Maker 门户](https://qnamaker.ai)以编辑方式向知识库添加内容。

</details>

<details>
<summary><b>我能创建多大的知识库？</b></summary>

答：知识库的大小取决于在创建 QnA Maker 服务时选择的 Azure 搜索的 SKU。 请参阅[此处](./concepts/azure-resources.md)了解详细信息。

</details>

<details>
<summary><b>当我尝试创建新知识库时，为什么在下拉列表中看不到任何内容？</b></summary>

答：你尚未在 Azure 中创建任何 QnA Maker 服务。 请阅读[此文](./How-To/set-up-qnamaker-service-azure.md)了解如何执行该操作。

</details>

<details>
<summary><b>如何与他人共享知识库？</b></summary>

答：共享 QnA Maker 服务级别的工作，即将共享服务中的所有知识库。 请参阅[此处](./index.yml)了解如何在知识库上进行协作。

</details>

<details>
<summary><b>是否可以与不在同一个 AAD 租户中的参与者共享知识库，以修改知识库？</b></summary>

答：共享基于 Azure 基于角色的访问控制 (Azure RBAC)。 如果可与其他用户共享 Azure 中的任何资源，则也可以共享 QnA Maker。

</details>

<details>
<summary><b>如果应用服务计划有 5 个 QnAMaker 知识库，能否向 5 位不同的用户分配读/写权限，使每位用户只能访问 1 个 QnAMaker 知识库？</b></summary>

答：可以共享整个 QnAMaker 服务，但不能共享个体知识库。

</details>

<details>
<summary><b>如果找不到良好匹配，如何更改默认消息？</b></summary>

答：默认消息是应用服务中设置的一部分。
- 在 Azure 门户中转到自己的应用服务资源

![qnamaker 应用服务](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- 单击“设置”选项

![qnamaker 应用服务设置](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- 更改 **DefaultAnswer** 设置的值
- 重启应用服务

![qnamaker 应用服务重启](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>为什么我的 SharePoint 链接没有被提取？</b></summary>

答：有关详细信息，请参阅[数据源位置](./concepts/data-sources-and-content.md#data-source-locations)。

</details>

<details>
<summary><b>我对知识库所做的更新未反映在发布中，为什么？</b></summary>

答：每个编辑操作（无论在表更新、测试还是在设置中）需要先保存才能发布。 每次编辑操作后，请确保单击“保存并定型”按钮。

</details>

<details>
<summary><b>知识库是否支持丰富的数据或多媒体？</b></summary>

答案：

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>文件和 URL 的多媒体自动提取

* URLS - HTML-to-Markdown 转换功能有限。
* 文件 - 不受支持

#### <a name="answer-text-in-markdown"></a>Markdown 中的回答文本
一旦 QnA 对位于知识库中，即可编辑回答的 Markdown 文本以包含指向公用 URL 上可用媒体的链接。


</details>

<details>
<summary><b>QnA Maker 是否支持非英语语言？</b></summary>

答：查看有关[支持的语言](./overview/language-support.md)的更多详细信息。

如果你有多种语言的内容，请务必为每种语言创建单独的服务。

</details>

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

<details>
<summary><b>为什么我的 URL/文件没有提取问答对？</b></summary>

答：QnA Maker 可能无法从有效的常见问题解答 URL 中自动提取某些问答 (QnA) 内容。 在这种情况下，可以将 QnA 内容粘贴到 .txt 文件中，并查看该工具是否可以引入它。 或者，也可以通过 [QnA Maker 门户](https://qnamaker.ai)以编辑方式向知识库添加内容。

</details>

<details>
<summary><b>我能创建多大的知识库？</b></summary>

答：知识库的大小取决于在创建 QnA Maker 服务时选择的 Azure 搜索的 SKU。 请参阅[此处](./concepts/azure-resources.md)了解详细信息。

</details>

<details>
<summary><b>当我尝试创建新知识库时，为什么在下拉列表中看不到任何内容？</b></summary>

答：你尚未在 Azure 中创建任何 QnA Maker 服务。 请阅读[此文](./How-To/set-up-qnamaker-service-azure.md)了解如何执行该操作。

</details>

<details>
<summary><b>如何与他人共享知识库？</b></summary>

答：共享 QnA Maker 服务级别的工作，即将共享服务中的所有知识库。 请参阅[此处](./index.yml)了解如何在知识库上进行协作。

</details>

<details>
<summary><b>是否可以与不在同一个 Azure Active Directory 租户中的参与者共享知识库，以修改知识库？</b></summary>

答：共享基于 Azure 基于角色的访问控制 (Azure RBAC)。 如果可与其他用户共享 Azure 中的任何资源，则也可以共享 QnA Maker。

</details>

<details>
<summary><b>是否可以向 5 个不同的用户分配读/写权限，使得其中每个用户只能访问 1 个 QnAMaker 知识库？</b></summary>

答：可以共享整个 QnAMaker 服务，但不能共享个体知识库。

</details>

<details>
<summary><b>为什么我的 SharePoint 链接没有被提取？</b></summary>

答：有关详细信息，请参阅[数据源位置](./concepts/data-sources-and-content.md#data-source-locations)。

</details>

<details>
<summary><b>我对知识库所做的更新未反映在发布中，为什么？</b></summary>

答：每个编辑操作（无论在表更新、测试还是在设置中）需要先保存才能发布。 每次编辑操作后，请确保单击“保存并定型”按钮。

</details>

<details>
<summary><b>知识库是否支持丰富的数据或多媒体？</b></summary>

答案：

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>文件和 URL 的多媒体自动提取

* URLS - HTML-to-Markdown 转换功能有限。
* 文件 - 不受支持

#### <a name="answer-text-in-markdown"></a>Markdown 中的回答文本
一旦 QnA 对位于知识库中，即可编辑回答的 Markdown 文本以包含指向公用 URL 上可用媒体的链接。


</details>

<details>
<summary><b>QnA Maker 是否支持非英语语言？</b></summary>

答：查看有关[支持的语言](./overview/language-support.md)的更多详细信息。

如果你有多种语言的内容，请务必为每种语言创建单独的服务。

</details>

---

## <a name="manage-service"></a>管理服务

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

<details>
<summary><b>我何时应该重启应用服务？</b></summary>

答：当警告图标位于“用户设置”[页](https://www.qnamaker.ai/UserSettings)上“终结点密钥”表中知识库版本值旁边时，请刷新应用服务。

</details>

<details>
<summary><b>我删除了现有搜索服务，该如何解决此问题？</b></summary>

答：如果删除 Azure 认知搜索索引，则该操作是最终操作，无法恢复索引。

</details>

<details>
<summary><b>我删除了搜索服务中的 `testkb` 索引，该如何解决此问题？</b></summary>

答：如果你删除了搜索服务中的 `testkb` 索引，可从上次发布的知识库中还原数据。 请使用 GitHub 上提供的恢复工具 [RestoreTestKBIndex](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/RestoreTestKBFromProd)。 

</details>

<details>
<summary>https://www.qnamaker.ai 我收到如下错误：“请检查 QnA Maker 应用服务的 CORS 设置是否允许 <b> 或是否存在组织特定的网络限制。”该如何解决此问题？</b></summary>

答：在“应用服务”边栏选项卡的“API”部分，将“CORS 设置”更新为 * 或“https://www.qnamaker.ai ”。 如果这没有解决问题，请检查是否有组织特定的限制。

</details>

<details>
<summary><b>我应该何时刷新终结点密钥？</b></summary>

答：如果怀疑终结点密钥已被泄漏，请刷新终结点密钥。

</details>

<details>
<summary><b>是否可以将同一个 Azure 认知搜索资源用于使用多种语言的知识库？</b></summary>

答：若要使用多种语言和多个知识库，用户必须为每种语言创建一个 QnA Maker 资源。 这会为每种语言创建单独的 Azure 搜索服务。 在单个 Azure 搜索服务中混合使用不同语言的知识库会导致结果相关性下降。

</details>

<details>
<summary><b>如何更改 QnA Maker 使用的 Azure 认知搜索资源的名称？</b></summary>

答：Azure 认知搜索资源的名称是 QnA Maker 资源名称加上在末尾追加的一些随机字母。 这使得 QnA Maker 难以区分多个搜索资源。 请创建一个单独的搜索服务（以你喜欢的方式对其进行命名）并将其连接到你的 QnA 服务。 步骤类似于[升级 Azure 搜索](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)时需要执行的步骤。

</details>

<details>
<summary><b>当 QnA Maker 返回时`Runtime core is not initialized,`，我该如何修复它？</b></summary>

答：应用服务的磁盘空间可能已满。 修复磁盘空间的步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择 QnA Maker 的应用服务，然后停止该服务。
1. 在仍然使用 App 服务时，依次选择“开发工具”、“高级工具”和“开始”。 此时将打开一个新的浏览器窗口。
1. 选择“调试控制台”，再选择“CMD”以打开命令行工具。
1. 导航到“site/wwwroot/Data/QnAMaker/”目录。
1. 删除以 `rd` 名称开头的所有文件夹。

    不要删除以下内容：

    * KbIdToRankerMappings.txt 文件
    * EndpointSettings.json 文件
    * EndpointKeys 文件夹

1. 启动应用服务。
1. 访问知识库，以验证其现在是否正常工作。

</details>
<details>
<summary><b>为什么我的 Application Insights 不起作用？</b></summary>

答：请交叉检查并更新以下步骤来解决此问题：

1. 在“应用服务”->“设置组”->“配置部分”->“应用程序设置”->“名称”中，“UserAppInsightsKey”参数已正确配置，并设置为相应的应用程序见解“概述”选项卡（“检测密钥”）向导。 

1. 在“应用服务”->“设置组”->“应用程序见解”部分中，确保应用见解已启用，并将其连接到相应的应用程序见解资源。

</details>

<details>
<summary><b>我的 Application Insights 已启用，但为什么无法正常工作？</b></summary>

答：请按以下给定步骤进行操作： 

1.  如果其中已存在某个值，请通过重写将“APPINSIGHTS_INSTRUMENTATIONKEY”名称的值复制到“UserAppInsightsKey”名称。 

1.  如果“UserAppInsightsKey”密钥在应用设置中不存在，请使用该名称添加新密钥并复制值。

1.  然后进行保存，之后将自动重启应用服务。 这应该可以解决问题。 

</details>

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)


<details>
<summary><b>我删除了现有搜索服务，该如何解决此问题？</b></summary>

答：如果删除 Azure 认知搜索索引，则该操作是最终操作，无法恢复索引。

</details>

<details>
<summary><b>我删除了搜索服务中的 `testkbv2` 索引，该如何解决此问题？</b></summary>

答：如果你删除了搜索服务中的 `testkbv2` 索引，可从上次发布的知识库中还原数据。 请使用 GitHub 上提供的恢复工具 [RestoreTestKBIndex](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/RestoreTestKBFromProd)。 

</details>

<details>
<summary><b>是否可以将同一个 Azure 认知搜索资源用于使用多种语言的知识库？</b></summary>

答：若要使用多种语言和多个知识库，用户必须为每种语言创建一个 QnA Maker 资源。 这会为每种语言创建单独的 Azure 搜索服务。 在单个 Azure 搜索服务中混合使用不同语言的知识库会导致结果相关性下降。

</details>

<details>
<summary><b>如何更改 QnA Maker 使用的 Azure 认知搜索资源的名称？</b></summary>

答：Azure 认知搜索资源的名称是 QnA Maker 资源名称加上在末尾追加的一些随机字母。 这使得 QnA Maker 难以区分多个搜索资源。 请创建一个单独的搜索服务（以你喜欢的方式对其进行命名）并将其连接到你的 QnA 服务。 步骤类似于[升级 Azure 搜索](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)时需要执行的步骤。

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>与其他服务（包括机器人）集成

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

<details>
<summary><b>为了使用 QnA Maker，需要使用 Bot Framework 吗？</b></summary>

答：否，不需要将 [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) 与 QnA Maker 一起使用。 但是，QnA Maker 作为 [Azure 机器人服务](/azure/bot-service/)中的几个模板之一提供。 机器人服务通过 Microsoft Bot Framework 实现快速智能机器人开发，并且它在无服务器的环境中运行。

</details>

<details>
<summary><b>如何使用 QnA Maker 创建新的机器人？</b></summary>

答：请按照[本](./Quickstarts/create-publish-knowledge-base.md)文档中的说明使用 Azure 机器人服务创建你的机器人。

</details>

<details>
<summary><b>如何将其他知识库与现有的 Azure 机器人服务一起使用？</b></summary>

答：你需要了解有关知识库的下列信息：

* 知识库 ID。
* 发布后，“设置”页面上称为 `host` 的知识库的已发布终结点自定义子域名。
* 发布后，“设置”页面上的知识库的已发布终结点密钥。

有关此信息，请转到 Azure 门户中的机器人的应用服务。 在“设置”->“配置”->“应用程序设置”下，更改这些值。

知识库的终结点密钥在 ABS 服务中标记为 `QnAAuthkey`。

</details>

<details>
<summary><b>两个或多个客户端应用程序是否可以共享知识库？</b></summary>

答：是的，可以从任意数量的客户端查询知识库。 如果发现来自知识库的响应速度缓慢或超时，请考虑升级与知识库关联的应用服务的服务层。

</details>

<details>
<summary><b>如何将 QnA Maker 服务嵌入网站？</b></summary>

答：按照以下步骤将 QnA Maker 服务作为 Web 聊天控件嵌入网站：

1. 按照[此处](./Quickstarts/create-publish-knowledge-base.md)的说明创建常见问题解答机器人。
2. 按照[此处](/azure/bot-service/bot-service-channel-connect-webchat)的步骤启用 Web 聊天

</details>

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)


<details>
<summary><b>为了使用 QnA Maker，需要使用 Bot Framework 吗？</b></summary>

答：否，不需要将 [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) 与 QnA Maker 一起使用。 但是，QnA Maker 作为 [Azure 机器人服务](/azure/bot-service/)中的几个模板之一提供。 机器人服务通过 Microsoft Bot Framework 实现快速智能机器人开发，并且它在无服务器的环境中运行。

</details>

<details>
<summary><b>如何使用 QnA Maker 创建新的机器人？</b></summary>

答：请按照[本](./Quickstarts/create-publish-knowledge-base.md)文档中的说明使用 Azure 机器人服务创建你的机器人。

</details>

<details>
<summary><b>如何将其他知识库与现有的 Azure 机器人服务一起使用？</b></summary>

答：你需要了解有关知识库的下列信息：

* 知识库 ID。
* 发布后，“设置”页面上称为 `host` 的知识库的已发布终结点自定义子域名。
* 发布后，“设置”页面上的知识库的已发布终结点密钥。

有关此信息，请转到 Azure 门户中的机器人的应用服务。 在“设置”->“配置”->“应用程序设置”下，更改这些值。

知识库的终结点密钥在 ABS 服务中标记为 `QnAAuthkey`。

</details>

<details>
<summary><b>两个或多个客户端应用程序是否可以共享知识库？</b></summary>

答：是的，可以从任意数量的客户端查询知识库。 如果发现来自知识库的响应速度缓慢或超时，请考虑升级与知识库关联的应用服务的服务层。

</details>

<details>
<summary><b>如何将 QnA Maker 服务嵌入网站？</b></summary>

答：按照以下步骤将 QnA Maker 服务作为 Web 聊天控件嵌入网站：

1. 按照[此处](./Quickstarts/create-publish-knowledge-base.md)的说明创建常见问题解答机器人。
2. 按照[此处](/azure/bot-service/bot-service-channel-connect-webchat)的步骤启用 Web 聊天

---

## <a name="data-storage"></a>数据存储

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

<details>
<summary><b>存储哪些数据以及存储在何处？</b></summary>

答案：

创建 QnA Maker 服务时，你选择了 Azure 区域。 知识库和日志文件存储在此区域中。

</details>

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

<details>
<summary><b>存储哪些数据以及存储在何处？</b></summary>

答案：

创建 QnA Maker 服务时，你选择了 Azure 区域。 知识库和日志文件存储在此区域中。

</details>

---
