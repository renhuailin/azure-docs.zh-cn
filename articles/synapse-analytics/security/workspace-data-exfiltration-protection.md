---
title: Azure Synapse Analytics 工作区的数据渗透保护
description: 本文介绍 Azure Synapse Analytics 中的数据渗透保护
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 7af49b31aed3794d204b653f0ebfd66283c26cd4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501408"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 工作区的数据渗透保护
本文介绍 Azure Synapse Analytics 中的数据渗透保护

## <a name="securing-data-egress-from-synapse-workspaces"></a>保护从 Synapse 工作区传出的数据
Azure Synapse Analytics 工作区支持对工作区启用 data 渗透 protection。 借助渗透保护，可以防止恶意预览体验人员访问 Azure 资源，并将敏感数据窃取到组织范围之外的位置。 创建工作区时，可以选择使用托管虚拟网络配置工作区，并对数据渗透进行额外保护。 当使用 [托管虚拟网络](./synapse-workspace-managed-vnet.md)创建工作区时，会在托管的虚拟网络中部署数据集成和 Spark 资源。 工作区的专用 SQL 池和无服务器 SQL 池具有多租户功能，因此需要存在于托管虚拟网络之外。 对于具有数据渗透保护的工作区，托管虚拟网络中的资源始终通过 [托管的专用终结点](./synapse-workspace-managed-private-endpoints.md) 进行通信，Synapse SQL 资源只能连接到已获授权的 Azure 资源 (从工作区) 批准的托管专用终结点连接。 

>[!Note]
>创建工作区后，不能更改托管虚拟网络和数据渗透保护的工作区配置。

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>管理 Synapse 工作区数据出口到已批准目标
创建工作区并启用 data 渗透后，工作区资源的所有者可以管理工作区的已批准 Azure AD 租户的列表。 具有工作区 [适当权限](./access-control.md) 的用户可以使用 Synapse Studio 来创建对工作区批准 Azure AD 租户中的资源的托管专用终结点连接请求。 如果用户尝试创建与未批准的租户中的资源的专用终结点连接，则将阻止托管专用终结点创建。

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>已启用 data 渗透 protection 的示例工作区
让我们使用一个示例来说明 Synapse 工作区的数据渗透保护。 Contoso 在租户 A 和租户 B 中有 Azure 资源，需要这些资源来安全地连接。 已在租户 A 中创建 Synapse 工作区，并将租户 B 添加为批准的 Azure AD 租户。 此图显示了与租户 A 中的 Azure 存储帐户和存储帐户所有者批准的租户 B 之间的专用终结点连接。 该关系图还显示阻止的专用终结点创建。 已阻止创建此专用终结点，因为它针对 Fabrikam Azure AD 租户中的 Azure 存储帐户，该帐户不是 Contoso 的工作区的批准 Azure AD 租户。 
:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="此图显示了如何为 Synapse 工作区实现 data 渗透 protection":::

>[!IMPORTANT]
>除了工作区租户之外的租户中的资源不能有阻止防火墙规则来连接到它们。 工作区托管的虚拟网络中的资源（如 Spark 群集）可通过托管的专用链接连接到受防火墙保护的资源。
## <a name="next-steps"></a>后续步骤

了解如何 [创建启用了数据渗透保护的工作区](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

了解有关[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)的详细信息

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)

[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)
