---
title: 创建新应用 - LUIS
titleSuffix: Azure Cognitive Services
description: 在语言理解 (LUIS) 网页上创建和管理应用程序。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/13/2021
ms.openlocfilehash: 31316a5d0688c7cee0bc15f6e7cebdea23d0f29d
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110097078"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>在 LUIS 门户中创建新的 LUIS 应用
可通过多种方法创建 LUIS 应用。 可以在 LUIS 门户中创建 LUIS 应用，也可以通过 LUIS 创作 [API](developer-reference-resource.md) 进行创建。

## <a name="using-the-luis-portal"></a>使用 LUIS 门户

可以通过以下几种方式在门户中创建新应用：

* 从一个空应用开始，创建意向、表述和实体。
* 从一个空应用开始，添加[预生成的域](./howto-add-prebuilt-models.md)。
* 从已包含意向、言语和实体的 `.lu` 或 `.json` 文件导入 LUIS 应用。

## <a name="using-the-authoring-apis"></a>使用创作 API
可以通过以下几种方式使用创作 API 创建新应用：

* [添加应用程序](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - 从一个空应用开始，创建意向、言语和实体。
* [添加预生成应用程序](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - 从一个预生成域开始，包括意向、言语和实体。


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>在 LUIS 中创建新应用

1. 在“我的应用”页上，依次选择“订阅”、“创作资源”和“+ 创建”。 
    
    :::image type="content" source="media/create-app-in-portal.png" alt-text="LUIS 应用列表" lightbox="media/create-app-in-portal.png":::

1. 在对话框中，输入应用程序的名称，例如“`Pizza Tutorial`”。

    :::image type="content" source="media/create-pizza-tutorial-app-in-portal.png" alt-text="“创建新应用”对话框" lightbox="media/create-pizza-tutorial-app-in-portal.png":::

1. 选择应用程序区域性，然后选择“完成”。 此时，“说明”和“预测资源”是可选项。 以后随时可以在门户的“管理”部分进行设置。

    > [!NOTE]
    > 创建应用程序后将无法更改区域性。

    创建应用后，LUIS 门户会显示“意向”列表，其中包含已为你创建的 `None` 意向。 现在，你已有一个空应用。

    :::image type="content" source="media/pizza-tutorial-new-app-empty-intent-list.png" alt-text="无任何意向和示例言语的意向列表" lightbox="media/pizza-tutorial-new-app-empty-intent-list.png":::

## <a name="other-actions-available-on-my-apps-page"></a>“我的应用”页上提供的其他操作

上下文工具栏提供其他操作：

* 重命名应用
* 使用 `.lu` 或 `.json` 从文件导入
* 将应用导出为 `.lu`（适用于 [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)）、`.json` 或 `.zip`（适用于 [LUIS 容器](luis-container-howto.md)）
* 导入容器终结点日志以查看终结点言语
* 将终结点日志导出为 `.csv` 以进行脱机分析
* 删除应用

## <a name="next-steps"></a>后续步骤

如果应用设计包含意向检测，请[创建新意向](luis-how-to-add-intents.md)并添加示例言语。 如果应用设计仅是数据提取，则将示例言语添加到“None”意向，然后[创建实体](./luis-how-to-add-entities.md)，并使用这些实体标记示例言语。