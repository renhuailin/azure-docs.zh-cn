---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 59d65673c425523fae46a85db18ca76db5d420eb
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072967"
---
请按照以下步骤创建 Web API 应用注册：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如 my-api1。 保留“重定向 URI”的默认值。 
1. 选择“注册”。
1. 完成应用注册后，选择“概述”。
1. 记录“应用程序（客户端）ID”，以便在稍后配置 Web 应用程序时使用。

    ![屏幕截图演示如何获取 Web API 应用程序 ID。](./media/active-directory-b2c-app-integration-register-api/get-azure-ad-b2c-web-api-app-id.png)