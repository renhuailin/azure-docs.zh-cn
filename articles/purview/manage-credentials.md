---
title: 创建和管理用于扫描的凭据
description: 了解在 Azure Purview 中创建和管理凭据的步骤。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: f3367883b35e35a4dd726b92af48a92f863cfa5d
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207842"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure Purview 中用于源身份验证的凭据

本文介绍如何在 Azure Purview 中创建凭据。 这些保存的凭据可让你快速重复使用已保存的身份验证信息并将其应用于数据源扫描。

## <a name="prerequisites"></a>先决条件

- Azure 密钥保管库。 若要详细了解如何创建密钥保管库，请参阅[快速入门：使用 Azure 门户创建密钥保管库](../key-vault/general/quick-create-portal.md)。

## <a name="introduction"></a>简介

凭据是可供 Azure Purview 用来对已注册的数据源进行身份验证的身份验证信息。 可为各种类型的身份验证方案（例如，需要用户名/密码的基本身份验证）创建凭据对象。 凭据根据所选类型的身份验证方法，捕获进行身份验证所需的特定信息。 在凭据创建过程中，凭据使用现有的 Azure 密钥保管库机密来检索敏感的身份验证信息。

在 Azure Purview 中，有几个选项可用作身份验证方法来扫描数据源，例如以下选项：

- Azure Purview 托管标识
- 帐户密钥（使用密钥保管库）
- SQL 身份验证（使用密钥保管库）
- 服务主体（使用 Key Vault）

在创建任何凭据之前，请考虑数据源类型和网络要求，确定方案需要哪种身份验证方法。 查看以下决策树，查找最适合的凭据：

   :::image type="content" source="media/manage-credentials/manage-credentials-decision-tree-small.png" alt-text="管理凭据决策树" lightbox="media/manage-credentials/manage-credentials-decision-tree.png":::

## <a name="use-purview-managed-identity-to-set-up-scans"></a>使用 Purview 托管标识来设置扫描

如果使用 Purview 托管标识来设置扫描，则无需显式创建凭据，并将密钥保管库链接到 Purview 来存储这些凭据。 有关添加有权扫描数据源的 Purview 托管标识的详细说明，请参阅以下特定于数据源的身份验证部分：

- [Azure Blob 存储](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL 数据库](register-scan-azure-sql-database.md)
- [Azure SQL 数据库托管实例](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>在 Azure Purview 帐户中创建 Azure 密钥保管库连接

在创建凭据之前，首先需要将一个或多个现有 Azure 密钥保管库实例与 Azure Purview 帐户相关联。

1. 在 [Azure 门户](https://portal.azure.com)中选择你的 Azure Purview 帐户，然后打开 [Purview Studio](https://web.purview.azure.com/resource/)。 在 Studio 中导航到“管理中心”，然后导航到“凭据”。 

2. 在“凭据”页中选择“管理密钥保管库连接”。 

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="管理 Azure 密钥保管库连接":::

3. 在“管理密钥保管库连接”页中选择“+ 新建”。

4. 提供所需的信息，然后选择“创建”。

5. 确认密钥保管库已成功关联到你的 Azure Purview 帐户，如以下示例中所示：

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="查看 Azure 密钥保管库连接以确认。":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>授予 Purview 托管标识对 Azure 密钥保管库的访问权限

目前 Azure 密钥保管库支持两种权限模型：

- 选项 1 - 访问策略 
- 选项 2 - 基于角色的访问控制 

在分配对 Purview 托管标识的访问权限之前，请先从菜单的密钥保管库资源“访问策略”确定你的 Azure 密钥保管库权限模型。 根据相关的权限模型，执行以下步骤。  

:::image type="content" source="media/manage-credentials/akv-permission-model.png" alt-text="Azure 密钥保管库权限模型"::: 

### <a name="option-1---assign-access-using-key-vault-access-policy"></a>选项 1 - 使用密钥保管库访问策略分配访问权限  

只有当 Azure 密钥保管库资源中的权限模型设置为“保管库访问策略”时，才执行以下步骤：

1. 导航到你的 Azure 密钥保管库。

2. 选择“访问策略”页。

3. 选择“添加访问策略”。

   :::image type="content" source="media/manage-credentials/add-msi-to-akv-2.png" alt-text="将 Purview MSI 添加到 AKV":::

4. 在“机密权限”下拉列表中，选择“获取”和“列出”权限。  

5. 对于“选择主体”，请选择“Purview 托管标识”。 可以使用 Purview 实例名称 **或** 托管标识应用程序 ID 搜索 Purview MSI。 我们目前不支持复合标识（托管标识名称 + 应用程序 ID）。

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="添加访问策略":::

6. 选择 **添加** 。

7. 选择“保存”以保存访问策略。

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="保存访问策略":::

### <a name="option-2---assign-access-using-key-vault-azure-role-based-access-control"></a>选项 2 - 使用密钥保管库 Azure 基于角色的访问控制分配访问权限 

只有当 Azure 密钥保管库资源中的权限模型设置为“Azure 基于角色的访问控制”时，才执行以下步骤：

1. 导航到你的 Azure 密钥保管库。

2. 在左侧导航栏菜单中，选择“访问控制 (IAM)”。

3. 选择“+ 添加”。

4. 将“角色”设置为“密钥保管库机密用户”，然后在“选择”输入框下输入 Azure Purview 帐户名称。 然后，选择“保存”，将此角色分配给 Purview 帐户。

   :::image type="content" source="media/manage-credentials/akv-add-rbac.png" alt-text="Azure 密钥保管库 RBAC":::


## <a name="create-a-new-credential"></a>创建新凭据

Purview 支持以下凭据类型：

- 基本身份验证：将 **密码** 添加为密钥保管库中的机密。
- 服务主体：将 **服务主体密钥** 添加为密钥保管库中的机密。
- SQL 身份验证：将 **密码** 添加为密钥保管库中的机密。
- 帐户密钥：将 **帐户密钥** 添加为密钥保管库中的机密。
- 角色 ARN：对于 Amazon S3 数据源，请在 AWS 中添加你的 **角色 ARN**。 

有关详细信息，请参阅[将机密添加到密钥保管库](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)和[为 Purview 创建新的 AWS 角色](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview)。

在密钥保管库中存储机密后：

1. 在 Azure Purview 中转到“凭据”页。

2. 选择“+ 新建”来创建新凭据。

3. 提供所需的信息。 选择“身份验证方法”，以及要从中选择机密的 **密钥保管库连接**。

4. 填写所有详细信息后，选择“创建”。

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="新建凭据":::

5. 确认新凭据是否显示在列表视图中并随时可供使用。

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="查看凭据":::

## <a name="manage-your-key-vault-connections"></a>管理密钥保管库连接

1. 按名称搜索/查找密钥保管库连接

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="搜索密钥保管库":::

2. 删除一个或多个密钥保管库连接

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="删除密钥保管库":::

## <a name="manage-your-credentials"></a>管理凭据

1. 按名称搜索/查找凭据。
  
2. 选择并更新现有凭据。

3. 删除一个或多个凭据。

## <a name="next-steps"></a>后续步骤

[创建扫描规则集](create-a-scan-rule-set.md)
