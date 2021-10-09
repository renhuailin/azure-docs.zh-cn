---
title: 注册和扫描 Azure Database for PostgreSQL
description: 本教程介绍如何在 Azure Purview 中扫描 Azure Database for PostgreSQL 数据库。
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: abc676fbff551781f720db5937a9c35c7c8f81ea
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209789"
---
# <a name="register-and-scan-an-azure-database-for-postgresql"></a>注册和扫描 Azure Database for PostgreSQL

本文介绍如何注册和扫描 Azure Database for PostgreSQL。


## <a name="supported-capabilities"></a>支持的功能
- 完全扫描和增量扫描，用于从 Azure Database for PostgreSQL 数据库捕获元数据和分类。

- ADF 复制和数据流活动的数据资产之间的世系。

### <a name="known-limitations"></a>已知的限制

Purview 仅支持 Azure Database for PostgreSQL 的 SQL 身份验证。


## <a name="prerequisites"></a>先决条件

1. 创建新 Purview 帐户（如果还没有该帐户）。

2. Purview 帐户与 Azure Database for PostgreSQL 之间的网络访问权限。

#### <a name="sql-authentication-for-an-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 的 SQL 身份验证

连接到 Azure Database for PostgreSQL 数据库需要完全限定的服务器名称和登录凭据。 如果没有可用的登录名，则可以按照[连接和查询](../postgresql/connect-python.md)中的说明为 Azure Database for PostgreSQL 创建登录名。 后续步骤将用到该用户名和密码 。

1. 在 Azure 门户中，导航到密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并在 Azure PostgreSQL Database 中输入“名称”和“值”作为密码  
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，请使用用户名和密码[创建新凭据](manage-credentials.md#create-a-new-credential)（SQL 身份验证类型）以设置扫描 

## <a name="register-an-azure-database-for-postgresql-data-source"></a>注册 Azure Database for PostgreSQL 数据源

若要在数据目录中注册新的 Azure Database for PostgreSQL，请执行以下操作：

1. 导航到你的 Purview 帐户。

1. 在左侧导航区域中选择“数据映射”。

1. 选择“注册”

1. 在“注册源”上选择“Azure Database for PostgreSQL” 。 选择“继续”。

:::image type="content" source="media/register-scan-azure-postgresql/01-register-azure-postgres.png" alt-text="注册新数据源" border="true":::

在“注册源 Azure Database for PostgreSQL”屏幕上，执行以下操作：

1. 输入数据源的“名称”。 这将是此数据源在目录中的显示名称。
1. 选择“从 Azure 订阅中”，然后从“Azure 订阅”下拉框中选择相应订阅，并从“服务器名称”下拉框中选择相应服务器  。
1. 选择“注册”以注册数据源。 
 

:::image type="content" source="media/register-scan-azure-postgresql/02-register-source-azure-postgres.png" alt-text="注册源选项" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 [Purview Studio](https://web.purview.azure.com/resource/) 的左窗格中选择“数据映射”选项卡。

1. 选择你已注册的 Azure Database for PostgreSQL 源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据。

   :::image type="content" source="media/register-scan-azure-postgresql/03-azure-postgres-scan.png" alt-text="设置扫描":::

1. 可以选择列表中适当的项，以便将扫描范围限定在特定的文件夹或子文件夹。

   :::image type="content" source="media/register-scan-azure-postgresql/04-scope-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/register-scan-azure-postgresql/05-scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/register-scan-azure-postgresql/06-trigger-scan.png" alt-text="触发扫描":::

1. 查看扫描并选择“保存并运行”。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> * 删除扫描不会删除以前扫描中创建的类别资产。
> * 如果在 Purview 的架构选项卡中编辑描述后，更改了源表并重新扫描源表，则不会再用架构更改来更新该资产。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)