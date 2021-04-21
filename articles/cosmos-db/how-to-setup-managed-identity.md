---
title: 使用 Azure AD 为 Azure Cosmos DB 帐户配置托管标识
description: 了解如何使用 Azure Active Directory 为 Azure Cosmos DB 帐户配置托管标识
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230817"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>使用 Azure Active Directory 为 Azure Cosmos DB 帐户配置托管标识
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 本文介绍如何为 Azure Cosmos DB 帐户创建托管标识。

> [!NOTE]
> Azure Cosmos DB 目前仅支持系统分配的托管标识。

## <a name="prerequisites"></a>必备条件

- 如果你不熟悉 Azure 资源托管标识，请参阅[什么是 Azure 资源托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。 若要了解托管标识类型，请参阅[托管标识类型](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)。
- 若要设置托管标识，你的帐户需有 [DocumentDB 帐户参与者角色](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)。

## <a name="add-a-system-assigned-identity"></a>添加系统分配的标识

### <a name="using-an-azure-resource-manager-arm-template"></a>使用 Azure 资源管理器 (ARM) 模板

> [!IMPORTANT]
> 使用托管标识时，请确保使用 `2021-03-15` 或更高的 `apiVersion`。

若要在新的或现有的 Azure Cosmos DB 帐户中启用系统分配的标识，请在资源定义中包含以下属性：

```json
"identity": {
    "type": "SystemAssigned"
}
```

然后，ARM 模板的 `resources` 节应如下所示：

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

创建或更新 Azure Cosmos DB 帐户后，它将显示以下属性：

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)
- 详细了解 [Azure Cosmos DB 上的客户管理的密钥](how-to-setup-cmk.md)
