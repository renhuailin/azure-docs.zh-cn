---
title: 在商业市场中管理租户 - Azure 市场
description: 了解如何在合作伙伴中心管理商业市场计划的租户。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 79120eb6d3d5aad9c27ed42bc825fdad4907af49
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144755"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>在商业市场中管理租户

**相应的角色**

- Manager

本文档中的 Azure Active Directory (AD) 租户（也称为工作帐户）是 Azure 门户中设置的组织的表示形式，可帮助你为内部和外部用户管理 Microsoft 云服务的特定实例。 如果你的组织已订阅 Microsoft 云服务（如 Azure、Microsoft Intune 或 Microsoft 365），则会为你建立 Azure AD 租户。

可以设置多个用于合作伙伴中心的租户。 如果你的公司有多个租户（例如 contoso.com、contoso.uk 等），则你可能需要执行此操作，你可以在此处链接其他租户。 此关联使你能够在商业市场帐户上添加和管理来自其他租户的用户。

具有合作伙伴中心帐户中的管理员角色的任何用户都可以在该帐户中添加和删除 Azure AD 租户。

## <a name="add-an-existing-tenant"></a>添加现有租户

若要将其他 Azure AD 租户与合作伙伴中心帐户关联，请执行以下操作：

1. 在合作伙伴中心的右上方，选择“设置” > “帐户设置” 。
1. 在“组织配置文件”下，选择“租户” 。 将显示当前租户关联。
1. 在“开发人员”选项卡上，选择“关联” 。
1. 输入要关联的租户的 Azure AD 凭据。
1. 查看 Azure AD 租户的组织名称和域名。 若要完成关联，请选择“确认”。

如果关联成功，即可在合作伙伴中心的“用户”部分中添加和管理帐户用户。 若要了解有关添加用户的信息，请参阅[管理用户](add-manage-users.md)。

## <a name="create-a-new-tenant"></a>创建新租户

若要使用合作伙伴中心帐户创建全新的 Azure AD 租户，请执行以下操作：

1. 在合作伙伴中心的右上方，选择“设置” > “帐户设置” 。
1. 在“组织配置文件”下，选择“租户” 。 将显示当前租户关联。
1. 在“开发人员”选项卡上，选择“创建”。
1. 为新的 Azure AD 输入目录信息：
    - **域名**：将用于 Azure AD 域的唯一名称，以及“.onmicrosoft.com”。 例如，如果输入了“example”，则 Azure AD 域为“example.onmicrosoft.com”。
    - 联系人电子邮件：电子邮件地址，我们可以在必要时就帐户相关事宜与你联系。
    - 全局管理员用户帐户信息：要用于新的全局管理员帐户的名字、姓氏、用户名和密码。
1. 选择“创建”以确认新域和帐户信息。
1. 使用新的 Azure AD 全局管理员用户名和密码登录以开始[添加和管理用户](add-manage-users.md)。

有关如何在 Azure 门户中（而不是通过合作伙伴中心门户）创建新租户的详细信息，请参阅[在 Azure Active Directory 中创建新租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文。

## <a name="remove-a-tenant"></a>删除租户

若要从合作伙伴中心帐户中删除租户，请在“租户”页（在“帐户设置”中）上查找其名称，然后选择“删除”  。 系统将提示你确认要删除该租户。 完成此操作后，该租户中的任何用户都将无法登录到合作伙伴中心帐户，并且将删除为这些用户配置的任何权限。

> [!TIP]
> 如果你当前使用某个租户中的帐户登录到合作伙伴中心，则无法删除该租户。 若要删除租户，必须以与帐户关联的其他租户的管理员身份登录到合作伙伴中心。 如果只有一个租户与帐户相关联，则只能在使用打开帐户所用的 Microsoft 帐户登录后删除该租户。