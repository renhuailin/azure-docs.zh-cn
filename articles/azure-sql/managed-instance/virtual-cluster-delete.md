---
title: 删除 SQL 托管实例后删除子网
description: 了解如何在删除 Azure SQL 托管实例后删除 Azure 虚拟网络。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496ff6c7ec39706a99bb40447b6443ca71f19e5e
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093666"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>删除 Azure SQL 托管实例后删除子网
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍了如何在删除位于其上的最后一个 Azure SQL 托管实例之后手动删除子网。

SQL 托管实例将部署到 [虚拟群集](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)中。 每个虚拟群集都与一个子网关联。 在最后一次实例删除后，虚拟群集将按设计持续12小时，使你能够更快地在同一子网中创建 SQL 托管实例。 可以免费保留空的虚拟群集。 在此期间，无法删除与该虚拟群集关联的子网。

如果不想等待 12 个小时，而希望更快地删除虚拟群集及其子网，则可以手动执行此操作。 请通过 Azure 门户或虚拟群集 API 手动删除虚拟群集。

> [!IMPORTANT]
> - 虚拟群集不应包含任何 SQL 托管实例，删除操作才能成功。 
> - 删除虚拟群集是长时间运行的操作，持续大约1.5 小时 (参阅 [SQL 托管实例管理操作](./sql-managed-instance-paas-overview.md#management-operations) 以获取最新的虚拟群集删除时间) 。 在此过程完成之前，仍会在门户中显示虚拟群集。

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>在 Azure 门户中删除虚拟群集

若要使用 Azure 门户删除虚拟群集，请搜索虚拟群集资源。

![Azure 门户的屏幕截图，其中突出显示了搜索框](./media/virtual-cluster-delete/virtual-clusters-search.png)

找到要删除的虚拟群集后，请选择此资源，然后选择“删除”。 系统会提示你确认删除该虚拟群集。

![Azure 门户“虚拟群集”仪表板的屏幕截图，其中突出显示了“删除”选项](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure 门户通知会显示确认信息，确认已成功提交删除虚拟群集的请求。 删除操作本身将持续约 1.5 个小时，在此期间，虚拟群集仍在门户中可见。 完成此过程后，虚拟群集将不再可见，与之关联的子网并将被释放以供重复使用。

> [!TIP]
> 如果虚拟群集中没有显示任何 SQL 托管实例，并且无法删除虚拟群集，请确保没有正在进行的实例部署。 这包括已启动的和已取消的仍在进行的部署。 这是因为这些操作仍将使用虚拟群集，所以会将其锁定，使其无法删除。 实例部署到的资源组的查看“部署”选项卡会指示正在进行的任何部署。 在这种情况下，请等待部署完成，删除 SQL 托管实例，然后删除虚拟群集。

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>使用 API 删除虚拟群集

若要通过 API 删除虚拟群集，请使用[虚拟群集删除方法](/rest/api/sql/virtualclusters/delete)中指定的 URI 参数。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是 Azure SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 了解 [SQL 托管实例中的连接体系结构](connectivity-architecture-overview.md)。
- 了解如何[为 SQL 托管实例修改现有的虚拟网络](vnet-existing-add-subnet.md)。
- 有关演示如何创建虚拟网络、创建 Azure SQL 托管实例和从数据库备份还原数据库的教程，请参阅 [创建 AZURE sql 托管实例 (门户) ](instance-create-quickstart.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](custom-dns-configure.md)。