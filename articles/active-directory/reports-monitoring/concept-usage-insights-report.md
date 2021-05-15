---
title: 使用情况和见解报表 | Microsoft Docs
description: Azure Active Directory 门户中的使用情况和见解报表简介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 54bce5e839786862a6dac9aeb685dd364547a09a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685016"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的使用情况和见解报表

通过使用情况和见解报表，你可以获取以应用程序为中心的登录数据的视图。 可找到以下问题的答案：

*   组织中最常用的应用程序是哪些？
*   哪些应用程序的登录失败次数最多？ 
*   每个应用程序的最常见登录错误是什么？

## <a name="prerequisites"></a>先决条件 

若要访问使用情况和见解报表中的数据，需要：

* Azure AD 租户
* 获得 Azure AD 高级版 (P1/P2) 许可以查看登录数据
* 具有全局管理员、安全管理员、安全读取者或报表读取者角色的用户。 此外，任何用户（非管理员）都可以访问自己的登录。 

## <a name="access-the-usage-and-insights-report"></a>访问使用情况和见解报告

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 选择正确的目录，然后选择“Azure Active Directory”并选择“企业应用程序”。
3. 在“活动”部分中，选择“使用情况和见解”以打开报表。 

![屏幕截图显示从“活动”部分选择的“使用情况和见解”。](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>使用报表

使用情况和见解报表显示具有一个或多个登录尝试的应用程序列表，并且你可以按成功登录的次数、失败的登录次数和成功率进行排序。

单击列表底部的“加载更多”，可以在页面上查看更多应用程序。 可以选择日期范围来查看在该范围内已使用的所有应用程序。

![屏幕截图显示了应用程序活动的使用情况和见解，可以在其中选择范围并查看不同应用的登录活动。](./media/concept-usage-insights-report/usage-and-insights-report.png)

还可以在特定应用程序上设置焦点。 选择“查看登录活动”，可查看应用程序随时间变化的登录活动以及最常见的错误。  

选择应用程序使用情况图中的某一天时，可以获取该应用程序登录活动的详细列表。  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="屏幕截图显示特定应用程序的使用情况和见解，从中可查看登录登录活动的图表。":::

## <a name="next-steps"></a>后续步骤

* [登录报告](concept-sign-ins.md)
