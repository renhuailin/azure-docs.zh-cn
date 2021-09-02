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
ms.date: 05/04/2021
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14a2dea0445a866f7c352eedf64d631d83b1b0f0
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864405"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>关闭非托管 Azure AD 组织中的工作或学校帐户

如果你是非托管 Azure Active Directory (Azure AD) 组织中的用户，并且不再需要使用该组织中的应用或与该组织保持任何关联，则可以随时关闭你的帐户。 非托管组织没有全局管理员。 非托管组织中的用户可以自行关闭自己的帐户，而无需联系管理员。

非托管组织中的用户通常是在自助注册期间创建的。 例如，组织中的信息工作者可以注册免费服务。 有关自助注册的详细信息，请参阅[什么是 Azure Active Directory 的自助注册？](directory-self-service-signup.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>开始之前

在关闭帐户之前，应确认以下事项：

* 确保你是非托管 Azure AD 组织的用户。 如果你属于托管组织，则无法关闭你的帐户。 如果你属于托管组织，但想要关闭你的帐户，则必须与你的管理员联系。 若要了解如何确定你是否属于非托管组织，请参阅[从非托管租户中删除用户](/power-automate/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)。

* 保存想要保留的任何数据。 若要了解如何提交导出请求，请参阅[访问和导出非托管租户的系统生成日志](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)。

> [!WARNING]
> 关闭帐户的操作不可逆。 关闭帐户时，所有个人数据都会被删除。 你将无法再访问你的帐户以及与该帐户关联的数据。

## <a name="close-your-account"></a>关闭你的帐户

若要关闭非托管工作或学校帐户，请执行以下步骤：

1. 使用要关闭的帐户登录以[关闭帐户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/PrivacyDataRequests)。

1. 在“我的数据请求”中，选择“关闭帐户”。

    ![我的数据请求 - 关闭帐户](./media/users-close-account/close-account.png)

1. 查看确认消息，然后选择“是”。

    ![我的数据请求 - 确认关闭](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Active Directory 的自助注册？](directory-self-service-signup.md)
- [从非托管租户删除用户](/power-automate/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [访问和导出非托管租户的系统生成日志](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)