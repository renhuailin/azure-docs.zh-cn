---
author: jovanpop-msft
ms.service: sql-database
ms.subservice: deployment-configuration
ms.topic: include
ms.date: 12/17/2020
ms.author: jovanpop
ms.openlocfilehash: a253cae49bcb8fff1b14736302810bffc9d1ecb0
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2021
ms.locfileid: "111593576"
---
  可以根据下述快速入门之一，创建数据库，然后对其进行配置：

  | 操作 | SQL 数据库 | SQL 托管实例 | Azure VM 上的 SQL Server | Azure Synapse Analytics |
  |:--- |:--- |:---|:---|:---|
  | 创建| [门户](../database/single-database-create-quickstart.md) | [门户](../managed-instance/instance-create-quickstart.md) | [门户](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) | [门户](../../synapse-analytics/quickstart-create-workspace.md) |
  || [CLI](../database/scripts/create-and-configure-database-cli.md) | | | [CLI](../../synapse-analytics/quickstart-create-workspace-cli.md) |
  || [PowerShell](../database/scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md) | [PowerShell](../../synapse-analytics/quickstart-create-workspace-powershell.md) |
  || | | [部署模板](../virtual-machines/windows/create-sql-vm-resource-manager-template.md) | [部署模板](../../synapse-analytics/quickstart-deployment-template-workspaces.md) | 
  | 配置 | [服务器级别 IP 防火墙规则](../database/firewall-create-server-level-portal-quickstart.md)| [从 VM 进行连接](../managed-instance/connect-vm-instance-configure.md)| |
  |||[来自本地的连接](../managed-instance/point-to-site-p2s-configure.md) | [连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) |
  | 获取连接信息 | [Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)|[Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)| [SQL VM](../virtual-machines/windows/sql-vm-create-portal-quickstart.md?#connect-to-sql-server)| Synapse SQL|
  |||||
