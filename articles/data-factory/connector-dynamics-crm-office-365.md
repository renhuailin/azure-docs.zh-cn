---
title: 在 Dynamics 中复制数据 (Microsoft Dataverse)
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何通过使用 Azure 数据工厂或 Azure Synapse Analytics 管道中的复制活动，将数据从 Microsoft Dynamics CRM 或 Microsoft Dynamics 365 (Microsoft Dataverse) 复制到受支持的接收器数据存储，或者从受支持的源数据存储复制到 Dynamics CRM 或 Dynamics 365。
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.author: jianleishen
author: jianleishen
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 6f95e117865ccf9d242d595ec98b66d7cd344a85
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597644"
---
# <a name="copy-data-from-and-to-dynamics-365-microsoft-dataverse-or-dynamics-crm"></a>从/向 Dynamics 365 (Microsoft Dataverse) 或 Dynamics CRM 复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
本文概述了如何使用 Azure 数据工厂或 Synapse 管道中的复制活动，从/向 Microsoft Dynamics 365 和 Microsoft Dynamics CRM 复制数据。 它基于[复制活动概述](copy-activity-overview.md)一文，该文概述了复制活动。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此连接器：

- [复制活动](copy-activity-overview.md)和[支持的源和接收器矩阵](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可将数据从 Dynamics 365 (Microsoft Dataverse) 或 Dynamics CRM 复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到 Dynamics 365 (Microsoft Dataverse) 或 Dynamics CRM。 如需复制活动支持将其用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

>[!NOTE]
>自 2020 年 11 月起，Common Data Service 已更名为 [Microsoft Dataverse](/powerapps/maker/data-platform/data-platform-intro)。 本文已更新并使用最新术语。 

此 Dynamics 连接器支持联机的和本地的 Dynamics 版本 7-9。 更具体地说：
- 版本 7 映射到 Dynamics CRM 2015。
- 版本 8 映射到 Dynamics CRM 2016 和早期版 Dynamics 365。
- 版本 9 映射到较新版 Dynamics 365。


请参阅下表，了解各种 Dynamics 版本和产品支持的身份验证类型和配置。

| Dynamics 版本 | 身份验证类型 | 链接的服务示例 |
|:--- |:--- |:--- |
| Dataverse <br/><br/> Dynamics 365 联机 <br/><br/> Dynamics CRM 联机 | Azure Active Directory (Azure AD) 服务主体 <br/><br/> Office 365 | [联机 Dynamics 和 Azure AD 服务主体或 Office 365 身份验证](#dynamics-365-and-dynamics-crm-online) |
| 本地 Dynamics 365，具有面向 Internet 的部署 (IFD) <br/><br/> 带有 IFD 的本地 Dynamics CRM 2016 <br/><br/> 带有 IFD 的本地 Dynamics CRM 2015 | IFD | [带有 IFD 和 IFD 身份验证的本地 Dynamics](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

>[!NOTE]
>因[弃用区域发现服务](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)，该服务已升级为在使用 Office 365 身份验证时利用[全局发现服务](/powerapps/developer/data-platform/webapi/discover-url-organization-web-api#global-discovery-service)。

> [!IMPORTANT]
>如果在 Azure Active Directory 中为[条件访问](../active-directory/conditional-access/overview.md)租户和用户和/或需要多重身份验证，你将无法使用 Office 365 身份验证类型。 在这些情况下，必须使用 Azure Active Directory (Azure AD) 服务主体身份验证。

具体而言，对于 Dynamics 365，支持以下应用程序类型：
- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing 此连接器不支持其他应用程序类型，如“财务”、“运营”和“人才”。

>[!TIP]
>若要从 Dynamics 365 Finance and Operations 复制数据，可以使用 [Dynamics AX 连接器](connector-dynamics-ax.md)。

此 Dynamics 连接器基于 [Dynamics XRM 工具](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)构建。

## <a name="prerequisites"></a>先决条件
若要将此连接器与 Azure AD 服务主体身份验证配合使用，必须在 Dataverse 或 Dynamics 中设置服务器到服务器 (S2S) 身份验证。 先在 Azure Active Directory 中注册应用程序用户（服务主体）。 可在[此处](../active-directory/develop/howto-create-service-principal-portal.md)了解如何执行此操作。 在应用程序注册期间，需在 Dataverse 或 Dynamics 中创建该用户并授予权限。 可以直接授予这些权限，也可以通过将应用程序用户添加到已在 Dataverse 或 Dynamics 中为其授予了这些权限的团队来间接授权。 可在[此处](/powerapps/developer/data-platform/use-single-tenant-server-server-authentication)详细了解如何将应用程序用户设置为使用 Dataverse 进行身份验证。 


## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-dynamics-365-using-ui"></a>使用 UI 创建到 Dynamics 365 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 Dynamics 365 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="屏幕截图，显示如何使用 Azure 数据工厂 UI 创建新的链接服务。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索“Dynamics”并选择 Dynamics 365 连接器。

    :::image type="content" source="media/connector-dynamics-crm-office-365/dynamics-crm-office-365-connector.png" alt-text="Dynamics 365 连接器的屏幕截图。":::    

1. 配置服务详细信息、测试连接并创建新的链接服务。

    :::image type="content" source="media/connector-dynamics-crm-office-365/configure-dynamics-crm-office-365-linked-service.png" alt-text="Dynamics 365 的链接服务配置的屏幕截图。":::

## <a name="connector-configuration-details"></a>连接器配置详细信息

以下部分提供了有关各属性的详细信息，这些属性用于定义特定于 Dynamics 的实体。

## <a name="linked-service-properties"></a>链接服务属性

Dynamics 链接服务支持以下属性。

### <a name="dynamics-365-and-dynamics-crm-online"></a>联机 Dynamics 365 和 Dynamics CRM

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为“Dynamics”、“DynamicsCrm”或“CommonDataServiceForApps”。 | 是 |
| deploymentType | Dynamics 实例的部署类型。 对于联机 Dynamics，此值必须为“Online”。 | 是 |
| serviceUri | Dynamics 实例的服务 URL，与从浏览器访问的 URL 相同。 例如“https://\<organization-name>.crm[x].dynamics.com”。 | 是 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 有效值为“AADServicePrincipal”和“Office365”。 | 是 |
| servicePrincipalId | Azure AD 应用程序的客户端 ID。 | 当身份验证为“AADServicePrincipal”时，此项为必需的 |
| servicePrincipalCredentialType | 要用于服务主体身份验证的凭据类型。 有效值为“ServicePrincipalKey”和“ServicePrincipalCert”。 | 当身份验证为“AADServicePrincipal”时，此项为必需的 |
| servicePrincipalCredential | 服务主体凭据。 <br/><br/>使用“ServicePrincipalKey”作为凭据类型时，`servicePrincipalCredential` 可以是服务在进行链接服务部署时加密的字符串。 它也可以是对 Azure Key Vault 中机密的引用。 <br/><br/>使用“ServicePrincipalCert”作为凭据时，`servicePrincipalCredential` 必须是对 Azure Key Vault 中证书的引用。 | 当身份验证为“AADServicePrincipal”时，此项为必需的 |
| username | 用于连接到 Dynamics 的用户名。 | 当身份验证为“Office365”时，此项为必需的 |
| password | 作为用户名指定的用户帐户的密码。 将此字段标记为“SecureString”以安全地存储它，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 当身份验证为“Office365”时，此项为必需的 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定任何值，则此属性使用默认的 Azure 集成运行时。 | 否 |

>[!NOTE]
>Dynamics 连接器以前使用可选的 **organizationName** 属性来标识 Dynamics CRM 或 Dynamics 365 联机实例。 虽然该属性仍然有效，但建议你改为指定新的 **serviceUri** 属性以实现更好的实例发现性能。

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>示例：使用 Azure AD 服务主体和密钥身份验证的联机 Dynamics

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>示例：使用 Azure AD 服务主体和证书身份验证的联机 Dynamics

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```
#### <a name="example-dynamics-online-using-office-365-authentication"></a>示例：使用 Office 365 身份验证的联机 Dynamics

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>带有 IFD 的本地 Dynamics 365 和 Dynamics CRM

与联机 Dynamics 进行对比的其他属性是 **hostName** 和 **port**。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为“Dynamics”、“DynamicsCrm”或“CommonDataServiceForApps”。 | 是的。 |
| deploymentType | Dynamics 实例的部署类型。 对于带有 IFD 的本地 Dynamics，此值必须为“OnPremisesWithIfd”。| 是的。 |
| hostName | 本地 Dynamics 服务器的主机名称。 | 是的。 |
| port | 本地 Dynamics 服务器的端口。 | 否。 默认值为 443。 |
| organizationName | Dynamics 实例的组织名称。 | 是的。 |
| authenticationType | 要连接到 Dynamics 服务器的身份验证类型。 对于带有 IFD 的本地 Dynamics，请指定“Ifd”。 | 是的。 |
| username | 用于连接到 Dynamics 的用户名。 | 是的。 |
| password | 指定为用户名的用户帐户的密码。 可以将此字段标记为“SecureString”以安全地存储它。 也可将密码存储在 Key Vault 中，让复制活动在进行数据复制时从其中拉取密码。 在[在 Key Vault 中存储凭据](store-credentials-in-key-vault.md)中了解详细信息。 | 是的。 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果未指定任何值，则此属性使用默认的 Azure 集成运行时。 | 否 |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>示例：带有 IFD 且使用 IFD 身份验证的本地 Dynamics

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Dynamics 数据集支持的属性列表。

支持使用以下属性从/向 Dynamics 复制数据：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为“DynamicsEntity”、“DynamicsCrmEntity”或“CommonDataServiceForAppsEntity”。 |是 |
| entityName | 要检索的实体的逻辑名称。 | 对于源非必需（如果将活动源指定为“query”），对于接收器为必需的 |

#### <a name="example"></a>示例

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Dynamics 源和接收器类型支持的属性列表。

### <a name="dynamics-as-a-source-type"></a>将 Dynamics 用作源类型

为了从 Dynamics 复制数据，复制活动的 **source** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为“DynamicsSource”、“DynamicsCrmSource”或“CommonDataServiceForAppsSource”。 | 是 |
| query | FetchXML 是在联机和本地 Dynamics 中使用的专属查询语言。 请参阅以下示例。 若要了解详细信息，请参阅[使用 FetchXML 生成查询](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8))。 | 否（如果指定了数据集中的 `entityName`） |

>[!NOTE]
>即使在 FetchXML 查询中配置的列投影不包含 PK 列，也将始终复制该列。

> [!IMPORTANT]
>- 从 Dynamics 复制数据时，从 Dynamics 到接收器的显式列映射为可选的， 但我们强烈建议使用此映射，以确保获得确定性的复制结果。
>- 当服务在创作 UI 中导入架构时，会对架构进行推断。 其方法是：对 Dynamics 查询结果中的前几行进行采样来初始化源列列表。 在这种情况下，将省略在前几行中不含任何值的列。 如果没有明确的映射，则相同的行为也适用于复制执行。 可以查看列并将更多列添加到映射，这将在副本运行时起作用。

#### <a name="example"></a>示例

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>示例 FetchXML 查询

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>将 Dynamics 用作接收器类型

为了将数据复制到 Dynamics，复制活动的 **sink** 节支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为“DynamicsSink”、“DynamicsCrmSink”或“CommonDataServiceForAppsSink”。 | 是的。 |
| writeBehavior | 操作的写入行为。 值必须是“Upsert”。 | 是 |
| alternateKeyName | 在实体上定义的用于执行更新插入的备用密钥名称。 | 否。 |
| writeBatchSize | 每批中写入到 Dynamics 的数据行计数。 | 否。 默认值为 10。 |
| ignoreNullValues | 在执行写入操作期间是否忽略输入数据中的 NULL 值（键字段除外）。<br/><br/>有效值为 **TRUE** 和 **FALSE**：<ul><li>**TRUE**：执行更新插入或更新操作时，保持目标对象中的数据不变。 插入在执行插入操作时定义的默认值。</li><li>**FALSE**：执行更新插入或更新操作时，将目标对象中的数据更新为 NULL 值。 执行插入操作时插入 NULL 值。</li></ul> | 否。 默认值为 **FALSE**。 |
| maxConcurrentConnections |活动运行期间与数据存储建立的并发连接的上限。 仅在要限制并发连接时指定一个值。| 否 |

>[!NOTE]
>接收器“writeBatchSize”和 Dynamics 接收器的复制活动“[parallelCopies](copy-activity-performance-features.md#parallel-copy)” 的默认值都是 10。 因此，默认情况下会将 100 条记录并发提交到 Dynamics。

对于联机 Dynamics 365，存在[每个组织进行两次并发批量调用](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations)的限制。 如果超出该限制，则会在运行第一个请求之前引发“服务器忙”异常。 使 **writeBatchSize** 保持小于或等于 10 可避免这种并发调用限制。

**writeBatchSize** 和 **parallelCopies** 的最佳组合取决于实体的架构。 架构元素包括列数、行大小以及挂到这些调用的插件、工作流或工作流活动的数量。 默认设置 **writeBatchSize** (10) &times; **parallelCopies** (10) 是基于 Dynamics 服务提供的建议设置。 该值适用于大多数 Dynamics 实体，但可能无法提供最佳性能。 你可以通过在复制活动设置中调整组合来调整性能。

#### <a name="example"></a>示例

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="retrieving-data-from-views"></a>从视图检索数据

若要从 Dynamics 视图检索数据，需要获取视图已保存的查询，并使用该查询来获取数据。

有两个实体用于存储不同类型的视图：“已保存的查询”存储系统视图，而“用户查询”存储用户视图。 若要获取这些视图的信息，请参阅下面的 FetchXML 查询，并将“TARGETENTITY”替换为 `savedquery` 或 `userquery`。 每个实体类型都有更多可用属性，你可根据需要向查询添加它们。 详细了解 [savedquery 实体](/dynamics365/customer-engagement/web-api/savedquery)和 [userquery 实体](/dynamics365/customer-engagement/web-api/userquery)。

```xml
<fetch top="5000" >
  <entity name="<TARGETENTITY>">
    <attribute name="name" />
    <attribute name="fetchxml" />
    <attribute name="returnedtypecode" />
    <attribute name="querytype" />
  </entity>
</fetch>
```

还可添加筛选器来筛选视图。 例如，添加以下筛选器以获取帐户实体中名为“我的活动帐户”的视图。

```xml
<filter type="and" >
    <condition attribute="returnedtypecode" operator="eq" value="1" />
    <condition attribute="name" operator="eq" value="My Active Accounts" />
</filter>
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics 的数据类型映射

下表显示了从 Dynamics 复制数据时，从 Dynamics 数据类型到该服务中临时数据类型的映射。 若要了解复制活动如何映射到源架构以及数据类型如何映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

使用以下映射表，在基于源 Dynamics 数据类型的数据集结构中配置相应的临时数据类型：

| Dynamics 数据类型 | 服务临时数据类型 | 支持用作源 | 支持用作接收器 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Long | ✓ | ✓ |
| AttributeTypeCode.Boolean | 布尔 | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓（请参阅[指南](#writing-data-to-a-lookup-field)） |
| AttributeType.DateTime | datetime | ✓ | ✓ |
| AttributeType.Decimal | 小数 | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓（请参阅[指南](#writing-data-to-a-lookup-field)） |
| AttributeType.ManagedProperty | 布尔 | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | 小数 | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓（请参阅[指南](#writing-data-to-a-lookup-field)） |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Dynamics 数据类型 **AttributeType.CalendarRules**、**AttributeType.MultiSelectPicklist** 和 **AttributeType.PartyList** 不受支持。

## <a name="writing-data-to-a-lookup-field"></a>将数据写入查找字段

若要将数据写入包含多个目标（例如 Customer 和 Owner）的查找字段，请遵循以下指导和示例：

1. 使源同时包含字段值和相应的目标实体名称。
   - 如果所有记录都映射到同一目标实体，请确保满足以下条件之一：
      - 源数据包含一个可存储目标实体名称的列。
      - 你已在复制活动源中添加了一个额外的列来定义目标实体。
   - 如果不同的记录映射到不同的目标实体，请确保源数据包含一个可存储相应目标实体名称的列。

1. 将值列和实体引用列都从源映射到接收器。 实体引用列必须通过特殊命名模式 `{lookup_field_name}@EntityReference` 映射到虚拟列。 此列实际上并不存在于 Dynamics 中。 它用来指示此列是给定多目标查找字段的元数据列。

例如，假设源包含下面的两个列：

- 类型为 **GUID** 的 **CustomerField** 列，它是 Dynamics 中目标实体的主键值。
- 类型为 **String** 的 **Target** 列，它是目标实体的逻辑名称。

另外假设你希望将此类数据复制到类型为 **Customer** 的接收器 Dynamics 实体字段 **CustomerField**。

在复制活动列映射中，如下所述映射这两个列：

- 将 **CustomerField** 映射到 **CustomerField**。 此映射是标准字段映射。
- 将 **Target** 映射到 **CustomerField\@EntityReference**。 接收器列是表示实体引用的虚拟列。 请在映射中输入此类字段名称，因为不可通过导入架构来显示它们。

:::image type="content" source="./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png" alt-text="Dynamics 查找字段列映射":::

如果所有源记录都映射到同一目标实体，并且源数据不包含目标实体名称，则可使用下面的快捷方式：在复制活动源中添加一个额外的列。 使用模式 `{lookup_field_name}@EntityReference` 命名新列，将值设置为目标实体名称，然后按常规方式进行列映射。 如果源和接收器列名称相同，则还可跳过显式列映射，因为默认情况下，复制活动按名称映射列。

:::image type="content" source="./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png" alt-text="Dynamics 查找字段：添加实体引用列":::

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请参阅[“查找”活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤

有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
