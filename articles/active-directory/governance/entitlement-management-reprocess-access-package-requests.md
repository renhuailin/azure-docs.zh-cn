---
title: 在 Azure AD 权利管理中重新处理对访问包的请求 - Azure Active Directory
description: 了解如何在 Azure Active Directory 权利管理中重新处理对访问包的请求。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/25/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a85d796f8b66cca16d4d3c01ecbfc5f6c43c79f9
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129231"
---
# <a name="reprocess-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中重新处理对访问包的请求

作为访问包管理员，你可以随时使用重新处理功能自动重试用户对访问包的访问请求。 如果未成功配置用户对资源的访问，则重新处理可使用户无需重复访问包请求过程。

> [!NOTE]
> 最多可以在原始请求完成后的 14 天内重新处理请求。 对于超过 14 天前完成的请求，用户需要在 MyAccess 中取消并发出新请求。

本文介绍如何重新处理对现有访问包的请求。

## <a name="prerequisites"></a>必备条件

若要使用 Azure AD 权利管理并将用户分配到访问包，必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5 许可证

## <a name="open-an-existing-access-package-and-reprocess-user-requests"></a>打开现有的访问包并重新处理用户请求

必备角色：全局管理员、Identity Governance 管理员、用户管理员、目录所有者、访问包管理员或访问包分配管理员

如果有一组用户的请求处于“部分交付”或“失败”状态，你可能需要重新处理其中一些请求。 按照以下步骤重新处理现有访问包的请求：

1.  登录 [Azure 门户](https://portal.azure.com)。

1.  依次单击“Azure Active Directory”、“标识治理” 。

1.  在左侧菜单中单击“访问包”，然后打开访问包。

1.  在左侧的“管理”下，单击“请求” 。

1.  选择要重新处理其请求的所有用户。

1.  单击“重新处理”。

## <a name="next-steps"></a>后续步骤

- [查看访问包的请求](entitlement-management-access-package-requests.md)
- [批准或拒绝访问请求](entitlement-management-request-approve.md)