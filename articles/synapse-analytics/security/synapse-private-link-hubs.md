---
title: 使用专用链接连接到 Synapse Studio
description: 本文介绍如何使用专用链接连接到 Azure Synapse Studio
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 967d7462ebdbbeab9d0653914dc0640af453b7ee
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291882"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>使用 Azure 专用链接中心连接到 Azure Synapse Studio 

本文介绍如何使用 Azure Synapse Analytics 专用链接中心的专用链接安全地连接到 Synapse Studio。 

## <a name="azure-private-link-hubs"></a>Azure 专用链接中心 
可以使用专用链接从 Azure 虚拟网络安全地连接到 Azure Synapse Studio。 Azure Synapse Analytics 专用链接中心是充当安全网络和 Synapse Studio Web 体验之间的连接器的 Azure 资源。 

可以使用专用链接通过两个步骤连接到 Synapse Studio。 首先，必须创建专用链接中心资源。 其次，必须创建从 Azure 虚拟网络到此专用链接中心的专用终结点。 然后，可以使用专用终结点与 Synapse Studio 安全地进行通信。 必须将专用终结点与 DNS 解决方案（本地解决方案或 Azure 专用 DNS）集成。 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Azure 专用链接中心和 Azure Synapse Studio
可以使用单个 Azure Synapse 专用链接中心资源，通过 Azure Synapse Studio 以私密方式连接到所有 Azure Synapse Analytics 工作区。 工作区无需与 Azure Synapse 专用链接中心位于同一区域。 Azure Synapse 专用链接中心资源还可用于连接到不同订阅或 Azure AD 租户中的 Synapse 工作区。

可以通过在 Azure 门户中搜索“Synapse 专用链接中心”，并从“服务”中选择“Azure Synapse Analytics (专用链接中心)”，来创建专用链接中心。 有关详细信息，请参阅如何[从受限网络连接到工作区资源](./how-to-connect-to-workspace-from-restricted-network.md)这一指南中的步骤。

>[!NOTE]
>专用链接中心旨在通过专用链接安全地加载静态内容 Synapse Studio。 必须为要连接到的工作区中的资源创建单独的专用终结点，例如预配/专用的 SQL 池或 Spark 池。 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure 专用链接中心和 Azure 虚拟网络
必须将 Azure 虚拟网络连接到 Synapse 专用链接中心资源，才能保护与 Synapse Studio 建立的端到端连接。 为此，必须创建一个专用终结点，使其从虚拟网络连接到创建的专用链接中心。 可以使用专用链接中心的 Azure 门户，并转到“专用终结点”部分。 选择“+ 专用终结点”创建连接到专用链接中心的新专用终结点。

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="显示专用终结点连接页面的屏幕截图。":::

确保选中“资源”选项卡上的“Microsoft.Synapse/privateLinkHubs”资源类型。

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="显示“创建专用终结点”页的屏幕截图，其中突出显示了“资源类型”。":::

在“配置”选项卡上，在与虚拟网络和专用 DNS 区域集成时为专用 DNS 区域选择“privatelink.azuresynapse.net”。

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="创建连接到专用链接中心的专用终结点":::

## <a name="next-steps"></a>后续步骤

[从受限网络连接到工作区资源](./how-to-connect-to-workspace-from-restricted-network.md)

详细了解[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)

[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)

[使用专用终结点连接到 Synapse 工作区](./how-to-connect-to-workspace-with-private-links.md)

