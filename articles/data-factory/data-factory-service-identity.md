---
title: 数据工厂的托管标识
description: 了解 Azure 数据工厂的托管标识。
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a350553659ea6028e3fb2079f790d14ae1653a86
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746566"
---
# <a name="managed-identity-for-data-factory"></a>数据工厂的托管标识

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文将帮助你了解什么是数据工厂的托管标识（以前称为托管服务标识/MSI）及其工作原理。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述

数据工厂中的托管标识使数据工程师无需管理凭据。 托管标识为数据工厂实例提供一个标识，可以在连接到支持 Azure Active Directory (Azure AD) 身份验证的资源时使用。 例如，数据工厂可以使用托管标识来访问 [Azure 密钥保管库](../key-vault/general/overview.md)等资源，开发人员可以采用安全的方式存储凭据或访问存储帐户。 数据工厂可以使用托管标识来获取 Azure AD 令牌。

数据工厂支持两种类型的托管标识： 

- 系统分配：数据工厂允许你直接在服务实例上启用托管标识。 如果在创建数据工厂期间允许系统分配的托管标识，则会在与该服务实例的生命周期相关联的 Azure AD 中创建标识。 按照设计，只有该 Azure 资源可以使用此标识从 Azure AD 请求令牌。 因此，资源被删除时，Azure 会自动删除标识。
- 用户分配：你也可以将托管标识创建为独立的 Azure 资源。 你可以[创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)，并将其分配给一个或多个数据工厂实例。 对于用户分配的托管标识，标识与使用它的资源分开管理。



数据工厂的托管标识具有以下优势：

- [在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)，在这种情况下，数据工厂托管标识用于 Azure Key Vault 身份验证。
- 使用托管标识身份验证访问数据存储或计算，包括 Azure Blob 存储、Azure 数据资源管理器、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库、Azure SQL 托管实例、Azure Synapse Analytics、REST、Databricks 活动、Web 活动等。 有关详细信息，请查看连接器和活动文章。
- 用户分配的托管标识还用于使用存储在 Azure 密钥保管库中的客户托管密钥对数据工厂元数据进行加密/解密，从而提供双重加密。 

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识 

>[!NOTE]
> 系统分配的托管标识在数据工厂文档和数据工厂 UI 中也称为“托管标识”，以实现向后兼容目的。 在引用时，我们会明确提及“用户分配的托管标识”。 

#### <a name="generate-system-assigned-managed-identity"></a><a name="generate-managed-identity"></a> 生成系统分配的托管标识

系统分配的数据工厂托管标识按如下方式生成：

