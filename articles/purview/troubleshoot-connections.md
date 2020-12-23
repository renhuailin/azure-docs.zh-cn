---
title: Azure 监控范围中的连接疑难解答
description: 本文介绍了在 Azure 监控范围中对连接进行故障排除的步骤。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: f76a05757f86308785d4ca678675b87b3fa9d63e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551769"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Azure 监控范围中的连接疑难解答

本文介绍如何在 Azure 监控范围中设置对数据源的扫描时解决连接错误。

## <a name="permission-the-credential-on-the-data-source"></a>针对数据源的凭据的权限

如果使用托管标识或服务主体作为扫描的身份验证方法，则必须允许这些标识有权访问数据源。

每种源类型都有特定说明：

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

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>在密钥保管库中存储凭据并使用正确的机密名称和版本

还必须将凭据存储在 Azure Key Vault 实例中，并使用正确的机密名称和版本。

按照以下步骤进行验证：

1. 导航到 Key Vault。
1. 选择“设置” > “机密”。
1. 选择用于对数据源进行身份验证以进行扫描的机密。
1. 选择要使用的版本，并通过单击 " **显示机密值**" 来验证密码或帐户密钥是否正确。 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>验证 Azure Key Vault 上的监控范围托管标识的权限

验证是否已为监控范围托管标识配置了正确的权限以访问 Azure Key Vault。

若要验证这一点，请执行以下步骤：

1. 导航到密钥保管库，然后转到 " **访问策略** " 部分
1. 验证你的监控范围托管标识是否显示在 " *当前访问策略* " 部分下，并提供对机密的 "至少 **获取** " 权限

如果看不到列出的监控范围托管标识，请按照 [创建和管理用于扫描的凭据](manage-credentials.md) 中的步骤添加。 

## <a name="next-steps"></a>后续步骤

- [浏览 Azure 监控范围数据目录](how-to-browse-catalog.md)
- [搜索 Azure 监控范围数据目录](how-to-search-catalog.md)
