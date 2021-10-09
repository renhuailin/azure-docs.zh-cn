---
title: 以来宾用户身份离开组织 - Azure Active Directory
description: 演示 Azure AD B2B 来宾用户如何使用访问面板离开组织。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/10/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 547c27b8faf9f282c836bdda2d57bf9d13603423
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780692"
---
# <a name="leave-an-organization-as-a-guest-user"></a>以来宾用户身份离开组织

如果 Azure Active Directory (Azure AD) B2B 来宾用户不再需要使用来自某个组织的应用或保持任何关联，则可随时决定离开该组织。 用户可自行离开组织，无需联系管理员。

> [!NOTE]
> 如果来宾用户的帐户在家庭租户或资源租户中被禁用，则来宾用户不能离开组织。 如果来宾用户的帐户被禁用，则来宾用户需要联系租户管理员，租户管理员可以删除来宾帐户或启用来宾帐户，使该用户可以离开组织。

## <a name="leave-an-organization"></a>离开组织

若要离开组织，请执行以下步骤。

1. 执行以下操作之一，转到“我的帐户”页：
- 如果使用工作或学校帐户，请转到 https://myaccount.microsoft.com 并登录。
- 如果使用个人帐户，请转到 https://myapps.microsoft.com 并登录，单击右上角的帐户图标，然后选择“查看帐户”。 或者，使用包含租户信息的我的帐户 URL 直接转到“我的帐户”页面（以下注释中显示了示例）。  
   > [!NOTE]
   > 如果你在登录时使用电子邮件一次性密码功能，则需要使用包含你的租户名称或租户 ID 的我的帐户 URL，例如：`https://myaccount.microsoft.com?tenantId=wingtiptoys.onmicrosoft.com` 或 `https://myaccount.microsoft.com?tenantId=ab123456-cd12-ef12-gh12-ijk123456789`。

2. 在“组织”下，找到要离开的组织，然后选择“离开组织”   。

   ![显示用户界面中“离开组织”选项的屏幕截图](media/leave-the-organization/leave-org.png)
3. 系统要求确认时，请选择“离开”  。

> [!NOTE]
   > 无法离开本组织。

## <a name="account-removal"></a>删除帐户

当用户离开组织时，用户帐户会在目录中被“软删除”。 默认情况下，用户对象会移至 Azure AD 中的“删除的用户”区域，但在 30 天后才会永久删除  。 如果用户在 30 天内请求恢复帐户，管理员可借助此软删除功能恢复用户帐户（包括群组和权限）。

如果需要，租户管理员可以在 30 天期间内的任何时间永久删除帐户。 为此，请按以下步骤操作：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”  。
2. 在“管理”下，选择“用户”   。
3. 选择“删除的用户”  。
4. 选中删除的用户旁边的复选框，然后选择“永久删除”  。

如果永久删除用户，此操作不可撤销。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>后续步骤

- 有关 Azure AD B2B 的概述，请参阅[什么是 Azure AD B2B 协作？](what-is-b2b.md)



