---
title: 排查 Azure Purview 中的连接问题
description: 本文介绍排查 Azure Purview 中的连接问题的步骤。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: cfcac68bfc09a4c3b6d4794469734d0634b3ad0c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213451"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>排查 Azure Purview 中的连接问题

本文介绍如何排查在 Azure Purview 中设置数据源扫描时出现的连接错误。

## <a name="permission-the-credential-on-the-data-source"></a>对数据源上的凭据授权

如果使用托管标识或服务主体来对扫描进行身份验证，必须允许这些标识访问你的数据源。

下面是每个源类型的特定说明：

- [Azure 多个源](register-scan-azure-multiple-sources.md#set-up-authentication-to-scan-resources-under-a-subscription-or-resource-group)
- [Azure Blob 存储](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure 数据资源管理器](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL 数据库](register-scan-azure-sql-database.md)
- [Azure SQL 数据库托管实例](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-s3-scan)

## <a name="verifying-azure-role-based-access-control-to-enumerate-azure-resources-in-azure-purview-studio"></a>验证 Azure 基于角色的访问控制，以枚举 Azure Purview Studio 中的 Azure 资源

### <a name="registering-single-azure-data-source"></a>注册单个 Azure 数据源
若要在 Azure Purview 中注册单个数据源（如 Azure 博客存储或 Azure SQL 数据库），必须至少具有对该资源的“读者”角色或从更高范围（例如资源组或订阅）继承。 请注意，某些 Azure RBAC 角色（如安全管理员）没有在控制平面中查看 Azure 资源的读取访问权限。  

按照以下步骤对此进行验证：

1. 在 [Azure 门户](https://portal.azure.com)中，导航到要在 Azure Purview 中注册的资源。 如果可以查看该资源，那么你可能至少已对该资源具有读者角色。 
2. 选择“访问控制(IAM)” > “角色分配” 。
3. 按尝试在 Azure Purview 中注册数据源的用户的姓名或电子邮件地址进行搜索。
4. 验证在列表中是否存在任何角色分配（如“读者”），或者根据需要添加新的角色分配。

### <a name="scanning-multiple-azure-data-sources"></a>扫描多个 Azure 数据源
1. 在 [Azure 门户中](https://portal.azure.com)，导航到订阅或资源组。  
2. 从左侧菜单中选择“访问控制(IAM)” **** 。  
3. 选择“+添加”  。 
4. 在“选择输入”框中选择“读者”角色，然后输入 Azure Purview 帐户名称（表示其 MSI 名） 。 
5. 选择“保存”以完成角色分配。
6. 重复上述步骤，添加在 Azure Purview 中尝试为多个数据源创建新扫描的用户的标识。

## <a name="scanning-data-sources-using-private-link"></a>使用专用链接扫描数据源 
如果对数据源限制了公共终结点，若要使用专用链接扫描 Azure 数据源，需要设置自承载集成运行时并创建凭据。 

> [!IMPORTANT]
> 扫描包含如 Azure SQL 数据库这样具有“拒绝公共网络访问权限”的数据库的多个数据源将失败。 若要使用专用终结点扫描这些数据源，请改为使用注册单个数据源选项。

有关设置自承载集成运行时的详细信息，请参阅[引入专用终结点和扫描源](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)

有关如何在 Azure Purview 中创建新凭据的详细信息，请参阅 [Azure Purview 中的源身份验证凭据](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>将凭据存储在 Key Vault 中，并使用正确的机密名称和版本

还必须将凭据存储在 Azure Key Vault 实例中，并使用正确的机密名称和版本。

按照以下步骤对此进行验证：

1. 导航到你的 Key Vault。
1. 选择“设置” > “机密”。
1. 选择要用于针对要扫描的数据源进行身份验证的机密。
1. 选择要使用的版本，并选择“显示机密值”来验证密码或帐户密钥是否正确。 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>验证 Azure Key Vault 上的 Purview 托管标识的权限

验证是否已为 Purview 托管标识配置访问你的 Azure Key Vault 的权限。

请执行以下步骤来进行验证：

1. 导航到你的 Key Vault，然后转到“访问策略”部分

1. 验证你的 Purview 托管标识是否显示在“当前访问策略”部分下，且是否至少对机密具有 Get 和 List 权限 

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="显示具有 Get 和 List 权限选项的下拉列表选择":::

如果没看到列出你的 Purview 托管标识，请按照[针对扫描创建和管理凭据](manage-credentials.md)来添加该标识。 

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
