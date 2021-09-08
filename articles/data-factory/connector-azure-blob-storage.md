---
title: 在 Azure Blob 存储中复制和转换数据
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何使用 Azure 数据工厂或 Azure Synapse Analytics 向/从 Blob 存储复制数据，以及如何转换 Blob 存储中的数据。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/30/2021
ms.openlocfilehash: e7d9e4da611bbbf13bacee60ed73248f5b39c14c
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123307034"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory-or-azure-synapse-analytics"></a>使用 Azure 数据工厂或 Azure Synapse Analytics 复制和转换 Azure Blob 存储中的数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> - [版本 1](v1/data-factory-azure-blob-connector.md)
> - [当前版本](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂和 Azure Synapse 管道中的复制活动向/从 Azure Blob 存储复制数据。 还介绍了如何使用数据流活动转换 Azure Blob 存储中的数据。 要了解详细信息，请阅读 [Azure 数据工厂](introduction.md)和 [Azure Synapse Analytics](..\synapse-analytics\overview-what-is.md) 简介文章。

>[!TIP]
>要了解针对 Data Lake 或数据仓库的迁移方案，请参阅[将 Data Lake 或数据仓库中的数据迁移到 Azure](data-migration-guidance-overview.md) 一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Blob 存储连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Delete 活动](delete-activity.md)

对于复制活动，此 Blob 存储连接器支持以下操作：

