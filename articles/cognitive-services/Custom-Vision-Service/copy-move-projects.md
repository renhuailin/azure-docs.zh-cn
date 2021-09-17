---
title: 复制和移动“自定义视觉”项目
titleSuffix: Azure Cognitive Services
description: 了解如何使用 ExportProject 和 ImportProject API 来复制和移动“自定义视觉”项目。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: d546726e03420b41b4ff445418e30274904c6e5c
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635056"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>复制和移动“自定义视觉”项目

创建并训练“自定义视觉”项目后，你可能会想要将项目复制到另一个资源。 例如，你可能想要将项目从开发环境移至生产环境，或将项目备份到不同 Azure 区域中的帐户，以提高数据安全性。

**[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** 和 **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** API 允许将项目从一个“自定义视觉”帐户复制到其他帐户，从而实现这种场景。 本指南演示如何将这些 REST API 与 cURL 配合使用。 还可以使用 HTTP 请求服务（如 Postman）来发出请求。

> [!TIP]
> 有关使用 Python 客户端库的此方案示例，请参阅 GitHub 上的[移动自定义视觉项目](https://github.com/Azure-Samples/custom-vision-move-project/tree/master/)存储库。

## <a name="business-scenarios"></a>业务场景

如果你的应用或业务依赖于使用“自定义视觉”项目，我们建议你将模型复制到另一个区域中的另一个“自定义视觉”帐户。 这样一来，如果发生区域性服务中断，便可在项目复制到的区域中访问项目。

##  <a name="prerequisites"></a>先决条件

- 两个 Azure“自定义视觉”资源。 如果你没有这些资源，请转到 Azure 门户并[创建一个新的“自定义视觉”资源](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true)。
- “自定义视觉”资源的训练密钥和终结点 URL。 可以在 Azure 门户上资源的“概述”选项卡中找到这些值。
- 一个已创建的“自定义视觉”项目。 有关如何执行此操作的说明，请参阅[生成分类器](./getting-started-build-a-classifier.md)。
* [PowerShell 6.0 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)，或者类似的命令行实用工具。

## <a name="process-overview"></a>过程概述

复制项目的过程包括以下步骤：

1. 首先，获取源帐户中你想要复制的项目的 ID。
1. 然后，使用该项目 ID 和源帐户的训练密钥调用 **ExportProject** API。 你将获得一个临时令牌字符串。
1. 然后，使用该令牌字符串和目标帐户的训练密钥调用 **ImportProject** API。 然后，该项目将在目标帐户下列出。

## <a name="get-the-project-id"></a>获取项目 ID

首先，调用 **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** 以查看现有“自定义视觉”项目及其 ID 的列表。 使用源帐户的训练密钥和终结点。

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

你将收到一个 `200\OK` 响应，该响应的正文中包含项目及其元数据的列表。 `"id"` 值是要为后续步骤复制的字符串。

```json
[
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "string",
    "description": "string",
    "settings": {
      "domainId": "00000000-0000-0000-0000-000000000000",
      "classificationType": "Multiclass",
      "targetExportPlatforms": [
        "CoreML"
      ],
      "useNegativeSet": true,
      "detectionParameters": "string",
      "imageProcessingSettings": {
        "augmentationMethods": {}
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>导出项目

使用项目 ID 与源训练密钥和终结点调用 **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** 。

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

你将收到一个 `200/OK` 响应，其中包含有关已导出项目的元数据和一个引用字符串 `"token"`。 复制令牌的值。

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

> [!TIP]
> 如果在导入项目时收到“无效令牌”错误，则可能是令牌 URL 字符串未进行 web 编码。 你可以使用 [URL 编码器](https://meyerweb.com/eric/tools/dencoder/)对令牌进行编码。

## <a name="import-the-project"></a>导入项目

使用目标训练密钥和终结点以及引用令牌来调用 **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** 。 还可以在新帐户中为你的项目命名。

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

你将收到一个 `200/OK` 响应，其中包含有关新导入的项目的元数据。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>后续步骤

在本指南中，你已学习如何在“自定义视觉”资源之间复制和移动项目。 接下来请浏览 API 参考文档，以了解“自定义视觉”的其他作用。
* [REST API 参考文档](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
