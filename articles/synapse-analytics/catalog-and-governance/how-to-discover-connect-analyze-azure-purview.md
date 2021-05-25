---
title: 使用 Azure Purview 发现、连接和浏览 Synapse 中的数据
description: 有关如何发现、连接和浏览 Synapse 中的数据的指南
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a3ed7979584787627c97cfec3bb2e19c147f7152
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872650"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>使用 Azure Purview 发现、连接和浏览 Synapse 中的数据 

本文档介绍将 Azure Purview 帐户注册到 Synapse 时可执行的交互类型。 

## <a name="prerequisites"></a>必备知识 

- [Azure Purview 帐户](../../purview/create-catalog-portal.md) 
- [Synapse 工作区](../quickstart-create-workspace.md) 
- [将 Azure Purview 帐户连接到 Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>在 Synapse 中使用 Azure Purview 

若要在 Synapse 中使用 Azure Purview，你必须有权访问该 Purview 帐户。 Synapse 会传递 Purview 权限。 例如，如果你拥有策展人权限角色，你将能够编辑 Azure Purview 扫描的元数据。 

### <a name="data-discovery-search-datasets"></a>数据发现：搜索数据集 

若要发现 Azure Purview 注册和扫描的数据，可以使用 Synapse 工作区顶部中心的搜索栏。 请确保选择 Azure Purview 以搜索所有组织数据。 

[![搜索 Azure Purview 资产](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure Purview 操作 

Synapse 中提供的 Azure Purview 功能列表如下： 
- 元数据概述 
- 查看和编辑元数据的“架构”，其中包含分类、术语表术语、数据类型和说明 
- 查看“世系”，了解依赖关系并进行影响分析。 有关详细信息，请参见[世系](../../purview/catalog-lineage-user-guide.md)
- 查看和编辑“联系人”，了解数据集的所有者或专家 
- “相关内容”可用于了解特定数据集的分层依赖关系。 此体验有助于浏览数据层次结构。

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>可以使用 Synapse 资源对数据集执行的操作 

### <a name="connect-data-to-synapse"></a>将数据连接到 Synapse 

- 可以在 Synapse 中创建新的链接服务。 需要执行该操作才能将数据复制到 Synapse，或将数据复制到数据中心（对于受支持的数据源，如 ADLSg2） 
- 对于文件、文件夹或表等对象，可以直接创建新的集成数据集，并利用现有的链接服务（如果已创建） 

我们尚无法推断是否有现有的链接服务或集成数据集。 

###  <a name="develop-in-synapse"></a>在 Synapse 中进行开发 

可以执行三个操作：“新建 SQL 脚本”、“新建笔记本”和“新建数据流”。 

对于“新建 SQL 脚本”，根据支持类型，可以执行以下操作： 
- 查看前 100 行，以了解数据的形状。 
- 从 Synapse SQL 数据库创建外部表 
- 将数据加载到 Synapse SQL 数据库 
 
对于“新建笔记本”，可以执行以下操作： 
- 将数据加载到 Spark 数据帧 
- 创建 Spark 表（如果以 Parquet 格式创建此表，还会创建一个无服务器 SQL 池表）。 
 
对于“新建数据流”，可以创建可在数据流管道中用作源的集成数据集。 数据流是一项无代码开发人员功能，用于执行数据转换。 有关[在 Synapse 中使用数据流](../quickstart-data-flow.md)的详细信息。

##  <a name="nextsteps"></a>后续步骤 

- [在 Azure Purview 中注册并扫描 Azure Synapse 资产](../../purview/register-scan-azure-synapse-analytics.md)
- [如何在 Azure Purview 数据目录中搜索数据](../../purview/how-to-search-catalog.md)
