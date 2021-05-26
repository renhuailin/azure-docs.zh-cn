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
ms.date: 05/25/2021
ms.openlocfilehash: d03d104a5b0d9ad9d83cbecdeeab8614cf0ee728
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385348"
---
# <a name="quickstart-create-an-azure-sql-database-with-ledger-enabled"></a>快速入门：创建启用了账本的 Azure SQL 数据库

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版。

在此快速入门中，你将在 Azure SQL 数据库中创建一个[账本数据库](ledger-overview.md#ledger-database)，并使用 Azure 门户[通过 Azure Blob 存储配置自动摘要存储](ledger-digest-management-and-database-verification.md#automatic-generation-and-storage-of-database-digests)。 有关账本的详细信息，请参阅 [Azure SQL 数据库账本](ledger-overview.md)。

## <a name="prerequisite"></a>先决条件

- 一个有效的 Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-ledger-database-and-configure-digest-storage"></a>创建账本数据库并配置摘要存储

在[无服务器计算层](serverless-tier-overview.md)中创建单一账本数据库，并配置将账本摘要上传到 Azure 存储帐户。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

为了在 Azure 门户中创建单一数据库，本快速入门将从 Azure SQL 页面开始。

1. 浏览[选择 SQL 部署选项](https://portal.azure.com/#create/Microsoft.AzureSQL)页。

1. 在“SQL 数据库”下将“资源类型”设置保留为“单一数据库”，然后选择“创建”   。

   ![添加到 Azure SQL](./media/single-database-create-quickstart/select-deployment.png)

1. 在“创建 SQL 数据库”窗体的“基本信息”选项卡上的“项目详细信息”下，选择所需的 Azure订阅   。

1. 对于“资源组”，选择“新建”，输入“myResourceGroup”，然后选择“确定” 。

1. 对于“数据库名称”，请输入“demo”。

1. 对于“服务器”，选择“新建”，并使用以下值填写“新服务器”窗体  ：
   - **服务器名称**：输入“mysqlserver”并添加一些字符以实现唯一性。 我们无法提供要使用的确切服务器名称，因为对于 Azure 中的所有服务器，服务器名称必须全局唯一，而不只是在订阅中唯一。 因此，输入类似于 mysqlserver12345 的名称，然后门户会告知你是否可用。
   - **服务器管理员登录名**：输入“azureuser”。
   - **密码**：输入符合要求的密码，然后在“确认密码”字段中再次输入该密码。
   - 位置：从下拉列表中选择一个位置。
   - 选择“允许 Azure 服务访问此服务器”选项以启用对摘要存储的访问。
   
   选择“确定”。
   
1. 将“想要使用 SQL 弹性池”设置保留为“否” 。

1. 在“计算 + 存储”下选择“配置数据库” 。

1. 此快速入门使用无服务器数据库，因此请选择“无服务器”，然后选择“应用” 。 

      ![配置无服务器数据库](./media/single-database-create-quickstart/configure-database.png)

1. 在“网络”选项卡上，对于“连接方法”，选择“公共终结点”  。
1. 对于“防火墙规则”，将“添加当前客户端 IP 地址”设置为“是”  。 将“允许 Azure 服务和资源访问此服务器”设置保留为“否” 。
1. 在页面底部选择“下一步: 安全”。

   :::image type="content" source="media/ledger/ledger-create-database-networking-tab.png" alt-text="Azure 门户中“创建数据库”的“网络”选项卡":::

1. 在“安全”选项卡上的“账本”部分中，选择“配置账本”选项  。

    :::image type="content" source="media/ledger/ledger-configure-ledger-security-tab.png" alt-text="在 Azure 门户的“安全”选项卡中配置账本":::

1. 在“配置账本”窗格的“账本”部分中，选中“为此数据库中所有将来的表启用”复选框  。 此设置可确保数据库中所有将来的表均为账本表，这意味着数据库中的所有数据都可以防止被篡改。 默认情况下，即使未在 [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) 中指定 `LEDGER = ON`，新表也将被创建为可更新的账本表。 或者，你可以将此项保留为未选中状态，这要求你在使用 Transact-SQL 创建新表时按表启用账本功能。

1. 在“摘要存储”部分中，将自动选择“启用自动摘要存储”，然后创建新的 Azure 存储帐户和用于存储摘要的容器 。

1. 单击“应用”  按钮。

    :::image type="content" source="media/ledger/ledger-configure-ledger-pane.png" alt-text="Azure 门户中的“配置账本”窗格":::

1. 在页面底部选择“查看 + 创建”：

    :::image type="content" source="media/ledger/ledger-review-security-tab.png" alt-text="在 Azure 门户的“安全”选项卡中查看和创建账本数据库":::

1. 在“查看 + 创建”页上，查看后选择“创建”。 

## <a name="clean-up-resources"></a>清理资源

保留资源组、服务器和单一数据库可继续执行后续步骤，并了解如何以不同方法使用数据库的账本功能。

用完这些资源后，可以删除创建的资源组，这也会删除该资源组中的服务器和单一数据库。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

若要使用 Azure 门户删除 **myResourceGroup** 及其包含的所有资源：

1. 在 Azure 门户中搜索并选择“资源组”，然后从列表中选择“myResourceGroup”。 
1. 在资源组页上，选择“删除资源组”。
1. 在“键入资源组名称”下输入 *myResourceGroup*，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

使用不同的工具与语言连接和查询数据库：

- [创建和使用可更新账本表](ledger-how-to-updatable-ledger-tables.md)
- [创建和使用仅追加账本表](ledger-how-to-append-only-ledger-tables.md) 
