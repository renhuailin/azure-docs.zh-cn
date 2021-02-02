---
title: 将 Azure Active Directory 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何从 Azure Active Directory 收集数据并将其流式传输到 Azure Sentinel 中 Azure AD 登录、审核和预配日志。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99251975"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>将 Azure Active Directory (Azure AD) 数据连接到 Azure Sentinel

可以使用 Azure Sentinel 的内置连接器从 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 收集数据，并将其流式传输到 Azure Sentinel。 连接器允许流式传输以下日志类型：

- [**登录日志**](../active-directory/reports-monitoring/concept-all-sign-ins.md)，其中包含有关用户提供身份验证系数的 [交互式用户登录](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) 信息。

    Azure AD 连接器现在包含以下三个其他类别的登录日志，当前为 **预览版**：
    
    - [**非交互式用户登录日志**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)，其中包含客户端代表用户执行的登录的相关信息，而无需用户进行任何交互或身份验证。
    
    - [**服务主体登录日志**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)，其中包含有关不涉及任何用户的应用程序和服务主体登录的信息。 在此类登录中，应用或服务代表自己提供对资源进行身份验证或访问所需的凭据。
    
    - [**托管标识登录日志**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins)，其中包含有关 azure 资源的登录信息，这些信息包含由 azure 管理的机密。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)

- [**审核日志**](../active-directory/reports-monitoring/concept-audit-logs.md)，其中包含与用户和组管理、托管应用程序和目录活动相关的系统活动的相关信息。

- [**预配日志**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (也是 **预览版**) ，其中包含有关 Azure AD 预配服务预配的用户、组和角色的系统活动信息。 

> [!IMPORTANT]
> 如上所述，某些可用的日志类型当前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。
## <a name="prerequisites"></a>必备条件

- 任何 Azure AD 许可证 (免费/O365/P1/P2) 足以将登录日志引入 Azure Sentinel。 对于 Azure Monitor (Log Analytics) 和 Azure Sentinel，可能会收取额外的每 gb 费用。

- 必须在工作区中为你的用户分配 Azure Sentinel 参与者角色。

- 你的用户必须在你要从中流式传输日志的租户上向全局管理员或安全管理员角色分配。

- 用户必须对 Azure AD 诊断设置具有读取和写入权限，才能查看连接状态。 

## <a name="connect-to-azure-active-directory"></a>连接到 Azure Active Directory

1. 在 Azure Sentinel 中，从导航菜单中选择 " **数据连接器** "。

1. 从 "数据连接器" 库中，选择 " **Azure Active Directory** "，然后选择 " **打开连接器" 页面**。

1. 将想要流式传输到 Azure 标记的日志类型旁边的复选框标记 (参阅上方) ，并单击 " **连接**"。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **日志**" 的 " **LogManagement** " 部分的下表中：

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

若要查询 Azure AD 日志，请在 "查询" 窗口的顶部输入相关表名称。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure Active Directory 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
