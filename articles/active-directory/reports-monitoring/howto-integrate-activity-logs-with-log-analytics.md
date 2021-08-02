---
title: 将 Azure Active Directory 日志流式传输到 Azure Monitor 日志 | Microsoft Docs
description: 了解如何将 Azure Active Directory 日志与 Azure Monitor 日志集成
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/11/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: c29b631d3002f0c79fb2dd1b1f26dfa65051368c
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112018940"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>将 Azure AD 日志与 Azure Monitor 日志集成

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

使用 Azure Monitor 日志可以跨各种数据源查询数据以查找特定事件、分析趋势和执行关联。 通过将 Azure AD 活动日志与 Azure Monitor 日志集成，你现在可以执行以下任务：

 * 比较 Azure AD 登录日志与 Azure 安全中心发布的安全日志

 * 通过从 Azure Application Insights 关联应用程序性能数据，可以解决应用程序登录页上的性能瓶颈。  

Ignite 会话中的以下视频通过实际用户方案演示了将 Azure Monitor 日志用于 Azure AD 日志的优点。

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

本文介绍如何将 Azure Active Directory (Azure AD) 日志与 Azure Monitor 集成。

## <a name="supported-reports"></a>支持的报表

可以将审核活动日志和登录活动日志路由到 Azure Monitor 日志以供进一步分析。 

* **审核日志**：可以通过 [审核日志活动报表](concept-audit-logs.md)访问在租户中执行的每个任务的历史记录。
* **登录日志**：可以通过 [登录活动报表](concept-sign-ins.md)来确定谁执行了审核日志中报告的任务。
* 预配日志：借助[预配日志](../app-provisioning/application-provisioning-log-analytics.md)，可以监视已在所有第三方应用程序中创建、更新和删除的用户。 

> [!NOTE]
> 目前不支持 B2C 相关的审核和登录活动日志。
>

## <a name="prerequisites"></a>必备条件 

若要使用此功能，需满足以下条件:

* Azure 订阅。 如果没有 Azure 订阅，可以[注册免费试用版](https://azure.microsoft.com/free/)。
* Azure AD 租户。
* 一个是 Azure AD 租户的全局管理员或安全管理员的用户。 
* 在 Azure 订阅中创建 Log Analytics 工作区。 了解如何[创建 Log Analytics 工作区](../../azure-monitor/logs/quick-create-workspace.md)。

## <a name="licensing-requirements"></a>许可要求

使用此功能需要 Azure AD Premium P1 或 P2 租户。 可以在 Azure Active Directory 的[“概述”](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)页上找到租户的许可证类型。

![租户信息](./media/howto-integrate-activity-logs-with-log-analytics/tenant-information.png)
 
如果想要了解活动数据在高级租户中的存储时间，请参阅[数据在 Azure AD 中的存储时长](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)

## <a name="send-logs-to-azure-monitor"></a>将日志发送到 Azure Monitor

1. 登录 [Azure 门户](https://portal.azure.com)。 

2. 选择“Azure Active Directory” > “诊断设置” -> “添加诊断设置”。 还可以从“审核日志”或“登录”页选择“导出设置”，以转到诊断设置配置页。  
    
3. 在“诊断设置”菜单中，选中“发送到 Log Analytics 工作区”复选框，并选择“配置”。

4. 选择要将日志发送到的 Log Analytics 工作区，或在提供的对话框中创建新的工作区。  

5. 执行下列两项操作或之一：
    * 若要将审核日志发送到 Log Analytics 工作区，请选中“AuditLogs”复选框。 
    * 若要将登录日志发送到 Log Analytics 工作区，请选中“SignInLogs”复选框。

6. 选择“保存”，保存设置。

    ![诊断设置](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. 大约 15 分钟后，验证事件是否已流式传输到 Log Analytics 工作区。

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 日志分析 Azure AD 活动日志](howto-analyze-activity-logs-log-analytics.md)
* [安装和使用用于 Azure Active Directory 的日志分析视图](howto-install-use-log-analytics-views.md)