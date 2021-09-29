---
title: 在 Azure 门户中创建和删除 Azure AD B2C 使用者用户帐户
description: 了解如何使用 Azure 门户在 Azure AD B2C 目录中创建和删除使用者用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 02c0197af40854bfbcff04401dbc4e9ca0d990dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573890"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>使用 Azure 门户在 Azure AD B2C 中创建和删除使用者用户

某些情况下，可能需要在 Azure Active Directory B2C (Azure AD B2C) 目录中手动创建所有者帐户。 虽然最常见的情况下，Azure AD B2C 目录中的使用者帐户是在用户注册使用你的应用程序之一时创建的，但你可以通过编程方式和 Azure 门户创建它们。 本文重点介绍如何通过 Azure 门户方法来创建和删除用户。

若要添加或删除用户，必须为帐户分配“用户管理员”或“全局管理员”角色。

## <a name="types-of-user-accounts"></a>用户帐户的类型

根据 [Azure AD B2C 中的用户帐户概述](user-overview.md)中的介绍，有三种类型的用户帐户可以在 Azure AD B2C 目录中创建：

* 工作
* 来宾
* 使用者

本文重点介绍如何在 Azure 门户中使用 **使用者帐户**。 若要了解如何创建和删除工作帐户和来宾帐户，请参阅[使用 Azure Active Directory 添加或删除用户](../active-directory/fundamentals/add-users-azure-active-directory.md)。

## <a name="create-a-consumer-user"></a>创建使用者用户

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 在“管理”下，选择“用户” 。
1. 选择“新建用户”。
1. 选择“创建 Azure AD B2C 用户”。
1. 选择“登录方法”，然后输入新用户的 **电子邮件** 地址或 **用户名**。 在此处选择的登录方法必须与你为 Azure AD B2C 租户的“本地帐户”标识提供者指定的设置匹配（请参阅 Azure AD B2C 租户中的“管理” > “标识提供者”）。
1. 输入该用户的“名称”。 该名称通常是用户的完整名称（姓和名）。
1. （可选）如果希望延迟用户登录，可以选择“阻止登录”。 可以稍后在 Azure 门户中通过编辑用户的“配置文件”来启用登录。
1. 选择“自动生成密码”或“让我创建密码”。 
1. 指定用户的 **名字** 和 **姓氏**。
1. 选择“创建”  。

除非你已选择“阻止登录”，否则用户现在就可以使用你指定的登录方法（电子邮件或用户名）登录。

## <a name="reset-a-users-password"></a>重置用户密码

管理员可以重置用户的密码（如果用户忘记了密码）。 重置用户的密码时，将自动为用户自动生成一个临时密码。 临时密码永不过期。 用户下次登录时，无论自生成临时密码以来已经过了多长时间，密码仍然有效。 然后，用户必须将密码重置为永久密码。 

> [!IMPORTANT]
> 在重置用户的密码之前，请[在 Azure Active Directory B2C 中设置一个强制密码重置流](force-password-reset.md)，否则用户将无法登录。

重置用户的密码：

1. 在 Azure AD B2C 目录中，选择“用户”，然后选择要重置密码的用户。
1. 搜索并选择需要重置的用户，然后选择“重置密码”。

    将显示“Alain Charon - 配置文件”页面，其中包含“重置密码”选项。

    ![用户的配置文件页面，其中突出显示“重置密码”选项](media/manage-users-portal/user-profile-reset-password-link.png)

1. 在“重置密码”页面中，选择“重置密码”。
1. 复制密码并将其提供给用户。 在下次登录过程中，用户需要更改密码。


## <a name="delete-a-consumer-user"></a>删除使用者用户

1. 在 Azure AD B2C 目录中选择“用户”，然后选择要删除的用户。
1. 选择“删除”，然后选择“是”，确认进行删除。

若要详细了解如何在删除后的头 30 天内还原用户或永久删除用户，请参阅[使用 Azure Active Directory 还原或删除最近删除的用户](../active-directory/fundamentals/active-directory-users-restore.md)。

## <a name="next-steps"></a>后续步骤

有关自动用户管理方案（例如，将用户从其他标识提供者迁移到 Azure AD B2C 目录），请参阅 [Azure AD B2C：用户迁移](user-migration.md)。
