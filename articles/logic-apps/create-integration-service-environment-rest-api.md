---
title: " (具有逻辑应用的 ISEs) 创建 integration service 环境 REST API"
description: 使用逻辑应用 REST API (ISE) 创建集成服务环境，以便可以从 Azure 逻辑应用 (Vnet) 访问 Azure 虚拟网络
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d4500229800fa5d1743779b29927637777647e47
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550651"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>使用逻辑应用 REST API 创建集成服务环境 (ISE)

在逻辑应用和集成帐户需要访问 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的情况下，可以使用逻辑应用 REST API [ (ISE) 来创建 *integration service 环境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 。 若要详细了解 ISE，请参阅[从独立 Azure 逻辑应用访问 Azure 虚拟网络资源](connect-virtual-network-vnet-isolated-environment-overview.md)。

本文说明了如何使用逻辑应用 REST API 一般创建 ISE。 （可选）还可以在 ISE 上启用 [系统分配的或用户分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) ，但此时仅通过使用逻辑应用 REST API。 使用此标识，你的 ISE 可以验证对位于或连接到 Azure 虚拟网络中的受保护资源（例如虚拟机和其他系统或服务）的访问权限。 这样一来，就不必使用您的凭据登录。

有关创建 ISE 的其他方法的详细信息，请参阅以下文章：

* [使用 Azure 门户创建 ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [使用示例 Azure 资源管理器快速入门模板创建 ISE](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [创建支持使用客户管理的密钥来加密静态数据的 ISE](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>先决条件

* 与在 Azure 门户中创建 ISE 时相同的[先决条件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和[访问要求](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* 要用于 ISE 的任何其他资源，以便可以将其信息包括在 ISE 定义中，例如： 

  * 若要启用自签名证书支持，你需要在 ISE 定义中包含有关该证书的信息。

  * 若要启用用户分配的托管标识，需要事先创建该标识，并在 `objectId` `principalId` `clientId` ISE 定义中包含、和属性及其值。 有关详细信息，请参阅 [在 Azure 门户中创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

* 一种工具，可用于通过调用具有 HTTPS PUT 请求 REST API 的逻辑应用来创建 ISE。 例如，可以使用 [Postman](https://www.getpostman.com/downloads/)，也可以生成执行此任务的逻辑应用。

## <a name="create-the-ise"></a>创建 ISE

若要通过调用逻辑应用 REST API 创建 ISE，请发出此 HTTPS PUT 请求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 逻辑应用 REST API 2019-05-01 版本要求你为 ISE 连接器自行发出 HTTP PUT 请求。

部署通常需要两个小时才能完成。 有时，部署过程可能长达四个小时。 若要检查部署状态，请在 " [Azure 门户](https://portal.azure.com)的 Azure 工具栏上，选择" 通知 "图标，打开" 通知 "窗格。

> [!NOTE]
> 如果部署失败或删除 ISE，Azure 在释放子网之前可能需要长达一个小时。 此延迟意味着可能需要等待一段时间才能在另一个 ISE 中重复使用这些子网。
>
> 如果删除虚拟网络，Azure 通常需要长达两小时才能释放子网，但是此操作可能需要更长的时间。 
> 删除虚拟网络时，请确保没有资源仍处于连接状态。 
> 请参阅[删除虚拟网络](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

## <a name="request-header"></a>请求头

在请求标头中，包括以下属性：

* `Content-type`：将此属性值设置为 `application/json` 。

* `Authorization`：将此属性值设置为有权访问要使用的 Azure 订阅或资源组的客户的持有者令牌。

<a name="request-body"></a>

## <a name="request-body"></a>请求正文

在请求正文中，提供用于创建 ISE 的资源定义，其中包括要在 ISE 上启用的其他功能的信息，例如：

* 如本文后面所述，若要创建允许使用自签名证书和由安装在该位置的企业证书颁发机构颁发的证书的 ISE，请在 `TrustedRoot` `certificates` ise 定义的部分中包括该对象 `properties` 。

* 若要创建使用系统分配的托管标识或用户分配的托管标识的 ISE，请在 `identity` ise 定义中包含具有托管标识类型和其他所需信息的对象，如本文后面所述。

* 若要创建使用客户管理的密钥并 Azure Key Vault 加密静态数据的 ISE，请包含支持 [客户托管的密钥支持的信息](customer-managed-keys-integration-service-environment.md)。 *只能在创建时* 设置客户管理的密钥，而不能在之后设置。

### <a name="request-body-syntax"></a>请求正文语法

下面是请求正文语法，描述创建 ISE 时要使用的属性：

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

此示例请求正文显示示例值：

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

通常使用 ISE 连接到虚拟网络或本地上的自定义服务。 这些自定义服务通常由自定义根证书颁发机构颁发的证书（例如企业证书颁发机构或自签名证书）所保护。 有关使用自签名证书的详细信息，请参阅 [对其他服务和系统的出站调用的安全访问和数据访问](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)。 为了使 ISE 通过传输层安全性 (TLS) 成功连接到这些服务，ISE 需要访问这些根证书。

#### <a name="considerations-for-adding-custom-root-certificates"></a>添加自定义根证书的注意事项

使用自定义受信任的根证书更新 ISE 之前，请查看以下注意事项：

* 请确保上载根证书 *和* 所有中间证书。 最大证书数为20。

* 上传根证书是一项替换操作，其中最新的上传将覆盖以前的上传。 例如，如果发送一个请求，该请求上传一个证书，然后再发送另一个请求来上传另一个证书，则 ISE 只使用第二个证书。 如果需要使用这两个证书，请将它们一起添加到同一个请求中。  

* 上传根证书是一种可能需要一些时间的异步操作。 若要检查状态或结果，可以 `GET` 使用同一 URI 发送请求。 `provisioningState` `InProgress` 当上传操作仍在运行时，响应消息具有返回值的字段。 当 `provisioningState` value 为时 `Succeeded` ，上传操作完成。

#### <a name="request-syntax"></a>请求语法

若要使用自定义受信任的根证书更新 ISE，请将以下 HTTPS 修补请求发送到 [azure 资源管理器 URL，该 URL 因 azure 环境而异](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane)，例如：

| 环境 | Azure 资源管理器 URL |
|-------------|----------------------------|
| Azure global (多租户)  | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure 中国世纪互联 | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>用于添加自定义根证书的请求正文语法

下面是请求正文语法，描述在添加根证书时要使用的属性：

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