- 通过 **Azure 门户或 PowerShell** 创建数据工厂时，始终会自动创建托管标识。
- 通过 **SDK** 创建数据工厂时，仅当在要创建的工厂对象中指定了“Identity = new FactoryIdentity()”时，才会创建托管标识。 请参阅 [.NET 快速入门 - 创建数据工厂](quickstart-create-data-factory-dot-net.md#create-a-data-factory)中的示例。
- 通过 **REST API** 创建数据工厂时，仅当在请求正文中指定了 "identity" 节时，才会创建托管标识。 请参阅 [REST 快速入门 - 创建数据工厂](quickstart-create-data-factory-rest-api.md#create-a-data-factory)中的示例。

如果发现数据工厂没有与以下[检索托管标识](#retrieve-managed-identity)说明相关的托管标识，可以使用标识发起程序以编程方式更新数据工厂，从而显式生成一个服务标识：

- [使用 PowerShell 生成托管标识](#generate-system-assigned-managed-identity-using-powershell)
- [使用 REST API 生成托管标识](#generate-system-assigned-managed-identity-using-rest-api)
- [使用 Azure 资源管理器模板生成托管标识](#generate-system-assigned-managed-identity-using-an-azure-resource-manager-template)
- [使用 SDK 生成托管标识](#generate-system-assigned-managed-identity-using-sdk)

>[!NOTE]
>
>- 无法修改托管标识。 更新已带有托管标识的数据工厂不会产生任何影响，托管标识将保持不变。
>- 如果更新已带有托管标识的数据工厂，但未在工厂对象中指定 "identity" 参数，或者未在 REST 请求正文中指定 "identity" 节，将会收到错误。
>- 删除某个数据工厂时，会一并删除关联的托管标识。

##### <a name="generate-system-assigned-managed-identity-using-powershell"></a>使用 PowerShell 生成系统分配的托管标识

调用 Set-AzDataFactoryV2 命令，然后你会看到正在生成新的“标识”字段：

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

##### <a name="generate-system-assigned-managed-identity-using-rest-api"></a>使用 REST API 生成系统分配的托管标识

调用以下 API 并在请求正文中包含 "identity" 节：

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**请求正文**：添加 "identity": { "type": "SystemAssigned" }。

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**响应**：自动创建托管标识并相应地填充 "identity" 部分。

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

##### <a name="generate-system-assigned-managed-identity-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板生成系统分配的托管标识

**模版**：添加 "identity": { "type": "SystemAssigned" }。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

##### <a name="generate-system-assigned-managed-identity-using-sdk"></a>使用 SDK 生成系统分配的托管标识

结合 Identity=new FactoryIdentity() 调用数据工厂 create_or_update 函数。 使用 .NET 的示例代码：

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

#### <a name="retrieve-system-assigned-managed-identity"></a><a name="retrieve-managed-identity"></a> 检索系统分配的托管标识

可以通过 Azure 门户或以编程方式检索托管标识。 以下部分演示了一些示例。

>[!TIP]
> 如果看不到托管标识，请通过更新工厂来[生成托管标识](#generate-managed-identity)。

#### <a name="retrieve-system-assigned-managed-identity-using-azure-portal"></a>使用 Azure 门户检索系统分配的托管标识

可以从 Azure 门户 -> 数据工厂 ->“属性”找到托管标识信息。

- 托管标识对象 ID
- 托管标识租户

当你创建支持托管标识身份验证的链接服务（如 Azure Blob、Azure Data Lake Storage、Azure Key Vault 等）时，托管标识信息也会显示。

授予权限时，在 Azure 资源的访问控制 (IAM) 选项卡 -> 添加角色分配 -> 将访问权限分配到 -> 在“系统分配的托管标识”下选择“数据工厂”-> 按工厂名称选择；或是一般而言，可以使用对象 ID 或数据工厂名称（作为托管标识名称）查找此标识。 如果需要获取托管标识的应用程序 ID，可以使用 PowerShell。

#### <a name="retrieve-system-assigned-managed-identity-using-powershell"></a>使用 PowerShell 检索系统分配的托管标识

获取特定的数据工厂时，会返回托管标识主体 ID 和租户 ID，如下所示。 使用 **PrincipalId** 授予访问权限：

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

可以通过以下方法获取应用程序 ID：复制上面的主体 ID，然后以主体 ID 作为参数运行以下 Azure Active Directory 命令。

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

#### <a name="retrieve-managed-identity-using-rest-api"></a>使用 REST API 检索托管标识

获取特定的数据工厂时，会返回托管标识主体 ID 和租户 ID，如下所示。

在请求中调用以下 API：

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**响应**：你将获得如下所示的响应。 “标识”部分会相应进行填充。

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> 若要从 ARM 模板检索托管标识，请在 ARM JSON 中添加“输出”部分：

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

可以在 Azure Active Directory 中创建、删除、管理用户分配的托管标识。 有关详细信息，请参阅[使用 Azure 门户创建、列出、删除用户分配的托管标识或为其分配角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

### <a name="credentials"></a>凭据

我们会引入凭据，这些凭据可以包含用户分配的托管标识、服务主体，还列出系统分配的托管标识，你可以在支持 Azure Active Directory (AAD) 身份验证的链接服务中使用该标识。 它可帮助你合并和管理所有基于 AAD 的凭据。  

下面是在链接服务中使用用户分配的托管标识进行身份验证的一般步骤。 

1. 使用 Azure 门户、SDK、PowerShell、REST API 将用户分配的托管标识关联到数据工厂实例。 
   下面的屏幕截图使用 Azure 门户（数据工厂边栏选项卡）关联用户分配的托管标识。 

   :::image type="content" source="media/managed-identities/uami-azure-portal.jpg" alt-text="显示如何使用 Azure 门户关联用户分配的托管标识的屏幕截图。" lightbox="media/managed-identities/uami-azure-portal.jpg":::

2. 以交互方式在数据工厂用户界面中创建“凭据”。 你可以在步骤 1 中选择与数据工厂关联的用户分配的托管标识。 

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-1.png" alt-text="显示创建新凭据的第一步的屏幕截图。" lightbox="media/managed-identities/credential-adf-ui-create-new-1.png":::

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-2a.png" alt-text="显示创建新凭据的第二步的屏幕截图。" lightbox="media/managed-identities/credential-adf-ui-create-new-2a.png":::

3. 创建新的链接服务，并选择“身份验证”下的“用户分配的托管标识”

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-linked-service.png" alt-text="显示具有用户分配的托管标识身份验证的新链接服务的屏幕截图。" lightbox="media/managed-identities/credential-adf-ui-create-new-linked-service.png":::

> [!NOTE] 
> 你可以使用 SDK/PowerShell/REST API 来执行上述操作。

## <a name="next-steps"></a>后续步骤

请参阅以下主题，其中介绍了何时以及如何使用数据工厂托管标识：

- [在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)
- [使用 Azure 资源的托管标识身份验证从/向 Azure Data Lake Store 复制数据](connector-azure-data-lake-store.md)

有关数据工厂托管标识所基于的 Azure 资源的托管标识的更多背景信息，请参阅 [Azure 资源的托管标识概述](../active-directory/managed-identities-azure-resources/overview.md)。
