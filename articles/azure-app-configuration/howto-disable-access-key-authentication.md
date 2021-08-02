---
title: 对 Azure 应用程序配置实例禁用访问密钥身份验证（预览版）
titleSuffix: Azure App Configuration
description: 了解如何对 Azure 应用程序配置实例禁用访问密钥身份验证（预览版）
ms.service: azure-app-configuration
author: jimmyca15
ms.author: jimmyca
ms.topic: how-to
ms.date: 5/14/2021
ms.openlocfilehash: 451d9701b62cf46fe81bdd17f4eded63a38d1dce
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482306"
---
# <a name="disable-access-key-authentication-for-an-azure-app-configuration-instance-preview"></a>对 Azure 应用程序配置实例禁用访问密钥身份验证（预览版）

对 Azure 应用程序配置资源的每个请求必须经过身份验证。 默认情况下，可以使用 Azure Active Directory (Azure AD) 凭据或访问密钥对请求进行身份验证。 在这两种类型的身份验证方案中，Azure AD 提供的安全性和易用性要优于访问密钥，并且 Azure AD 是 Microsoft 推荐的身份验证方法。 若要要求客户端使用 Azure AD 对请求进行身份验证，可以禁止对 Azure 应用程序配置资源使用访问密钥。

对 Azure 应用程序配置资源禁用访问密钥身份验证时，会删除该资源的任何现有访问密钥。 使用以前存在的访问密钥向资源发起的任何后续请求将被拒绝。 只有那些使用 Azure AD 完成了身份验证的请求才会成功。 有关使用 Azure AD 的详细信息，请参阅[使用 Azure Active Directory 授权访问 Azure 应用程序配置](./concept-enable-rbac.md)。

## <a name="disable-access-key-authentication"></a>禁用访问密钥身份验证

禁用访问密钥身份验证会删除所有访问密钥。 如果任何运行中应用程序正在使用访问密钥进行身份验证，则在禁用访问密钥身份验证后，这些应用程序将开始失败。 再次启用访问密钥身份验证会生成一组新的访问密钥，任何尝试使用旧访问密钥的应用程序仍将失败。

