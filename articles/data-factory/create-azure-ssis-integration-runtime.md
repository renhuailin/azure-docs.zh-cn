---
title: 在 Azure 数据工厂中创建 Azure-SSIS 集成运行时
description: 了解如何在 Azure 数据工厂中创建 Azure-SSIS 集成运行时，以便能够在 Azure 中部署和运行 SSIS 包。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/19/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a3e6c8318309959eb2a709837040db42575b91b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215370"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中创建 Azure-SSIS 集成运行时

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文提供了在 Azure 数据工厂 (ADF) 中预配 Azure-SQL Server Integration Services (SSIS) 集成运行时 (IR) 的步骤。 Azure-SSIS IR 支持：

- 运行部署在由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型）
- 运行部署在由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中的包（包部署模型）

预配 Azure-SSIS IR 后，可以使用熟悉的工具在 Azure 中部署和运行包。 这些工具已启用 Azure 并包括 SQL Server Data Tools (SSDT)、SQL Server Management Studio (SSMS) 和命令行实用工具，例如 [dtutil](/sql/integration-services/dtutil-utility) 和 [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md)。

[预配 Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md) 教程介绍了如何通过 Azure 门户或数据工厂应用创建 Azure-SSIS IR。 该教程还介绍了如何选择性地使用 Azure SQL 数据库服务器或托管实例来承载 SSISDB。 本文对该教程的基础上有所延伸，介绍如何执行以下可选任务：

- 使用具有IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例来承载 SSISDB。 作为先决条件，需要配置虚拟网络权限和设置，才能让 Azure-SSIS IR 加入虚拟网络。

- 对数据工厂指定系统/用户分配的托管标识使用 Azure Active Directory (Azure AD) 身份验证，以连接到 Azure SQL 数据库服务器或托管实例。 作为先决条件，需要将数据工厂指定系统/用户分配的托管标识添加为可创建 SSISDB 实例的数据库用户。

- 将 Azure-SSIS IR 加入虚拟网络，或将自承载 IR 配置为代理，使 Azure-SSIS IR 能够访问本地数据。

