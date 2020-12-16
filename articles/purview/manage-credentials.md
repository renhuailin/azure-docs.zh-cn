---
title: 为扫描创建和管理凭据
description: 本文介绍了在 Azure 监控范围中创建和管理凭据的步骤。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 30004306d9ff44df04a26640a2bd7a09256fce25
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516681"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>用于 Azure 监控范围中的源身份验证的凭据

本文介绍如何在 Azure 监控范围中创建凭据，以便快速重复使用保存的身份验证信息并将其应用于数据源扫描。

## <a name="prerequisites"></a>先决条件

* Azure 密钥保管库。 若要了解如何创建一个 [密钥保管库，请参阅快速入门：使用 Azure 门户创建密钥保管库](../key-vault/general/quick-create-portal.md)。

## <a name="introduction"></a>简介
凭据是 Azure 监控范围可用于向已注册的数据源进行身份验证的身份验证信息。 可以为各种类型的身份验证方案创建凭据对象 (如需要用户名/密码) 的基本身份验证，并将根据所选的身份验证方法类型捕获所需的特定信息。 凭据使用现有的 Azure 密钥保管库机密来检索凭据创建过程中的敏感身份验证信息。

## <a name="using-purview-managed-identity-to-set-up-scans"></a>使用监控范围托管标识设置扫描
如果使用监控范围托管标识来设置扫描，则无需显式创建凭据并将密钥保管库链接到监控范围以存储它们。 有关添加监控范围托管标识以便有权扫描数据源的详细说明，请参阅下面的数据源特定身份验证部分：

- [Azure Blob 存储](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL 数据库](register-scan-azure-sql-database.md)
- [Azure SQL 数据库托管实例](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>在 Azure 监控范围帐户中创建 Azure 密钥保管库连接

你首先需要将一个或多个现有 Azure Key Vault 实例与 Azure 监控范围帐户关联，然后才能创建凭据。

1. 在 Azure 监控范围导航菜单中，导航到管理中心，然后导航到 "凭据"

2. 从 "凭据" 命令栏中，选择 "管理 Key Vault 连接"

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="管理 AKV 连接":::

3. 从 "管理 Key Vault 连接" 面板中选择 "+ 新建" 

4. 提供所需的信息，然后选择 "创建"

5. 确认你的 Key Vault 已成功与你的 Azure 监控范围帐户关联

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="查看 AKV 连接":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>向监控范围托管标识授予对 Azure Key Vault 的访问权限

导航到密钥保管库-> 访问策略-> "添加访问策略"。 在 "机密权限" 下拉列表中授予 Get 权限，并选择 "主体" 作为监控范围 MSI。 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="将监控范围 MSI 添加到 AKV":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="添加访问策略":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="保存访问策略":::

## <a name="create-a-new-credential"></a>创建新凭据

目前在监控范围中支持的凭据类型：
* 基本身份验证：将 **密码** 添加为密钥保管库中的机密
* 服务主体：将 **服务主体密钥** 添加为密钥保管库中的机密 
* SQL 身份验证：你将在密钥保管库中将 **密码** 添加为机密
* 帐户密钥：将 **帐户密钥** 作为机密添加到密钥保管库中

下面是有关如何将机密添加到密钥保管库的详细信息： (插入密钥保管库 "一文) 

在密钥保管库中存储机密后，通过从凭据命令栏中选择 "+ 新建" 来创建新凭据。 提供所需的信息，包括选择要从中从中选择密码的身份验证方法和 Key Vault 实例。 填写所有详细信息后，单击 "创建"。

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="新凭据":::

验证新凭据是否显示在凭据列表视图中并可供使用

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="查看凭据":::

## <a name="manage-your-key-vault-connections"></a>管理密钥保管库连接

1. 按名称搜索/查找 Key Vault 连接

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="搜索密钥保管库":::

1. 删除一个或多个 Key Vault 连接
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="删除密钥保管库":::

## <a name="manage-your-credentials"></a>管理凭据

1. 按名称搜索/查找凭据
  
2. 选择并更新现有凭据

3. 删除一个或多个凭据