- 向/从常规用途的 Azure 存储帐户和热/冷 Blob 存储复制 Blob。
- 通过使用帐户密钥身份验证、服务共享访问签名 (SAS) 身份验证、服务主体身份验证或 Azure 资源托管标识身份验证来复制 Blob。
- 从块、追加或页 Blob 中复制 Blob，并将数据仅复制到块 Blob。
- 按原样复制 Blob，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析或生成 Blob。
- [在复制期间保留文件元数据](#preserving-metadata-during-copy)。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-azure-blob-storage-linked-service-using-ui"></a>使用 UI 创建 Azure Blob 存储链接服务

使用以下步骤在 Azure 门户 UI 中创建 Azure Blob 存储链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新链接服务的屏幕截图。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索 Blob 并选择 Azure Blob 存储连接器。

    :::image type="content" source="media/connector-azure-blob-storage/azure-blob-storage-connector.png" alt-text="选择 Azure Blob 存储连接器。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-azure-blob-storage/configure-azure-blob-storage-linked-service.png" alt-text="Azure Blob 存储链接服务的配置屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

以下部分详细介绍了用于定义特定于 Blob 存储的数据工厂和 Synapse 管道实体的属性。

## <a name="linked-service-properties"></a>链接服务属性

此 Blob 存储连接器支持以下身份验证类型。 有关详细信息，请参阅相应部分。

- [帐户密钥身份验证](#account-key-authentication)
- [共享访问签名身份验证](#shared-access-signature-authentication)
- [服务主体身份验证](#service-principal-authentication)
- [系统分配的托管标识身份验证](#managed-identity)
- [用户分配的托管标识身份验证](#user-assigned-managed-identity-authentication)

>[!NOTE]
>- 如果要利用在 Azure 存储防火墙上启用的“允许受信任的 Microsoft 服务访问此存储帐户”选项来使用公共 Azure 集成运行时连接到 Blob 存储，则必须使用[托管标识身份验证](#managed-identity)。
>- 在使用 PolyBase 或 COPY 语句将数据载入 Azure Synapse Analytics 时，如果为源或暂存 Blob 存储配置了 Azure 虚拟网络终结点，则必须按照 Azure Synapse 的要求使用托管标识身份验证。 请参阅[托管标识身份验证](#managed-identity)部分，了解更多的配置先决条件。

>[!NOTE]
>Azure HDInsight 和 Azure 机器学习活动只支持使用 Azure Blob 存储帐户密钥的身份验证。

### <a name="account-key-authentication"></a>帐户密钥身份验证

Azure 数据工厂或 Synapse 管道中的存储帐户密钥身份验证支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | `type` 属性必须设置为 `AzureBlobStorage`（建议）或 `AzureStorage`（请查看以下注释）。 | 是 |
| connectionString | 为 `connectionString` 属性指定连接到存储所需的信息。 <br/> 还可以将帐户密钥放在 Azure Key Vault 中，从连接字符串中拉取 `accountKey` 配置。 有关详细信息，请参阅以下示例和[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)一文。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络中）。 如果未指定此属性，服务会使用默认的 Azure Integration Runtime。 | 否 |

>[!NOTE]
>使用帐户密钥身份验证时，不支持辅助 Blob 服务终结点。 可以使用其他身份验证类型。

>[!NOTE]
>如果使用 `AzureStorage` 类型的链接服务，则仍然按原样支持它。 但是，建议今后使用这个新的 `AzureBlobStorage` 链接服务类型。

**示例：**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
          "referenceName": "<name of Integration Runtime>",
          "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure Key Vault 中存储帐户密钥**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="shared-access-signature-authentication"></a>共享访问签名身份验证

共享访问签名对存储帐户中的资源提供委托访问。 使用共享访问签名可以在指定的时间内授予客户端对存储帐户中对象的有限访问权限。 

无需共享帐户访问密钥。 共享访问签名是一个 URI，在其查询参数中包含对存储资源已验证访问所需的所有信息。 若要使用共享访问签名访问存储资源，客户端只需将共享访问签名传入到相应的构造函数或方法。

有关共享访问签名的详细信息，请参阅[共享访问签名：了解共享访问签名模型](../storage/common/storage-sas-overview.md)。

> [!NOTE]
>- 该服务现在支持服务共享访问签名和帐户共享访问签名 。 有关共享访问签名的详细信息，请参阅[使用共享访问签名授予对 Azure 存储资源的有限访问权限](../storage/common/storage-sas-overview.md)。
>- 在稍后的数据集配置中，文件夹路径是从容器级别开始的绝对路径。 需要配置与 SAS URI 中的路径一致的路径。

以下属性支持使用共享访问签名身份验证：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | `type` 属性必须设置为 `AzureBlobStorage`（建议）或 `AzureStorage`（请查看以下注释）。 | 是 |
| sasUri | 指定存储资源（例如 Blob 或容器）的共享访问签名 URI。 <br/>将此字段标记为 `SecureString` 以安全存储它。 还可以将 SAS 令牌放在 Azure Key Vault 中，以使用自动轮换和删除令牌部分。 有关详细信息，请参阅以下示例和[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络中）。 如果未指定此属性，服务会使用默认的 Azure Integration Runtime。 | 否 |

>[!NOTE]
>如果使用 `AzureStorage` 类型的链接服务，则仍然按原样支持它。 但是，建议今后使用这个新的 `AzureBlobStorage` 链接服务类型。

**示例：**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure Key Vault 中存储帐户密钥**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

在创建共享访问签名 URI 时，请注意以下几点：

- 根据链接服务（读取、写入、读/写）的用法，设置针对对象的适当读/写权限。
- 根据需要设置“到期时间”。 确保存储对象的访问权限不会在管道的活动期限内过期。
- 应该根据需要在正确的容器或 Blob 中创建 URI。 数据工厂或 Synapse 管道可以使用 Blob 的共享访问签名 URI 访问该特定 blob。 数据工厂或 Synapse 管道可以使用 Blob 存储容器的共享访问签名 URI 迭代该容器中的 blob。 以后若要提供更多/更少对象的访问权限或需要更新共享访问签名 URI，请记得使用新 URI 更新链接服务。

### <a name="service-principal-authentication"></a>服务主体身份验证

有关 Azure 存储服务主体身份验证的常规信息，请参阅[使用 Azure Active Directory 验证对 Azure 存储的访问权限](../storage/blobs/authorize-access-azure-active-directory.md)。

若要使用服务主体身份验证，请执行以下步骤：

1. 遵循[将应用程序注册到 Azure AD 租户](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，在 Azure Active Directory (Azure AD) 中注册一个应用程序实体。 记下以下值，这些值用于定义链接服务：

    - 应用程序 ID
    - 应用程序密钥
    - 租户 ID

2. 授予服务主体在 Azure Blob 存储中的适当权限。 有关角色的详细信息，请参阅[使用 Azure 门户分配用于访问 Blob 和队列数据的 Azure 角色](../storage/blobs/assign-azure-role-data-access.md)。

    - 作为源，在“访问控制(标识和访问管理)”中至少授予“存储 Blob 数据读取者”角色。
    - 作为接收器，在“访问控制(标识和访问管理)”中至少授予“存储 Blob 数据参与者”角色。

Azure Blob 存储链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AzureBlobStorage**。 | 是 |
| serviceEndpoint | 使用 `https://<accountName>.blob.core.windows.net/` 模式指定 Azure Blob 存储服务终结点。 | 是 |
| accountKind | 指定存储帐户的种类。 允许值包括：Storage（常规用途 v1）、StorageV2（常规用途 v2）、BlobStorage 或 BlockBlobStorage   。 <br/><br/>在数据流中使用 Azure Blob 链接服务时，如果帐户类型为空或“存储”，则不支持托管标识或服务主体身份验证。 请指定正确的帐户类型，选择一种不同的身份验证，或者将你的存储帐户升级到常规用途 v2。 | 否 |
| servicePrincipalId | 指定应用程序的客户端 ID。 | 是 |
| servicePrincipalKey | 指定应用程序的密钥。 将此字段标记为 SecureString 以安全地将其存储在工厂中，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定应用程序的租户信息（域名或租户 ID）。 通过将鼠标悬停在 Azure 门户右上角来检索租户信息。 | 是 |
| azureCloudType | 对于服务主体身份验证，请指定 Azure Active Directory 应用程序注册到的 Azure 云环境的类型。 <br/> 允许的值为“AzurePublic”、“AzureChina”、“AzureUsGovernment”和“AzureGermany”   。 默认情况下，使用数据工厂或 Synapse 管道的云环境。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络中）。 如果未指定此属性，服务会使用默认的 Azure Integration Runtime。 | 否 |

>[!NOTE]
>
>- 如果你的 blob 帐户启用了[软删除](../storage/blobs/soft-delete-blob-overview.md)，则数据流中不支持服务主体身份验证。
>- 如果使用数据流通过专用终结点访问 blob 存储，请注意，使用服务主体身份验证时，数据流会连接到 ADLS Gen2 终结点，而不是 Blob 终结点。 确保在数据工厂或 Synapse 工作区中创建相应的专用终结点，以启用访问权限。

>[!NOTE]
>服务主体身份验证仅受“AzureBlobStorage”类型的链接服务支持，而不受以前的“AzureStorage”类型的链接服务支持。

**示例：**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a> 系统分配的托管标识身份验证

数据工厂或 Synapse 管道可以与[系统分配的 Azure 资源的托管标识](data-factory-service-identity.md#system-assigned-managed-identity)相关联，这表示该资源用于对其他 Azure 服务进行身份验证。 可以像使用自己的服务主体一样，直接使用此系统分配的托管标识进行 Blob 存储身份验证。 这个指定的资源可通过此方法访问和复制 Blob 存储中的数据或将数据复制到其中。 若要详细了解 Azure 资源托管标识，请参阅 [Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)

有关 Azure 存储身份验证的常规信息，请参阅[使用 Azure Active Directory 验证对 Azure 存储的访问权限](../storage/blobs/authorize-access-azure-active-directory.md)。 若要使用 Azure 资源托管标识身份验证，请执行以下步骤：

1. 通过复制随工厂或 Synapse 工作区一起生成的系统分配的托管标识对象 ID 的值，[检索系统分配的托管标识信息](data-factory-service-identity.md#retrieve-managed-identity)。

2. 授予托管标识在 Azure Blob 存储中的权限。 有关角色的详细信息，请参阅[使用 Azure 门户分配用于访问 Blob 和队列数据的 Azure 角色](../storage/blobs/assign-azure-role-data-access.md)。

    - 作为源，在“访问控制(标识和访问管理)”中至少授予“存储 Blob 数据读取者”角色。
    - 作为接收器，在“访问控制(标识和访问管理)”中至少授予“存储 Blob 数据参与者”角色。

Azure Blob 存储链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AzureBlobStorage**。 | 是 |
| serviceEndpoint | 使用 `https://<accountName>.blob.core.windows.net/` 模式指定 Azure Blob 存储服务终结点。 | 是 |
| accountKind | 指定存储帐户的种类。 允许值包括：Storage（常规用途 v1）、StorageV2（常规用途 v2）、BlobStorage 或 BlockBlobStorage   。 <br/><br/>在数据流中使用 Azure Blob 链接服务时，如果帐户类型为空或“存储”，则不支持托管标识或服务主体身份验证。 请指定正确的帐户类型，选择一种不同的身份验证，或者将你的存储帐户升级到常规用途 v2。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络中）。 如果未指定此属性，服务会使用默认的 Azure Integration Runtime。 | 否 |

**示例：**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="user-assigned-managed-identity-authentication"></a>用户分配的托管标识身份验证
可以为数据工厂分配一个或多个[用户分配的托管标识](data-factory-service-identity.md#user-assigned-managed-identity)。 可以将此用户分配的托管标识用于 Blob 存储身份验证，该身份验证允许访问和复制 Blob 存储中的数据或将数据复制到其中。 若要详细了解 Azure 资源托管标识，请参阅 [Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)

有关 Azure 存储身份验证的常规信息，请参阅[使用 Azure Active Directory 验证对 Azure 存储的访问权限](../storage/blobs/authorize-access-azure-active-directory.md)。 若要使用用户分配的托管标识身份验证，请按照以下步骤操作：

1. [创建一个或多个用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)，并在 Azure Blob 存储中授予权限。 有关角色的详细信息，请参阅[使用 Azure 门户分配用于访问 Blob 和队列数据的 Azure 角色](../storage/blobs/assign-azure-role-data-access.md)。

    - 作为源，在“访问控制(标识和访问管理)”中至少授予“存储 Blob 数据读取者”角色。
    - 作为接收器，在“访问控制(标识和访问管理)”中至少授予“存储 Blob 数据参与者”角色。
     
2. 为数据工厂分配一个或多个用户分配的托管标识，并为每个用户分配的托管标识[创建凭据](data-factory-service-identity.md#credentials)。 


Azure Blob 存储链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **AzureBlobStorage**。 | 是 |
| serviceEndpoint | 使用 `https://<accountName>.blob.core.windows.net/` 模式指定 Azure Blob 存储服务终结点。 | 是 |
| accountKind | 指定存储帐户的种类。 允许值包括：Storage（常规用途 v1）、StorageV2（常规用途 v2）、BlobStorage 或 BlockBlobStorage   。 <br/><br/>在数据流中使用 Azure Blob 链接服务时，如果帐户类型为空或“存储”，则不支持托管标识或服务主体身份验证。 请指定正确的帐户类型，选择一种不同的身份验证，或者将你的存储帐户升级到常规用途 v2。 | 否 |
| 凭据 | 将用户分配的托管标识指定为凭据对象。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络中）。 如果未指定此属性，服务会使用默认的 Azure Integration Runtime。 | 否 |

**示例：**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

>[!IMPORTANT]
>如果使用 PolyBase 或 COPY 语句将数据从 Blob 存储（作为源或作为暂存位置）载入到 Azure Synapse Analytics 中，则在对 Blob 存储使用托管标识身份验证时，请确保还按照[此指南](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)中步骤 1 到步骤 3 的要求进行操作。 这两个步骤将向 Azure AD 注册服务器并将“存储 Blob 数据参与者”角色分配给服务器。 其余事项由数据工厂处理。 如果使用 Azure 虚拟网络终结点配置 Blob 存储，还需要按照 Azure Synapse 的要求，在 Azure 存储帐户“防火墙和虚拟网络”设置菜单下开启“允许受信任的 Microsoft 服务访问此存储帐户” 。

> [!NOTE]
>
> - 如果 Blob 帐户启用了[软删除](../storage/blobs/soft-delete-blob-overview.md)，则数据流不支持系统分配的/用户分配的托管标识身份验证。
> - 如果使用数据流通过专用终结点访问 Blob 存储，请注意，使用系统分配的/用户分配的托管标识身份验证时，数据流会连接到 ADLS Gen2 终结点，而不是 Blob 终结点。 请确保在 ADF 中创建相应的专用终结点以启用访问权限。

> [!NOTE]
> 仅“AzureBlobStorage”类型的链接服务支持系统分配的/用户分配的托管标识身份验证，而以前的“AzureStorage”类型链接服务并不支持。

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Azure Blob 存储支持基于格式的数据集中 `location` 设置下的以下属性：

| 属性   | 说明                                                  | 必需 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 数据集中位置的 **type** 属性必须设置为 **AzureBlobStorageLocation**。 | 是      |
| container  | Blob 容器。                                          | 是      |
| folderPath | 给定容器下的文件夹的路径。 如果要使用通配符来筛选文件夹，请跳过此设置并在活动源设置中进行相应的指定。 | 否       |
| fileName   | 给定容器和文件夹路径下的文件名。 如果要使用通配符来筛选文件，请跳过此设置并在活动源设置中进行相应的指定。 | 否       |

**示例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Blob 存储源和接收器支持的属性的列表。

### <a name="blob-storage-as-a-source-type"></a>将 Blob 存储用作源类型

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Azure Blob 存储支持基于格式的复制源中 `storeSettings` 设置下的以下属性：

| 属性                 | 说明                                                  | 必需                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 **type** 属性必须设置为 **AzureBlobStorageReadSettings**。 | 是                                           |
| 找到要复制的文件： |  |  |
| 选项 1：静态路径<br> | 从数据集中指定的给定容器或文件夹/文件路径复制。 若要复制容器或文件夹中的所有 Blob，请另外将 `wildcardFileName` 指定为 `*`。 |  |
| 选项 2：Blob 前缀<br>- prefix | 在数据集中配置的给定容器下的 Blob 名称的前缀，用于筛选源 Blob。 名称以 `container_in_dataset/this_prefix` 开头的 Blob 会被选择。 它利用 Blob 存储的服务端筛选器。与通配符筛选器相比，该服务端筛选器可提供更好的性能。<br><br>在使用前缀的情况下选择复制到基于文件的接收器并保留层次结构时，请注意，系统会保留前缀中最后一个“/”后面的子路径。 例如，如果在源为 `container/folder/subfolder/file.txt` 的情况下将前缀配置为 `folder/sub`，则保留的文件路径为 `subfolder/file.txt`。 | 否                                                          |
| 选项 3：通配符<br>- wildcardFolderPath | 数据集中配置的给定容器下包含通配符的文件夹路径，用于筛选源文件夹。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果文件夹名内包含通配符或此转义字符，请使用 `^` 进行转义。 <br>请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 否                                            |
| 选项 3：通配符<br>- wildcardFileName | 给定容器和文件夹路径（或通配符文件夹路径）下包含通配符的文件名，用于筛选源文件。 <br>允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果文件名包含通配符或此转义字符，请使用 `^` 进行转义。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 是 |
| 选项 4：文件列表<br>- fileListPath | 指明复制给定文件集。 指向包含要复制的文件列表的文本文件，其中每行一个文件（即数据集中所配置路径的相对路径）。<br/>使用此选项时，请不要在数据集中指定文件名。 请参阅[文件列表示例](#file-list-examples)中的更多示例。 | 否 |
| 其他设置： |  | |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 recursive 设置为 true 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。 <br>允许的值为 **true**（默认值）和 **false**。<br>如果配置 `fileListPath`，则此属性不适用。 | 否 |
| deleteFilesAfterCompletion | 指示是否会在二进制文件成功移到目标存储后将其从源存储中删除。 文件删除按文件进行。因此，当复制活动失败时，你会看到一些文件已经复制到目标并从源中删除，而另一些文件仍保留在源存储中。 <br/>此属性仅在二进制文件复制方案中有效。 默认值：false。 | 否 |
| modifiedDatetimeStart    | 文件根据“上次修改时间”属性进行筛选。 <br>如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br> 属性可以为 **NULL**，这意味着不会向数据集应用任何文件属性筛选器。  如果 `modifiedDatetimeStart` 具有日期/时间值，但 `modifiedDatetimeEnd` 为 NULL，则会选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果 `modifiedDatetimeEnd` 具有日期/时间值，但 `modifiedDatetimeStart` 为 NULL，则会选中“上次修改时间”属性小于该日期/时间值的文件。<br/>如果配置 `fileListPath`，则此属性不适用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| enablePartitionDiscovery | 对于已分区的文件，请指定是否从文件路径分析分区，并将它们添加为附加的源列。<br/>允许的值为 false（默认）和 true 。 | 否                                            |
| partitionRootPath | 启用分区发现时，请指定绝对根路径，以便将已分区文件夹读取为数据列。<br/><br/>如果未指定，默认情况下，<br/>- 在数据集或源的文件列表中使用文件路径时，分区根路径是在数据集中配置的路径。<br/>- 使用通配符文件夹筛选器时，分区根路径是第一个通配符前的子路径。<br/>- 在使用前缀时，分区根路径是最后一个“/”前的子路径。 <br/><br/>例如，假设你将数据集中的路径配置为“root/folder/year=2020/month=08/day=27”：<br/>- 如果将分区根路径指定为“root/folder/year=2020”，则除了文件内的列外，复制活动还将生成另外两个列 `month` 和 `day`，其值分别为“08”和“27”。<br/>- 如果未指定分区根路径，则不会生成额外的列。 | 否                                            |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否                                            |

> [!NOTE]
> 对于 Parquet/带分隔符的文本格式，仍然会按原样支持下一部分中提到的 **BlobSource** 类型复制活动源，以实现后向兼容性。 建议使用新模型，直到创作 UI 已切换为生成这些新类型为止。

**示例：**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> `$logs` 容器是在为存储帐户启用“存储分析”时自动创建的，在通过 UI 执行容器列出操作时并不会显示。 必须直接将文件路径提供给​数据工厂或 Synapse 管道，以便于使用 `$logs` 容器中的文件。

### <a name="blob-storage-as-a-sink-type"></a>用作接收器类型的 Blob 存储

[!INCLUDE [data-factory-v2-file-sink-formats](includes/data-factory-v2-file-sink-formats.md)] 

Azure Blob 存储支持基于格式的复制接收器中 `storeSettings` 设置下的以下属性：

| 属性                 | 说明                                                  | 必需 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 `type` 属性必须设置为 `AzureBlobStorageWriteSettings`。 | 是      |
| copyBehavior             | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名或 Blob 名称，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否       |
| blockSizeInMB | 指定用于将数据写入块 Blob 的块大小（以 MB 为单位）。 详细了解[块 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)。 <br/>允许的值介于 4 MB 到 100 MB 之间。 <br/>默认情况下，该服务会自动根据源存储类型和数据确定块大小。 以非二进制格式复制到 Blob 存储中时，默认块大小为 100 MB，因此该存储（最多）可容纳 4.95 TB 的数据。 当数据不大时，它可能并非最优，特别是当你在网络连接不佳的情况下使用自承载集成运行时的时候，这会导致操作超时或性能问题。 可以显式指定块大小，同时确保 `blockSizeInMB*50000` 足够大，可以存储数据， 否则复制活动运行会失败。 | 否 |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否       |
| metadata |在复制到接收器时设置自定义元数据。 `metadata` 数组下的每个对象都表示一个额外的列。 `name` 定义元数据键名称，`value` 表示该键的数据值。 如果使用了[保留属性功能](./copy-activity-preserve-metadata.md#preserve-metadata)，则指定的元数据会与源文件元数据联合，或者会被其覆盖。<br/><br/>允许的数据值为：<br/>- `$$LASTMODIFIED`：保留变量指示存储源文件的上次修改时间。 仅适用于二进制格式的基于文件的源。<br/><b>- 表达式<b><br/>- <b>静态值<b>| 否       |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy",
                    "metadata": [
                        {
                            "name": "testKey1",
                            "value": "value1"
                        },
                        {
                            "name": "testKey2",
                            "value": "value2"
                        },
                        {
                            "name": "lastModifiedKey",
                            "value": "$$LASTMODIFIED"
                        }
                    ]
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>文件夹和文件筛选器示例

本部分介绍使用通配符筛选器生成文件夹路径和文件名的行为。

| folderPath | fileName | recursive | 源文件夹结构和筛选器结果（用 **粗体** 表示的文件已检索）|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | （为空，使用默认值） | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | （为空，使用默认值） | 是 | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | 是 | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>文件列表示例

本部分介绍了在复制活动源中使用文件列表路径时产生的行为。

假设有以下源文件夹结构，并且要复制以粗体显示的文件：

| 示例源结构                                      | FileListToCopy.txt 中的内容                             | 配置 
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;元数据<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | 在数据集中：<br>- 容器：`container`<br>- 文件夹路径：`FolderA`<br><br>在复制活动源中：<br>- 文件列表路径：`container/Metadata/FileListToCopy.txt` <br><br>文件列表路径指向同一数据存储中的一个文本文件，该文件包含要复制的文件列表（每行一个文件，带有数据集中所配置路径的相对路径）。 |

### <a name="some-recursive-and-copybehavior-examples"></a>一些 recursive 和 copyBehavior 示例

此部分介绍了将 **recursive** 和 **copyBehavior** 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 源文件夹结构 | 生成目标 |
|:--- |:--- |:--- |:--- |
| 是 |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用与源相同的结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| 是 |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成的名称 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的内容将合并到一个文件中，且自动生成文件名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成的名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成的名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 使用以下结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成的名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |

## <a name="preserving-metadata-during-copy"></a>在复制期间保留元数据

将文件从 Amazon S3、Azure Blob 存储或 Azure Data Lake Storage Gen2 复制到 Azure Data Lake Storage Gen2 或 Azure Blob 存储时，可以选择将文件元数据和数据一起保留。 从[保留元数据](copy-activity-preserve-metadata.md#preserve-metadata)中了解更多信息。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以在 Azure Blob 存储中读取和写入以下格式的文件：

- [Avro](format-avro.md#mapping-data-flow-properties)
- [带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties)
- [增量](format-delta.md#mapping-data-flow-properties)
- [Excel](format-excel.md#mapping-data-flow-properties)
- [JSON](format-json.md#mapping-data-flow-properties)
- [Parquet](format-parquet.md#mapping-data-flow-properties)

格式特定的设置位于该格式的文档中。 有关详细信息，请参阅[映射数据流中的源转换](data-flow-source.md)和[映射数据流中的接收器转换](data-flow-sink.md)。

### <a name="source-transformation"></a>源转换

在源转换中，可以从 Azure Blob 存储中的容器、文件夹或单个文件进行读取。 使用“源选项”选项卡，可以管理文件的读取方式。 

![源选项](media/data-flow/sourceOptions1.png "源选项")

通配符路径：如果使用通配符模式，则会指示服务在单个源转换中循环访问每个匹配的文件夹和文件。 这是在单个流中处理多个文件的有效方法。 使用将鼠标指针悬停在现有通配符模式上时出现的加号来添加多个通配符匹配模式。

从源容器中，选择与模式匹配的一系列文件。 数据集中只能指定容器。 因此，通配符路径必须也包含根文件夹中的文件夹路径。

通配符示例：

- `*` 表示任意字符集。
- `**` 表示递归目录嵌套。
- `?` 替换一个字符。
- `[]` 与括号中的一个或多个字符匹配。

- `/data/sales/**/*.csv` 获取 /data/sales 下的所有 .csv 文件。
- `/data/sales/20??/**/` 获取 20 世纪的所有文件。
- `/data/sales/*/*/*.csv` 获取比 /data/sales 低两个级别的 .csv 文件。
- `/data/sales/2004/*/12/[XY]1?.csv` 获取时间为 2004 年 12 月、以 X 或 Y 开头且以两位数为前缀的所有 .csv 文件。

分区根路径：如果文件源中存在 `key=value` 格式（例如 `year=2019`）的分区文件夹，则可以向该分区文件夹树的顶层分配数据流中的列名称。

首先，设置一个通配符，用于包括属于分区文件夹以及要读取的叶文件的所有路径。

![分区源文件设置](media/data-flow/partfile2.png "分区文件设置")

使用“分区根路径”设置来定义文件夹结构的顶级。 通过数据预览查看数据内容时会看到，该服务会添加在每个文件夹级别中找到的已解析的分区。

![分区根路径](media/data-flow/partfile1.png "分区根路径预览")

**文件列表：** 这是一个文件集。 创建一个文本文件，其中包含要处理的相对路径文件的列表。 指向此文本文件。

**用于存储文件名的列：** 将源文件的名称存储在数据的列中。 请在此处输入新列名称以存储文件名字符串。

**完成后：** 数据流运行后，可以选择不对源文件执行任何操作、删除源文件或移动源文件。 移动路径是相对路径。

要将源文件移到其他位置进行后期处理，请首先选择“移动”以执行文件操作。 然后，设置“从”目录。 如果未对路径使用任何通配符，则“从”设置中的文件夹将是与源文件夹相同的文件夹。

如果源路径包含通配符，则语法将如下所示：

`/data/sales/20??/**/*.csv`

可以将“源”指定为：

`/data/sales`

可以将“目标”指定为：

`/backup/priorSales`

在此例中，源自 `/data/sales` 下的所有文件都将移动到 `/backup/priorSales`。

> [!NOTE]
> 仅当从在管道中使用“执行数据流”活动的管道运行进程（管道调试或执行运行）中启动数据流时，文件操作才会运行。 文件操作不会在数据流调试模式下运行。

**按上次修改时间筛选：** 通过指定上次修改的日期范围，可以筛选要处理的文件。 所有日期/时间均采用 UTC 格式。 

### <a name="sink-properties"></a>接收器属性

在接收器转换中，可以将内容写入 Azure Blob 存储中的容器或文件夹。 使用“设置”选项卡，可以管理文件的写入方式。

![接收器选项​​](media/data-flow/file-sink-settings.png "接收器选项​​")

**清除文件夹：** 确定在写入数据之前是否清除目标文件夹。

**文件名选项：** 确定目标文件在目标文件夹中的命名方式。 文件名选项有：
   - **默认**：允许 Spark 根据 PART 默认值为文件命名。
   - **模式**：输入一种模式，该模式枚举每个分区的输出文件。 例如，`loans[n].csv` 将创建 `loans1.csv`、`loans2.csv` 等等。
   - **每个分区**：为每个分区输入一个文件名。
   - **作为列中的数据**：将输出文件设置为列的值。 此路径是数据集容器而非目标文件夹的相对路径。 如果数据集中有文件夹路径，则会将其重写。
   - **输出到一个文件**：将分区输出文件合并为一个命名的文件。 此路径是数据集文件夹的相对路径。 请注意，合并操作可能会因为节点大小而失败。 对于大型数据集，不建议使用此选项。

全部引用：确定是否将所有值置于引号中。

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活动属性

若要了解有关属性的详细信息，请查看 [GetMetadata 活动](control-flow-get-metadata-activity.md)。 

## <a name="delete-activity-properties"></a>Delete 活动属性

若要了解有关属性的详细信息，请查看[删除活动](delete-activity.md)。

## <a name="legacy-models"></a>旧模型

>[!NOTE]
>仍会按原样支持以下模型，以实现后向兼容性。 建议使用前面提到的新模型。 创作 UI 已切换为生成新模型。

### <a name="legacy-dataset-model"></a>旧数据集模型

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 AzureSqlTable 。 | 是 |
| folderPath | 指向 Blob 存储中的容器和文件夹的路径。 <br/><br/>不包含容器名称的路径支持通配符筛选器。 允许的通配符为：`*`（匹配零个或更多字符）和 `?`（匹配零个或单个字符）。 如果文件夹名内包含通配符或此转义字符，请使用 `^` 进行转义。 <br/><br/>例如：`myblobcontainer/myblobfolder/`。 请参阅[文件夹和文件筛选器示例](#folder-and-file-filter-examples)中的更多示例。 | 对于复制或查找活动为“是”，对于 GetMetadata 活动为“否” |
| fileName | 指定的“folderPath”值下 Blob 的名称或通配符筛选器。 如果没有为此属性指定任何值，则数据集会指向文件夹中的所有 Blob。 <br/><br/>对于筛选器，允许的通配符为 `*`（匹配零个或零个以上的字符）和 `?`（匹配零个或单个字符）。<br/>- 示例 1：`"fileName": "*.csv"`<br/>- 示例 2：`"fileName": "???20180427.txt"`<br/>如果文件名包含通配符或此转义字符，请使用 `^` 进行转义。<br/><br/>如果没有为输出数据集指定 `fileName`，并且没有在活动接收器中指定 `preserveHierarchy`，则复制活动会自动生成采用以下模式的 Blob 名称：“*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* ”。 例如：“Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”。 <br/><br/>如果使用表名称（而不是查询）从表格源进行复制，则名称模式为 `[table name].[format].[compression if configured]`。 例如：“MyTable.csv”。 | 否 |
| modifiedDatetimeStart | 文件根据“上次修改时间”属性进行筛选。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，要对大量文件进行筛选时，启用此设置会影响数据移动的整体性能。 <br/><br/> 属性可以为 `NULL`，这意味着不会向数据集应用任何文件属性筛选器。  如果  具有日期/时间值，但  为 NULL，则会选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果  具有日期/时间值，但  为 NULL，则会选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| modifiedDatetimeEnd | 文件根据“上次修改时间”属性进行筛选。 如果文件的上次修改时间在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd` 之间的时间范围内，则将选中这些文件。 该时间应用于 UTC 时区，格式为“2018-12-01T05:00:00Z”。 <br/><br/> 请注意，要对大量文件进行筛选时，启用此设置会影响数据移动的整体性能。 <br/><br/> 属性可以为 `NULL`，这意味着不会向数据集应用任何文件属性筛选器。  如果  具有日期/时间值，但  为 NULL，则会选中“上次修改时间”属性大于或等于该日期/时间值的文件。  如果  具有日期/时间值，但  为 NULL，则会选中“上次修改时间”属性小于该日期/时间值的文件。| 否 |
| format | 若要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>若要分析或生成具有特定格式的文件，以下是受支持的文件格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat    。 请将 **format** 中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)部分。 | 否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支持的类型为 **GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为“最佳”和“最快”。 | 否 |

>[!TIP]
>如需复制文件夹下的所有 blob，请仅指定 folderPath。<br>如需复制具有给定名称的单个 Blob，请使用文件夹部分指定 folderPath，并使用文件名指定 fileName。<br>如需复制文件夹下 Blob 的子集，请使用文件夹部分指定 folderPath，并使用通配符筛选器指定 fileName。 

**示例：**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-source-model-for-the-copy-activity"></a>复制活动的旧源模型

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 `type` 属性必须设置为 `BlobSource`。 | 是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 请注意，当 `recursive` 设置为 `true` 且接收器是基于文件的存储时，将不会在接收器上复制或创建空的文件夹或子文件夹。<br/>允许的值为 `true`（默认值）和 `false`。 | 否 |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-sink-model-for-the-copy-activity"></a>复制活动的旧接收器模型

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 `type` 属性必须设置为 `BlobSink`。 | 是 |
| copyBehavior | 定义以基于文件的数据存储中的文件为源时的复制行为。<br/><br/>允许值包括：<br/><b>- PreserveHierarchy（默认）</b>：将文件层次结构保留到目标文件夹中。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><b>- FlattenHierarchy</b>：源文件夹中的所有文件都位于目标文件夹的第一级中。 目标文件具有自动生成的名称。 <br/><b>- MergeFiles</b>：将源文件夹中的所有文件合并到一个文件中。 如果指定了文件名或 Blob 名称，则合并文件的名称为指定名称。 否则，它是自动生成的文件名。 | 否 |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤

有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
