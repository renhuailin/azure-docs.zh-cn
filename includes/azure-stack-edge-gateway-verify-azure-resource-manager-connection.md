---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: e0770eb27bfebc085d99af9fdc9749676b699b70
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106565"
---
确保可使用以下步骤从客户端访问设备。

验证客户端是否可以连接到本地 Azure 资源管理器。 

1. 调用本地设备 API 进行身份验证：

    ### <a name="az"></a>[Az](#tab/az)

    ```powershell
    login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

    ### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. 提供用户名 `EdgeArmUser` 和密码以通过 Azure 资源管理器进行连接。 如果你忘记了密码，请[重置 Azure 资源管理器的密码](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md)，然后使用此密码登录。