---
title: 注册并扫描 Azure MySQL 数据库
description: 本教程介绍如何扫描 Azure MySQL 数据库
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: ed3a0abc6bdd99e5c86e67a410d7c9ecadf7a718
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737647"
---
# <a name="register-and-scan-an-azure-mysql-database"></a>注册并扫描 Azure MySQL 数据库

本文介绍如何注册并扫描 Azure MySQL 数据库。


## <a name="supported-capabilities"></a>支持的功能
- 完全扫描和增量扫描，用于从 Azure MySQL 数据库捕获元数据和分类。

- ADF 复制和数据流活动的数据资产之间的世系。

### <a name="known-limitations"></a>已知的限制
Purview 仅支持 Azure MySQL 数据库的 SQL 身份验证。


## <a name="prerequisites"></a>先决条件

1. 创建新 Purview 帐户（如果还没有该帐户）。

2. Purview 帐户与 Azure MySQL 数据库之间的网络访问权限。

### <a name="set-up-authentication-for-a-scan"></a>为扫描设置身份验证

后续步骤将用到该用户名和密码 。

按照[创建数据库和用户](../mysql/howto-create-users.md)中的说明创建 Azure MySQL 数据库的登录名。

1. 在 Azure 门户中，导航到密钥保管库
1. 选择“设置”>“机密”
1. 选择“+ 生成/导入”并在 Azure SQL 数据库中输入“名称”和“值”作为密码  
1. 选择“创建”以完成
1. 如果密钥保管库尚未连接到 Purview，则需要[创建新的密钥保管库连接](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. 最后，请使用用户名和密码[创建新凭据](manage-credentials.md#create-a-new-credential)（SQL 身份验证类型）以设置扫描 

## <a name="register-an-azure-mysql-database-data-source"></a>注册 Azure MySQL 数据库数据源

若要在数据目录中注册新的 Azure MySQL 数据库，请执行以下操作：

1. 导航到你的 Purview 帐户。

1. 在左侧导航区域中选择“数据映射”。

1. 选择“注册”。

1. 在“注册源”上选择“Azure MySQL 数据库” 。 选择“继续”。

:::image type="content" source="media/register-scan-azure-mysql/01-register-azure-mysql-data-source.png" alt-text="注册新数据源" border="true":::

在“注册源(Azure MySQL 数据库)”屏幕上，执行以下操作：

1. 在“名称”中输入数据源的名称。 这将是此数据源在目录中的显示名称。
1. 选择“从 Azure 订阅中”，然后从“Azure 订阅”下拉框中选择相应订阅，并从“服务器名称”下拉框中选择相应服务器  。
1. 选择“注册”以注册数据源。 

:::image type="content" source="media/register-scan-azure-mysql/02-register-azure-mysql-name-connection.png" alt-text="注册源选项" border="true":::

## <a name="creating-and-running-a-scan"></a>创建和运行扫描

若要创建并运行新扫描，请执行以下操作：

1. 在 Purview Studio 的左窗格中选择“数据映射”选项卡。

1. 选择你已注册的 Azure Database for MySQL 源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据，并检查连接以确保正确配置凭据。

   :::image type="content" source="media/register-scan-azure-mysql/03-new-scan-azure-mysql-connection-credential.png" alt-text="设置扫描":::

1. 可以选择列表中适当的项，以便将扫描范围限定在特定的文件夹或子文件夹。

   :::image type="content" source="media/register-scan-azure-mysql/04-scope-azure-mysql-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/register-scan-azure-mysql/05-scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/register-scan-azure-mysql/06-trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。-->

:::image type="content" source="media/register-scan-azure-mysql/07-review-your-scan.png" alt-text="查看扫描" border="true":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

> [!NOTE]
> * 删除扫描不会删除以前的 Azure MySQL 数据库扫描中创建的资产。
> * 如果在 Purview 的“架构”选项卡中编辑描述后，更改了源表并重新扫描源表，则不会再用架构更改来更新该资产。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
