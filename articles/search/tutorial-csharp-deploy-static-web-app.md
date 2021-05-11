---
title: .NET 教程：部署已启用搜索的网站
titleSuffix: Azure Cognitive Search
description: 将包含 .NET API 的已启用搜索的网站部署到 Azure 静态 Web 应用。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.devlang: .NET
ms.openlocfilehash: 800d89f22d54b185111f7385ebe07771f228d8cf
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107981937"
---
# <a name="3---deploy-the-search-enabled-net-website"></a>3 - 部署已启用搜索的 .NET 网站

将启用搜索的网站部署为 Azure 静态 Web 应用。 此部署包括 React 应用和 Function 应用。  

静态 Web 应用使用示例存储库的分支从 GitHub 拉取信息和文件，以便进行部署。  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>在 Visual Studio Code 中创建静态 Web 应用

1. 从活动栏中选择 **Azure**，然后从侧边栏中选择 **静态 Web 应用**。 
1. 右键单击订阅名称，然后选择 **创建静态 Web 应用（高级）** 。    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="右键单击订阅名称，然后选择 **创建静态 Web 应用（高级）**。":::

1. 按照提示提供以下信息：

    |Prompt|Enter|
    |--|--|
    |要如何创建静态 Web 应用？|使用现有的 GitHub 存储库|
    |选择组织|选择您 _自己_ 的 GitHub 别名作为组织。|
    |选择存储库|从列表中选择“azure-search-dotnet-samples”。 |
    |选择存储库的分支|从列表中选择 **主设备**。 |
    |输入新静态 Web 应用的名称。|创建资源的唯一名称。 例如，您可以在存储库名称之前预置名称，如`joansmith-azure-search-dotnet-samples`。 |
    |选择新资源的资源组。|使用为本教程创建的资源组。|
    |选择“生成预设”以配置默认项目结构。|选择“自定义”|
    |选择应用程序代码的位置|`search-website`|
    |选择 Azure Functions 代码的位置|`search-website/api`|
    |输入构建输出的路径...|build|
    |选择新资源的位置。|选择附近的区域。|

1. 创建资源后，从“通知”中选择 **打开 GitHub 中的操作**。 这会打开一个浏览器窗口，该窗口指向分支存储库。 

    操作列表指示已成功将 Web 应用（客户端和函数）推送到 Azure 静态 Web 应用。 

    等到构建和部署完成，再继续。 这可能需要一、两分钟时间才能完成。

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>在 Visual Studio Code 中获取认知搜索查询密钥

1. 在 Visual Studio Code 中，开启[活动栏](https://code.visualstudio.com/docs/getstarted/userinterface)，然后选择 Azure 图标。 

1. 在侧边栏中，在 **Azure：认知搜索** 区域下选择 Azure 订阅，右键单击搜索资源，然后选择 **复制查询密钥**。 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="在侧边栏中，在 **Azure：认知搜索** 区域下选择 Azure 订阅，右键单击搜索资源，然后选择 **复制查询密钥**。":::

1. 请保留此查询密钥，下一部分将需要使用它。 查询密钥能够查询您的索引。 

## <a name="add-configuration-settings-in-azure-portal"></a>在 Azure 门户中添加配置设置

当搜索密码在设置中之前，Azure Function 应用不会返回搜索数据。 

1. 在活动栏中选择“Azure”。 
1. 右键单击你的静态 Web 应用资源，然后选择“在门户中打开”。

    :::image type="content" source="media/tutorial-javascript-static-web-app/open-static-web-app-in-azure-portal.png" alt-text="右键单击你的 JavaScript 静态 Web 应用资源，然后选择“在门户中打开”。":::

1. 选择“配置”，然后选择“+ 添加” 。

    :::image type="content" source="media/tutorial-javascript-static-web-app/add-new-application-setting-to-static-web-app-in-portal.png" alt-text="对于你的 JavaScript 应用，选择“配置”，然后选择“添加”。":::

1. 添加以下每个设置：

    |设置|搜索资源值|
    |--|--|
    |SearchApiKey|搜索查询密钥|
    |SearchServiceName|搜索资源名称|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

    对于筛选集合，Azure 认知搜索需要不同于筛选字符串所使用的语法。 在字段名后添加一个 `*`，表示该字段的类型为 `Collection(Edm.String)`。 这允许 Azure Function 向查询正确添加筛选器。

1. 选择“保存”，保存这些设置。 

    :::image type="content" source="media/tutorial-javascript-static-web-app/save-new-application-setting-to-static-web-app-in-portal.png" alt-text="选择“保存”以保存 JavaScript 应用的设置。":::

1. 返回到 VS Code。 
1. 刷新静态 Web 应用以查看静态 Web 应用的应用程序设置。 

    :::image type="content" source="media/tutorial-javascript-static-web-app/visual-studio-code-extension-fresh-resource.png" alt-text="刷新 JavaScript 静态 Web 应用以查看静态 Web 应用的应用程序设置。":::

## <a name="use-search-in-your-static-web-app"></a>在静态 Web 应用中使用搜索

1. 在 Visual Studio Code 中，开启[活动栏](https://code.visualstudio.com/docs/getstarted/userinterface)，然后选择 Azure 图标。
1. 在侧边栏中，**右键单击 `Static web apps` 区域下的“Azure 订阅”** ，然后找到为本教程创建的静态 Web 应用。
1. 右键单击静态 Web 应用名称，然后选择 **浏览网站**。
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="右键单击静态 Web 应用名称，然后选择 **浏览网站**。":::    

1. 在弹出对话框中选择 **开启**。
1. 在网站搜索栏中 _缓慢_ 输入搜索查询，如 `code`，以便建议功能建议书籍标题。 选择一个建议，或继续输入自己的查询。 完成搜索查询后，按 enter。 
1. 查看结果，然后选择其中一个书籍以查看更多详细信息。 

## <a name="clean-up-resources"></a>清理资源

若要清理在本教程中创建的资源，请删除资源组。

1. 在 Visual Studio Code 中，开启[活动栏](https://code.visualstudio.com/docs/getstarted/userinterface)，然后选择 Azure 图标。 

1. 在侧边栏中，**右键单击 `Resource Groups` 区域下的“Azure 订阅”** ，然后找到为本教程创建的资源组。
1. 右键单击资源组名称，然后选择 **删除**。
    这会同时删除搜索和静态 Web 应用资源。
1. 如果不再需要该示例的 GitHub 分支，请记得在 GitHub 上将其删除。 转至分支的 **设置**，然后删除该分支。 


## <a name="next-steps"></a>后续步骤

* [了解启用搜索的网站的搜索集成](tutorial-csharp-search-query-integration.md)
