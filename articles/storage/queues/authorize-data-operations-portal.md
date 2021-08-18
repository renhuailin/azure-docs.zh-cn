---
title: 选择如何在 Azure 门户中授予对队列数据的访问权限
titleSuffix: Azure Storage
description: 使用 Azure 门户访问队列数据时，门户会在后台对 Azure 存储发出请求。 可以使用 Azure AD 帐户或存储帐户访问密钥对这些 Azure 存储请求进行身份验证和授权。
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 06/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: da6dfb018885ce55323e4a05e738501692c68a58
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727969"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>选择如何在 Azure 门户中授予对队列数据的访问权限

使用 [Azure 门户](https://portal.azure.com)访问队列数据时，门户会在后台对 Azure 存储发出请求。 可以使用 Azure AD 帐户或存储帐户访问密钥对 Azure 存储请求进行授权。 门户会指示使用的是哪种方法，如果你有相应的权限，则门户还允许在这两种方法之间切换。

## <a name="permissions-needed-to-access-queue-data"></a>访问队列数据所需的权限

视你要如何在 Azure 门户中授予对队列数据的访问权限而定，你将需要特定权限。 在大多数情况下，这些权限是通过 Azure 基于角色的访问控制 (Azure RBAC) 提供的。 有关 Azure RBAC 的详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)。

### <a name="use-the-account-access-key"></a>使用帐户访问密钥

若要使用帐户访问密钥访问队列数据，你必须已分配到一个 Azure 角色，此角色包含 Azure RBAC 操作 **Microsoft.Storage/storageAccounts/listkeys/action**。 此 Azure 角色可以是内置角色，也可以是自定义角色。 支持“Microsoft.Storage/storageAccounts/listkeys/action”的内置角色（按权限从小到大排序）包括：

- [读取者和数据访问](../../role-based-access-control/built-in-roles.md#reader-and-data-access)角色
- [存储帐户参与者角色](../../role-based-access-control/built-in-roles.md#storage-account-contributor)
- Azure 资源管理器[参与者角色](../../role-based-access-control/built-in-roles.md#contributor)
- Azure 资源管理器[所有者角色](../../role-based-access-control/built-in-roles.md#owner)

尝试在 Azure 门户中访问队列数据时，门户首先会检查你是否被分配了一个包含 **Microsoft.Storage/storageAccounts/listkeys/action** 的角色。 如果你被分配了包含此操作的角色，则门户将使用帐户密钥来访问队列数据。 如果你不拥有包含此操作的角色，则门户会尝试使用你的 Azure AD 帐户访问数据。

> [!IMPORTANT]
> 在使用 Azure 资源管理器 ReadOnly 锁锁定了某个存储帐户时，不允许为该存储帐户执行[列出密钥](/rest/api/storagerp/storageaccounts/listkeys)操作。 “列出密钥”是 POST 操作，并且在为该帐户配置了 ReadOnly 锁时，所有的 POST 操作都会被阻止 。 因此，当帐户被 ReadOnly 锁锁定时，用户必须使用 Azure AD 凭据访问门户中的队列数据。 若要了解如此使用 Azure AD 访问门户中的队列数据，请参阅[使用 Azure AD 帐户](#use-your-azure-ad-account)。

> [!NOTE]
> 经典订阅管理员角色“服务管理员”和“共同管理员”具有 Azure 资源管理器[`Owner`](../../role-based-access-control/built-in-roles.md#owner)角色的等效权限 。 “所有者”角色包含所有操作，其中包括 **Microsoft.Storage/storageAccounts/listkeys/action**，因此，拥有其中一种管理角色的用户也可以使用帐户密钥访问队列数据。 有关详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

### <a name="use-your-azure-ad-account"></a>使用 Azure AD 帐户

若要使用 Azure AD 帐户从 Azure 门户访问队列数据，必须符合以下条件：

- 拥有一个可提供队列数据访问权限的内置角色或自定义角色。
- 至少拥有 Azure 资源管理器[读取者](../../role-based-access-control/built-in-roles.md#reader)角色，该角色的权限范围为存储帐户或更高级别。 “读取者”角色授予限制性最高的权限，但也接受可授予存储帐户管理资源访问权限的其他 Azure 资源管理器角色。

Azure 资源管理器“读取者”角色允许用户查看存储帐户资源，但不允许修改这些资源。 该角色不提供对 Azure 存储中的数据的读取权限，而只提供对帐户管理资源的读取权限。 “读取者”角色是用户导航到 Azure 门户中的队列所需的。

有关支持访问队列数据的内置角色的信息，请参阅[使用 Azure Active Directory 授权访问队列](authorize-access-azure-active-directory.md)。

自定义角色能够支持内置角色所提供的相同权限的不同组合。 若要详细了解如何创建 Azure 自定义角色，请参阅 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)和[了解 Azure 资源的角色定义](../../role-based-access-control/role-definitions.md)。

> [!NOTE]
> Azure 门户中存储资源管理器的预览版不支持使用 Azure AD 凭据来查看和修改队列数据。 Azure 门户中的存储资源管理器始终使用帐户密钥来访问数据。 若要在 Azure 门户中使用存储资源管理器，你必须被分配一个包含 **Microsoft.Storage/storageAccounts/listkeys/action** 的角色。

## <a name="navigate-to-queues-in-the-azure-portal"></a>在 Azure 门户中导航到队列

若要在门户中查看队列数据，请导航到存储帐户的“概述”，然后单击“队列”对应的链接。  或者，可以在菜单中导航到“队列服务”部分。

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="显示如何在 Azure 门户中导航到队列数据的屏幕截图":::

## <a name="determine-the-current-authentication-method"></a>确定当前的身份验证方法

导航到队列时，Azure 门户会指示当前是使用帐户访问密钥还是使用 Azure AD 帐户进行身份验证。

### <a name="authenticate-with-the-account-access-key"></a>使用帐户访问密钥进行身份验证

如果使用帐户访问密钥进行身份验证，则会在门户中看到“访问密钥”已指定为身份验证方法：

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="显示用户当前正在使用帐户密钥访问队列的屏幕截图":::

若要改用 Azure AD 帐户，请单击图中突出显示的链接。 如果你通过分配给你的 Azure 角色获得了相应的权限，则可以继续访问。 但是，如果你缺少相应的权限，则会看到如下所示的错误消息：

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Azure AD 帐户不支持访问时显示的错误":::

请注意，如果你的 Azure AD 帐户缺少队列查看权限，则列表中不会显示任何队列。 单击“切换为访问密钥”链接，以再次使用访问密钥进行身份验证。

### <a name="authenticate-with-your-azure-ad-account"></a>使用 Azure AD 帐户进行身份验证

如果使用 Azure AD 帐户进行身份验证，则会在门户中看到“Azure AD 用户帐户”已指定为身份验证方法：

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="显示用户当前正在使用 Azure AD 访问队列的屏幕截图":::

若要改用帐户访问密钥，请单击图中突出显示的链接。 如果你有权访问帐户密钥，则可以继续访问。 但是，如果你缺少帐户密钥的访问权限，则 Azure 门户会显示一条错误消息。

如果你无权访问帐户密钥，则门户中不会列出队列。 单击“切换为 Azure AD 用户帐户”链接，以再次使用 Azure AD 帐户进行身份验证。

## <a name="next-steps"></a>后续步骤

- [授权访问 Azure 存储中的数据](../common/authorize-data-access.md)
- [分配用于访问队列数据的 Azure 角色](assign-azure-role-data-access.md)
