---
title: 将 Azure Active Directory 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何从 Azure Active Directory 收集数据，并将 Azure AD 登录、审核和预配日志流式传输到 Azure Sentinel。
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 20e9920921afe1eae8babb76212a6de7ea2e1321
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251858"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>将 Azure Active Directory (Azure AD) 数据连接到 Azure Sentinel

> [!IMPORTANT]
> 如下所述，某些可用的日志类型目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

可以使用 Azure Sentinel 的内置连接器从 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 收集数据，并将其流式传输到 Azure Sentinel。 通过使用连接器，可以流式传输以下日志类型：

- [**登录日志**](../active-directory/reports-monitoring/concept-all-sign-ins.md)，包含用户提供身份验证因子的交互式用户登录信息。

    Azure AD 连接器包含以下三个其他类别的登录日志，当前为预览版：
    
    - [**非交互式用户登录日志**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)，包含了客户端代表用户进行登录的信息，没有来自用户的任何交互或身份验证因素。
    
    - [**服务主体登录日志**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)，包含了应用程序和服务主体登录信息，不涉及任何用户。 在此类登录中，应用或服务代表自己提供对资源进行身份验证或访问所需的凭据。
    
    - [**托管标识登录日志**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins)，包含了 Azure 资源的登录信息，这些资源包含由 azure 管理的机密信息。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)

- [**审核日志**](../active-directory/reports-monitoring/concept-audit-logs.md)，包含了有关用户和组管理、托管应用程序和目录活动的系统活动信息。

- [**预配日志**](../active-directory/reports-monitoring/concept-provisioning-logs.md)(也是预览版)，包含了有关 Azure AD 预配服务预配的用户、组和角色的系统活动信息。 


## <a name="prerequisites"></a>先决条件

- 将登录日志引入 Azure Sentinel 需要 Azure Active Directory P1 或 P2 许可证。 任何 Azure AD 许可证（免费/O365/P1/P2）均足以引入其他日志类型。 对于 Azure Monitor (Log Analytics) 和 Azure Sentinel，可能会收取额外的每 GB 费用。

- 必须在工作区中为用户分配 Azure Sentinel 参与者角色。

- 必须在要从中流式传输日志的租户上为用户分配全局管理员或安全管理员角色。

- 用户必须具有对 Azure AD 诊断设置进行读取和写入的权限，才能查看连接状态。 

## <a name="connect-to-azure-active-directory"></a>连接到 Azure Active Directory

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从“数据连接器”库中，选择“Azure Active Directory”，然后选择“打开连接器”页面。

1. 在流式传输到 Azure Sentinel 的日志类型旁的复选框中进行勾选（参阅上方说明），并选择“连接”。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在“日志管理”部分下的“日志”中，如下表：

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

如要查询 Azure AD 日志，请在“查询”窗口的顶部输入相关表名称。

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解了如何将 Azure Active Directory 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
