---
title: 操作方法：获取租户中使用 Active Directory 身份验证库 (ADAL) 的所有应用的完整列表 | Azure
titleSuffix: Microsoft identity platform
description: 在本操作指南中，你将获取租户中使用 ADAL 的所有应用的完整列表。
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/22/2021
ms.author: shermanouko
ms.custom: aaddev, has-adal-ref
ms.reviewer: aiwang, marsma
ms.openlocfilehash: 9d9c0f9787360ed599d586295181ef359fdff293
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418667"
---
# <a name="how-to-get-a-complete-list-of-apps-using-adal-in-your-tenant"></a>操作方法：获取租户中使用 ADAL 的应用的完整列表

对 Active Directory 身份验证库 (ADAL) 的支持将于 2022 年 6 月 30 日结束。 在现有 OS 版本上使用 ADAL 的应用将继续正常运行，但技术支持或安全更新将结束。 如果没有持续安全更新，使用 ADAL 的应用将变得越来越容易遭受最新安全攻击模式的攻击。 本文介绍如何使用 Azure Monitor 工作簿获取租户中使用 ADAL 的所有应用的列表。

## <a name="sign-ins-workbook"></a>登录工作簿

工作簿是一组查询，用于收集和可视化 Azure Active Directory (Azure AD) 日志中提供的信息。 [在此处了解有关登录日志架构的详细信息](../reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)。 Azure AD 管理门户中的登录工作簿现在提供了一个表，可帮助你确定哪些应用程序使用 ADAL 和使用频率。 首先，我们将详细介绍如何在显示应用程序列表的可视化对象之前访问工作簿。

## <a name="step-1-integrate-audit-logs-with-azure-monitor"></a>步骤 1：通过 Azure Monitor 集成审核日志

在访问工作簿之前，请按照[使用 Azure Monitor 将 Azure AD 登录和审核日志集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)中的步骤进行操作。 仅存储 Azure Monitor 集成后创建的登录和审核事件。

## <a name="step-2-access-sign-ins-workbook-in-azure-portal"></a>步骤 2：访问 Azure 门户中的登录工作簿

根据 Azure Monitor 集成中的指定通过 Azure Monitor 将 Azure AD 登录和审核日志集成后，请访问登录工作簿：

   1. 登录到 Azure 门户 
   1. 导航到 Azure Active Directory >“监视” >“工作簿” ****  ****  **** 
   1. 在“使用情况”部分中，打开“登录”工作簿 **** 

   :::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/sign-in-workbook.png" alt-text="Azure Active Directory 门户工作簿界面的屏幕截图，其中突出显示了登录工作簿。":::

## <a name="step-3-identify-apps-that-use-adal"></a>步骤 3：识别使用 ADAL 的应用

“登录工作簿”页底部的表列出了最近使用 ADAL 的应用。 你还可以导出应用列表。 将这些应用更新为使用 MSAL。
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/active-directory-auth-library-apps-present.png" alt-text="登录工作簿的屏幕截图，其中显示使用 Active Directory 身份验证库的应用。":::
    
如果没有使用 ADAL 的应用，则工作簿将显示一个视图，如下所示。 
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/no-active-directory-auth-library-apps.png" alt-text="登录工作簿的屏幕截图，其中无应用使用 Active Directory 身份验证库。":::

## <a name="step-4-update-your-code"></a>步骤 4：更新代码

确定使用 ADAL 的应用后，根据应用程序类型将它们迁移到 MSAL，如下所示。

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解 MSAL（包括使用情况信息以及可用于不同编程语言和应用程序类型的库），请参阅：

- [使用 MSAL 获取和缓存令牌](msal-acquire-cache-tokens.md)
- [应用程序配置选项](msal-client-application-configuration.md)
- [MSAL 身份验证库列表](reference-v2-libraries.md)
