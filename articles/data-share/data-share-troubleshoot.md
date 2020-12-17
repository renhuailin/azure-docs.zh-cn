---
title: 排查 Azure Data Share 问题
description: 了解如何在通过 Azure 数据共享创建或接收数据共享时解决与邀请和错误有关的问题。
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: c93ce9c81ada3c30128846b43041603e132abd88
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617232"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>排查 Azure Data Share 中的常见问题 

本文介绍如何解决 Azure 数据共享的常见问题。 

## <a name="azure-data-share-invitations"></a>Azure Data Share 邀请 

在某些情况下，当新用户单击发送的电子邮件邀请中的 " **接受邀请** " 时，可能会显示一个空邀请列表。 

![无邀请](media/no-invites.png)

这可能是由以下原因造成的：

* **Azure Data Share 服务未在 Azure 租户中注册为任何 Azure 订阅的资源提供程序。** 如果你的 Azure 租户中没有 Data Share 资源，则会遇到此问题。 当你创建 Azure Data Share 资源时，它会自动在你的 Azure 订阅中注册资源提供程序。 你还可以按照以下步骤手动注册 Data Share 服务。 你需要拥有“Azure 参与者”角色才能完成这些步骤。

    1. 在 Azure 门户中，导航到“订阅”
    1. 选择要用于创建 Azure Data Share 资源的订阅
    1. 单击“资源提供程序”
    1. 搜索“Microsoft.DataShare”
    1. 单击“注册” 

    需要 Azure 订阅的 [Azure 参与者角色](../role-based-access-control/built-in-roles.md#contributor) 才能完成这些步骤。 

* **邀请将发送到你的电子邮件别名而不是 Azure 登录电子邮件处。** 如果你已注册了 Azure Data Share 服务或已在 Azure 租户中创建了 Data Share 资源，但仍无法看到邀请，原因可能是提供者已将你的电子邮件别名而不是 Azure 登录电子邮件地址作为收件人输入。 请联系你的数据提供者，确保他们已将邀请发送到你的 Azure 登录电子邮件地址而非电子邮件别名处。

* **邀请已被接受。** 电子邮件中的链接将你转到 Azure 门户中的“Data Share 邀请”页面，此页面仅列出待处理邀请。 如果你已接受邀请，则它将不再显示在“Data Share 邀请”页中。 转到你用于接受邀请的 Data Share 资源，以便查看收到的共享并配置你的目标 Azure 数据资源管理器群集设置。

## <a name="error-when-creating-or-receiving-a-new-share"></a>创建或接收新共享时出错

"未能添加数据集"

"未能映射数据集"

"无法授予数据共享资源 x 对 y 的访问权限"

"您对 x 没有适当的权限

"我们无法向一个或多个所选资源添加 Azure 数据共享帐户的写入权限"

如果在创建新的共享、添加数据集或映射数据集时收到上述任何错误，可能是由于对 Azure 数据存储的权限不足。 请参阅所需权限的 [角色和要求](concepts-roles-permissions.md) 。 

需要具有写入权限，才能共享或接收 Azure 数据存储中的数据，这些数据通常位于 **参与者** 角色中。 

如果这是你第一次在 Azure 数据存储中共享或接收数据，则还需要 *Microsoft。授权/角色分配/写入* 权限，后者通常存在于 **所有者** 角色中。 即使您创建了 Azure 数据存储资源，也不会自动使您成为该资源的所有者。 使用适当的权限时，Azure 数据共享服务会自动向数据共享资源的托管标识授予对数据存储区的访问权限。 此过程可能需要几分钟才能生效。 如果遇到此延迟，请在几分钟后重试。

基于 SQL 的共享需要其他权限。 有关先决条件的详细列表，请参阅 [从 SQL 源共享](how-to-share-from-sql.md) 。

## <a name="snapshot-failed"></a>快照失败
快照可能因多种原因而失败。 单击快照的开始时间，然后单击每个数据集的状态，即可找到详细的错误消息。 下面是快照失败的常见原因：

* 数据共享没有读取源数据存储或写入目标数据存储的权限。 有关详细权限要求，请参阅 [角色和要求](concepts-roles-permissions.md) 。 如果这是你第一次拍摄快照，可能需要几分钟时间才能向数据共享资源授予对 Azure 数据存储的访问权限。 请等待几分钟，然后重试。
* 与源或目标数据存储的数据共享连接已被防火墙阻止。
* 共享数据集，或源或目标数据存储已删除。

对于存储帐户，以下是快照失败的其他原因。

* 正在源中更新文件，但发生了快照。 这可能会导致目标为0字节的文件。 在源中完成更新后，后续快照应该会成功。

对于 SQL 源，以下是快照失败的其他原因。 

* 不运行用于授予数据共享权限的源或目标 SQL 脚本。 或者对于 Azure SQL 数据库或 Azure Synapse Analytics (以前称为 Azure SQL DW) ，它是使用 SQL 身份验证而不是 Azure Active Directory 身份验证运行的。  
* 源或目标 SQL 数据存储已暂停。
* 快照进程或目标数据存储不支持 SQL 数据类型。 有关详细信息，请参阅 [从 SQL 源共享](how-to-share-from-sql.md#supported-data-types) 。
* 源或目标 SQL 数据存储区已被其他进程锁定。 Azure 数据共享不会将锁应用于源和目标 SQL 数据存储区。 但是，源和目标 SQL 数据存储上的现有锁将导致快照失败。
* 目标 SQL 表被 foreign key 约束引用。 在快照过程中，如果存在具有相同名称的目标表，则 Azure 数据共享会删除表并创建新表。 如果目标 SQL 表被 foreign key 约束引用，则无法删除此表。
* 生成目标 CSV 文件，但无法在 Excel 中读取数据。 如果源 SQL 表包含非英语字符的数据，则可能会发生这种情况。 在 Excel 中，选择 "获取数据" 选项卡并选择 CSV 文件，选择 "文件源" 作为65001： Unicode (UTF-8) 并加载数据。

## <a name="snapshot-issue-after-updating-snapshot-schedule"></a>更新快照计划后出现快照问题
在数据访问接口为已发送共享更新快照计划后，数据使用者需要禁用以前的快照计划，并为接收的共享重新启用更新后的快照计划。 

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。 

若要了解如何接收数据，请继续阅读 [接受和接收数据](subscribe-to-data-share.md) 教程。