---
title: 排查 Azure Purview 中的连接问题
description: 本文介绍排查 Azure Purview 中的连接问题的步骤。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c176fcafe13749ba89c04b34854f036aa5aea516
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677645"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>排查 Azure Purview 中的连接问题

本文介绍如何排查在 Azure Purview 中设置数据源扫描时出现的连接错误。

## <a name="permission-the-credential-on-the-data-source"></a>对数据源上的凭据授权

如果使用托管标识或服务主体来对扫描进行身份验证，必须允许这些标识访问你的数据源。

下面是每个源类型的特定说明：

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
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-bucket-scan)
## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>将凭据存储在 Key Vault 中，并使用正确的机密名称和版本

还必须将凭据存储在 Azure Key Vault 实例中，并使用正确的机密名称和版本。

按照以下步骤对此进行验证：

1. 导航到你的 Key Vault。
1. 选择“设置” > “机密”。
1. 选择要用于针对要扫描的数据源进行身份验证的机密。
1. 选择要使用的版本，并单击“显示机密值”来验证密码或帐户密钥是否正确。 

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
