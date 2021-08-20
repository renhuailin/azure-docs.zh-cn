---
title: 创建启用了账本的单一数据库
description: 使用 Azure 门户在 Azure SQL 数据库中创建启用了账本的单一数据库。
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: quickstart
author: JasonMAnderson
ms.author: janders
ms.reviewer: vanto
ms.date: 07/23/2021
ms.custom: references_regions
ms.openlocfilehash: ffde0d192888aea852eb4de73e24ce678c4a677a
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666667"
---
# <a name="quickstart-create-a-database-in-azure-sql-database-with-ledger-enabled"></a>快速入门：在 Azure SQL 数据库中创建启用了账本的数据库

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版，在欧洲西部、巴西南部和美国中西部提供。

在此快速入门中，你将在 Azure SQL 数据库中创建一个[账本数据库](ledger-overview.md#ledger-database)，并使用 Azure 门户[通过 Azure Blob 存储配置自动摘要存储](ledger-digest-management-and-database-verification.md#automatic-generation-and-storage-of-database-digests)。 有关账本的详细信息，请参阅 [Azure SQL 数据库账本](ledger-overview.md)。

## <a name="prerequisite"></a>先决条件

需要一个活动 Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-ledger-database-and-configure-digest-storage"></a>创建账本数据库并配置摘要存储

在[无服务器计算层](serverless-tier-overview.md)中创建单一账本数据库，并配置将账本摘要上传到 Azure 存储帐户。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

为了在 Azure 门户中创建单一数据库，本快速入门将从 Azure SQL 页面开始。

1. 浏览[选择 SQL 部署选项](https://portal.azure.com/#create/Microsoft.AzureSQL)页。

1. 在“SQL 数据库”下将“资源类型”设置保留为“单一数据库”，然后选择“创建”   。

   ![展示如何添加 Azure SQL 的屏幕截图。](./media/single-database-create-quickstart/select-deployment.png)

1. 在“创建 SQL 数据库”窗体的“基本信息”选项卡上的“项目详细信息”下，选择要使用的 Azure订阅。

1. 对于“资源组”，选择“新建”，输入“myResourceGroup”，然后选择“确定” 。

1. 对于“数据库名称”，请输入“demo”。

1. 对于“服务器”，选择“新建”。 使用以下值填写“新建服务器”窗体：
   - **服务器名称**：输入“mysqlserver”并添加一些字符以实现唯一性。 我们无法提供要使用的确切服务器名称，因为对于 Azure 中的所有服务器，服务器名称必须全局唯一，而不只是在订阅中唯一。 输入类似于 mysqlserver12345 的名称，然后门户会告知你是否可用。
   - **服务器管理员登录名**：输入“azureuser”。
   - 密码：输入符合要求的密码。 在“确认密码”框中再次输入。
   - 位置：从下拉列表中选择一个位置。
   - 允许 Azure 服务访问此服务器：选择此选项以启用对摘要存储的访问。
   
   选择“确定”。
   
1. 将“想要使用 SQL 弹性池”设置保留为“否” 。

1. 在“计算 + 存储”下选择“配置数据库” 。

1. 此快速入门使用无服务器数据库，因此请选择“无服务器”，然后选择“应用” 。 

      ![展示如何配置无服务器数据库的屏幕截图。](./media/single-database-create-quickstart/configure-database.png)

1. 在“网络”选项卡上，对于“连接方法”，选择“公共终结点”  。
1. 对于“防火墙规则”，将“添加当前客户端 IP 地址”设置为“是”  。 将“允许 Azure 服务和资源访问此服务器”设置保留为“否” 。
1. 在页面底部选择“下一步: 安全”。

   :::image type="content" source="media/ledger/ledger-create-database-networking-tab.png" alt-text="显示 Azure 门户中“创建 SQL 数据库”屏幕的“网络”选项卡的屏幕截图。":::

1. 在“安全”选项卡上的“账本”部分中，选择“配置账本”选项  。

    :::image type="content" source="media/ledger/ledger-configure-ledger-security-tab.png" alt-text="显示在 Azure 门户的“安全”选项卡上配置账本的屏幕截图。":::

1. 在“配置账本”窗格的“账本”部分中，选中“为此数据库中所有将来的表启用”复选框  。 此设置确保数据库中未来的所有表都是账本表。 为此，数据库中的所有数据都将显示任何篡改证据。 默认情况下，即使未在 [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) 中指定 `LEDGER = ON`，新表也将被创建为可更新的账本表。 也可以不选择此选项。 使用 SQL Transact-SQL 创建新表时，需要在每个表的基础上启用账本功能。

1. 在“摘要存储”部分，将自动选择“启用自动摘要存储”。 然后，将创建一个新的 Azure 存储帐户和存储摘要的容器。

1. 选择“应用”。

    :::image type="content" source="media/ledger/ledger-configure-ledger-pane.png" alt-text="显示 Azure 门户中的“配置账本(预览)”窗格的屏幕截图。":::

1. 在页面底部选择“查看 + 创建”。

    :::image type="content" source="media/ledger/ledger-review-security-tab.png" alt-text="展示如何在 Azure 门户的“安全”选项卡上查看和创建账本数据库的屏幕截图。":::

1. 在“查看 + 创建”页上，查看后选择“创建”。

## <a name="clean-up-resources"></a>清理资源

保留资源组、服务器和单个数据库以供后续步骤使用。 你将了解如何通过不同的方法使用数据库的账本功能。

使用完这些资源后，删除你创建的资源组。 此操作还会删除服务器和其中的单一数据库。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

若要使用 Azure 门户删除 myResourceGroup 及其包含的所有资源：

1. 在门户中，搜索并选择“资源组”。 然后从列表中选择“myResourceGroup”。
1. 在资源组页上，选择“删除资源组”。
1. 在“键入资源组名称”下输入 **myResourceGroup**，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

使用不同的工具与语言连接和查询数据库：

- [创建和使用可更新账本表](ledger-how-to-updatable-ledger-tables.md)
- [创建和使用仅追加账本表](ledger-how-to-append-only-ledger-tables.md) 
