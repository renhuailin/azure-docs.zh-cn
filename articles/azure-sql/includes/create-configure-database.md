---
author: jovanpop-msft
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 12/17/2020
ms.author: jovanpop
ms.openlocfilehash: d1422d029bc2c5d41330e4ac2d21ff14e4fa82ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97703954"
---
  可以根据下述快速入门之一，创建数据库，然后对其进行配置：

  | 操作 | SQL 数据库 | SQL 托管实例 | Azure VM 上的 SQL Server | Azure Synapse Analytics |
  |:--- |:--- |:---|:---|:---|
  | 创建| [Portal](../database/single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) | [Portal](../../synapse-analytics/quickstart-create-workspace.md) |
  || [CLI](../database/scripts/create-and-configure-database-cli.md) | | | [CLI](../../synapse-analytics/quickstart-create-workspace-cli.md) |
  || [PowerShell](../database/scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md) | [PowerShell](../../synapse-analytics/quickstart-create-workspace-powershell.md) |
  || | | [部署模板](../virtual-machines/windows/create-sql-vm-resource-manager-template.md) | [部署模板](../../synapse-analytics/quickstart-deployment-template-workspaces.md) | 
  | 配置 | [服务器级别 IP 防火墙规则](../database/firewall-create-server-level-portal-quickstart.md)| [从 VM 进行连接](../managed-instance/connect-vm-instance-configure.md)| |
  |||[来自本地的连接](../managed-instance/point-to-site-p2s-configure.md) | [连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) |
  | 获取连接信息 | [Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)|[Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)| [SQL VM](../virtual-machines/windows/sql-vm-create-portal-quickstart.md?#connect-to-sql-server)| Synapse SQL|
  |||||
