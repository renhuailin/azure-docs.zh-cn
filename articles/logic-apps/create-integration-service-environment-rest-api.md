---
title: 使用逻辑应用 REST API 创建集成服务环境 (ISE)
description: 使用逻辑应用 REST API 创建集成服务环境 (ISE)，以便从 Azure 逻辑应用访问 Azure 虚拟网络 (VNET)
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: f50fecfb2675e870025d9e188d5303aacf77f7a2
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112295716"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>使用逻辑应用 REST API 创建集成服务环境 (ISE)

对于逻辑应用和集成帐户需要访问 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的方案，可以使用逻辑应用 REST API 创建[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 若要详细了解 ISE，请参阅[从独立 Azure 逻辑应用访问 Azure 虚拟网络资源](connect-virtual-network-vnet-isolated-environment-overview.md)。

本文介绍了使用逻辑应用 REST API 创建 ISE 的一般方法。 你也可以选择在 ISE 上启用[系统分配或用户分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)，但此时仅能使用逻辑应用 REST API 执行此操作。 借助此标识，你的 ISE 会验证对位于或连接到 Azure 虚拟网络中的受保护资源（例如虚拟机和其他系统或服务）的访问权限。 如此，你便无需使用凭据就可以登录。

有关创建 ISE 的其他方法的详细信息，请参阅以下文章：

* [使用 Azure 门户创建 ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [使用示例 Azure 资源管理器快速入门模板创建 ISE](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/integration-service-environment)
* [创建支持使用客户管理的密钥来加密静态数据的 ISE](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>先决条件

* [必备条件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和[访问要求](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)与在 Azure 门户中创建 ISE 时相同

* 要与 ISE 配合使用的任何其他资源，以便可以将其信息包含在 ISE 定义中，例如： 

  * 如果要启用自签名证书支持，你需要将该证书的相关信息包含在 ISE 定义中。

  * 如果要启用用户分配的托管标识，你需要事先创建该标识，并将其包含在 ISE 定义的 `objectId`、`principalId` 和 `clientId` 属性及其值中。 有关详细信息，请参阅[在 Azure 门户中创建用户分配的托管身份](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

* 通过使用 HTTPS PUT 请求调用逻辑应用 REST API 来创建 ISE 的一种工具。 例如，你可以使用 [Postman](https://www.getpostman.com/downloads/)，也可以生成一个用于执行此任务的逻辑应用。

## <a name="create-the-ise"></a>创建 ISE

若要通过调用逻辑应用 REST API 来创建 ISE，请发出以下 HTTPS PUT 请求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 逻辑应用 REST API 2019-05-01 版本要求你对 ISE 连接器自行发出 HTTP PUT 请求。

通常，部署过程在两个小时以内完成。 有时，部署过程可能长达四个小时。 若要检查部署状态，请在 [Azure 门户](https://portal.azure.com)的 Azure 工具栏上选择通知图标，这将打开通知窗格。

> [!NOTE]
> 如果部署失败或删除 ISE，Azure 在释放子网之前可能需要长达一个小时。 此延迟意味着可能需要等待一段时间才能在另一个 ISE 中重复使用这些子网。
>
> 如果删除虚拟网络，Azure 通常需要长达两小时才能释放子网，但是此操作可能需要更长的时间。 
> 删除虚拟网络时，请确保没有资源仍处于连接状态。 
> 请参阅[删除虚拟网络](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

## <a name="request-header"></a>请求标头

在请求标头中，添加以下属性：

* `Content-type`：将此属性值设置为 `application/json`。

* `Authorization`：将此属性值设置为有权访问你要使用的 Azure 订阅或资源组的客户的持有者令牌。

<a name="request-body"></a>

## <a name="request-body"></a>请求正文

在请求正文中，提供用于创建 ISE 的资源定义，包括要在 ISE 上启用的其他功能的信息，例如：

* 若要创建允许使用安装在 `TrustedRoot` 位置的自签名证书和企业证书颁发机构颁发的证书的 ISE，请在 ISE 定义的 `properties` 部分中添加 `certificates`对象，如本文后面所述。

* 若要创建使用系统分配或用户分配的托管标识的 ISE，请在 ISE 定义中添加包含托管标识类型和其他所需信息的 `identity` 对象，如本文后面所述。

* 若要创建使用客户管理的密钥和 Azure 密钥保管库来加密静态数据的 ISE，请添加[支持使用客户管理的密钥的信息](customer-managed-keys-integration-service-environment.md)。 只能在创建时设置客户管理的密钥，而不能在之后设置。

### <a name="request-body-syntax"></a>请求正文语法

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
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
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
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>请求正文示例

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
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>添加自定义根证书

通常使用 ISE 连接到虚拟网络或本地上的自定义服务。 这些自定义服务通常由自定义根证书颁发机构颁发的证书（例如企业证书颁发机构或自签名证书）所保护。 有关使用自签名证书的详细信息，请参阅[安全访问和数据 - 对其他服务和系统的出站调用访问](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)。 为了使 ISE 通过传输层安全性 (TLS) 成功连接到这些服务，ISE 需要访问这些根证书。

#### <a name="considerations-for-adding-custom-root-certificates"></a>添加自定义根证书的注意事项

使用自定义受信任的根证书更新 ISE 之前，请查看以下注意事项：

* 请确保上传根证书和所有中间证书。 最大证书数量为 20 个。

* 证书上的主题名称必须与要从 Azure 逻辑应用调用的目标终结点的主机名匹配。 

* 上传根证书是一项替换操作，其中最新上传的文件将覆盖之前上传的文件。 例如，如果发送请求上传一个证书，然后再发送一个请求上传另一个证书，则 ISE 只使用第二个证书。 如果需要使用这两个证书，请将它们添加到同一个请求中。  

* 上传根证书是异步操作，需要花费一些时间。 如果要检查状态或结果，可以使用同一个 URI 发送 `GET` 请求。 当上传操作仍在进行时，响应消息的 `provisioningState` 字段返回 `InProgress` 值。 当 `provisioningState` 值为 `Succeeded` 时，上传操作完成。

#### <a name="request-syntax"></a>请求语法

如果要使用自定义受信任的根证书更新 ISE，请将以下 HTTPS PATCH 请求发送到 [Azure 资源管理器 URL，该 URL 因 Azure 环境而异](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane)，例如：

| 环境 | Azure 资源管理器 URL |
|-------------|----------------------------|
| Azure 全球（多租户） | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure 中国世纪互联 | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>用于添加自定义根证书的请求正文语法

下面是请求正文语法，其中说明了添加根证书时要使用的属性：

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>后续步骤

* [向集成服务环境添加资源](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
