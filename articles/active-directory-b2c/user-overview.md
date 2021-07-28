---
title: Azure Active Directory B2C 中的用户帐户概述
description: 了解可在 Azure Active Directory B2C 中使用的用户帐户类型。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 4b35cfeded13a50e5e27c240b0826f1d108ff7eb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529438"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的用户帐户概述

在 Azure Active Directory B2C (Azure AD B2C) 中，可以创建多种类型的帐户。 Azure Active Directory、Active Directory B2B 和 Active Directory B2C 共享可使用的用户帐户类型。

可以使用以下类型的帐户：

- 工作帐户 - 工作帐户可以访问租户中的资源，并且通过管理员角色可以管理租户。
- 来宾帐户 - 来宾帐户只能是一个 Microsoft 帐户或可用于访问应用程序或管理租户的 Azure Active Directory 用户。
- **使用者帐户** - 使用者帐户由已向 Azure AD B2C 注册的应用程序的用户使用。 可以通过以下方式创建使用者帐户：
  - 在 Azure AD B2C 应用程序中通过注册用户流程的用户
  - 使用 Microsoft Graph API
  - 使用 Azure 门户

## <a name="work-account"></a>工作帐户

基于 Azure AD 的所有租户以相同的方式创建工作帐户。 若要创建工作帐户，可以使用[快速入门：向 Azure Active Directory 添加新用户](../active-directory/fundamentals/add-users-azure-active-directory.md)中的信息。 使用 Azure 门户中的“新建用户”选项创建工作帐户。

添加新工作帐户时，需要考虑以下配置设置：

- “名称”和“用户名” - Name 属性包含用户的名和姓。 用户名是用户登录时输入的标识符。 用户名包括完整域。 用户名的域名部分必须是初始默认域名“your-domain.onmicrosoft.com”，或已验证的非联合[自定义域名](../active-directory/fundamentals/add-custom-domain.md)（例如“contoso.com”）。 
- **电子邮件** - 新用户也可以使用电子邮件地址登录。 在电子邮件中，不支持特殊字符或多字节字符，例如日语字符。
- 配置文件 - 通过用户数据的配置文件设置帐户。 可以输入名字、姓氏、职务和部门名称。 可以在创建帐户后编辑配置文件。
- 组 - 使用组执行管理任务，例如一次为多个用户或设备分配许可证或权限。 可以将新帐户放入租户现有的[组](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)中。
- 目录角色 - 需要指定用户帐户对租户中资源的访问级别。 可以使用以下权限级别：

    - 用户 - 用户可以访问分配的资源，但不能管理大多数租户资源。
    - 全局管理员 - 全局管理员可以完全控制所有租户资源。
    - 受限的管理员 - 为用户选择一个或多个管理角色。 有关可以选择的角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../active-directory/roles/permissions-reference.md)。

### <a name="create-a-work-account"></a>创建工作帐户

可以使用以下信息创建新的工作帐户：

- [Azure 门户](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>更新用户配置文件

可以使用以下信息来更新用户的配置文件：

- [Azure 门户](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>重置用户密码

可以使用以下信息来重置用户密码：

- [Azure 门户](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>来宾用户

可以邀请外部用户作为来宾用户访问租户。 邀请来宾用户访问 Azure AD B2C 租户的典型方案是共享管理职责。 有关使用来宾帐户的示例，请参阅 [Azure Active Directory B2B 协作用户的属性](../active-directory/external-identities/user-properties.md)。

在邀请来宾用户访问租户时，你需要提供收件人的电子邮件地址以及描述邀请的消息。 邀请链接会将用户带到同意页面。 如果未将收件箱附加到电子邮件地址，则用户可以通过使用受邀凭据转到 Microsoft 页面来导航到同意页面。 然后，强制用户兑换邀请，方法就是单击电子邮件中的链接。 例如：`https://myapps.microsoft.com/B2CTENANTNAME`。

还可以使用 [Microsoft Graph API](/graph/api/invitation-post) 来邀请来宾用户。

## <a name="consumer-user"></a>使用者用户

使用者用户可以登录受 Azure AD B2C 保护的应用程序，但无法访问 Azure 门户等 Azure 资源。 使用者用户可以使用本地帐户或联合帐户，例如 Facebook 或 Twitter。 通过使用[注册或登录用户流](user-flow-overview.md)、使用 Microsoft Graph API 或使用 Azure 门户创建使用者帐户。

可以指定在创建使用者用户帐户时收集的数据。 有关详细信息，请参阅[添加用户属性和自定义用户输入](configure-user-input.md)。

有关管理使用者帐户的详细信息，请参阅[使用 Microsoft Graph 管理 Azure AD B2C 用户帐户](./microsoft-graph-operations.md)。

### <a name="migrate-consumer-user-accounts"></a>迁移使用者用户帐户

可能需要将现有的使用者用户帐户从任何标识提供者迁移到 Azure AD B2C。 有关详细信息，请参阅[将用户迁移到 Azure AD B2C](user-migration.md)。
