---
title: 将笔记本发布到 Azure Cosmos DB 笔记本库
description: 了解如何从公共库下载笔记本，对其进行编辑，并将其发布到库。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: c7c910c7162bde2209b07eb4f1d533328bae08c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693026"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>将笔记本发布到 Azure Cosmos DB 笔记本库
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 内置 Jupyter 笔记本会直接集成到 Azure 门户中的 Azure Cosmos DB 帐户。 使用这些笔记本，可以从 Azure 门户分析和可视化数据。 适用于 Azure Cosmos DB 的内置笔记本目前在29个 [区域](#supported-regions)提供。 若要使用笔记本，请 [创建新的 Cosmos 帐户](#create-a-new-cosmos-account) ，或在其中一个区域中的 [现有帐户上启用笔记本](#enable-notebooks-in-an-existing-cosmos-account) 。

Azure 门户中的笔记本环境包含 Azure Cosmos DB 团队发布的一些示例。 它还具有一个公共库，可在其中发布和共享自己的笔记本。 将笔记本发布到库后，可供所有 Azure Cosmos DB 用户查看和使用。 本文介绍如何使用公共库中的笔记本，并将笔记本发布到库。

## <a name="download-a-notebook-from-the-gallery"></a>从库中下载笔记本

使用以下步骤可从库中查看笔记本并将其下载到自己的笔记本工作区：

1. 登录到 [Azure 门户](https://portal.azure.com/) 并导航到通过笔记本环境启用的 Azure Cosmos DB 帐户。

1. 导航到 **数据资源管理器**  >  **笔记本**  >  **库**""  >  **公共库**"选项卡。

1. 查看或发布库中的笔记本之前，请接受 [行为](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  准则。 每个用户按订阅级别记录行为准则。 切换到其他订阅时，必须在访问库之前再次接受它。 若要接受行为准则，请选中该复选框，然后 **继续**：

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="导航到 &quot;笔记本&quot; 公共库并接受行为准则。":::

1. 接下来，你可以将特定笔记本添加到 "收藏夹" 选项卡，或下载它。 下载笔记本时，会将其复制到 "笔记本" 工作区，你可以在其中运行或编辑它。

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="将笔记本从库下载到工作区。":::

## <a name="publish-a-notebook-to-the-gallery"></a>将笔记本发布到库

构建自己的笔记本或编辑现有笔记本以适应不同方案时，可能需要将其发布到库。 将笔记本发布到库后，其他用户就可以访问它。 可以 [浏览笔记本示例库](https://cosmos.azure.com/gallery.html) 以查看当前可用的笔记本。

使用以下步骤发布笔记本：

1. 登录到 [Azure 门户](https://portal.azure.com/) 并导航到通过笔记本环境启用的 Azure Cosmos DB 帐户。

1. 导航到 "**数据资源管理器** 笔记本" "笔记本"  >    >  选项卡。

1. 中转到要发布的笔记本。 选择命令栏中的 " **保存** " 按钮，然后选择 " **发布到库**"：

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="选择要发布到库中的笔记本。":::

   通过选择 " **发布到库** " 选项，还可以选择 **...** 笔记本名称旁边的按钮：

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="选择要发布到库中的笔记本。":::

1. 填写 " **发布到库** " 窗体，其中包含以下详细信息：

   * **名称：** 用于识别笔记本的友好名称。
   * **说明：**  笔记本功能的简短说明。
   * **标记：** 标记为可选，用于在通过关键字搜索时筛选结果。
   * **封面图像：** 发布笔记本时封面上使用的图像。 你可以选择以下选项之一：

     * **自定义映像** -可以从计算机上传图像。 选择具有纵横比的图像文件256x144。
     * **URL** -提供映像所在的可公开访问的 url。
     * **拍摄屏幕截图** -自动拍摄并上传到预览版的已打开笔记本的屏幕截图。
     * 使用具有显示输出的第一个单元的 **第一个显示输出**。 仅显示 markdown/text 的单元不计为显示输出。

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="填写 &quot;发布到库&quot; 窗体。":::

   > [!NOTE]
   > 如果你使用的是 " **发布到库** " 选项 **...** 按钮，则不会看到所有 **封面图像** 选项。 这是因为笔记本可能未处于打开状态，并且 Azure Cosmos DB 无权获取屏幕截图或访问第一个显示输出。

1. 验证预览外观是否正常，然后选择 " **发布**"。 发布后，此笔记本将显示在 Azure Cosmos DB 笔记本 "公共库中。 在发布之前，请确保已删除所有敏感数据或输出。 在发布之前，会扫描笔记本内容是否有任何违反 Microsoft 策略的情况。 此过程通常需要几秒钟才能完成。 如果发现任何冲突，则会在 "通知" 选项卡中显示一条消息。如果你的笔记本发现任何冲突，你将不会发布该笔记本，你会删除被破坏的文本并再次发布。

   > [!NOTE]
   > 将笔记本发布到公共库后，所有 Azure Cosmos DB 用户都可以查看它们。 访问不限于每个订阅或按组织级别。

1. 将笔记本发布到库后，可以在 " **我的已发布工作** " 选项卡中查看它。你还可以从公共库中删除或删除已发布的笔记本。

1. 你还可以报告违反行为准则的笔记本。 如果发现冲突，Cosmos DB 会自动从库中删除笔记本。 如果删除笔记本，则用户可以在 " **我的已发布工作** " 选项卡下看到已删除的注释。 若要报告笔记本，请前往 **数据资源管理器**  >  **笔记本**  >  **库**""  >  **公用库**"选项卡。打开要报告的笔记本，将鼠标悬停在右上角的"**帮助**"按钮上，并选择"**报告滥用** 情况 "。

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="报告违反行为准则的笔记本。":::

## <a name="next-steps"></a>后续步骤

* 了解[Azure Cosmos DB Jupyter 笔记本](cosmosdb-jupyter-notebooks.md)的优点
* [使用 Python 笔记本功能和命令](use-python-notebook-features-and-commands.md)
* [使用 C# 笔记本功能和命令](use-csharp-notebook-features-and-commands.md)
* [从 GitHub 存储库导入笔记本](import-github-notebooks.md)
