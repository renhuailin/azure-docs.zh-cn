---
title: 受 Azure 媒体服务信任的存储
description: 本教程介绍如何为 Azure 媒体服务启用受信任的存储、对受信任的存储使用托管标识，以及在使用防火墙或 VPN 时授予 Azure 服务访问存储帐户的权限。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: 18cb4e3ada94822c2f4cb1ca7675310a37e44e84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590658"
---
# <a name="tutorial-media-services-trusted-storage"></a>教程：受媒体服务信任的存储

在本教程中，学习：

> [!div class="checklist"]
> - 如何为 Azure 媒体服务启用受信任的存储
> - 如何对受信任的存储使用托管标识
> - 如何在使用网络访问控制（如防火墙或 VPN）时授予 Azure 服务访问存储帐户的权限

使用 2020-05-01 API，可以通过将托管标识与媒体服务帐户关联来启用受信任的存储。

>[!NOTE]
>受信任的存储仅在 API 中可用，并且当前在 Azure 门户中未启用。

媒体服务可以使用系统身份验证自动访问你的存储帐户。 媒体服务会验证媒体服务帐户和存储帐户是否在同一订阅中。 它还会验证添加关联的用户是否可以使用 Azure 资源管理器 RBAC 访问存储帐户。

但是，如果你想要使用网络访问控制来保护存储帐户并启用受信任的存储，则需要[托管标识](concept-managed-identities.md)身份验证。 这使媒体服务可通过受信任的存储访问权限访问配置有防火墙或 VNet 限制的存储帐户。

## <a name="overview"></a>概述

> [!IMPORTANT]
> 将 2020-05-01 API 用于所有对媒体服务的请求。

下面是为媒体服务创建受信任存储的一般步骤：

1. 创建资源组。
1. 创建存储帐户。
1. 轮询存储帐户，直到它准备就绪。 当存储帐户准备就绪时，请求将返回服务主体 ID。
1. 查找“存储 Blob 数据参与者”角色的 ID。
1. 调用授权提供程序并添加角色分配。
1. 更新媒体服务帐户，以便使用托管标识向存储帐户进行身份验证。
1. 如果你不想继续使用这些资源并为其付费，请将其删除。

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>先决条件

需要有一个 Azure 订阅才能开始。  如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free/)。

### <a name="get-your-tenant-id-and-subscription-id"></a>获取租户 ID 和订阅 ID

如果不知道如何获取租户 ID 和订阅 ID，请参阅[如何查找租户 ID](how-to-set-azure-tenant.md) 和[查找租户 ID](how-to-set-azure-tenant.md)。

### <a name="create-a-service-principal-and-secret"></a>创建服务主体和机密

如果不知道如何创建服务主体和机密，请参阅[获取访问媒体服务 API 的凭据](access-api-howto.md)。

## <a name="use-a-rest-client"></a>使用 REST 客户端

此脚本旨在用于 REST 客户端，例如 Visual Studio code 扩展中提供的客户端。  针对开发环境对其进行调整。

## <a name="set-initial-variables"></a>设置初始变量

此脚本的这一部分用于 REST 客户端。 可以在你的开发环境中以其他方式使用变量。

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>获取 Azure 资源管理器的令牌

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>获取 Graph API 的令牌

此脚本的这一部分用于 REST 客户端。 可以在你的开发环境中以其他方式使用变量。

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>获取服务主体详细信息

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>存储服务主体 ID

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>创建资源组

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>创建存储帐户

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>获取存储帐户状态

存储帐户将需要一段时间才能准备就绪，因此该请求会轮询其状态。  重复此请求，直到存储帐户准备就绪。

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>获取存储帐户详细信息

存储帐户准备就绪后，获取存储帐户的属性。

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>获取 ARM 的令牌

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>使用系统分配的托管标识创建媒体服务帐户

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>获取存储 Blob 数据角色定义

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>设置存储角色分配

角色分配指出，媒体服务帐户的服务主体具有存储角色“存储 Blob 数据参与者”。  这可能需要一些时间，因此请务必等待，否则媒体服务帐户将无法正确设置。

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>为托管标识提供对存储帐户的旁路访问权限

此操作会将访问权限从系统管理的标识更改为托管标识。 这样，存储帐户就可以通过防火墙访问存储帐户，因为无论 IP 访问规则 (ACL) 如何，Azure 服务都可以访问存储帐户。

请再次等待，直到在存储帐户中分配了角色，否则将无法正确设置媒体服务帐户。

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>更新媒体服务帐户以使用托管标识

由于存储角色分配可能需要几分钟才能传播，因此可能需要重试几次此请求。

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>测试访问权限

通过在存储帐户中创建资产来测试访问权限。

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>删除资源

如果你不想保留已创建的资源并继续为其付费，请将其删除。

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>后续步骤

[如何创建资产](how-to-create-asset.md)
