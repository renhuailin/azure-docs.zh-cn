---
title: '如何扫描 Azure Cosmos 数据库 (SQL API) '
description: 本操作方法指南介绍了如何在 SQL API)  (扫描 Azure Cosmos 数据库的详细信息。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: e1d2035b787380d9b93943b92fbe81c09fc6a527
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552223"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a> (SQL API 注册并扫描 Azure Cosmos 数据库) 

本文概述了如何在 Azure 监控范围中注册 Azure Cosmos 数据库 (SQL API) 帐户并设置扫描。

## <a name="supported-capabilities"></a>支持的功能

Azure Cosmos 数据库 (SQL API) 支持完整和增量扫描来捕获元数据和架构。 扫描还会根据系统和自定义分类规则自动对数据进行分类。

## <a name="prerequisites"></a>先决条件

- 在注册数据源之前，请创建一个 Azure 监控范围帐户。 有关创建监控范围帐户的详细信息，请参阅 [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)。
- 你需要成为 Azure 监控范围数据源管理员

## <a name="setting-up-authentication-for-a-scan"></a>设置扫描的身份验证

仅有一种方法可为 Azure Cosmos 数据库 (SQL API) 设置身份验证：

- 帐户密钥
 
### <a name="account-key"></a>帐户密钥

如果选择了 "身份验证方法"，则需要获取访问 **密钥，并** 将其存储在密钥保管库中：

1. 导航到 Azure 门户中的 Cosmos DB 帐户 
1. 选择 **设置**  >  **密钥** 
1. 复制 *密钥* 并将其保存到某个位置，以执行后续步骤
1. 导航到你的密钥保管库
1. 选择 **设置 > 机密**
1. 选择 " **+ 生成/导入**"，并输入存储帐户中的 *密钥***名称** 和 **值**
1. 选择 " **创建** " 完成
1. 如果密钥保管库尚未连接到监控范围，则需要 [创建新的 key vault 连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用密钥 [创建新凭据](manage-credentials.md#create-a-new-credential) 以设置扫描

## <a name="register-an-azure-cosmos-database-sql-api-account"></a> (SQL API) 帐户注册 Azure Cosmos 数据库

若要在数据目录中注册新的 Azure Cosmos 数据库 (SQL API) 帐户，请执行以下操作：

1. 导航到你的监控范围帐户
1. 在左侧导航栏中选择 **源**
1. 选择“注册”
1. 在 " **注册源**" 中，选择 " **AZURE COSMOS DB (SQL API")**
1. 选择“继续”

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="注册新的数据源" border="true":::

在 " **注册源" (Azure Cosmos DB (SQL API) # B3** 屏幕上，执行以下操作：

1. 输入将在目录中列出的数据源的 **名称** 。
1. 选择要指向所需存储帐户的方式：
   1. 选择 " **从 azure** 订阅"，从 " **azure 订阅** " 下拉框中选择适当的订阅，并从 " **Cosmos DB 帐户名称** " 下拉框中选择相应的 cosmosDB 帐户。
   1. 或者，可以选择 " **手动输入** "，然后输入服务终结点 (URL) 。
1. **完成** 注册数据源。

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="注册源选项" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure 监控范围数据目录](how-to-browse-catalog.md)
- [搜索 Azure 监控范围数据目录](how-to-search-catalog.md)
