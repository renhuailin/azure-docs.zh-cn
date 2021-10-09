---
title: 排查 Azure Data Share 问题
description: 了解在 Azure Data Share 中创建或接收数据共享时，如何排查与邀请和错误有关的问题。
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 09/10/2021
ms.openlocfilehash: da746d14d891c875c9bed49c94e8b408d956e29e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811248"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>排查 Azure Data Share 中的常见问题 

本文介绍如何排查 Azure Data Share 中的常见问题。 

## <a name="azure-data-share-invitations"></a>Azure Data Share 邀请 

在某些情况下，当新用户在电子邮件邀请中选择“接受邀请”时，可能会看到一个空的邀请列表。 导致此问题的原因可能是以下之一：

* **Azure Data Share 服务未在 Azure 租户中注册为任何 Azure 订阅的资源提供程序**。 当 Azure 租户没有 Data Share 资源时，就会出现此问题。 

    当你创建 Azure Data Share 资源时，它会自动在你的 Azure 订阅中注册资源提供程序。 你还可以按照以下步骤手动注册 Data Share 服务。 若要完成这些步骤，需要 Azure 订阅的[参与者角色](../role-based-access-control/built-in-roles.md#contributor)。 

    1. 在 Azure 门户中，转到 **订阅**。
    1. 选择要用于创建 Azure Data Share 资源的订阅。
    1. 选择“资源提供程序”。
    1. 搜索“Microsoft.DataShare”。
    1. 选择“注册”。

* **邀请会发送到你的电子邮件别名而不是 Azure 登录电子邮件地址。** 如果你已注册 Azure Data Share 服务或已在 Azure 租户中创建 Data Share 资源，但仍无法看到邀请，则可能是你的电子邮件别名列为了收件人。 请联系你的数据提供者，确保将邀请发送到你的 Azure 登录电子邮件地址而非电子邮件别名处。

* **已接受邀请。** 电子邮件中的链接将你转到 Azure 门户中的“Data Share 邀请”页面。 此页面仅列出待定邀请。 页面上不显示接受的邀请。 若要查看接收的共享并配置目标 Azure 数据资源管理器群集设置，请转到你用于接受邀请的“Data Share”资源。

* 你是租户的来宾用户。 如果你是租户的来宾用户，则需要在查看邀请之前验证租户的电子邮件地址。 验证后，它将在12 个月内有效。 

## <a name="creating-and-receiving-shares"></a>创建和接收共享

创建新的共享、添加数据集或映射数据集时，可能会出现以下错误：

* 未能添加数据集。
* 未能映射数据集。
* 无法授予 Data Share 资源 x 对 y 的访问权限。
* 你没有对 x 的适当权限。
* 我们无法向一个或多个所选资源添加 Azure Data Share 帐户的写入权限。

如果你没有足够的权限访问 Azure 数据存储，则可能会看到这些错误之一。 有关详细信息，请参阅[角色和要求](concepts-roles-permissions.md)。 

需要写入权限才能在 Azure 数据存储中共享或接收数据。 此权限通常是参与者角色的一部分。 

如果是第一次从 Azure 数据存储共享数据或接收数据，则还需要具有 Microsoft.Authorization/role assignments/write 权限。 此权限通常是所有者角色的一部分。 即使你创建了 Azure 数据存储资源，也不一定是资源的所有者。 

当你具有适当的权限时，Azure Data Share 服务会自动允许数据共享资源的托管标识访问数据存储。 此过程可能需要几分钟。 如果由于此延迟而导致失败，请在几分钟后重试。

基于 SQL 的共享需要额外的权限。 有关先决条件的信息，请参阅[从 SQL 源共享](how-to-share-from-sql.md)。

## <a name="snapshots"></a>快照
快照可能因多种原因而失败。 通过选择快照的开始时间，然后选择每个数据集的状态，打开详细的错误消息。 

快照通常因以下原因而失败：

* Data Share 缺少读取源数据存储或写入目标数据存储区的权限。 有关详细信息，请参阅[角色和要求](concepts-roles-permissions.md)。 如果是第一次拍摄快照，Data Share 资源可能需要几分钟才能访问 Azure 数据存储。 请等候几分钟，然后重试。
* 防火墙阻止了到源数据存储或目标数据存储的 Data Share 连接。
* 共享数据集、源数据存储或目标数据存储已被删除。

对于存储帐户，快照可能会失败，因为在拍摄快照时，正在源中更新文件。 因此，目标上可能会出现 0 字节的文件。 源中的更新完成后，快照应该会成功。

对于 SQL 源，快照可能因以下原因而失败：

* 授予 Data Share 权限的源 SQL 脚本或目标 SQL 脚本未运行。 或者对于 Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL 数据仓库），该脚本将使用 SQL 身份验证而不是 Azure Active Directory 身份验证来运行。 你可以运行以下查询来检查 Data Share 帐户是否拥有访问 SQL 数据库的适当权限。 对于源 SQL 数据库，查询结果应显示 Data Share 帐户拥有 db_datareader 角色。 对于目标 SQL 数据库，查询结果应显示 Data Share 帐户拥有 db_datareader、db_datawriter 和 db_dlladmin 角色。

    ```sql
        SELECT DP1.name AS DatabaseRoleName,
        isnull (DP2.name, 'No members') AS DatabaseUserName
        FROM sys.database_role_members AS DRM
        RIGHT OUTER JOIN sys.database_principals AS DP1
        ON DRM.role_principal_id = DP1.principal_id
        LEFT OUTER JOIN sys.database_principals AS DP2
        ON DRM.member_principal_id = DP2.principal_id
        WHERE DP1.type = 'R'
        ORDER BY DP1.name; 
     ``` 

* 源数据存储或目标 SQL 数据存储已暂停。
* 快照进程或目标数据存储不支持 SQL 数据类型。 有关详细信息，请参阅[从 SQL 源共享](how-to-share-from-sql.md#supported-data-types)。
* 源数据存储或目标 SQL 数据存储被其他进程锁定。 Azure Data Share 不会锁定这些数据存储。 但这些数据存储中的现有锁可能导致快照失败。
* 目标 SQL 表由外键约束引用。 在快照过程中，如果目标表具有与源数据中的表相同的名称，则 Azure Data Share 将删除该表并创建新表。 如果目标 SQL 表由外键约束引用，则无法删除此表。
* 将生成目标 CSV 文件，但无法在 Excel 中读取数据。 如果源 SQL 表包含的数据中存在包含非英语字符的数据，则可能会出现此问题。 在 Excel 中，选择“获取数据”选项卡，然后选择 CSV 文件。 选择文件来源“65001: Unicode (UTF-8)”，然后加载数据。

## <a name="update-snapshot-schedule"></a>更新快照计划
数据提供者更新所发送共享的快照计划后，数据使用者需要禁用以前的快照计划，然后为接收的共享启用已更新的快照计划。 快照计划以 UTC 格式存储，并在用户界面中显示为计算机的本地时间。 它不会根据夏令时自动调整。  

## <a name="in-place-sharing"></a>就地共享
由于以下原因，Azure 数据资源管理器群集的数据集映射可能会失败：

* 用户不具有对 Azure 数据资源管理器群集的写入权限。 此权限通常是参与者角色的一部分。 
* 源/目标 Azure 数据资源管理器群集已暂停。
* 源 Azure 数据资源管理器群集为 EngineV2，目标 Azure 数据资源管理器群集为 EngineV3，反之亦然。 不支持不同引擎版本的 Azure 数据资源管理器群集之间的共享。

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。 

若要了解如何接收数据，请继续阅读[接受和接收数据](subscribe-to-data-share.md)教程。