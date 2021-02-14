---
title: 为扫描创建和管理凭据
description: 了解在 Azure 监控范围中创建和管理凭据的步骤。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 091f4d7a4acdcc5d1a2b89a5121ee0cff3ee1f55
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381182"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>用于 Azure 监控范围中的源身份验证的凭据

本文介绍如何在 Azure 监控范围中创建凭据。 这些保存的凭据使你可以快速重复使用保存的身份验证信息，并将其应用于数据源扫描。

## <a name="prerequisites"></a>先决条件

- Azure 密钥保管库。 若要了解如何创建一个 [密钥保管库，请参阅快速入门：使用 Azure 门户创建密钥保管库](../key-vault/general/quick-create-portal.md)。

## <a name="introduction"></a>简介

凭据是 Azure 监控范围可用于向已注册的数据源进行身份验证的身份验证信息。 可以为各种类型的身份验证方案创建凭据对象，如需要用户名/密码的基本身份验证。 凭据根据所选的身份验证方法，捕获进行身份验证所需的特定信息。 凭据使用现有的 Azure 密钥保管库机密来检索凭据创建过程中的敏感身份验证信息。

## <a name="use-purview-managed-identity-to-set-up-scans"></a>使用监控范围托管标识设置扫描

如果使用监控范围托管标识来设置扫描，则无需显式创建凭据并将密钥保管库链接到监控范围以存储它们。 有关添加监控范围托管标识以便有权扫描数据源的详细说明，请参阅下面的特定于数据源的身份验证部分：

- [Azure Blob 存储](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL 数据库](register-scan-azure-sql-database.md)
- [Azure SQL 数据库托管实例](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>在 Azure 监控范围帐户中创建 Azure 密钥保管库连接

首先，请将一个或多个现有 Azure Key Vault 实例与 Azure 监控范围帐户关联，然后才能创建凭据。

1. 在 [Azure 门户](https://portal.azure.com)中，选择你的 Azure 监控范围帐户。 导航到 **管理中心** ，然后导航到 " **凭据**"。

2. 从 " **凭据** " 页上，选择 " **管理 Key Vault 连接**"。

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="管理 Azure Key Vault 连接":::

3. 从 "管理 Key Vault 连接" 页中选择 " **+ 新建** "。

4. 提供所需的信息，然后选择 " **创建**"。

5. 确认 Key Vault 已成功与 Azure 监控范围帐户关联，如以下示例中所示：

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="查看 Azure Key Vault 连接以进行确认。":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>向监控范围托管标识授予对 Azure Key Vault 的访问权限

1. 导航到 Azure Key Vault。

2. 选择 " **访问策略** " 页。

3. 选择“添加访问策略”。

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="将监控范围 MSI 添加到 AKV":::

4. 在 " **机密权限** " 下拉列表中，选择 " **获取** 和 **列出** 权限"。

5. 对于 " **选择主体**"，请选择监控范围托管标识。

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="添加访问策略":::

6. 选择“添加”。

7. 选择 " **保存** " 以保存访问策略。

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="保存访问策略":::

## <a name="create-a-new-credential"></a>创建新凭据

监控范围支持这些凭据类型：

- 基本身份验证：将 **密码** 添加为密钥保管库中的机密。
- 服务主体：将 **服务主体密钥** 添加为密钥保管库中的机密。
- SQL 身份验证：将 **密码** 作为机密添加到密钥保管库中。
- 帐户密钥：将 **帐户密钥** 作为机密添加到密钥保管库中。

有关详细信息，请参阅 [将机密添加到 Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)。

在密钥保管库中存储机密后：

1. 在 Azure 监控范围中，请参阅 "凭据" 页。

2. 通过选择 " **+ 新建**" 来创建新凭据。

3. 提供所需的信息。 选择 **身份验证方法** 和 **Key Vault 连接** ，从中从中选择机密。

4. 填写所有详细信息后，选择 " **创建**"。

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="新凭据":::

5. 验证新凭据是否显示在列表视图中并可供使用。

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="查看凭据":::

## <a name="manage-your-key-vault-connections"></a>管理密钥保管库连接

1. 按名称搜索/查找 Key Vault 连接

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="搜索密钥保管库":::

2. 删除一个或多个 Key Vault 连接

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="删除密钥保管库":::

## <a name="manage-your-credentials"></a>管理凭据

1. 按名称搜索/查找凭据。
  
2. 选择并更新现有凭据。

3. 删除一个或多个凭据。

## <a name="next-steps"></a>后续步骤

[创建扫描规则集](create-a-scan-rule-set.md)
