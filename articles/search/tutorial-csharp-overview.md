---
title: .NET 教程：搜索集成概述
titleSuffix: Azure Cognitive Search
description: 有关使用 .NET 将搜索添加到网站并部署到 Azure 静态 Web 应用的技术概述和设置。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.devlang: dotnet
ms.openlocfilehash: 8a73ab04ea782c248a8fab61fb375c01a2339d1c
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107981939"
---
# <a name="1---overview-of-adding-search-to-a-website-with-net"></a>1 - 使用 .NET 将搜索添加到网站的概述

本教程将构建一个网站来搜索书籍目录，然后将该网站部署到 Azure 静态 Web 应用。 

此应用程序可用： 
* [示例](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website)
* [演示网站 - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>该示例有什么作用？ 

此示例网站提供对 10000 本书籍的目录的访问权限。 用户可以通过在搜索栏中输入文本来搜索目录。 用户输入文本时，网站将使用搜索索引的建议功能来完成文本。 查询完成后，将显示书籍列表，其中包含详细信息部分。 用户可以选择书籍来查看该书籍的所有详细信息（存储在搜索索引中）。 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="此示例网站提供对 10000 本书籍的目录的访问权限。用户可以通过在搜索栏中输入文本来搜索目录。用户输入文本时，网站将使用搜索索引的建议功能来完成文本。搜索完成后，将显示书籍列表，其中包含详细信息部分。用户可以选择书籍来查看该书籍的所有详细信息（存储在搜索索引中）。":::

搜索体验包括： 

* 搜索 – 为应用程序提供搜索功能。
* 建议 – 当用户在搜索栏中键入内容时提供建议。
* 文档查找 – 按 ID 查找文档，以检索其详细信息页的所有内容。

## <a name="how-is-the-sample-organized"></a>如何对示例进行组织？

该[示例](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website)包含以下内容：

|应用|目标|GitHub<br>存储库<br>位置|
|--|--|--|
|客户端|React 应用（表示层），用于通过搜索显示书籍。 它调用 Azure Function 应用。 |[/search-website/src](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website/src)|
|服务器|Azure .NET Function 应用（业务层）- 使用 .NET SDK 调用 Azure 认知搜索 API |[/search-website/api](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website/api)|
|大容量插入|.NET 文件，用于创建索引并向其中添加文档。|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>设置开发环境

为本地开发环境安装以下各项。 

- [.NET 3](https://dotnet.microsoft.com/download/dotnet/5.0)  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) 和以下扩展
    - [Azure 资源](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Azure 认知搜索 0.2.0+](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Azure 静态 Web 应用](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- 可选：
    - 本教程不在本地运行 Azure Function API，但如果想要在本地运行它，则需要安装 [azure-functions-core-tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash#install-the-azure-functions-core-tools)。

## <a name="fork-and-clone-the-search-sample-with-git"></a>通过 git 创建搜索示例的分支和克隆搜索示例

为示例存储库创建分支对于能够部署静态 Web 应用至关重要。 Web 应用根据您自己的 GitHub 分支位置确定生成操作和部署内容。 静态 Web 应用中的代码远程执行，Azure 静态 Web 应用从已创建分支的示例中的代码读取。

1. 在 GitHub 上，创建[示例存储库](https://github.com/Azure-Samples/azure-search-dotnet-samples)分支。 

    在 Web 浏览器中使用 GitHub 帐户完成分支创建过程。 本教程使用分支作为部署到 Azure 静态 Web 应用的一部分。 

1. 在 bash 终端，将示例应用程序下载到本地计算机。 

    将 `YOUR-GITHUB-ALIAS` 替换为 GitHub 别名。 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-dotnet-samples
    ```

1. 在 Visual Studio Code 中，打开克隆存储库的本地文件夹。 其余任务从 Visual Studio Code 完成，除非另有指定。

## <a name="create-a-resource-group-for-your-azure-resources"></a>为 Azure 资源创建资源组

1. 在 Visual Studio Code 中，开启[活动](https://code.visualstudio.com/docs/getstarted/userinterface)栏，然后选择 Azure 图标。 
1. 在侧边栏中，**右键单击 `Resource Groups` 区域下的“Azure 订阅”** ，然后选择 **创建资源组**。

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="在侧边栏中，**右键单击“资源组”区域下的 Azure 订阅**，然后选择 **创建资源组**。":::
1. 输入资源组名称，例如 `cognitive-search-website-tutorial`。 
1. 选择靠近自己的位置。
1. 创建认知搜索和静态 Web 应用资源时，在本教程的后面部分，请使用此资源组。 

    创建资源组后，可以使用逻辑单元来管理资源，包括在使用完资源后将其删除。

## <a name="next-steps"></a>后续步骤

* [创建搜索索引并和文档一起加载](tutorial-csharp-create-load-index.md)
* [部署静态 Web 应用](tutorial-csharp-deploy-static-web-app.md)
