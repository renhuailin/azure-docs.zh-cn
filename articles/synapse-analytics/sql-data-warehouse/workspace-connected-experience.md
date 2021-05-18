---
title: 在专用 SQL 池（以前称为 SQL DW）中启用 Synapse 工作区功能
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
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694608"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>在现有的专用 SQL 池（以前称为 SQL DW）中启用 Synapse 工作区功能

现在，所有 SQL 数据仓库客户均可通过 Synapse Studio 和工作区访问和使用现有的专用 SQL 池（以前称为 SQL DW）实例，不会对自动化、连接或工具造成影响。 本文介绍了现有的 Azure Synapse Analytics 客户如何利用 Synapse 工作区和 Studio 目前提供的充满新特性的功能来扩展其现有的 Analytics 解决方案。   

## <a name="experience"></a>体验
 
现在，Synapse 工作区已发布，你可以在 DW 门户的概述部分找到一项新功能，该功能允许你为现有的专用 SQL 池（以前称为 SQL DW）实例创建 Synapse 工作区。 此新功能将使你能够将托管现有数据仓库实例的逻辑服务器连接到新的 Synapse 工作区。 此连接可确保在该服务器上托管的所有数据仓库都可以从工作区和 Studio 进行访问，并且可以与 Synapse 合作伙伴服务（无服务器 SQL 池、Apache Spark 池和 ADF）结合使用。 完成预配步骤并建立与新创建的工作区的连接后，就可以开始访问并使用资源。  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="连接的 Synapse 工作区":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>使用 Synapse 工作区和 Studio 功能访问和使用专用的 SQL 池（以前称为 SQL DW）
 
以下内容适用于使用一个启用了 Synapse 工作区功能的专用 SQL DW（以前称为 SQL DW）的情况： 
- **SQL 功能**：启用 Synapse 工作区功能后，所有 SQL 功能都会保留在逻辑 SQL 服务器中。 启用工作区后，仍然可以通过 SQL 资源提供程序访问服务器。 所有管理功能都可通过工作区启动，操作将在托管 SQL 池的逻辑 SQL 服务器上进行。 启用工作区后，不会中断现有的自动化、工具或连接。  
- **资源移动**：在启用了 Synapse 工作区功能的服务器上启动资源移动操作会导致服务器和工作区之间的链接断开，你将不再能够从工作区访问现有的专用 SQL 池（以前称为 SQL DW）实例。 为了确保保持连接，建议将两个资源保留在相同的订阅和资源组中。 
- **监视**：可以在监视中心查看通过 Synapse Studio 在启用工作区的专用 SQL 池（以前称为 SQL DW）中提交的 SQL 请求。 对于所有其他监视活动，你可以转到 Azure 门户专用 SQL 池（以前的 SQL DW）监视。 
- **安全性** 和 **访问控制**：如上所述，SQL Server 和专用 SQL 池（以前称为 SQL DW）实例的所有管理功能会继续驻留在逻辑 SQL 服务器上。 这些功能包括防火墙规则管理、服务器 Azure AD 管理员设置，以及专用 SQL 池（以前称为 SQL DW）中的数据的所有访问控制。 必须执行以下步骤，以确保专用 SQL 池（以前称为 SQL DW）可以通过 Synapse 工作区进行访问和使用。 工作区角色成员身份不会给予用户对专用 SQL 池（以前称为 SQL DW）实例中的数据的权限。 遵循一般的 [SQL 身份验证](sql-data-warehouse-authentication.md)策略，以确保用户可以访问逻辑服务器上的专用 SQL 池（以前称为 SQL DW）实例。 如果专用 SQL 池（以前称为 SQL DW）主机服务器已经分配了托管标识，则此托管标识的名称将与工作区托管标识的名称相同。此托管标识是自动创建的，用于支持工作区合作伙伴服务（例如 ADF 管道）。  联网场景中可以存在两个具有相同名称的托管标识。 托管标识可通过其 Azure AD 对象 ID 区分，即将推出使用对象 ID 创建 SQL 用户的功能。

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > 已连接的工作区 Synapse Studio 会根据用户在 Azure 中的权限显示专用池的名称。 如果用户没有 SQL 池的权限，则无法访问池中的对象。 

- **网络安全性**：如果在现有的专用 SQL 池（以前称为 SQL DW）中启用的 Synapse 工作区是为了进行数据渗透防护而启用的，则请： 创建从工作区到逻辑 SQL 服务器的托管专用终结点连接。 批准专用终结点连接请求，以允许服务器和工作区之间的通信。
- **数据中心** 的 **Studio** SQL 池：可通过数据中心的工具提示识别启用了工作区的专用 SQL 池（以前称为 SQL DW）。 
- **创建新的专用 SQL 池（以前称为 SQL DW）** ：启用工作区功能后，可以通过 Synapse 工作区和 Studio 创建新的专用 SQL 池，然后系统就会在逻辑 SQL 服务器上进行新池的预配。 预配完成后，新资源会显示在门户和 Studio 中。      

## <a name="next-steps"></a>后续步骤
在现有的专用 SQL 池（以前称为 SQL DW）中启用 [Synapse 工作区功能](workspace-connected-create.md)
