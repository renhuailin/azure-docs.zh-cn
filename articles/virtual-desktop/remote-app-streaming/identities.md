---
title: 在 Azure 虚拟桌面中设置托管标识 - Azure
description: 如何在 Azure 虚拟桌面中使用 Azure AD、Azure AD DS 或 AD DS 为客户设置托管标识。
author: Heidilohr
ms.topic: how-to
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 98dcd68e62f04eaf9ae439389cb31e281cdb2f69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777928"
---
# <a name="set-up-managed-identities"></a>设置托管标识

由于 Azure 虚拟桌面当前不支持外部配置文件或“标识”，用户无法使用自己的公司凭据访问你托管的应用。 因此，你需要在用于远程应用流式处理的 Active Directory 域中为用户创建标识，并将用户对象同步到关联的 Azure Active Directory (Azure AD) 租户。

在本文中，我们将介绍如何管理用户标识，以便为客户提供安全的环境。 我们还将介绍构成标识的各个部分。

## <a name="requirements"></a>要求

你创建的标识需要遵循以下准则：

- 标识必须是[混合标识](../../active-directory/hybrid/whatis-hybrid-identity.md)，这意味着它们同时存在于 [Active Directory (AD)](/previous-versions/windows/it-pro/windows-server-2003/cc781408(v=ws.10)) 和 [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 中。 你可以使用 [Active Directory 域服务 (AD DS)](/windows-server/identity/ad-ds/active-directory-domain-services) 或 [Azure Active Directory 域服务 (Azure AD DS)](https://azure.microsoft.com/services/active-directory-ds) 来创建这些标识。 若要详细了解每种方法，请参阅[比较标识解决方案](../../active-directory-domain-services/compare-identity-solutions.md)。
- 你应该将来自不同组织的用户保留在独立的 Azure AD 租户中，以防止出现安全违规事件。 我们建议为每个客户组织创建一个 Active Directory 域和 Azure Active Directory 租户。 该租户应当有自己的专用于该客户的关联 Azure AD DS 订阅或 AD DS 订阅。

以下两部分将介绍如何通过 AD DS 和 Azure AD DS 创建标识。 若要遵循[适用于跨组织应用的安全准则](security.md)，你需要为每个客户重复执行此过程。

## <a name="managing-users-with-active-directory-domain-services"></a>通过 Active Directory 域服务管理用户

在此方法中，你将使用 Active Directory 域控制器设置混合标识，以管理用户标识并将其同步到 Azure AD。

此方法涉及设置 Active Directory 域控制器来管理用户标识，以及将用户同步到 Azure AD 以创建混合标识。 然后可以使用这些标识来访问 Azure 虚拟桌面中的托管应用程序。 在此配置中，用户将从 Active Directory 同步到Azure AD，而会话主机 VM 将联接到 AD DS 域。

若要在 AD DS 中设置标识，请执行以下操作：

1. 为你的客户[创建 Azure AD 租户](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)和订阅。

2. 在用于客户的 Windows Server 虚拟机 (VM) 上[安装 Active Directory 域服务](/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-)。

3. 在已联接到域的单独 VM 上安装并配置 [Azure AD Connect](../../active-directory/hybrid/how-to-connect-install-roadmap.md)，以将用户帐户从 Active Directory 同步到 Azure Active Directory。

4. 如果你计划使用 Intune 来管理 VM，请通过 Azure AD Connect 启用[已联接到混合 Azure AD 的设备](../../active-directory/devices/hybrid-azuread-join-plan.md)。

5. 配置环境后，请在 Active Directory 中[创建新用户](/previous-versions/windows/it-pro/windows-server-2003/cc755607(v=ws.10))。 这些用户会自动与 Azure AD 同步。

6. 在主机池中部署会话主机时，请使用 Active Directory 域名来联接 VM，并确保会话主机可以看到域控制器。

此配置可让你更好地控制环境，但其复杂性可能会导致不容易管理。 但是，使用此选项，你可以为用户提供基于 Azure AD 的应用。 它还允许你使用 Intune 管理用户的 VM。

## <a name="managing-users-with-azure-active-directory-domain-services"></a>通过 Azure Active Directory 域服务管理用户

Azure AD DS 标识存储在 Microsoft 托管 Active Directory 平台即服务 (PaaS) 中。在此平台中，Microsoft 管理着两个 AD 域控制器，该控制器允许用户在其 Azure 订阅内使用 AD DS。 在此配置中，用户将从 Active Directory 同步到 Azure AD DS，而会话主机将被联接到 Azure AD DS 域。 Azure AD DS 标识更容易管理，但没有提供像常规 AD DS 标识那么多的控制。 只能将 Azure 虚拟桌面 VM 联接到 Azure AD DS 域，并且无法通过 Intune 管理它们。

若要通过 Azure AD DS 创建标识，请执行以下操作：

1. 为你的客户[创建 Azure AD 租户](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)和订阅。

2. 在用户的订阅中[部署 Azure AD 目录服务](../../active-directory-domain-services/tutorial-create-instance.md)。

3. 完成环境配置后，请在 Azure Active Directory 中[创建新用户](../../active-directory/fundamentals/add-users-azure-active-directory.md)。 这些用户对象会自动与 Azure AD DS 同步。

4. 在主机池中部署会话主机时，请使用 Azure AD DS 域名来联接 VM。

## <a name="next-steps"></a>后续步骤

若要详细了解设置标识和租户时的安全注意事项，请参阅[适用于跨组织应用的安全准则](security.md)。
