---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/15/2021
ms.author: glenga
ms.openlocfilehash: 33a4b0f7d25162cf258e9ef6ad4ee438d6b76c8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741374"
---
## <a name="local-settings"></a>本地设置

在 Azure 中的函数应用中运行时，函数所需的设置[安全地存储在应用设置中](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#settings)。 在本地开发期间，这些设置这改为添加到 local.settings.json 文件中的 `Values` 对象。 local.settings.json 文件还会存储本地开发工具使用的设置。 

因为 local.settings.json 可能包含机密（如连接字符串），因此你绝不应将其存储在远程存储库中。 若要了解有关本地设置的详细信息，请参阅[本地设置文件](../articles/azure-functions/functions-develop-local.md#local-settings-file)。