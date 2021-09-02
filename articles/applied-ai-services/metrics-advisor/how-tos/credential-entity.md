---
title: 创建凭据实体
titleSuffix: Azure Cognitive Services
description: 如何创建凭据实体来管理安全凭据。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: mbullwin
ms.openlocfilehash: 0e16b92ff4a2316042e2177f9525734ba1949db1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341127"
---
# <a name="how-to-create-a-credential-entity"></a>如何：创建凭据实体

载入数据馈送时，应选择身份验证类型，某些身份验证类型（例如 Azure SQL 连接字符串和服务主体）需要凭据实体来存储凭据相关信息，以便安全地管理凭据 。 本文将介绍如何在指标顾问中为不同的凭据类型创建凭据实体。
    

## <a name="basic-procedure-create-a-credential-entity"></a>基本过程：创建凭据实体

你可以创建凭据实体来存储凭据相关信息，并将其用于对数据源进行身份验证。 可以与其他人共享凭据实体，使其能够连接到数据源，而无需共享真实凭据。 可以在“添加数据馈送”选项卡或“凭据实体”选项卡中创建。为特定身份验证类型创建凭据实体后，只需选择添加新数据馈送时创建的一个凭据实体，这对于创建多个数据馈送非常有用。 创建和使用凭据实体的一般过程如下所示：

1. 选择“+”以在“添加数据馈送”选项卡中创建新的凭据实体（你还可以在“凭据实体源”选项卡中创建凭据实体）。

   ![创建凭据实体](../media/create-credential-entity.png)
 
2. 设置凭据实体名称、说明（如果需要）、凭据类型（等于身份验证类型）和其他设置。

   ![设置凭据实体](../media/set-credential-entity.png)
 
3. 创建凭据实体后，可以在指定身份验证类型时选择该实体。

   ![选择凭据实体](../media/choose-credential-entity.png)
 
指标顾问中有四种凭据类型： Azure SQL 连接字符串、Azure Data Lake Storage Gen2 共享密钥实体、服务主体、服务主体，Key Vault。 有关不同凭据类型设置，请参阅以下说明。

## <a name="azure-sql-connection-string"></a>Azure SQL 连接字符串

你应该设置名称和连接字符串，然后选择“创建” 。

![为 sql 连接字符串设置凭据实体](../media/credential-entity/credential-entity-sql-connection-string.png)

## <a name="azure-data-lake-storage-gen2-shared-key-entity"></a>Azure Data Lake Storage Gen2 共享密钥实体

你应该设置名称和帐户密钥，然后选择“创建” 。 可以在访问密钥设置中的 Azure 存储帐户 (Azure Data Lake Storage Gen2) 资源中找到帐户密钥。

<!-- 增加basic说明，tips是错的；增加一下怎么管理；加一个step1的link
-->
![设置数据湖的凭据实体](../media/credential-entity/credential-entity-data-lake.png)

## <a name="service-principal"></a>服务主体

若要为数据源创建服务主体，可以按照[连接不同数据源](../data-feeds-from-different-sources.md)中的详细说明进行操作。 在创建服务主体之后，需要在凭据实体中填写以下配置。

![sp 凭据实体](../media/credential-entity/credential-entity-service-principal.png)

* 名称：设置服务主体凭据实体的名称。
* 租户 ID 和客户端 ID：在 Azure 门户中创建服务主体后，你可以在概览中找到 `Tenant ID` 和 `Client ID`。

    ![sp 客户端 ID 和租户 ID](../media/credential-entity/sp-client-tenant-id.png)

* 客户端密码：在 Azure 门户中创建服务主体后，应该转至证书和密码，以创建新的客户端密码，在凭据实体中应将值作为 `Client Secret` 使用。 （注意：值仅出现一次，因此最好将其存储在某个位置。）


    ![sp 客户端密码值](../media/credential-entity/sp-secret-value.png)

## <a name="span-idsp-from-kvservice-principal-from-key-vaultspan"></a><span id="sp-from-kv">密钥保管库中的服务主体</span>

从密钥保管库创建服务主体分为多个步骤。

步骤 1. 创建一个服务主体，并向其授予对数据库的访问权限。 若要为每个数据源创建服务主体分区，可以按照[连接不同数据源](../data-feeds-from-different-sources.md)中的详细说明进行操作。 

