---
title: 请求增加配额
description: 本页介绍如何创建增加 Azure SQL 数据库和 Azure SQL 托管实例配额的支持请求。
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 06/04/2020
ms.openlocfilehash: a5530becdcbe150ddc7ab2608bdf964ac269ed99
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111409012"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>请求增加 Azure SQL 数据库和 SQL 托管实例配额
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文介绍如何请求增加 Azure SQL 数据库和 Azure SQL 托管实例的配额。 本文还介绍如何启用对区域的订阅访问以及如何请求启用区域中的特定硬件。

## <a name="create-a-new-support-request"></a><a id="newquota"></a> 创建新的支持请求

使用以下步骤在 Azure 门户中为 SQL 数据库创建新的支持请求。

1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“帮助 + 支持”。

   ![“帮助 + 支持”链接](./media/quota-increase-request/help-plus-support.png)

1. 在“帮助 + 支持”中，选择“新建支持请求” 。

    ![创建新的支持请求](./media/quota-increase-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制(配额)” 。

   ![选择问题类型](./media/quota-increase-request/select-quota-issue-type.png)

1. 对于“订阅”，请选择要增加其配额的订阅。

   ![选择已增加配额的订阅](./media/quota-increase-request/select-subscription-support-request.png)

1. 对于“配额类型”，请选择以下配额类型之一：

   - **SQL 数据库**，适用于单一数据库和弹性池配额。
   - **SQL 数据库托管实例**，适用于托管实例。

   然后选择“下一步:**解决方案 >>** 。

   ![选择配额类型](./media/quota-increase-request/select-quota-type.png)

1. 在“详细信息”窗口中，选择“输入详细信息”以输入其他信息 。

   ![输入详细信息链接](./media/quota-increase-request/provide-details-link.png)

单击“输入详细信息”将显示“配额详细信息”窗口，可在其中添加其他信息 。 以下各部分介绍适用于“SQL 数据库”和“SQL 托管实例”配额类型的不同选项 。

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> SQL 数据库配额类型

以下各部分介绍适用于 SQL 数据库配额类型的配额增加选项：

- 每个服务器的数据库事务单位 (DTU) 数
- 每个订阅的服务器数
- 订阅或特定硬件的区域访问权限

### <a name="database-transaction-units-dtus-per-server"></a>每个服务器的数据库事务单位 (DTU) 数

使用以下步骤来请求增加每个服务器的 DTU 数。

1. 选择“每个服务器的数据库事务单位 (DTU)”配额类型。

1. 在“资源”列表中，选择要作为目标的资源。

1. 在“新建配额”字段中，输入你请求的新 DTU 限制。

   ![DTU 配额详细信息](./media/quota-increase-request/quota-details-dtus.png)

有关详细信息，请参阅[使用 DTU 购买模型的单一数据库的资源限制](resource-limits-dtu-single-databases.md)和[使用 DTU 购买模型的弹性池的资源限制](resource-limits-dtu-elastic-pools.md)。

### <a name="servers-per-subscription"></a>每个订阅的服务器数

使用以下步骤来请求增加每个订阅的服务器数。

1. 选择“每个订阅的服务器数”配额类型。

1. 在“位置”列表中，选择要使用的 Azure 区域。 配额按每个区域的每个订阅设置。

1. 在“新配额”字段中，输入对该区域中服务器的最大数量的请求。

   ![服务器配额详细信息](./media/quota-increase-request/quota-details-servers.png)

有关详细信息，请参阅 [SQL 数据库资源限制和资源调控](resource-limits-logical-server.md)。

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> 启用对区域的订阅访问

某些产品/服务类型在每个区域中都不可用。 你可能会看到如下所示的错误：

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

如果你的订阅需要访问特定区域，请选择“区域访问”选项。 在请求中，指定要为区域启用的产品/服务和 SKU 详细信息。 若要浏览产品/服务和 SKU 选项，请参阅 [Azure SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/single/)。

1. 选择“区域访问”配额类型。

1. 在“选择位置”列表中，选择要使用的 Azure 区域。 配额按每个区域的每个订阅设置。

1. 输入“购买模型”和“预期使用量”详细信息 。

   ![请求区域访问权限](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>请求启用区域中的特定硬件

如果想要使用的硬件代系在你的区域中不可用，你可能需要使用以下步骤来进行请求。 有关硬件代系和区域可用性的详细信息，请参阅 [SQL 数据库的硬件代系](./service-tiers-sql-database-vcore.md#hardware-generations)或 [SQL 托管实例的硬件代系](../managed-instance/service-tiers-managed-instance-vcore.md#hardware-generations)。

1. 选择“其他配额请求”配额类型。

1. 在“说明”字段中，说明请求，其中需包括硬件生成的名称以及需要在其中使用它的区域的名称。

   ![请求新区域的硬件](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>提交请求

最后一步是填写 SQL 数据库配额请求的剩余详细信息。 然后选择“下一步:  查看 + 创建 >>”，在查看请求详细信息后，单击“创建”以提交请求  。

## <a name="next-steps"></a>后续步骤

提交请求后，可查看该请求。 系统会根据你在表单中提供的信息与你联系。

有关其他 Azure 限制的详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
