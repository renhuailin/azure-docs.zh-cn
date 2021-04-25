---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305544"
---
确保可使用以下步骤从客户端访问设备。

验证客户端是否可以连接到本地 Azure 资源管理器。 

1. 调用本地设备 API 进行身份验证：

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. 提供用户名 `EdgeArmUser` 和密码以通过 Azure 资源管理器进行连接。 如果你忘记了密码，请[重置 Azure 资源管理器的密码](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md)，然后使用此密码登录。