在 Azure 门户中创建服务主体后，你可以在概览中找到 `Tenant ID` 和 `Client ID`。 目录（租户）ID 在凭据实体配置中应为 `Tenant ID`。

![sp 客户端 ID 和租户 ID](../media/credential-entity/sp-client-tenant-id.png)

步骤 2. 创建新的客户端密码。 应转至证书和密码，以创建新的客户端密码，且在接下来的步骤中应使用值。 （注意：值仅出现一次，因此最好将其存储在某个位置。）

![sp 客户端密码值](../media/credential-entity/sp-secret-value.png)

步骤 3. 创建密钥保管库。 在 [Azure 门户](https://ms.portal.azure.com/#home)中选择密钥保管库来创建保管库。

![在 Azure 门户中创建密钥保管库](../media/credential-entity/create-key-vault.png)

创建密钥保管库后，保管库 URI 是 MA（指标顾问）凭据实体中的 `Key Vault Endpoint`。

![密钥保管库终结点](../media/credential-entity/key-vault-endpoint.png)

步骤 4. 为密钥保管库创建密码。 在密钥保管库的 Azure 门户中，在“设置”->“密码”中生成两个密码。
第一个用于 `Service Principal Client Id`，另一个用于 `Service Principal Client Secret`，其名称将用于凭据实体配置。

![生成密码](../media/credential-entity/generate-secrets.png)

* 服务主体客户端 ID：为此密码设置 `Name`，此密码的名称将在凭据实体配置中使用，且值应为步骤 1 中的 `Client ID`。

    ![密码 1：sp 客户端 ID](../media/credential-entity/secret-1-sp-client-id.png)

* 服务主体客户端密码：为此密码设置 `Name`，此密码的名称将在凭据实体配置中使用，且值应为步骤 2 中的 `Client Secret Value`。

    ![密码 2：sp 客户端密码](../media/credential-entity/secret-2-sp-secret-value.png)

至此，服务主体的客户端 ID 和客户端密码最终存储在密钥保管库中 。 接下来，需要创建另一个服务主体来存储密钥保管库。 因此，你应创建两个服务主体，一个用于保存客户端 ID 和客户端密码，它们将存储在密钥保管库中，另一个用于存储密钥保管库。

步骤 5.创建用于存储密钥保管库的服务主体。 

1. 转至 [Azure 门户 AAD (Azure Active Directory)](https://portal.azure.com/?trace=diagnostics&feature.customportal=false#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)，并创建一个新的注册。

    ![创建一个新的注册](../media/credential-entity/create-registration.png)

    创建服务主体后，概述中的应用程序（客户端）ID 将为凭据实体配置中的 `Key Vault Client ID`。

2. 在“管理”->“证书和密码”中，通过选择“新的客户端密码”来创建客户端密码。 然后，应向下复制值，因为值仅出现一次。 该值为凭据实体配置中的 `Key Vault Client Secret`。

    ![添加客户端密码](../media/credential-entity/add-client-secret.png)

步骤 6.为服务主体授予对密钥保管库的访问权限。  转至你在“设置”->“访问政策”中创建的密钥保管库资源，通过选择“添加访问策略”，在密钥保管库和步骤 5 中的第二个服务主体之间进行连接，然后选择“保存” 。

![向密钥保管库授予 sp](../media/credential-entity/grant-sp-to-kv.png)


## <a name="configurations-conclusion"></a>配置结论
最后，下表中显示了用于服务主体的指标顾问中的凭据实体配置密钥保管库和获取它们的方式：

| 配置 | 如何获取 |
|-------------| ---------------------|
| 密钥保管库终结点 | 步骤 3：密钥保管库的保管库 URI。 |
| 租户 ID | 步骤 1：第一个服务主体的目录（租户）ID。 |
| 密钥保管库客户端 ID | 步骤 5：第二个服务主体的应用程序（客户端）ID。 |
| 密钥保管库客户端密码 | 步骤 5：第二个服务主体的客户端密码值。 |
| 服务主体客户端 ID 名称 | 步骤 4：为客户端 ID 设置的密码名称。 |
| 服务主体客户端密码名称 | 步骤 4：为客户端密码值设置的密码名称。 |


## <a name="next-steps"></a>后续步骤

- [载入数据](onboard-your-data.md)
- [连接不同的数据源](../data-feeds-from-different-sources.md)
