---
title: 如何扫描 Azure Cosmos 数据库 (SQL API)
description: 本操作指南详细介绍如何扫描 Azure Cosmos 数据库 (SQL API)。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 5cbf179cd28a36705379049a66010d02d2e7cf24
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739088"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>注册并扫描 Azure Cosmos 数据库 (SQL API)

本文概述了如何在 Azure Purview 中注册 Azure Cosmos 数据库 (SQL API) 帐户并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure Cosmos 数据库 (SQL API) 支持通过完全扫描和增量扫描来捕获元数据和架构。 扫描时还会根据系统和自定义分类规则自动分类数据。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，先创建一个 Azure Purview 帐户。 有关创建 Purview 帐户的详细信息，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
- 你需要是 Azure Purview 数据源管理员

## <a name="setting-up-authentication-for-a-scan"></a>为扫描设置身份验证

只有一种方法可以设置 Azure Cosmos 数据库 (SQL API) 的身份验证：

- 帐户密钥
 
### <a name="account-key"></a>帐户密钥

如果选择的身份验证方法是“帐户密钥”，则需要获取访问密钥，并将其存储在密钥保管库中：

1. 导航到 Azure 门户中的 Cosmos DB 帐户 
1. 选择“设置” > “密钥”  
1. 从读写密钥或只读密钥复制主密钥或辅助密钥，并将其保存到某个位置以用于后续步骤 。
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”，然后输入“名称”和“值”作为 Azure Cosmos DB 帐户中的密钥  。
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用密钥[新建凭据](manage-credentials.md#create-a-new-credential)来设置扫描

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>注册 Azure Cosmos 数据库 (SQL API) 帐户

要在数据目录中注册新的 Azure Cosmos 数据库 (SQL API) 帐户，请执行以下操作：

1. 导航到你的 Purview 帐户
1. 在左侧导航中选择“数据映射”。
1. 选择“注册”
1. 在“注册资源”上选择“Azure Cosmos DB (SQL API)” 
1. 选择“继续”

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="注册新数据源" border="true":::

在“注册资源 (Azure Cosmos DB (SQL API))”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
2. 选择 Azure 订阅，以筛选 Azure Cosmos DB。
3. 选择适当的 Cosmos DB 帐户名称。
4. 选择集合或创建新集合（可选）。
5. 选择“注册”以注册数据源。

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="注册源选项" border="true":::


## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 Purview Studio 的左窗格中选择“数据映射”选项卡。

1. 选择你已注册的 Azure Cosmos DB 数据源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据。 

   :::image type="content" source="media/register-scan-azure-cosmos-database/set-up-scan-cosmos.png" alt-text="设置扫描":::

1. 可以通过选择列表中相应的项，将扫描范围限定到特定的数据库。

   :::image type="content" source="media/register-scan-azure-cosmos-database/cosmos-database-scope-your-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/register-scan-azure-cosmos-database/select-scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/register-scan-azure-cosmos-database/trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
