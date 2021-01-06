---
title: 使用 Azure 监控范围发现、连接和浏览 Synapse 中的数据
description: 有关如何发现数据、连接数据以及在 Synapse 中浏览数据的指南
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7c6b25fd3615fa76bc76e6d360f4c76a21a9ad02
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918103"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>使用 Azure 监控范围发现、连接和浏览 Synapse 中的数据 

> [!IMPORTANT]
> Azure Synapse Analytics 和 Azure 监控范围之间的集成目前处于预览阶段。 如果你有兴趣在 Synapse 中试用 Azure 监控范围，请与你的 Microsoft 销售代表联系。 

在本文档中，你将了解在将 Azure 监控范围帐户注册到 Synapse 时可以执行的交互类型。 

## <a name="prerequisites"></a>先决条件 

- [Azure 监控范围帐户](../../purview/create-catalog-portal.md) 
- [Synapse 工作区](../quickstart-create-workspace.md) 
- [将 Azure 监控范围帐户连接到 Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>在 Synapse 中使用 Azure 监控范围 

在 Synapse 中使用 Azure 监控范围要求你有权访问该监控范围帐户。 Synapse 通过监控范围权限。 例如，如果你拥有陈列权限角色，你将能够编辑 Azure 监控范围扫描的元数据。 

### <a name="data-discovery-search-datasets"></a>数据发现：搜索数据集 

若要发现 Azure 监控范围注册和扫描的数据，可以使用 Synapse 工作区顶部中心的搜索栏。 请确保选择 "Azure 监控范围" 以搜索所有组织数据。 

## <a name="azure-purview-actions"></a>Azure 监控范围操作 

下面是 Synapse 中提供的 Azure 监控范围功能的列表： 
- 元数据 **概述** 
- 查看和编辑包含分类、术语表术语、数据类型和说明的元数据 **架构** 
- 查看 **沿袭** 以了解依赖关系和执行影响分析。 有关的详细信息，请参阅 [沿袭](../../purview/catalog-lineage-user-guide.md)
- 查看和编辑 **联系人** ，以了解谁是数据集的所有者或专家 
- **与** 了解特定数据集的层次结构依赖关系相关。 此体验可帮助浏览数据层次结构。

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>可对具有 Synapse 资源的数据集执行的操作 

### <a name="connect-data-to-synapse"></a>将数据连接到 Synapse 

- 你可以创建 **新的链接服务** 到 Synapse。 需要执行该操作，将数据复制到 Synapse，或将数据复制到数据中心 (支持的数据源（例如 ADLSg2）)  
- 对于文件、文件夹或表等对象，可以直接创建 **新的集成数据集** 并利用现有的链接服务（如果已创建） 

我们尚无法推断是否存在现有的链接服务或集成数据集。 

###  <a name="develop-in-synapse"></a>在 Synapse 中进行开发 

可以执行三个操作： **新的 SQL 脚本**、 **新笔记本** 和 **新** 数据流。 

利用 **新的 SQL 脚本**，根据支持类型，可以： 
- 查看前100行以了解数据的形状。 
- 从 Synapse SQL 数据库创建外部表 
- 将数据加载到 Synapse SQL 数据库 
 
对于 **新笔记本**，可以： 
- 将数据加载到 Spark 数据帧 
- 创建 Spark 表 (如果通过 Parquet 格式执行此操作，它还会创建一个无服务器 SQL 池表) 。 
 
使用 **新** 的数据流，可以创建可在数据流管道中使用源的集成数据集。 数据流是一项无代码开发人员功能，用于执行数据转换。 有关 [在 Synapse 中使用](../quickstart-data-flow.md)数据流的详细信息。

##  <a name="nextsteps"></a>后续步骤 

- [注册并扫描 Azure 监控范围中的 Azure Synapse 资产](../../purview/register-scan-azure-synapse-analytics.md)
- [如何在 Azure 监控范围数据目录中搜索数据](../../purview/how-to-search-catalog.md)