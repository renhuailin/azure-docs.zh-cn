---
title: JavaScript 教程：向 Web 应用添加搜索
titleSuffix: Azure Cognitive Search
description: 使用 npm SDK @azure/search-documents 创建索引，并使用 JavaScript 将 CSV 数据导入到搜索索引。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: ef48074c5346374b12eb5a3a3aea50b2c753a082
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107950291"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2 - 创建和加载包含 JavaScript 的搜索索引

继续生成启用搜索的网站，方法是：
* 使用 VS Code 扩展创建搜索资源
* 使用示例脚本和 Azure SDK [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) 创建新的索引，并使用 JavaScript 导入数据。

## <a name="create-an-azure-search-resource"></a>创建 Azure 搜索资源 

使用用于 Visual Studio Code 的 [Azure 认知搜索](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) 扩展创建新的搜索资源。

1. 在 Visual Studio Code 中，开启[活动](https://code.visualstudio.com/docs/getstarted/userinterface)栏，然后选择 Azure 图标。 

1. 在侧边栏中，**右键单击 `Azure: Cognitive Search` 区域下的“Azure 订阅”** ，然后选择 **新建搜索服务**。

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="在侧边栏中，右键单击 **Azure：认知搜索** 区域下的“Azure 订阅”，然后选择 **新建搜索服务**。":::

1. 按照提示提供以下信息：

    |Prompt|Enter|
    |--|--|
    |为新搜索服务输入全局唯一名称。|**请记住此名称**。 此资源名称将成为您的资源终结点的一部分。|
    |选择新资源的资源组|使用为本教程创建的资源组。|
    |选择搜索服务的 SKU。|对于本教程，请选择 **免费**。 创建服务后无法更改 SKU 定价层。|
    |选择新资源的位置。|选择附近的区域。|

1. 完成提示后，将创建新的搜索资源。 

## <a name="get-your-search-resource-admin-key"></a>获取搜索资源管理密钥

获取具有 Visual Studio Code 扩展的搜索资源管理密钥。 

1. 在 Visual Studio Code 的侧边栏中，右键单击搜索资源，然后选择 **复制管理密钥**。

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="在侧边栏中，右键单击“搜索”资源，然后选择 **复制管理密钥**。":::

1. 保留此管理密钥，您将需要在[后面的部分](#prepare-the-bulk-import-script-for-search)中使用它。 

## <a name="prepare-the-bulk-import-script-for-search"></a>准备批量导入脚本以进行搜索

此脚本使用 Azure SDK 进行认知搜索：

* [npm 包@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [参考文档](/javascript/api/overview/azure/search-documents-readme)

1. 在 Visual Studio Code 中，在子目录 `search-website/bulk-insert` 中打开 `bulk_insert_books.js` 文件，将以下变量替换为您自己的值，以便在 Azure 搜索 SDK 中进行身份验证：

    * YOUR-SEARCH-RESOURCE-NAME
    * YOUR-SEARCH-ADMIN-KEY

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. 在 Visual Studio 中打开用于项目目录的子目录 `search-website/bulk-insert` 的集成终端，并运行以下命令以安装依赖项。 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>运行批量导入脚本以进行搜索

1. 继续使用 Visual Studio 中的集成终端作为项目目录的子目录 `search-website/bulk-insert` ，运行以下 bash 命令来运行 `bulk_insert_books.js` 脚本：

    ```javascript
    npm start
    ```

1. 当代码运行时，控制台将显示进度。 
1. 上传完成后，打印到控制台的最后一个语句将为“已完成”。

## <a name="review-the-new-search-index"></a>查看新的搜索索引

上传完成后，搜索索引就可以使用了。 查看新索引。

1. 在 Visual Studio Code 中，打开 Azure 认知搜索扩展，并选择搜索资源。  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="在 Visual Studio Code 中，打开 Azure 认知搜索扩展，并打开搜索资源。":::

1. 依次展开“索引”、“文档”、`good-books`，然后选择一个文档以查看所有特定于该文档的数据。
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="展开“索引”和 `good-books`，然后选择一个文档。":::

## <a name="copy-your-search-resource-name"></a>复制搜索资源名称

记下 **搜索资源名称**。 将 Azure Function 应用连接到搜索资源时需要用到它。 

> [!CAUTION]
> 尽管您可能会尝试在 Azure Function 中使用搜索管理密钥，但这并不遵循最低权限原则。 Azure Function 将使用查询密钥以符合最小特权。 

## <a name="next-steps"></a>后续步骤

[部署静态 Web 应用](tutorial-javascript-deploy-static-web-app.md)