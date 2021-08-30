---
title: Azure SQL 的 Azure Active Directory 中的目录读取者角色
description: 了解 Azure SQL 的 Azure AD 中的目录读取者角色。
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 07/30/2021
ms.openlocfilehash: 738338c4f3931e97965e697ca1bf316018a495c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735591"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Azure SQL 的 Azure Active Directory 中的目录读取者角色

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Active Directory (Azure AD) 推出了[使用 Azure AD 组来管理角色分配](../../active-directory/roles/groups-concept.md)的功能。 这可以实现向组分配 Azure AD 角色。

为 Azure SQL 数据库、Azure SQL 托管实例或 Azure Synapse Analytics 启用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)时，必须向标识分配 Azure AD [目录读取者](../../active-directory/roles/permissions-reference.md#directory-readers)角色，以实现对 [Azure AD 图形 API](/graph/migrate-azure-ad-graph-planning-checklist) 的读取访问。 SQL 数据库和 Azure Synapse 的托管标识称为服务器标识。 SQL 托管实例的托管标识称为托管实例标识，在创建实例时会自动分配。 有关向 SQL 数据库或 Azure Synapse 分配服务器标识的详细信息，请参阅[启用服务主体以创建 Azure AD 用户](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)。

目录读取者角色是执行以下操作所必需的：

- 为 SQL 托管实例创建 Azure AD 登录名
- 在 Azure SQL 中模拟 Azure AD 用户
- 将使用 Windows 身份验证的 SQL Server 用户迁移到使用 Azure AD 身份验证的 SQL 托管实例（使用 [ALTER USER (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) 命令）
- 更改 SQL 托管实例的 Azure AD 管理员
- 允许[服务主体（应用程序）](authentication-aad-service-principal.md)在 Azure SQL 中创建 Azure AD 用户

## <a name="assigning-the-directory-readers-role"></a>分配目录读取者角色

为了将[目录读取者](../../active-directory/roles/permissions-reference.md#directory-readers)角色分配给标识，需要一个具有[全局管理员](../../active-directory/roles/permissions-reference.md#global-administrator)或[特权角色管理员](../../active-directory/roles/permissions-reference.md#privileged-role-administrator)权限的用户。 经常管理或部署 SQL 数据库、SQL 托管实例或 Azure Synapse 的用户可能无法访问这些高特权角色。 如果用户创建了计划外 Azure SQL 资源，或者如果用户需要高特权角色成员的帮助，而在大型组织中经常无法接触到这些成员，这往往会带来麻烦。

对于 SQL 托管实例，必须先将目录读取者角色分配给托管实例标识，然后才能[为托管实例设置 Azure AD 管理员](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。 

为逻辑服务器设置 Azure AD 管理员时，SQL 数据库或 Azure Synapse 不需要向服务器标识分配目录读取者角色。 但是，若要在 SQL 数据库或 Azure Synapse 中代表 Azure AD 应用程序启用 Azure AD 对象创建，则需要目录读取者角色。 如果未将该角色分配给 SQL 逻辑服务器标识，则在 Azure SQL 中创建 Azure AD 用户将失败。 有关详细信息，请参阅[使用 Azure SQL 的 Azure Active Directory 服务主体](authentication-aad-service-principal.md)。

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>向 Azure AD 组授予目录读取者角色

可以让[全局管理员](../../active-directory/roles/permissions-reference.md#global-administrator)或[特权角色管理员](../../active-directory/roles/permissions-reference.md#privileged-role-administrator)创建一个 Azure AD 组，并为该组分配[目录读取者](../../active-directory/roles/permissions-reference.md#directory-readers)权限。 这将允许该组的成员访问 Azure AD 图形 API。 此外，作为该组所有者的 Azure AD 用户可以为该组分配新成员，包括 Azure SQL 逻辑服务器的标识。

此解决方案仍要求高特权用户（全局管理员或特权角色管理员）一次性创建组和分配用户，但 Azure AD 组所有者能够在以后分配其他成员。 这样，以后就不必需要高特权用户在其 Azure AD 租户中配置所有 SQL 数据库、SQL 托管实例或 Azure Synapse 服务器。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：将目录读取者角色分配到 Azure AD 组并管理角色分配](authentication-aad-directory-readers-role-tutorial.md)