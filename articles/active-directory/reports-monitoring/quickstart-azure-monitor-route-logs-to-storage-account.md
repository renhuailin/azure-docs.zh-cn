---
title: 教程 - 将目录日志存档到存储帐户 | Microsoft Docs
description: 了解如何设置 Azure 诊断来将 Azure Active Directory 日志推送到存储帐户
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/05/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a50ef779dd65696ae62b6b08b04e65ca19291944
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233389"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>教程：将 Azure AD 日志存档到 Azure 存储帐户

本教程介绍如何设置 Azure Monitor 诊断设置，以便将 Azure Active Directory (Azure AD) 日志路由到 Azure 存储帐户。

## <a name="prerequisites"></a>先决条件 

若要使用此功能，需满足以下条件:

* 一个具有 Azure 存储帐户的 Azure 订阅。 如果没有 Azure 订阅，可以[注册免费试用版](https://azure.microsoft.com/free/)。
* Azure AD 租户。
* 一个是 Azure AD 租户的全局管理员或安全管理员的用户。 

## <a name="archive-logs-to-an-azure-storage-account"></a>将日志存档到 Azure 存储帐户

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 选择“Azure Active Directory” > “监视” > “审核日志”。   

3. 选择“导出数据设置”。 

4. 在“诊断设置”窗格中，执行下述操作之一：
    1. 若要更改现有设置，请选择要更新的诊断设置旁边的“编辑设置”。
    1. 若要添加新设置，请选择“添加诊断设置”。  

    最多可以有三个设置。

     ![导出设置](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. 若要创建新设置，请在“诊断设置”窗格中输入设置名称以提醒其用途（例如，“发送到 Azure 存储帐户”）。 无法更改现有设置的名称。

6. 在“目标详细信息”下，选中“存档到存储帐户”复选框 。 

7. 在“订阅”下拉菜单中选择 Azure 订阅，并在“存储帐户”下拉菜单中选择要将日志路由到的存储帐户 。

8. 在“类别详细信息”下，选择所有相关类别：

    执行下列两项操作或之一：
    1. 选中“AuditLogs”复选框，将审核日志发送到存储帐户。
    
    1. 选中“SignInLogs”复选框，将登录日志发送到存储帐户。

    ![诊断设置](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

9. 选择类别后，在“保留天数”字段中，输入需要日志数据保留的天数。 默认情况下，此值为 *0*，这意味着日志将无限期地保留在存储帐户中。 如果设置其他值，早于所选天数的事件会自动清除。
 
10. 选择“保存”，保存设置。

11. 关闭窗口以返回到“诊断设置”窗格。

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的审核日志架构](./overview-reports.md)
* [解释 Azure Monitor 中的登录日志架构](reference-azure-monitor-sign-ins-log-schema.md)
* [常见问题解答和已知的问题](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
