---
title: 教程 - 使用 Azure API 管理发布 API 版本
description: 遵循本教程的步骤了解如何在 API 管理中发布多个 API 版本。
author: dlepow
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: danlep
ms.openlocfilehash: 0f9d43f0b9abd53f76efb1e29d40667f1daf19b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644783"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>教程：发布 API 的多个版本 

有时，让 API 的所有调用方都使用完全相同的版本是不切实际的。 如果调用方想要升级到更高版本，他们会希望采用一种易于理解的方法来实现此目的。 如本教程所示，可以在 Azure API 管理中提供多个版本。 

有关背景知识，请参阅[版本与修订](https://azure.microsoft.com/blog/versions-revisions/)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 将新版本添加到现有 API
> * 选择版本方案
> * 将版本添加到产品
> * 浏览开发人员门户以查看版本

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Azure 门户中显示的版本":::

## <a name="prerequisites"></a>必备条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入并发布第一个 API](import-and-publish.md)。

## <a name="add-a-new-version"></a>添加新版本

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 选择“API”。
1. 从 API 列表中选择“演示会议 API”。 
1. 选择“演示会议 API”旁边的上下文菜单 。
1. 选择“添加版本”。

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="API 上下文菜单 - 添加版本":::


> [!TIP]
> 创建新 API 时也可启用版本。 在“添加 API”屏幕上，选择“对此 API 进行版本控制?” 。

## <a name="choose-a-versioning-scheme"></a>选择版本控制方案

在 Azure API 管理中，可通过选择“版本控制方案”来选择调用方指定 API 版本的方式：路径、标头或查询字符串 。 在下面的示例中，路径用作版本控制方案。

输入下表中的值。 然后选择“创建”以创建版本。

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="“添加版本”窗口":::



|设置   |值  |说明  |
|---------|---------|---------|
|**Name**     |  *demo-conference-api-v1*       |  API 管理实例中的唯一名称。<br/><br/>因为某个版本实际上是一个基于 API [修订版](api-management-get-started-revise-api.md)的新 API，所以此设置为新 API 的名称。   |
|**版本控制方案**     |  **路径**       |  调用方指定 API 版本的方式。     |
|**版本标识符**     |  *v1*       |  特定于方案的版本指示符。 对于“路径”，其标识符应为 API URL 路径的后缀。 <br/><br/> 如果选择“标头”或“查询字符串”，请输入附加的值：标头或查询字符串参数的名称。 <br/><br/> 将显示一个用法示例。        |
|**产品**     |  **不受限制**       |  （可选）与 API 版本关联的一个或多个产品。 若要发布 API，必须将其与某个产品相关联。 稍后还可[将版本添加到产品](#add-the-version-to-a-product)。      |

创建版本后，它现在显示在 API 列表中的“演示会议 API”下。 现在会看到两个 API：“原始”和“v1” 。

![Azure 门户中 API 下面列出的版本](media/api-management-getstarted-publish-versions/version-list.png)

现在，可以编辑和配置“v1”使其作为与“原始”API 不同的独立 API。  对一个版本进行更改不会影响另一个版本。

> [!Note]
> 如果为不受版本控制的 API 添加了版本，还会自动创建“原始”API。 此版本是对默认 URL 的响应。 创建“原始”版本可确保所有现有调用方不会被添加版本的过程中断。 如果在一开始就启用了版本的情况下创建新 API，则不会创建“原始”API。

## <a name="add-the-version-to-a-product"></a>将版本添加到产品

要使调用方看到新版本，必须将该版本添加到 *产品*。 如果尚未将版本添加到产品，可以随时将其添加到产品。

例如，若要将版本添加到“不受限制”产品：
1. 在 Azure 门户，导航到 API 管理实例。
1. 选择“产品” > “不受限制” > “API” > “+ 添加”   。
1. 选择“演示会议 API”、版本“v1”。 
1. 单击“选择”。

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="向产品添加版本":::

## <a name="use-version-sets"></a>使用版本集

创建多个版本时，Azure 门户会创建一个版本集，它表示单个逻辑 API 的一组版本。 选择具有多个版本的 API 的名称。 Azure 门户会显示其版本集。 你可以自定义虚拟集的名称和说明 。

你可以通过使用 Azure CLI 直接与版本集交互：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

若要查看所有版本集，请运行 [az apim api versionset list](/cli/azure/apim/api/versionset#az_apim_api_versionset_list) 命令：

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

当 Azure 门户为你创建一个版本集时，它会分配一个字母数字名称并显示在列表的“名称”列中。 在其他 Azure CLI 命令中使用此名称。

若要查看有关版本集的详细信息，请运行 [az apim api versionset show](/cli/azure/apim/api/versionset#az_apim_api_versionset_show) 命令：

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

有关版本集的详细信息，请参阅 [Azure API 管理中的版本](api-management-versions.md#how-versions-are-represented)。

## <a name="browse-the-developer-portal-to-see-the-version"></a>浏览开发人员门户以查看版本

如果已试用[开发人员门户](api-management-howto-developer-portal-customize.md)，可在此处查看 API 版本。

1. 在顶部菜单中选择“开发人员门户”。
2. 依次选择“API”、“演示会议 API”。
3. 可在 API 名称旁边看到一个含有多个版本的下拉列表。
4. 选择“v1”。
5. 请注意列表中第一个操作的“请求 URL”。 其中显示 API URL 路径包含“v1”。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 将新版本添加到现有 API
> * 选择版本方案 
> * 将版本添加到产品
> * 浏览开发人员门户以查看版本

转到下一教程：

> [!div class="nextstepaction"]
> [自定义开发人员门户页面的样式](api-management-howto-developer-portal-customize.md)
