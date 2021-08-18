---
title: 在 Azure Cosmos DB 帐户中启用笔记本（预览版）
description: 利用 Azure Cosmos DB 的内置笔记本，你可以从门户内分析并可视化数据。 本文介绍了如何为 Cosmos 帐户启用此功能。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: f1309ac31c8669ad678b96a4d99d32daf8f89815
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112234208"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>为 Azure Cosmos DB 帐户中启用笔记本（预览版）
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB 的内置笔记本当前在 [29 个区域](#supported-regions)中提供。 若要使用笔记本，请在其中某一个区域中[创建新 Cosmos 帐户](#create-a-new-cosmos-account)或[在现有帐户上启用笔记本](#enable-notebooks-in-an-existing-cosmos-account)。 

使用 Azure Cosmos DB 中的内置 Jupyter 笔记本可以从 Azure 门户分析和可视化数据。 本文介绍了如何为 Azure Cosmos DB 帐户启用此功能。

## <a name="create-a-new-cosmos-account"></a>创建新 Cosmos 帐户
从 2021 年 2 月 10 日开始，在某一个[受支持的区域](#supported-regions)中创建的新 Azure Cosmos 帐户将会自动启用笔记本。 启用笔记本不需要进行其他配置。 请使用以下说明来创建新帐户：
1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择“创建资源” > “数据库” > “Azure Cosmos DB”。
1. 输入该帐户的基本设置。

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Azure Cosmos DB 的“新建帐户”页面":::

1. 选择“查看 + 创建”。 可以跳过“网络”和“标记”选项 。 
1. 检查帐户设置，然后选择“创建”。 创建帐户需要几分钟时间。 等待门户页显示“你的部署已完成”消息。

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Azure 门户“通知”窗格":::

1. 选择“转到资源”，转到 Azure Cosmos DB 帐户页。

   :::image type="content" source="includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Azure Cosmos DB 帐户页面":::

1. 导航到“数据资源管理器”窗格。 现在你应该会看到笔记本工作区。

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="新 Azure Cosmos DB 笔记本工作区":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>在现有的 Cosmos 帐户中启用笔记本

也可以在现有的帐户上启用该笔记本。 每个帐户只需执行一次此步骤。

1. 在 Cosmos 帐户中导航到“数据资源管理器”窗格。
1. 选择“启用笔记本”。

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="在数据资源管理器中创建新笔记本工作区":::

1. 这样将会提示你创建新的笔记本工作区。 选择“完成安装”。
1. 你的帐户现在已经能够使用笔记本了！

## <a name="create-and-run-your-first-notebook"></a>创建并运行你的第一个笔记本

若要验证是否可以使用笔记本，请在“示例笔记本”下选择一个笔记本。 这样就会将该笔记本的副本保存到你的工作区并将它打开。

在本示例中，我们将使用“GettingStarted.ipynb”。

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="查看 GettingStarted.ipynb 笔记本":::

运行笔记本：
1. 选择包含 Python 代码的第一个代码单元。
1. 选择“运行”以运行该单元。 也可以使用 Shift + Enter 来运行该单元。
1. 刷新资源窗格，以查看已创建的数据库和容器。

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="运行入门笔记本":::

也可以选择“新建笔记本”来创建新笔记本，或通过从“我的笔记本”菜单中选择“上传文件”来上传现有的笔记本文件 (.ipynb)  。 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="创建或上传新笔记本":::

## <a name="supported-regions"></a>支持的区域
Azure Cosmos DB 的内置笔记本当前在 29 个 Azure 区域提供。 在这些区域中创建的新 Azure Cosmos 帐户将会自动启用笔记本。 你的帐户可免费使用笔记本。 

- 澳大利亚中部
- 澳大利亚中部 2
- 澳大利亚东部
- 澳大利亚东南部
- 巴西南部
- 加拿大中部
- 加拿大东部
- 印度中部
- 美国中部
- 美国东部
- 美国东部 2
- 法国中部
- 法国南部
- 德国北部
- 德国中西部
- 日本西部
- 韩国南部
- 美国中北部
- 北欧
- 美国中南部
- 东南亚
- 瑞士北部
- 阿联酋中部
- 英国南部
- 英国西部
- 美国中西部
- 西欧
- 印度西部
- 美国西部 2

## <a name="next-steps"></a>后续步骤

* 了解 [Azure Cosmos DB Jupyter Notebook](cosmosdb-jupyter-notebooks.md) 的优点
* [浏览笔记本示例库](https://cosmos.azure.com/gallery.html)
* [将笔记本发布到 Azure Cosmos DB 笔记本库](publish-notebook-gallery.md)
* [使用 Python 笔记本功能和命令](use-python-notebook-features-and-commands.md)
* [使用 C# 笔记本功能和命令](use-csharp-notebook-features-and-commands.md)
* [从 GitHub 存储库导入笔记本](import-github-notebooks.md)
