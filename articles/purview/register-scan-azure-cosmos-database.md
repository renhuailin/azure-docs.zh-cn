---
title: 如何扫描 Azure Cosmos 数据库 (SQL API)
description: 本操作指南详细介绍如何扫描 Azure Cosmos 数据库 (SQL API)。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696252"
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
1. 复制密钥，并将其保存在某个位置以供后续步骤使用
1. 导航到你的密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”，然后输入“名称”和“值”作为存储帐户中的密钥
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，使用密钥[新建凭据](manage-credentials.md#create-a-new-credential)来设置扫描

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>注册 Azure Cosmos 数据库 (SQL API) 帐户

要在数据目录中注册新的 Azure Cosmos 数据库 (SQL API) 帐户，请执行以下操作：

1. 导航到你的 Purview 帐户
1. 在左侧导航区域中选择“源”
1. 选择“注册”
1. 在“注册资源”上选择“Azure Cosmos DB (SQL API)” 
1. 选择“继续”

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="注册新数据源" border="true":::

在“注册资源 (Azure Cosmos DB (SQL API))”屏幕上，执行以下操作：

1. 输入数据源将在目录中列出的名称。
1. 选择要指向所需存储帐户的方式：
   1. 选择“从 Azure 订阅中”，然后从“Azure 订阅”下拉框中选择相应订阅，并从“Cosmos DB 帐户名称”下拉框中选择相应的 Cosmos DB 帐户  。
   1. 也可选择“手动输入”并输入服务终结点 (URL)。
1. 选择“完成”以注册数据源。

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="注册源选项" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
