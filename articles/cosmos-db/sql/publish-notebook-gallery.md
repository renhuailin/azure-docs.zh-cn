---
title: 将笔记本发布到 Azure Cosmos DB 笔记本库
description: 了解如何从公共库下载笔记本、对笔记本进行编辑，以及如何将自己的笔记本发布到库。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 016bd8c73e12297988f3a2d47cde0163821d2dcd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113285"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>将笔记本发布到 Azure Cosmos DB 笔记本库
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB 内置 Jupyter Notebook 直接集成到 Azure 门户中的 Azure Cosmos DB 帐户。 使用这些笔记本，可以从 Azure 门户分析和可视化你的数据。 Azure Cosmos DB 的内置笔记本当前在[许多区域](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)提供。 若要使用笔记本，请在其中某个区域中[创建新 Cosmos 帐户](create-cosmosdb-resources-portal.md)或[在现有帐户上启用笔记本](enable-notebooks.md)。

Azure 门户中的笔记本环境包含 Azure Cosmos DB 团队发布的一些示例。 它还有一个公共库，你可以在其中发布和共享自己的笔记本。 笔记本发布到库后，可供所有 Azure Cosmos DB 用户查看和使用。 在本文中，你将了解如何使用公共库中的笔记本，以及如何将你的笔记本发布到库。

## <a name="download-a-notebook-from-the-gallery"></a>从库中下载笔记本

使用以下步骤查看库中的笔记本并将其下载到你自己的笔记本工作区：

1. 登录到 [Azure 门户](https://portal.azure.com/)并导航到启用了笔记本环境的 Azure Cosmos DB 帐户。

1. 导航到“数据资源管理器” > “笔记本” > “库” > “公共库”选项卡。   

1. 在库中查看笔记本或将笔记本发布到库中之前，请接受[行为准则](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)。 行为准则是在订阅级别按用户记录的。 切换到其他订阅时，在访问库之前必须再次接受行为准则。 若要接受行为准则，请选中相应的复选框，然后选择“继续”：

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="导航到笔记本公共库并接受行为准则。":::

1. 接下来，你可以将特定笔记本添加到“收藏夹”选项卡，或者下载它。 当你下载笔记本时，系统会将其复制到你的笔记本工作区，你可以在其中运行或编辑它。

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="将笔记本从库下载到你的工作区。":::

## <a name="publish-a-notebook-to-the-gallery"></a>将笔记本发布到库

构建你自己的笔记本或根据不同方案编辑现有的笔记本时，可能需要将其发布到库。 将笔记本发布到库后，其他用户就可以访问它。 你可以[浏览笔记本示例库](https://cosmos.azure.com/gallery.html)以查看当前可用的笔记本。

按照以下步骤发布笔记本：

1. 登录到 [Azure 门户](https://portal.azure.com/)并导航到启用了笔记本环境的 Azure Cosmos DB 帐户。

1. 导航到“数据资源管理器” > “笔记本” > “我的笔记本”选项卡。  

1. 转到你要发布的笔记本。 选择命令栏中的“保存”按钮，然后选择“发布到库”：

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="选择要发布到库中的笔记本。":::

   你还可以通过选择笔记本名称旁边的“…”按钮 来找到“发布到库”选项：

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="另一种用来选择发布到库的笔记本的方法。":::

1. 填写“发布到库”表单，其中包含以下详细信息：

   * 名称：用于标识笔记本的易记名称。
   * 说明：笔记本功能的简短说明。
   * 标记：标记是可选的，用于在通过关键字进行搜索时筛选结果。
   * 封面图像：发布笔记本时封面上使用的图像。 你可以选择以下选项之一：
   * 自定义图像 - 可以从计算机上传图像。 选择纵横比为 256x144 的一个图像文件。
   * URL - 提供图像所在的可供公开访问的 URL。
   * 拍摄屏幕截图 - 自动拍摄已打开笔记本的屏幕截图并将其上传到预览版。
   * 使用第一个显示输出 - 具有显示输出的第一个单元格的输出。 仅显示 Markdown/文本的单元格不计为显示输出。

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="填写“发布到库”表单。":::

   > [!NOTE]
   > 如果你通过笔记本名称旁边的“…”按钮使用“发布到库”选项， 则不会看到所有封面图像选项。 这是因为笔记本可能未打开，并且 Azure Cosmos DB 无权拍摄屏幕截图或访问第一个显示输出。

1. 验证预览外观是否正常，然后选择“发布”。 发布后，此笔记本会显示在 Azure Cosmos DB 笔记本公共库中。 在发布之前，请确保已删除所有敏感数据或输出。 在发布之前，系统会扫描笔记本内容是否有任何违反 Microsoft 政策的情况。 完成此过程通常需要几秒钟。 如果发现任何违规情况，则通知选项卡中会显示一条消息。如果发现你的笔记本有任何违规情况，则不会发布你的笔记本。你需要删除违规的文本，并再次发布它。

   > [!NOTE]
   > 将笔记本发布到公共库后，所有 Azure Cosmos DB 用户都可以查看它们。 访问不限于每个订阅或组织级别。

1. 将笔记本发布到库后，你可以在“我的已发布作品”选项卡中看到它。你还可以移除或删除公共库中的已发布笔记本。

1. 你还可以报告违反了行为准则的笔记本。 如果发现违规，则 Cosmos DB 会自动从库中移除笔记本。 如果某个笔记本被移除，则用户可以在“我的已发布作品”选项卡下看到该笔记本带有“已移除”注释。 若要报告某个笔记本，请转到“数据资源管理器” > “笔记本” > “库” > “公共库”选项卡。   打开你要报告的笔记本，将鼠标指针悬停在右上角的“帮助”按钮上，然后选择“报告滥用行为”。

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="报告违反了行为准则的笔记本。":::

## <a name="next-steps"></a>后续步骤

* 了解 [Azure Cosmos DB Jupyter Notebook](../cosmosdb-jupyter-notebooks.md) 的优势
* [使用 Python 笔记本功能和命令](use-python-notebook-features-and-commands.md)
* [使用 C# 笔记本功能和命令](use-csharp-notebook-features-and-commands.md)
* [从 GitHub 存储库导入笔记本](import-github-notebooks.md)
