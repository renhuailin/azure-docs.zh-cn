---
title: 关闭非托管 Azure AD 组织中的工作或学校帐户
description: 如何在非托管 Azure Active Directory 中关闭工作或学校帐户。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/03/2020
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 252c993f5679d80cda97a6fade08f5f92489840d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575578"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>关闭非托管 Azure AD 组织中的工作或学校帐户

如果你是非托管 Azure Active Directory (Azure AD) 组织中的用户，并且不再需要使用该组织中的应用或与该组织保持任何关联，则可以随时关闭你的帐户。 非托管组织没有全局管理员。 非托管组织中的用户可以自行关闭自己的帐户，而无需联系管理员。

非托管组织中的用户通常是在自助注册期间创建的。 例如，组织中的信息工作者可以注册免费服务。 有关自助注册的详细信息，请参阅[什么是 Azure Active Directory 的自助注册？](directory-self-service-signup.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>开始之前

在关闭帐户之前，应确认以下事项：

* 确保你是非托管 Azure AD 组织的用户。 如果你属于托管组织，则无法关闭你的帐户。 如果你属于托管组织，但想要关闭你的帐户，则必须与你的管理员联系。 若要了解如何确定你是否属于非托管组织，请参阅[从非托管租户中删除用户](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)。

* 保存想要保留的任何数据。 若要了解如何提交导出请求，请参阅[访问和导出非托管租户的系统生成日志](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)。

> [!WARNING]
> 关闭帐户的操作不可逆。 关闭帐户时，所有个人数据都会被删除。 你将无法再访问你的帐户以及与该帐户关联的数据。

## <a name="close-your-account"></a>关闭你的帐户

若要关闭非托管工作或学校帐户，请执行以下步骤：

1. 使用要关闭的帐户登录以[关闭帐户](https://go.microsoft.com/fwlink/?linkid=873123)。

1. 在“我的数据请求”中，选择“关闭帐户”。

    ![我的数据请求 - 关闭帐户](./media/users-close-account/close-account.png)

1. 查看确认消息，然后选择“是”。

    ![我的数据请求 - 确认关闭](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Active Directory 的自助注册？](directory-self-service-signup.md)
- [从非托管租户删除用户](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [访问和导出非托管租户的系统生成日志](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)