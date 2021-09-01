---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: b356480175c4b3da537b1e3e49fcdca676b2d6d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777828"
---
若要向应用（应用 ID：1）授予权限，请执行以下步骤： 

1. 选择“应用注册”，然后选择你所创建的应用（应用 ID：1）。
1. 在“管理”下选择“API 权限”。
1. 在“已配置权限”下，选择“添加权限”。
1. 选择“我的 API”选项卡。
1. 选择应授予 Web 应用程序访问权限的 API（应用 ID：2）。 例如 my-api1。
1. 在“权限”下展开“任务”，然后选择之前定义的范围（例如，tasks.read 和 tasks.write )。
1. 选择“添加权限”。
1. 选择“为 \<*your tenant name*> 授予管理员许可”。
1. 请选择“是”。
1. 选择“刷新”，然后确认两个范围的“状态”下是否均显示“已授予...”。 
1. 从“配置权限”列表中，选择范围，然后复制范围全名。 

    ![已配置权限窗格的屏幕截图，其中显示了授予的读取访问权限。](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  