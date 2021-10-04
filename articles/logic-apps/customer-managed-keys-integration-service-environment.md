---
title: 设置客户管理的密钥以加密 ISE 中的静态数据
description: 在 Azure 逻辑应用中创建并管理自己的加密密钥，以保护集成服务环境 (ISE) 中的静态数据
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, azla
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: db99be325d50f89e6e1c96c1471431815b98936d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824303"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>在 Azure 逻辑应用中设置客户管理的密钥以加密集成服务环境 (ISE) 的静态数据

Azure 逻辑应用依赖 Azure 存储来存储和自动[加密静态数据](../storage/common/storage-service-encryption.md)。 此加密可保护数据，并帮助你履行组织的安全性和符合性承诺。 默认情况下，Azure 存储使用 Microsoft 管理的密钥来加密数据。 有关 Azure 存储加密工作原理的详细信息，请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)和 [Azure 静态数据加密](../security/fundamentals/encryption-atrest.md)。

当你创建用于托管逻辑应用的[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 并希望更好地控制 Azure 存储使用的加密密钥时，可以通过 [Azure 密钥保管库](../key-vault/general/overview.md)设置、使用和管理自己的密钥。 此功能称为“创建自己的密钥”(BYOK)，该密钥称为“客户管理的密钥”。 借助此功能，Azure 存储可以[使用平台管理的密钥自动对你的密钥启用双重加密或基础结构加密](../security/fundamentals/double-encryption.md)。 有关详细信息，请参阅[通过基础结构加密对数据进行双重加密](../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)。

本主题将介绍如何通过逻辑应用 REST API，设置和指定你在创建 ISE 时要使用的自己的加密密钥。 有关通过逻辑应用 REST API 创建 ISE 的一般步骤，请参阅[使用逻辑应用 REST API 创建集成服务环境 (ISE)](../logic-apps/create-integration-service-environment-rest-api.md)。

## <a name="considerations"></a>注意事项

* 目前，只有以下区域为 ISE 提供客户管理的密钥支持：

  * Azure：美国西部 2、美国东部和美国中南部。

  * Azure 政府：亚利桑那州、弗吉尼亚州和德克萨斯州。

* 只能在创建 ISE 时指定客户管理的密钥，事后无法指定。 创建 ISE 之后无法禁用此密钥。 目前不支持为 ISE 轮换客户管理的密钥。

* 存储客户管理的密钥的密钥保管库必须与 ISE 位于同一 Azure 区域。

* 为了支持客户管理的密钥，ISE 要求启用[系统分配的或用户分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)。 ISE 通过此标识验证对位于或连接到 Azure 虚拟网络中的受保护资源（例如虚拟机和其他系统或服务）的访问权限。 这样，无需使用凭据即可登录。

* 目前，若要创建支持客户管理的密钥并已启用托管标识类型的 ISE，必须使用 HTTPS PUT 请求调用逻辑应用 REST API。

* 必须[向 ISE 的托管标识授予密钥保管库访问权限](#identity-access-to-key-vault)，但授予权限的时机取决于所用的托管标识。

  * 系统分配的托管标识：在发送创建 ISE 的 HTTPS PUT 请求后的 30 分钟内，必须[向 ISE 的托管标识授予密钥保管库访问权限](#identity-access-to-key-vault)。 否则 ISE 创建将会失败，并出现权限错误。

  * 用户分配的托管标识：在发送创建 ISE 的 HTTPS PUT 请求之前，[向 ISE 的托管标识授予密钥保管库访问权限](#identity-access-to-key-vault)。

## <a name="prerequisites"></a>先决条件

* 为 ISE 启用访问权限的[先决条件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和[要求](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)与在 Azure 门户中创建 ISE 时相同

* 一个已启用“软删除”和“不清除”属性的 Azure 密钥保管库 

  有关启用这些属性的详细信息，请参阅 [Azure 密钥保管库软删除概述](../key-vault/general/soft-delete-overview.md)和[在 Azure 密钥保管库中配置客户管理的密钥](../storage/common/customer-managed-keys-configure-key-vault.md)。 如果你不熟悉 [Azure 密钥保管库](../key-vault/general/overview.md)，请了解如何使用 [Azure 门户](../key-vault/general/quick-create-portal.md)、[Azure CLI](../key-vault/general/quick-create-cli.md) 或 [Azure PowerShell](../key-vault/general/quick-create-powershell.md) 创建密钥保管库。

* 在你的密钥保管库中有一个使用以下属性值创建的密钥：

  | 属性 | 值 |
  |----------|-------|
  | **键类型** | RSA |
  | RSA 密钥大小 | 2048 |
  | **已启用** | 是 |
  |||

  ![创建客户管理的加密密钥](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  有关详细信息，请参阅[在 Azure 密钥保管库中配置客户管理的密钥](../storage/common/customer-managed-keys-configure-key-vault.md)或 Azure PowerShell 命令 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。

* 通过使用 HTTPS PUT 请求调用逻辑应用 REST API 来创建 ISE 的一种工具。 例如，你可以使用 [Postman](https://www.getpostman.com/downloads/)，也可以生成一个用于执行此任务的逻辑应用。

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>创建包含密钥保管库并支持托管标识的 ISE

若要通过调用逻辑应用 REST API 来创建 ISE，请发出以下 HTTPS PUT 请求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 逻辑应用 REST API 2019-05-01 版本要求你对 ISE 连接器自行发出 HTTPS PUT 请求。

通常，部署过程在两个小时以内完成。 有时，部署过程可能长达四个小时。 若要检查部署状态，请在 [Azure 门户](https://portal.azure.com)的 Azure 工具栏上选择通知图标，这将打开通知窗格。

> [!NOTE]
> 如果部署失败或删除 ISE，Azure 在释放子网之前可能需要长达一个小时。 此延迟意味着可能需要等待一段时间才能在另一个 ISE 中重复使用这些子网。
>
> 如果删除虚拟网络，Azure 通常需要长达两小时才能释放子网，但是此操作可能需要更长的时间。 
> 删除虚拟网络时，请确保没有资源仍处于连接状态。 
> 请参阅[删除虚拟网络](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

### <a name="request-header"></a>请求标头

在请求标头中，添加以下属性：

* `Content-type`：将此属性值设置为 `application/json`。

* `Authorization`：将此属性值设置为有权访问你要使用的 Azure 订阅或资源组的客户的持有者令牌。

### <a name="request-body"></a>请求正文

在请求正文中，通过在 ISE 定义中提供这些附加项的信息，来启用对这些项的支持：

* 由 ISE 用来访问密钥保管库的托管标识
* 要使用的密钥保管库和客户管理的密钥

#### <a name="request-body-syntax"></a>请求正文语法

下面是请求正文语法，其中说明了创建 ISE 时要使用的属性：

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>请求正文示例

此请求正文示例显示了示例值：

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>授予对 Key Vault 的访问权限

尽管授权时机因所用的托管标识而异，仍必须[向 ISE 的托管标识授予密钥保管库访问权限](#identity-access-to-key-vault)。

* 系统分配的托管标识：在发送创建 ISE 的 HTTPS PUT 请求后的 30 分钟内，必须在密钥保管库中为 ISE 的系统分配托管标识添加一个访问策略。 否则 ISE 创建将会失败，并出现权限错误。

* 用户分配的托管标识：在发送创建 ISE 的 HTTPS PUT 请求之前，在密钥保管库中为 ISE 的用户分配托管标识添加一个访问策略。

对于此任务，可以使用 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 命令，或者在 Azure 门户中执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中打开你的 Azure 密钥保管库。

1. 在密钥保管库菜单中，选择“访问策略” > “添加访问策略”，例如 ：

   ![为系统分配的托管标识添加访问策略](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. 在“添加访问策略”窗格打开后，请执行以下步骤：

   1. 选择以下选项：

      | 设置 | 值 |
      |---------|--------|
      | “从模板配置(可选)”列表 | 密钥管理 |
      | 密钥权限 | - 密钥管理操作：获取、列出 <p><p>- 加密操作：解包密钥、包装密钥 |
      |||

      ![选择“密钥管理”>“密钥权限”](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. 对于“选择主体”，请选择“未选择任何项” 。 在“主体”窗格打开后，请在搜索框中找到并选择你的 ISE。 完成后，选择“选择” > “添加” 。

      ![选择要用作主体的 ISE](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. 完成“访问策略”窗格中的设置后，选择“保存” 。

有关详细信息，请参阅[如何对密钥保管库进行身份验证](../key-vault/general/authentication.md)和[分配密钥保管库访问策略](../key-vault/general/assign-access-policy-portal.md)。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Key Vault](../key-vault/general/overview.md)
