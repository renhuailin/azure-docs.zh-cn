---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 6d62dda341c6acb783bd2525c2f6ba7a76b312ec
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072959"
---
1. 在“管理”下选择“API 权限”。
1. 在“已配置权限”下，选择“添加权限”。
1. 选择“我的 API”选项卡。
1. 选择应授予 Web 应用程序对其的访问权限的 API。 例如 my-api1。
1. 在“权限”下展开“任务”，然后选择前面定义的范围。 例如，tasks.read 和 tasks.write。
1. 选择“添加权限”。
1. 选择“向(租户名称)授予管理员许可”。
1. 请选择“是”。
1. 选择“刷新”，然后确认两个范围的“状态”下是否均显示“已授予...”。 
1. 从“配置权限”列表中，选择范围并复制范围全名。 

    ![获取你的 Web 应用范围](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  