---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 4ab3986f66d420d1fcb1ae67747cae5e42f7c59b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777846"
---
请按照以下步骤创建 Web API 应用注册（应用 ID: 2）：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的名称，例如 my-api1。 保留“重定向 URI”的默认值。 
1. 选择“注册”  。
1. 完成应用注册后，选择“概述”。
1. 记录“应用程序(客户端)ID”，以便在稍后配置 Web 应用程序时使用。

    ![演示如何获取 Web API 应用程序 ID 的屏幕截图。](./media/active-directory-b2c-app-integration-register-api/get-azure-ad-b2c-web-api-app-id.png)