本文将介绍如何使用 [Azure 门户](create-azure-ssis-integration-runtime-portal.md)、[Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md) 和 [Azure 资源管理器模板](create-azure-ssis-integration-runtime-resource-manager-template.md)来预配 Azure-SSIS IR。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 订阅**。 如果还没有订阅，则可以创建[免费试用](https://azure.microsoft.com/pricing/free-trial/)帐户。

- Azure SQL 数据库服务器或 SQL 托管实例（可选）。 如果还没有数据库服务器或托管实例，请在启动之前在 Azure 门户中创建一个。 数据工厂进而会在此数据库服务器上创建一个 SSISDB 实例。 

  建议在集成运行时所在的同一 Azure 区域中创建数据库服务器或托管实例。 此配置允许集成运行时将执行日志写入 SSISDB 而无需跨 Azure 区域。

  请记住以下几点：

  - 系统可以代表你创建 SSISDB 实例作为单一数据库、创建此实例作为弹性池的一部分，或者在托管实例中创建。 可以在公用网络中访问或者通过加入虚拟网络来访问该实例。 有关在 SQL 数据库与 SQL 托管实例之间进行选择以托管 SSISDB 的指导，请参阅本文中的[比较 SQL 数据库与 SQL 托管实例](#comparison-of-sql-database-and-sql-managed-instance)部分。 
  
    如果使用具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的 SQL 托管实例来承载 SSISDB，或者需要在未配置自承载 IR 的情况下访问本地数据，则需要将 Azure-SSIS IR 加入虚拟网络。 有关详细信息，请参阅[将 Azure-SSIS IR 加入虚拟网络](./join-azure-ssis-integration-runtime-virtual-network.md)。

  - 确认为数据库服务器启用了“允许访问 Azure 服务”设置。 使用具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的 SQL 托管实例来承载 SSISDB 时，此设置并不适用。 有关详细信息，请参阅[保护 Azure SQL 数据库的安全](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules)。 若要通过 PowerShell 来启用此设置，请参阅 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。

  - 将客户端计算机的 IP 地址或一系列包括客户端计算机 IP 地址的 IP 地址添加到数据库服务器的防火墙设置中的客户端 IP 地址列表。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](../azure-sql/database/firewall-configure.md)。

  - 若要连接到数据库服务器，可以通过使用 SQL 身份验证（使用服务器管理员凭据）的方式，也可以通过使用 Azure AD 身份验证（使用为数据工厂指定的系统/用户分配的托管标识）的方式。 对于后一种方式，需要将为数据工厂指定的系统/用户分配的托管标识添加到有权访问数据库服务器的 Azure AD 组中。 有关详细信息，请参阅[为 Azure-SSIS IR 启用 Azure AD 身份验证](./enable-aad-authentication-azure-ssis-ir.md)。

  - 确认你的数据库服务器还没有 SSISDB 实例。 预配 Azure-SSIS IR 时不支持使用现有的 SSISDB 实例。

- **Azure 资源管理器虚拟网络（可选）** 。 如果下列条件中至少有一个属实，则必须配置 Azure 资源管理器虚拟网络：

  - 在具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例上承载 SSISDB。

  - 需要从 SSIS 包连接到本地据存储，该包运行在没有配置自承载 IR 的 Azure-SSIS IR 上。

- **Azure PowerShell（可选）** 。 若要运行 PowerShell 脚本来预配 Azure-SSIS IR，请按照[如何安装和配置 Azure PowerShell](/powershell/azure/install-az-ps) 中的说明进行操作。

### <a name="regional-support"></a>区域支持

有关提供数据工厂和 Azure-SSIS IR 的 Azure 区域列表，请参阅[数据工厂和 SSIS IR 在各区域的上市情况](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>比较 SQL 数据库和 SQL 托管实例

下表比较了 Azure SQL 数据库服务器和 SQL 托管实例与 Azure-SSIR IR 相关的某些功能：

| 功能 | SQL 数据库| SQL 托管实例 |
|---------|--------------|------------------|
| **计划** | SQL Server 代理不可用。<br/><br/>请参阅[在数据工厂管道中计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity)。| 可以使用托管实例代理。 |
| **身份验证** | 可以使用包含的数据库用户创建 SSISDB 实例。该用户是 **db_owner** 角色的成员，代表具有数据工厂托管标识的任意 Azure AD 组。<br/><br/>请参阅[在 Azure SQL 数据库服务器中启用 Azure AD 身份验证以创建 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-database)。 | 可以使用代表数据工厂托管标识的包含的数据库用户创建 SSISDB 实例。 <br/><br/>请参阅[在 Azure SQL 托管实例中启用 Azure AD 身份验证以创建 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-authentication-on-azure-sql-managed-instance)。 |
| **服务层** | 在 Azure SQL 数据库服务器上创建 Azure-SSIS IR 时，可以选择 SSISDB 的服务层级。 有多个服务层级。 | 在托管实例上创建 Azure-SSIS IR 时，无法选择 SSISDB 的服务层级。 托管实例上的所有数据库共享分配给该实例的相同资源。 |
| **虚拟网络** | 如果使用具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器，则 Azure-SSIS IR 可以加入 Azure 资源管理器虚拟网络。 | 如果将托管实例与专用终结点结合使用，则 Azure-SSIS IR 可加入 Azure 资源管理器虚拟网络。 在未为托管实例启用公共终结点的情况下，虚拟网络是必需的。<br/><br/>如果将 Azure-SSIS IR 加入与托管实例相同的虚拟网络，请确保 Azure-SSIS IR 与托管实例位于不同的子网中。 如果将 Azure-SSIS IR 加入与托管实例不同的虚拟网络，我们建议使用虚拟网络对等互连或网络间的连接。 请参阅[将应用程序连接到 Azure SQL 数据库托管实例](../azure-sql/managed-instance/connect-application-instance.md)。 |
| **分布式事务** | 通过弹性事务支持此功能。 不支持 Microsoft 分布式事务处理协调器 (MSDTC) 事务。 如果 SSIS 包使用 MSDTC 协调分布式事务，请考虑迁移到 Azure SQL 数据库弹性事务。 有关详细信息，请参阅[跨云数据库的分布式事务](../azure-sql/database/elastic-transactions-overview.md)。 | 不支持。 |
| | | |


## <a name="next-steps"></a>后续步骤

- [了解如何使用 Azure 门户预配 Azure-SSIS IR](create-azure-ssis-integration-runtime-portal.md)。
- [了解如何使用 Azure PowerShell 预配 Azure-SSIS IR](create-azure-ssis-integration-runtime-powershell.md)。
- [了解如何使用 Azure 资源管理器模板预配 Azure-SSIS IR](create-azure-ssis-integration-runtime-resource-manager-template.md)。
- [在 Azure 数据工厂中部署和运行 SSIS 包](create-azure-ssis-integration-runtime-deploy-packages.md)。

请参阅本文档中的其他 Azure-SSIS IR 主题：

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般信息。
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索并了解有关 Azure-SSIS IR 的信息。
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过添加更多节点来横向扩展 Azure-SSIS IR。
- [在 Azure 中部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [连接到 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [在 Azure 中计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
