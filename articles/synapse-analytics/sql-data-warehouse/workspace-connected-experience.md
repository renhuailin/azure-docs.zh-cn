---
title: " (以前的 SQL DW) 启用专用 SQL 池上的 Synapse 工作区功能"
description: 本文档介绍了客户如何在工作区中访问和使用其现有的 SQL DW 独立实例。
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: d477693667c8d78687d27b291d2b3c15612a0f30
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989045"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a> (以前的 SQL DW) 启用现有专用 SQL 池上的 Synapse 工作区功能

所有 SQL 数据仓库客户现在都可以通过 Synapse Studio 和工作区访问并使用现有的专用 SQL 池 (以前的 SQL DW) 实例，而不会影响自动化、连接或工具。 本文介绍了现有的 Azure Synapse Analytics 客户如何通过使用通过 Synapse 工作区和工作室提供的新功能丰富功能来构建和扩展其现有分析解决方案。   

## <a name="experience"></a>体验
 
现在，Synapse 工作区已发布，可在 DW 门户的 "概述" 部分中找到新功能，该部分允许你 (以前的 SQL DW) 实例为现有专用 SQL 池创建 Synapse 工作区。 这一新功能使你能够将托管现有数据仓库实例的逻辑服务器连接到新的 Synapse 工作区。 此连接确保在该服务器上托管的所有数据仓库都可从工作区和 Studio 访问，并可与 Synapse 合作伙伴服务 (无服务器 SQL 池、Apache Spark 池和 ADF) 结合使用。 完成预配步骤并建立与新创建的工作区的连接后，可以开始访问并使用资源。  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="连接的 Synapse 工作区":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>使用 Synapse 工作区和工作室功能访问和使用专用的 SQL 池 (以前的 SQL DW) 
 
使用专用的 SQL DW (以前启用了 Synapse 工作区功能的 SQL DW) 时，将应用以下信息： 
- **SQL 功能** 启用 Synapse 工作区功能后，所有 SQL 功能都将保留在逻辑 SQL server 上。 启用工作区后，仍然可以通过 SQL 资源提供程序访问服务器。 所有管理功能都可通过工作区启动，操作将在托管 SQL 池的逻辑 SQL Server 上进行。 启用工作区后，不会中断或中断现有的自动化、工具或连接。  
- **资源移动**  在启用了 Synapse 工作区功能的服务器上启动资源移动将导致服务器和工作区之间的链接中断，并且你将不再能够从工作区 (以前的 SQL DW) 实例访问现有专用 SQL 池。 为了确保保持连接，建议将两个资源保留在相同的订阅和资源组中。 
- **监视** 通过启用了工作区中的 Synapse Studio 提交的 SQL 请求， (以前的 SQL DW) 可以在监视中心中查看。 对于所有其他监视活动，你可以转到 Azure 门户专用 SQL 池（以前的 SQL DW）监视。 
- **安全性** 和 **访问控制** 如上所述，sql server 的所有管理功能和专用 sql DW (以前的 sql DW) 实例将继续驻留在逻辑 SQL server 上。 这些功能包括防火墙规则管理、设置服务器 Azure AD 管理员以及专用 SQL 池中的数据的所有访问控制 (以前的 SQL DW) 。 必须执行以下步骤以确保专用 SQL DW)  (以前的 SQL DW 可访问，并可通过 Synapse 工作区使用。 工作区角色成员身份不会授予用户对专用 SQL 池中的数据的权限， (以前的 SQL DW) 实例。 遵循一般的 [sql 身份验证](sql-data-warehouse-authentication.md) 策略，以确保用户可以访问逻辑服务器上 (以前的 sql DW) 实例的专用 sql 池。 如果专用 SQL 池 (以前的 SQL DW) 主机服务器已经分配了托管标识，则此托管标识的名称将与工作区托管标识的名称相同，该标识是自动创建的，用于支持工作区伙伴服务 (如 ADF 管道) 。  连接的方案中可以存在两个具有相同名称的托管标识。 托管标识可由其 Azure AD 对象 Id 区分，即将推出使用对象 Id 创建 SQL 用户的功能。

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > 已连接的工作区 Synapse Studio 将根据用户在 Azure 中的权限显示专用池的名称。 如果用户不具有 SQL 池的权限，则无法访问池中的对象。 

- **网络安全性** 如果在现有专用 SQL 池上启用的 Synapse 工作区 (以前的 SQL DW) 启用 data 渗透 protection。 创建从工作区到逻辑 SQL 服务器的托管专用终结点连接。 批准专用终结点连接请求，以允许服务器和工作区之间的通信。
- **工作室****数据中心** 内的 sql 池启用了工作区的专用 sql 池 (以前的 sql DW) 可通过数据中心中的工具提示进行标识。 
- **创建新的专用 sql 池 (以前的 SQL DW)** 启用工作区功能后，可以通过 Synapse 工作区和工作室创建新的专用 SQL 池，并将在逻辑 SQL 服务器上进行新池的预配。 预配完成后，新资源将显示在门户和 Studio 中。      

## <a name="next-steps"></a>后续步骤
 (以前的 SQL DW) 启用现有专用 SQL 池上的[Synapse 工作区功能](workspace-connected-create.md)
