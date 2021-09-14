---
title: 访问安全的 Azure Purview 帐户
description: 了解如何通过 Synapse 中的专用终结点访问受防火墙保护的 Azure Purview 帐户
author: linda33wj
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: how-to
ms.date: 09/02/2021
ms.author: jingwang
ms.reviewer: jrasnick
ms.openlocfilehash: e147ee8cd6483ab32fee0fe393b104669370ab5c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439832"
---
# <a name="access-a-secured-azure-purview-account-from-azure-synapse-analytics"></a>从 Azure Synapse Analytics 访问受保护的 Azure Purview 帐户

本文介绍如何针对不同的集成方案，从 Azure Synapse Analytics 访问受保护的 Azure Purview 帐户。

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Azure Purview 专用终结点部署方案

可为 Azure Purview 帐户使用 [Azure 专用终结点](../../private-link/private-endpoint-overview.md)，以允许通过专用链接从虚拟网络 (VNet) 安全访问目录。 Purview 为各种访问需求提供不同类型的专用点：帐户专用终结点、门户专用终结点和引入专用终结点  。 有关详细信息，请参阅 [Purview 专用终结点概念概述](../../purview/catalog-private-link.md#conceptual-overview)。 

如果你的 Purview 帐户受防火墙保护并拒绝公共访问，请确保按照以下清单设置专用终结点，以便 Synapse 可以成功连接到 Purview。 

| 方案                                                     | 所需的 Purview 专用终结点                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [运行管道并向 Purview 报告世系](../../purview/how-to-lineage-azure-synapse-analytics.md) | 要使 Synapse 管道将世系推送到 Purview，需要 Purview 帐户和引入专用终结点。 <br>- 使用 Azure Integration Runtime 时，请按照 [Purview 的托管专用终结点](#managed-private-endpoints-for-purview)部分中的步骤，在 Synapse 托管虚拟网络中创建托管专用终结点。<br>- 使用自承载集成运行时时，请按照[本部分](../../purview/catalog-private-link-end-to-end.md#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts)中的步骤，在集成运行时的虚拟网络中创建帐户和引入专用终结点 。 |
| [在 Synapse Studio 上使用 Purview 发现和探索数据](how-to-discover-connect-analyze-azure-purview.md) | 若要使用 Synapse Studio 顶部中心的搜索栏搜索 Purview 数据并执行操作，需要在启动 Synapse Studio 的虚拟网络中创建 Purview 帐户和门户专用终结点。 请按照[启用帐户和门户专用终结点](../../purview/catalog-private-link-account-portal.md#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts)中的步骤操作。 |

## <a name="managed-private-endpoints-for-purview"></a>Purview 的托管专用终结点

[托管专用终结点](../security/synapse-workspace-managed-private-endpoints.md)是在与 Azure Synapse 工作区关联的托管虚拟网络中创建的专用终结点。 运行管道并向受防火墙保护的 Azure Purview 帐户报告世系时，请确保在启用“托管虚拟网络”选项的情况下创建 Synapse 工作区，然后创建 Purview 帐户和引入托管专用终结点，如下所示。

### <a name="create-managed-private-endpoints"></a>创建托管专用终结点

在 Synapse Studio 上为 Purview 创建托管专用终结点：

1. 转到“管理” -> “Azure Purview”，然后单击“编辑”以编辑现有已连接的 Purview 帐户，或单击“连接到 Purview 帐户”连接到新的 Purview 帐户   。

2. 为“创建托管专用终结点”选择“是” 。 你需要具有“workspaces/managedPrivateEndpoint/write”权限，例如 Synapse 管理员或 Synapse 链接数据管理员角色。

3. 单击“+ 全部创建”按钮批量创建所需的 Purview 专用终结点，包括 Purview 托管资源（Blob 存储、队列存储和事件中心命名空间）的帐户专用终结点和引入专用终结点。你的 Purview 帐户至少需要具有读取者角色，Synapse 才能检索 Purview 托管资源的信息 。

   :::image type="content" source="./media/purview-create-all-managed-private-endpoints.png" alt-text="为连接的 Purview 帐户创建托管专用终结点。":::

4. 在下一页中，指定专用终结点的名称。 它将用于生成包含后缀的引入专用终结点的名称。

   :::image type="content" source="./media/name-purview-private-endpoints.png" alt-text="为连接的 Purview 帐户的托管专用终结点命名。":::

5. 单击“创建”以创建专用终结点。 创建后，会生成 4 个专用终结点请求，这些请求必须[获得 Purview 所有者的批准](#approve-private-endpoint-connections)。

此类批处理托管专用终结点创建仅在 Synapse Studio 上提供。 若要以编程方式创建托管专用终结点，需要单独创建这些 PE。 可以从 Azure 门户 -> 你的 Purview 帐户 ->“托管资源”中找到 Purview 托管资源的信息。

### <a name="approve-private-endpoint-connections"></a>批准专用终结点连接

为 Purview 创建托管专用终结点后，你首先会看到“挂起”状态。 Purview 所有者需要批准每个资源的专用终结点连接。

如果有权从 Synapse Studio 批准 Purview 专用终结点连接，请执行以下操作： 

1. 转到“管理” -> “Azure Purview” -> “编辑”  
2. 在专用终结点列表中，单击每个专用终结点名称旁边的“编辑”（铅笔）按钮
3. 单击“在 Azure 门户中管理批准”，转到相关资源。
4. 在给定的资源上，转到“网络” -> “专用终结点连接”以批准专用终结点连接 。 专用终结点命名为 `data_factory_name.your_defined_private_endpoint_name`，其描述为“由 data_factory_name 请求”。
5. 对所有专用终结点重复此操作。

如果无权批准 Purview 专用终结点连接，请让 Purview 帐户所有者执行以下操作。

- 对于帐户专用终结点，请转到 Azure 门户 -> 你的 Purview 帐户 ->“网络”->“专用终结点连接”，以进行批准。
- 对于引入专用终结点，请转到 Azure 门户 -> 你的 Purview 帐户 ->“托管资源”，分别单击进入“存储帐户”和“事件中心命名空间”，并在“网络”->“专用终结点连接”页面中批准专用终结点连接。

### <a name="monitor-managed-private-endpoints"></a>监视托管专用终结点

可以在两处监视为 Purview 创建的托管专用终结点：

- 转到“管理” -> “Azure Purview” -> “编辑”以打开现有已连接的 Purview 帐户  。 若要查看所有相关的专用终结点，你的 Purview 帐户至少需要具有读取者角色，Synapse 才能检索 Purview 托管资源的信息。 否则，你只会看到带有警告的帐户专用终结点。
- 转到“管理” -> “托管专用终结点”，其中会显示在 Synapse 工作区下创建的所有托管专用终结点 。 如果你的 Purview 帐户至少具有读取者角色，则会看到 Purview 相关的专用终结点分组在一起。 否则，它们会单独显示在列表中。

## <a name="nextsteps"></a>后续步骤 

- [将 Synapse 工作区连接到 Azure Purview](quickstart-connect-azure-purview.md)
- [来自 Azure Synapse Analytics 的元数据和世系](../../purview/how-to-lineage-azure-synapse-analytics.md)
- [使用 Azure Purview 发现、连接和浏览 Synapse 中的数据](how-to-discover-connect-analyze-azure-purview.md)