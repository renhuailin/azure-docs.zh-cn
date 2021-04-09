---
title: 快速入门：创建工作负荷分类器 - T-SQL
description: 使用 T-SQL 创建具有较高重要性的工作负荷分类器。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e757c8047bf6d634ab6d7cbc8963087c0eccc46a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677362"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>快速入门：使用 T-SQL 创建工作负荷分类器

在本快速入门中，我们将为组织的 CEO 快速创建一个具有较高重要性的工作负荷分类器。 此工作负荷分类器允许 CEO 查询的优先级高于队列中重要性更低的其他查询。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

> [!NOTE]
> 在 Azure Synapse Analytics 中创建专用 SQL 池实例可能会产生一个新的可计费服务。  有关详细信息，请参阅 [Azure Synapse Analytics 定价](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。
>
>

## <a name="prerequisites"></a>先决条件

本快速入门假定你已在 Azure Synapse Analytics 中预配专用 SQL 池，并且具有 CONTROL DATABASE 权限。 如果需要创建一个 SQL 池，请参考[创建和连接 - 门户](create-data-warehouse-portal.md)创建名为 mySampleDataWarehouse 的专用 SQL 池。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-login-for-theceo"></a>为 TheCEO 创建登录名

使用 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 在 `master` 数据库中为“TheCEO”创建 SQL Server 身份验证登录名。

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>创建用户

在 mySampleDataWarehouse 中[创建用户](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)“TheCEO”

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>创建工作负荷分类器

为“TheCEO”创建具有较高重要性的[工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>查看现有分类器

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>清理资源

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

我们会针对专用 SQL 池中存储的数据，按数据仓库单位收费。 这些计算和存储资源是分开计费的。

- 如果想要将数据保留在存储中，可以在不使用专用 SQL 池时暂停计算。 如果暂停计算资源，则你只需支付数据存储费用。 准备好处理数据时，可以恢复计算。
- 若要避免将来产生费用，可以删除该专用 SQL 池。

遵循以下步骤清理资源。

1. 登录到 [Azure 门户](https://portal.azure.com)，选择你的专用 SQL 池。

    ![清理资源](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. 若要暂停计算，请选择“暂停”按钮。 暂停专用 SQL 池后，会看到“启动”按钮。  若要恢复计算，请选择“启动”  。

3. 若要删除专用 SQL 池以免产生计算或存储费用，请选择“删除”。

## <a name="next-steps"></a>后续步骤

- 现已创建一个工作负荷分类器。 以 TheCEO 身份运行一些查询，以查看该分类器的执行情况。 参阅 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 以查看查询和分配的重要性。
- 有关专用 SQL 池工作负载管理的详细信息，请参阅[工作负载重要性](sql-data-warehouse-workload-importance.md)和[工作负载分类](sql-data-warehouse-workload-classification.md)。
- 请参阅有关[配置工作负荷重要性](sql-data-warehouse-how-to-configure-workload-importance.md)和[管理和监视工作负荷管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)的操作指南文章。
