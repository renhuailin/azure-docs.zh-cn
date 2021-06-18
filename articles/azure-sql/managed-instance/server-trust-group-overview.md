---
title: 服务器信任组
titleSuffix: Azure SQL Managed Instance
description: 了解服务器信任组，以及如何管理 Azure SQL 托管实例之间的信任。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: mathoma
ms.date: 10/08/2020
ms.openlocfilehash: f66050545c2c30c592b41cb21c30394316c80711
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110692722"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>使用服务器信任组来设置和管理 SQL 托管实例之间的信任
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

“服务器信任组”是一个用于管理 Azure SQL 托管实例之间的信任的概念。 可以通过创建组在其成员之间建立基于证书的信任。 此信任可用于不同的跨实例方案。 删除组中的服务器或删除组会删除服务器之间的信任。 若要创建或删除服务器信任组，用户需要具有托管实例的写入权限。
[服务器信任组](/azure/templates/microsoft.sql/allversions)是在 Azure 门户中标记为“SQL 信任组”的 Azure 资源管理器对象。

> [!NOTE]
> 服务器信任组是在 Azure SQL 托管实例之间的分布式事务的公共预览版中引入的，当前存在一些限制，本文稍后将对此进行介绍。

## <a name="server-trust-group-setup"></a>服务器信任组设置

可以通过 [Azure PowerShell](/powershell/module/az.sql/new-azsqlservertrustgroup) 或 [Azure CLI](/cli/azure/sql/stg) 设置服务器信任组。 以下部分介绍了使用 Azure 门户的服务器信任组的设置。

1. 转到 [Azure 门户](https://portal.azure.com/)。

2. 导航到你计划将其添加到新创建的服务器信任组的 Azure SQL 托管实例。

3. 在“安全性”设置中，选择“SQL 信任组”选项卡。 

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="服务器信任组":::

4. 在“服务器信任组”配置页中，选择“新建组”图标。

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="新建组":::

5. 在 **SQL 信任组** 的“创建”边栏选项卡上，设置“组名称”。 组名称在组成员驻留的全部区域中都必须是独一无二的。 **信任作用域** 定义了通过服务器信任组启用的跨实例方案的类型。 在预览版中，唯一适用的信任作用域是“分布式事务”，因此它是预先选择的，无法更改。 所有 **组成员** 必须属于同一 **订阅**，但可以位于不同的资源组下。 选择 **资源组** 和 **SQL Server/实例**，以选择将作为组成员的 Azure SQL 托管实例。

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="服务器信任组的“创建”边栏选项卡":::

6. 填写所有必填字段之后，单击“保存”。

## <a name="server-trust-group-maintenance-and-deletion"></a>服务器信任组的维护和删除

服务器信任组无法编辑。 若要从组中删除托管实例，需要删除该组并创建一个新组。

以下部分介绍了服务器信任组的删除过程。 
1. 转到 Azure 门户。
2. 导航到属于信任组的某个托管实例。
3. 在“安全性”设置中，选择“SQL 信任组”选项卡。 
4. 选择要删除的信任组。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="选择服务器信任组":::
5. 单击“删除组”。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="删除服务器信任组":::
6. 键入服务器信任组名称以确认删除，然后单击“删除”。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="确认服务器信任组删除":::

> [!NOTE]
> 删除服务器信任组可能不会立即删除两个托管实例之间的信任。 可以通过调用托管实例的[故障转移](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover)来强制执行信任删除。 请查看[已知问题](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues)来了解此方面的最新更新。

## <a name="limitations"></a>限制

在公共预览版中，服务器信任组存在以下限制。
 * 服务器信任组的名称在它隶属的所有区域中必须是唯一的。
 * 组只能包含 Azure SQL 托管实例，这些实例必须属于同一 Azure 订阅。
 * 在预览版中，组只能有两个托管实例。 若要跨两个以上的托管实例执行分布式事务，你需要为每对托管实例创建服务器信任组。
 * 对于服务器信任组，分布式事务是唯一适用的作用域。
 * 只能从 Azure 门户管理服务器信任组。 以后将支持通过 Powershell 和 CLI 进行管理。
 * 无法在 Azure 门户中编辑服务器信任组。 只能创建或删除它。
 * 分布式事务的其他限制可能与你的方案有关。 最值得注意的是，必须通过 VNET 或 VNET 对等互连在托管实例之间建立基于专用终结点的连接。 请确保了解当前的[托管实例分布式事务限制](../database/elastic-transactions-overview.md#limitations)。

## <a name="next-steps"></a>后续步骤

* 有关 Azure SQL 托管实例中的分布式事务的详细信息，请参阅[分布式事务](../database/elastic-transactions-overview.md)。
* 有关版本更新和已知问题的状态，请参阅[托管实例发行说明](../database/doc-changes-updates-release-notes.md)。
* 如果有功能请求，请将其添加到[托管实例论坛](https://feedback.azure.com/forums/915676-sql-managed-instance)。
