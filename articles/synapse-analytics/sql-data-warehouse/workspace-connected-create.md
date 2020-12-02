---
title: 启用 Synapse 工作区功能
description: 本文档介绍用户如何在现有的专用 SQL 池 (以前的 SQL DW) 上启用 Synapse 工作区功能。
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: b105c4c9ee0bd27af11fe09a0fd756467e30bb35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466352"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>为专用 SQL 池启用 Synapse 工作区功能 (以前的 SQL DW) 

所有 SQL 数据仓库用户现在都可以通过 Synapse Studio 和工作区访问现有专用 SQL 池 (以前的 SQL DW) 实例。 用户可以使用 Synapse Studio 和工作区，而不影响自动化、连接或工具。 本文介绍现有的 Azure Synapse Analytics 用户如何启用现有专用 SQL 池的 Synapse 工作区功能 (以前的 SQL DW) 。 用户可以通过 Synapse 工作区和工作室利用现在提供的新功能丰富的功能来扩展其现有分析解决方案。   

## <a name="prerequisites"></a>先决条件
在数据仓库上启用 Synapse 工作区功能之前，必须确保已完成以下工作：
- 用于创建和管理 SQL 逻辑服务器上托管的 SQL 资源的权限。
- 用于创建 Synapse 资源的权限。
- 在逻辑服务器上标识的 Azure Active Directory 管理员

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>为现有专用 SQL 池启用 Synapse 工作区功能 (以前的 SQL DW) 

可以在受支持的区域中 (以前的 SQL DW) 的任何现有专用 SQL 池启用 Synapse 工作区功能。 此功能只能通过 Azure 门户提供。

按照以下步骤为现有数据仓库创建 Synapse 工作区。
1. 选择 (以前的 SQL DW) 的现有专用 SQL 池，并打开 "概述" 页。
2. 选择顶部菜单栏中的 " **新建 Synapse 工作区** " 或紧靠下面的消息。
3. 查看在 " **创建新的 Azure Synapse" 工作区** 页上通过新的 Synapse 工作区提供的数据仓库列表之后。 选择 " **继续** " 以继续。
4. 在 "基本信息" 页上，在 " **项目** 详细信息" 部分 (的现有专用 SQL 池应预填充到逻辑服务器下部署的同一 **订阅** 和 **资源组** 。 在 " **工作区详细信息**" 下， **工作区** 名称预先填充了 SQL 逻辑服务器的相同名称和区域，以确保可以在设置过程中创建新的 Synapse 工作区和逻辑服务器之间的连接。 必须保留此连接的预配，以确保通过工作区和工作室 (以前的 SQL DW) 实例继续访问专用 SQL 池。
5. 导航到“选择 Data Lake Storage Gen 2”。
6. 选择 " **新建** " 或选择现有的 **Data Lake Storage Gen2** ，然后选择 " **下一步：网络**"。
7. 选择 **无服务器实例** 的 **SQL 管理员密码**。 更改此密码不会更新或更改逻辑服务器的 SQL 凭据。 如果你更愿意使用系统定义的密码，请将这些字段保留为空。 此密码可在新工作区内随时更改。 管理员名称必须与 SQL Server 上使用的名称相同。
8. 选择首选的 **网络设置** ，并选择 " **查看 + 创建** " 启动工作区设置。
9. 选择 " **转到资源** " 打开新工作区。

    > [!NOTE]
    > 在逻辑服务器上宿主 (的所有专用 sql DW) 实例都可以通过新的工作区获得。

## <a name="post-provisioning-steps"></a>设置后步骤
必须完成以下步骤，以确保现有专用 SQL 池 (以前的 SQL DW) 实例可以通过 Synapse Studio 访问。
1. 在 "Synapse 工作区概述" 页中，选择 " **已连接服务器**"。 **连接的服务器** 将转到托管数据仓库的连接的 SQL 逻辑服务器。 在基本菜单中，选择 " **已连接服务器**"。
2. 打开 **防火墙和虚拟网络** ，确保客户端 ip 或预定义的 ip 范围有权访问逻辑服务器。
3. 打开 **Active Directory 管理员** ，并确保已在逻辑服务器上设置 AAD 管理员。
4. 选择一个专用的 SQL 池 (以前托管在逻辑服务器上的 SQL DW) 实例。 在 "概述" 页中，选择 " **启动 Synapse studio** " 或 "进入 [Synapse studio](https://web.azuresynapse.net) "，然后登录到你的工作区。

5. 打开 **数据中心** ，并在对象资源管理器中展开专用 SQL 池，以确保你已访问和查询数据仓库。

## <a name="next-steps"></a>后续步骤
[Synapse 工作区和 Studio](../get-started.md)入门。