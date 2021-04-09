---
title: 启用 Synapse 工作区功能
description: 本文档介绍用户如何在现有的专用 SQL 池（以前称为 SQL DW）中启用 Synapse 工作区功能。
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 769ca4cf0ecbdba5fd80d977eb69c8a4f58df55f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98071181"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>为专用 SQL 池（以前称为 SQL DW）启用 Synapse 工作区功能

现在，所有 SQL 数据仓库用户均可通过 Synapse Studio 和工作区访问和使用现有的专用 SQL 池（以前称为 SQL DW）实例。 用户现在可以使用 Synapse Studio 和工作区，且不会对自动化、连接或工具造成影响。 本文介绍现有的 Azure Synapse Analytics 用户如何为现有的专用 SQL 池（以前称为 SQL DW）启用 Synapse 工作区功能。 用户可以利用 Synapse 工作区和 Studio 目前提供的丰富且全新的功能来扩展其现有的 Analytics 解决方案。   

## <a name="prerequisites"></a>先决条件
在数据仓库上启用 Synapse 工作区功能之前，必须确保满足以下条件
- 有权创建和管理 SQL 逻辑服务器中托管的 SQL 资源。
- 有权创建 Azure Synapse 资源。
- 逻辑服务器中标识的 Azure Active Directory 管理员

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>为现有的专用 SQL 池（以前称为 SQL DW）启用 Synapse 工作区功能

可以在支持区域中的任何现有专用 SQL 池（以前称为 SQL DW）上启用 Synapse 工作区功能。 仅可通过 Azure 门户使用此功能。

请按照以下步骤为现有的数据仓库创建 Synapse 工作区。
1. 选择现有的专用 SQL 池（以前称为 SQL DW），然后打开概述页。
2. 选择顶部菜单栏中的“新建 Synapse 工作区”，或下方的消息。
3. 查看数据仓库（通过“新建 Azure Synapse 工作区”页上的新 Synapse 工作区提供）的列表后。 选择“继续”以继续。
4. 在“基本”页面上，应使用在逻辑服务器下部署的“订阅”和“资源组”预填充现有的专用 SQL 池（“项目”详细信息部分）  。 在“工作区详细信息”下，使用 SQL 逻辑服务器的名称和区域预填充“工作区”名称，确保可以在预配过程中创建新 Synapse 工作区与逻辑服务器之间的连接 。 预配后必须保留此连接，确保可以通过工作区和 Studio 继续访问专用 SQL 池（以前为 SQL DW）实例。
5. 导航到“选择 Data Lake Storage Gen 2”。
6. 选择“新建”或选择现有的 Data Lake Storage Gen2，然后再选择“下一步：网络”  。
7. 为无服务器实例选择 SQL 管理员密码 。 更改此密码不会更新或更改逻辑服务器 SQL 凭据。 如果倾向使用系统定义的密码，请将这些字段留空。 可以随时在新工作区中更改此密码。 管理员名称必须与 SQL Server 中使用的名称相同。
8. 选择首选的“网络设置”，然后选择“审阅 + 创建”，以开始预配工作区 。
9. 选择“转到资源”以打开新工作区。

    > [!NOTE]
    > 逻辑服务器中托管的所有专用 SQL 池（以前称为 SQL DW）实例均可通过新工作区获得。

## <a name="post-provisioning-steps"></a>预配后的步骤
必须完成以下步骤，以确保可以通过 Synapse Studio 访问现有的专用 SQL 池（以前称为 SQL DW）实例。
1. 在“Synapse 工作区概述”页中，选择“连接的服务器”。 选择“连接的服务器”后将转到连接的托管数据仓库的 SQL 逻辑服务器。 在基本菜单中，选择“连接的服务器”。
2. 打开“防火墙和虚拟网络”，并确保客户端 IP 或预定的 IP 范围有权访问逻辑服务器。
3. 打开“Active Directory 管理员”，并确保已在逻辑服务器中设置了 AAD 管理员。
4. 选择逻辑服务器中托管的其中一个专用 SQL 池（以前称为 SQL DW）实例。 在概述页中，选择“启动 Synapse Studio”，或转到[登录 Synapse Studio](https://web.azuresynapse.net) 并登录到工作区。

5. 打开“数据中心”，然后在对象资源管理器中展开专用的 SQL 池，确保可以访问和查询数据仓库。

    > [!NOTE] 
    > 可以随时删除连接的工作区。 删除工作区不会删除连接的专用 SQL 池（以前称为 SQL DW）。 删除操作完成后，可以在专用 SQL 池（以前称为 SQL DW）中重新启用工作区功能。

## <a name="next-steps"></a>后续步骤
[Synapse 工作区和 Studio](../get-started.md) 入门。
