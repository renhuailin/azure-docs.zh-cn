---
title: 适用于 Azure SQL 数据库和托管实例的 Azure CLI 示例 | Microsoft Docs
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 找到用于创建和管理 Azure SQL 数据库和 Azure SQL 托管实例的 Azure CLI 脚本示例。
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli, seo-azure-cli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: ''
ms.date: 09/17/2021
keywords: sql 数据库, 托管实例, azure cli 示例, azure cli 示例, azure cli 代码示例, azure cli 脚本示例
ms.openlocfilehash: adc7f1db28fa1b0e8cafb2b3363876b1e316f8bd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662146"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>适用于 Azure SQL 数据库和 SQL 托管实例的 Azure CLI 示例 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

可以使用 <a href="/cli/azure">Azure CLI</a> 来配置 Azure SQL 数据库和 SQL 托管实例。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

# <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/single-database)

下表包括一个链接，该链接指向用于管理 Azure SQL 数据库中的单个和共用数据库的 Azure CLI 脚本示例。 

|领域|说明|
|---|---|
|在 Azure SQL 数据库中创建数据库||
| [创建单一数据库和配置防火墙规则](scripts/create-and-configure-database-cli.md) | 创建 SQL 数据库并配置服务器级防火墙规则。 |
| [创建弹性池并移动共用数据库](scripts/move-database-between-elastic-pools-cli.md) | 创建弹性池、移动共用数据库，并更改计算大小。 |
|在 Azure SQL 数据库中缩放数据库||
| [缩放单一数据库](scripts/monitor-and-scale-database-cli.md) | 先查询 SQL 数据库中某个数据库的大小信息，然后将该数据库缩放到不同的计算大小。 |
| [缩放弹性池](scripts/scale-pool-cli.md) | 将 SQL 弹性池缩放为不同的计算大小。 |
|**配置异地复制和故障转移**||
| [将单一数据库添加到故障转移组](scripts/add-database-to-failover-group-cli.md)| 创建一个数据库和一个故障转移组，将此数据库添加到该故障转移组，然后测试到辅助服务器的故障转移。 |
| [为弹性池配置故障转移组](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | 创建一个数据库，将其添加到弹性池，将弹性池添加到故障转移组，然后测试到辅助服务器的故障转移。 |
| [使用活动异地复制配置单一数据库并对其进行故障转移](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| 为 Azure SQL 数据库中的数据库配置活动异地复制，并将其故障转移到次要副本。 |
| [使用活动异地复制配置共用数据库并对其进行故障转移](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| 为弹性池中的数据库配置活动异地复制，然后将其故障转移到次要副本。 |
| **审核和威胁检测** |
| [配置审核和威胁检测](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| 为 Azure SQL 数据库中的数据库配置审核和威胁检测策略。 |
| 备份、还原、复制和导入数据库||
| [备份数据库](../../sql-database/scripts/sql-database-backup-database-cli.md)| 将 SQL 数据库中的数据库备份到 Azure 存储备份。 |
| [还原数据库](../../sql-database/scripts/sql-database-restore-database-cli.md)| 从异地冗余备份中还原 SQL 数据库中的数据库，并将已删除的数据库还原到最新备份。 |
| [将数据库复制到新服务器](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | 在新服务器中创建 SQL 数据库中某个现有数据库的副本。 |
| [从 BACPAC 文件导入数据库](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| 将数据库从 BACPAC 文件导入到 SQL 数据库。 |
|||

详细了解[单一数据库 Azure CLI API](single-database-manage.md#the-azure-cli)。

# <a name="azure-sql-managed-instance"></a>[Azure SQL 托管实例](#tab/managed-instance)

下表包括指向 Azure SQL 托管实例的 Azure CLI 脚本示例的链接。

|领域|说明|
|---|---|
| 创建 SQL 托管实例||
| [创建 SQL 托管实例](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| 创建 SQL 托管实例。 |
| **配置透明数据加密 (TDE)**||
| [使用 Azure Key Vault 管理 SQL 托管实例中的透明数据加密](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| 将 Azure Key Vault 用于各种关键方案，在 SQL 托管实例中配置透明数据加密 (TDE)。 |
|**配置故障转移组**||
| [为 SQL 托管实例配置故障转移组](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | 创建 SQL 托管实例的两个实例，将其添加到故障转移组，然后测试从主 SQL 托管实例到辅助 SQL 托管实例的故障转移。 |
|||

有关其他 SQL 托管实例示例，请参阅[创建](/archive/blogs/sqlserverstorageengine/create-azure-sql-managed-instance-using-azure-cli)、[更新](/archive/blogs/sqlserverstorageengine/modify-azure-sql-database-managed-instance-using-azure-cli)、[移动数据库](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)以及[使用](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)脚本。

详细了解 [SQL 托管实例 Azure CLI API](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances)。

---