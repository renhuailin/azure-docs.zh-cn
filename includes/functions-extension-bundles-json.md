---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 23e75e2cf2e097e08c0b6bf3c4b0b8c9842a9184
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609740"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

`extensionBundle` 中提供了以下属性：

| properties | 说明 |
| -------- | ----------- |
| id | Microsoft Azure Functions 扩展捆绑包的命名空间。 |
| 版本 | 要安装的捆绑包的版本。 Functions 运行时始终选取由版本范围或间隔定义的可允许最高版本。 上述版本值允许从 2.0.0 到（但不包括）3.0.0 的所有捆绑包版本。 有关详细信息，请参阅[用于指定版本范围的间隔表示法](/nuget/reference/package-versioning#version-ranges)。 |