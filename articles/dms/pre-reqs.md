---
title: 使用 Azure 数据库迁移服务的先决条件
description: 了解使用 Azure 数据库迁移服务执行数据库迁移的先决条件的概述。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 94acb0557fd69497ef7f599a157be8db3f2ce026
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128578465"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>使用 Azure 数据库迁移服务的先决条件概述

若要确保 Azure 数据库迁移服务在执行数据库迁移时顺利运行，需要满足几个先决条件。 某些先决条件适用于该服务支持的所有方案（源/目标对），而其他先决条件则是特定方案所特有的。

以下各部分列出了与使用 Azure 数据库迁移服务关联的先决条件。

## <a name="prerequisites-common-across-migration-scenarios"></a>在不同迁移方案中通用的先决条件

在所有支持的迁移方案中通用的 Azure 数据库迁移服务先决条件包括需要：

* 使用 Azure 资源管理器部署模型创建适合 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，它将使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 为本地源服务器提供站点到站点连接。
* 请确保虚拟网络的网络安全组(NSG) 规则不阻止 ServiceBus、存储服务和 AzureMonitor 的 ServiceTag 出站端口 443。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。
* 配置[针对数据库引擎访问的 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 按照[启用或禁用服务器网络协议](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文中的说明启用 TCP/IP 协议（在安装 SQL Server Express 时，会默认禁用它）。

    > [!IMPORTANT]
    > 创建 Azure 数据库迁移服务实例需要访问通常不在同一资源组中的虚拟网络设置。 因此，创建 DMS 实例的用户需要订阅级别的权限。 若要创建所需的角色（你可以根据需要分配这些角色），请运行以下脚本：
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/*/write", `
    > "Microsoft.DataMigration/*/delete", `
    > "Microsoft.DataMigration/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>将 SQL Server 迁移到 Azure SQL 数据库的先决条件

除了普遍适用于所有迁移方案的 Azure 数据库迁移服务先决条件外，还有特别适用于个别方案的先决条件。

使用 Azure 数据库迁移服务执行 SQL Server 到 Azure SQL 数据库的迁移时，除了普遍适用于所有迁移方案的先决条件外，还必须满足以下附加先决条件：

* 按照[使用 Azure 门户在 Azure SQL 数据库中创建数据库](../azure-sql/database/single-database-create-quickstart.md)一文中的详细说明创建 Azure SQL 数据库实例。
* 下载并安装[数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更高版本。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 SQL Server（默认情况下为 TCP 端口 1433）。
* 如果使用动态端口运行多个命名 SQL Server 实例，则可能需要启用 SQL Browser 服务并允许通过防火墙访问 UDP 端口 1434，以便 Azure 数据库迁移服务可连接到源服务器上的命名实例。
* 为 SQL 数据库创建服务器级[防火墙规则](../azure-sql/database/firewall-configure.md)，以允许 Azure 数据库迁移服务访问目标数据库。 提供用于 Azure 数据库迁移服务的虚拟网络子网范围。
* 确保用于连接到源 SQL Server 实例的凭据具有 [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) 权限。
* 确保用于连接到目标数据库的凭据具有目标数据库的 CONTROL DATABASE 权限。

   > [!NOTE]
   > 有关使用 Azure 数据库迁移服务执行从 SQL Server 到 Azure SQL 数据库的迁移所需的先决条件的完整列表，请参阅教程[将 SQL Server 迁移到 Azure SQL 数据库](./tutorial-sql-server-to-azure-sql.md)。
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>将 SQL Server 迁移到 Azure SQL 托管实例的先决条件

* 按照[在 Azure 门户中创建 Azure SQL 托管实例](../azure-sql/managed-instance/instance-create-quickstart.md)一文中的详述创建 SQL 托管实例。
* 打开防火墙以允许 Azure 数据库迁移服务 IP 地址或子网范围的端口 445 上的 SMB 流量。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 SQL Server（默认情况下为 TCP 端口 1433）。
* 如果使用动态端口运行多个命名 SQL Server 实例，则可能需要启用 SQL Browser 服务并允许通过防火墙访问 UDP 端口 1434，以便 Azure 数据库迁移服务可连接到源服务器上的命名实例。
* 确保用于连接源 SQL Server 和目标托管实例的登录名是 sysadmin 服务器角色的成员。
* 创建网络共享，供 Azure 数据库迁移服务用来备份源数据库。
* 确保运行源 SQL Server 实例的服务帐户对你创建的网络共享拥有写入权限，并且源服务器的计算机帐户具有对同一共享的读/写访问权限。
* 请记下在前面创建的网络共享中拥有完全控制权限的 Windows 用户（和密码）。 Azure 数据库迁移服务可模拟用户凭据，将备份文件上传到 Azure 存储容器，以执行还原操作。
* 通过采用[使用存储资源管理器管理 Azure Blob 存储资源](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)一文中的步骤，创建 blob 容器并检索其 SAS URI。 在创建 SAS URI 时，务必在策略窗口选择所有权限（读取、写入、删除、列出）。

   > [!NOTE]
   > 有关使用 Azure 数据库迁移服务执行从 SQL Server 到 SQL 托管实例的迁移所需的先决条件的完整列表，请参阅教程[将 SQL Server 迁移到 SQL 托管实例](./tutorial-sql-server-to-managed-instance.md)。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据库迁移服务和区域可用性的概述，请参阅[什么是 Azure 数据库迁移服务](dms-overview.md)一文。