> [!WARNING]
> 如果任何客户端当前正在使用访问密钥访问 Azure 应用程序配置资源中的数据，Microsoft 建议先将这些客户端迁移到 [Azure AD](./concept-enable-rbac.md)，然后再禁用访问密钥身份验证。
> 此外，建议阅读以下[限制](#limitations)部分，确认这些限制不会影响资源的预期使用。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中对 Azure 应用程序配置资源禁用访问密钥身份验证，请执行以下步骤：

1. 在 Azure 门户中导航到你的 Azure 应用程序配置资源。
2. 在“设置”下找到“访问密钥”设置 。

    :::image type="content" border="true" source="./media/access-keys-blade.png" alt-text="屏幕截图，显示如何访问 Azure 应用程序配置资源的访问密钥边栏选项卡":::

3. 将“启用访问密钥”切换开关设置为“已禁用” 。

    :::image type="content" border="true" source="./media/disable-access-keys.png" alt-text="屏幕截图，显示如何对 Azure 应用程序配置实例禁用访问密钥身份验证":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 禁用访问密钥身份验证的功能目前正在开发中。

---

### <a name="verify-that-access-key-authentication-is-disabled"></a>验证是否已禁用访问密钥身份验证

若要验证是否不再允许访问密钥身份验证，可以发出一个请求来列出 Azure 应用程序配置资源的访问密钥。 如果禁用了访问密钥身份验证，则不会列出任何访问密钥，即，列出操作将返回空列表。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中验证是否对某个 Azure 应用程序配置资源禁用了访问密钥身份验证，请执行以下步骤：

1. 在 Azure 门户中导航到你的 Azure 应用程序配置资源。
2. 在“设置”下找到“访问密钥”设置 。

    :::image type="content" border="true" source="./media/access-keys-blade.png" alt-text="屏幕截图，显示如何访问 Azure 应用程序配置资源的访问密钥边栏选项卡":::

3. 验证是否未显示任何访问密钥，并且“启用访问密钥”已切换为“已禁用” 。

    :::image type="content" border="true" source="./media/access-keys-disabled-portal.png" alt-text="屏幕截图，显示已对某个 Azure 应用程序配置资源禁用了访问密钥":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要在 Azure CLI 中验证是否对某个 Azure 应用程序配置资源禁用了访问密钥身份验证，请使用以下命令。 该命令将列出 Azure 应用程序配置资源的访问密钥；如果禁用了访问密钥身份验证，则列表将为空。

```azurecli-interactive
az appconfig credential list \
    --name <app-configuration-name> \
    --resource-group <resource-group>
```

如果禁用了访问密钥身份验证，则会返回空列表。

```
C:\Users\User>az appconfig credential list -g <resource-group> -n <app-configuration-name>
[]
```

---

## <a name="permissions-for-allowing-or-disallowing-access-key-authentication"></a>允许或禁止访问密钥身份验证时所需的权限

若要修改 Azure 应用程序配置资源的访问密钥身份验证状态，用户必须拥有 Azure 应用程序配置资源的创建和管理权限。 提供这些权限的 Azure 基于角色的访问控制 (Azure RBAC) 角色包括 Microsoft.AppConfiguration/configurationStores/write 或Microsoft.AppConfiguration/configurationStores/\* 操作 。 具有此操作的内置角色包括：

- Azure 资源管理器[所有者](../role-based-access-control/built-in-roles.md#owner)角色
- Azure 资源管理器[参与者](../role-based-access-control/built-in-roles.md#contributor)角色

这些角色不提供通过 Azure Active Directory (Azure AD) 访问 Azure 应用程序配置资源中的数据的权限。 但是，它们包括 Microsoft.AppConfiguration/configurationStores/listKeys/action 操作权限，而此权限可授予对资源访问密钥的访问权限。 有了此权限，用户就可以使用访问密钥来访问该资源中的所有数据。

角色分配的范围必须指定为 Azure 应用程序配置资源级别或更高级别，使用户能够允许或禁止对资源进行访问密钥身份验证。 有关角色作用域的详细信息，请参阅[了解 Azure RBAC 的作用域](../role-based-access-control/scope-overview.md)。

请慎重地将这些角色的分配限制为需要能够创建应用程序配置资源或更新其属性的用户。 使用最小特权原则确保用户拥有完成任务所需的最少权限。 有关使用 Azure RBAC 管理访问权限的详细信息，请参阅 [Azure RBAC 最佳做法](../role-based-access-control/best-practices.md)。

> [!NOTE]
> 经典订阅管理员角色“服务管理员”和“共同管理员”具有 Azure 资源管理器[所有者](../role-based-access-control/built-in-roles.md#owner)角色的等效权限。 “所有者”角色包括所有操作，因此具有这些管理角色之一的用户也可以创建和管理应用程序配置资源。 有关详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

## <a name="limitations"></a>限制

禁用访问密钥身份验证的功能以预览版提供。 目前存在以下限制。

### <a name="arm-template-access"></a>ARM 模板访问

禁用访问密钥身份验证时，也会禁用在 [ARM 模板](./quickstart-resource-manager.md)中读取/写入键-值的功能。 这是因为，访问 ARM 模板中使用的 Microsoft.AppConfiguration/configurationStores/keyValues 资源需有 Azure 资源管理器角色，例如参与者或所有者。 禁用访问密钥身份验证时，需有一个 Azure 应用程序配置[数据平面角色](concept-enable-rbac.md)才能访问资源，因此会拒绝访问 ARM 模板。

## <a name="next-steps"></a>后续步骤

- [使用客户管理的密钥加密应用程序配置数据](concept-customer-managed-keys.md)
- [为 Azure 应用配置使用专用终结点](concept-private-endpoint.md)