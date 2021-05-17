---
title: include 文件
description: include 文件
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 22411e5a80f555a3ead05d39466a7a175923d9bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105100"
---
* 在[配置平台](../articles/active-directory/develop/quickstart-register-app.md#configure-platform-settings)设置的第 4 步中选择适当的平台后，在用户界面右侧的侧面板中配置“重定向 URI”和“访问令牌”。 

    * “重定向 URI”必须与身份验证请求所提供的地址相匹配：

        * 对于本地开发环境中托管的应用，请选择“公共客户端(移动和桌面)”。 确保将“公共客户端”设为“是”。
        * 对于 Azure 应用服务上托管的单页应用，请选择“Web”。

    * 确定“注销 URL”是否合适。

    * 通过选中“访问令牌”或“ID 令牌”来启用隐式授权流。

    [![创建重定向 URI](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    单击“配置”，然后单击“保存”。

* 将 Azure Active Directory 应用与 Azure 时序见解关联。 依次选择“API 权限” > “添加权限” > “我的组织使用的 API”。

    [![将 API 与 Azure Active Directory 应用相关联](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   在搜索栏中键入 `Azure Time Series Insights`，然后选择 `Azure Time Series Insights`。

* 接下来，指定你的应用所需的 API 权限类型。 默认情况下，将突出显示“委派的权限”。 然后，选择一种权限类型，再选择“添加权限”。

    [![指定你的应用所需的 API 权限类型](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* 如果应用程序将自己调用环境的 API，请[添加凭据](../articles/active-directory/develop/quickstart-register-app.md#add-credentials)。 通过凭据，应用程序可以自己的身份进行身份验证，无需用户在运行时进行任何交互。