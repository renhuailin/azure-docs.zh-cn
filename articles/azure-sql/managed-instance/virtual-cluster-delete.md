---
title: 在删除 SQL 托管实例后删除子网
description: 了解如何在删除 Azure SQL 托管实例后删除 Azure 虚拟网络。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: d61a3f5a3dac8cfb6215bdfd6ff1c457c4e14150
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214450"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>在删除 Azure SQL 托管实例后删除子网
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文提供有关如何在删除子网中的最后一个 Azure SQL 托管实例后，手动删除该子网的指导。

SQL 托管实例将部署到[虚拟群集](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)中。 每个虚拟群集与某个子网关联，将在创建第一个实例时一同部署。 同样，虚拟群集会在删除最后一个实例时一起自动删除，使子网变空且可删除。 无需在虚拟群集上执行任何手动操作即可释放子网。 删除最后一个虚拟群集后，可以继续删除子网

在极少数情况下，创建操作可能失败并导致部署空的虚拟群集。 此外，由于实例创建操作[可以取消](management-operations-cancel.md)，因此部署的虚拟群集可能包含处于故障状态的实例。 在这种情况下，会自动启动虚拟群集删除操作，并在后台将其删除。

> [!NOTE]
> 保留空虚拟群集或未能创建的实例不会产生任何费用。

> [!IMPORTANT]
> - 若要成功删除，该虚拟群集不能包含任何 SQL 托管实例。 这不包括未能创建的实例。 
> - 删除虚拟群集是一项长时间运行的操作，持续约 1.5 个小时（有关最新的虚拟群集删除时间，请参阅 [SQL 托管实例管理操作](management-operations-overview.md)）。 在此过程完成之前，仍会在门户中显示虚拟群集。
> - 在虚拟群集上只能运行一项删除操作。 客户发起的所有后续删除请求都将导致错误，因为删除操作已在进行。

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>在 Azure 门户中删除虚拟群集

> [!IMPORTANT]
> 从 2021 年 9 月 1 日开始， 在删除群集中的最后一个实例后，会自动删除所有虚拟群集。 不再需要手动删除虚拟群集。

若要使用 Azure 门户删除虚拟群集，请搜索虚拟群集资源。

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕截图，其中突出显示了搜索框](./media/virtual-cluster-delete/virtual-clusters-search.png)

找到要删除的虚拟群集后，请选择此资源，然后选择“删除”。 系统会提示你确认删除该虚拟群集。

> [!div class="mx-imgBorder"]
> ![Azure 门户“虚拟群集”仪表板的屏幕截图，其中突出显示了“删除”选项](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure 门户通知会显示确认信息，确认已成功提交删除虚拟群集的请求。 删除操作本身将持续约 1.5 个小时，在此期间，虚拟群集仍在门户中可见。 完成此过程后，虚拟群集将不再可见，与之关联的子网并将被释放以供重复使用。

> [!TIP]
> 如果没有 SQL 托管实例显示在虚拟群集中，而你无法删除虚拟群集，请确保没有正在进行的实例部署。 这包括已启动的和已取消的仍在进行的部署。 这是因为这些操作仍将使用虚拟群集，所以会将其锁定，使其无法删除。 在实例所部署到的资源组的“部署”选项卡中可以查看正在进行的所有部署。 在这种情况下，请等待部署完成，然后删除 SQL 托管实例。 在删除实例的过程中，会同步删除虚拟群集。

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>使用 API 删除虚拟群集

若要通过 API 删除虚拟群集，请使用[虚拟群集删除方法](/rest/api/sql/virtualclusters/delete)中指定的 URI 参数。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是 Azure SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 了解 [SQL 托管实例中的连接体系结构](connectivity-architecture-overview.md)。
- 了解如何[为 SQL 托管实例修改现有的虚拟网络](vnet-existing-add-subnet.md)。
- 有关如何创建虚拟网络、创建 Azure SQL 托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](instance-create-quickstart.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](custom-dns-configure.md)。
