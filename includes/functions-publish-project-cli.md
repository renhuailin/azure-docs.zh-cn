---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424605"
---
## <a name="deploy-the-function-project-to-azure"></a>将函数项目部署到 Azure

在 Azure 中成功创建函数应用后，便可以使用 [func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) 命令部署本地函数项目。  

在以下示例中，请将 `<APP_NAME>` 替换为你的应用的名称。

```console
func azure functionapp publish <APP_NAME>
```

publish 命令显示类似于以下输出的结果（为简洁起见，示例中的结果已截断）：

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
