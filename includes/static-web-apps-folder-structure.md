---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543545"
---
| properties | 说明 | 示例 | 必选 |
|---|---|---|---|
| `app_location` | 应用程序代码的位置。 | 如果应用程序源代码位于存储库的根目录中，请输入 `/`；如果应用程序代码位于 `app` 目录中，则输入 `/app`。 | 是 |
| `api_location` | Azure Functions 代码的位置。 | 如果应用代码位于 `api` 文件夹中，请输入 `/api`。 如果未在文件夹中检测到 Azure Functions 应用，生成不会发生故障，而工作流会假定你不需要 API。 | 否 |
| `output_location` | 与 `app_location` 相对应的生成输出目录的位置。 | 例如，如果应用程序源代码位于 `/app` 中，并且生成脚本将文件输出到 `/app/build` 文件夹，则将 `build` 设置为 `output_location` 值。 | 否 |