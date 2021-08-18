---
title: Azure Synapse Analytics 工作区的数据外泄保护
description: 本文介绍 Azure Synapse Analytics 中的数据外泄保护
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 792197b3558a16706fee52204fa90a8ef8d0735a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232041"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 工作区的数据外泄保护
本文介绍 Azure Synapse Analytics 中的数据外泄保护

## <a name="securing-data-egress-from-synapse-workspaces"></a>防止数据从 Synapse 工作区流出
Azure Synapse Analytics 工作区支持对工作区启用数据外泄保护。 通过外泄保护，可以防止恶意的预览体验人员访问 Azure 资源并将敏感数据外泄到组织范围之外的位置。 创建工作区时，可以选择使用托管虚拟网络配置工作区，并针对数据外泄提供额外保护。 使用[托管虚拟网络](./synapse-workspace-managed-vnet.md)创建工作区时，数据集成和 Spark 资源将部署在托管虚拟网络中。 工作区的专用 SQL 池和无服务器 SQL 池具有多租户功能，因此需要存在于托管虚拟网络外部。 对于具有数据外泄保护的工作区，托管虚拟网络中的资源始终通过[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)进行通信，并且 Synapse SQL 资源只能连接到已获得授权的 Azure 资源（工作区中获得批准的托管专用终结点连接的目标）。 

> [!Note]
> 创建工作区后，无法更改托管虚拟网络和数据外泄保护功能的工作区配置。

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>管理到已批准目标的 Synapse 工作区数据流出
在创建启用了数据外泄保护功能的工作区时，工作区资源的所有者可以管理工作区的已批准 Azure AD 租户列表。 具有工作区的[适当权限](./synapse-workspace-access-control-overview.md)的用户可以使用 Synapse Studio 来创建对工作区的已批准 Azure AD 租户中的资源的托管专用终结点连接请求。 如果用户尝试创建到未获批准的租户中的资源的专用终结点连接，则将阻止创建托管专用终结点。

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>启用了数据外泄保护功能的示例工作区
让我们使用一个示例来说明 Synapse 工作区的数据外泄保护。 Contoso 的租户 A 和租户 B 有 Azure 资源，需要安全地连接这些资源。 已在租户 A 中创建 Synapse 工作区，并将租户 B 添加为获得批准的 Azure AD 租户。 此图显示租户 A 和租户 B 中已获得存储帐户所有者批准的 Azure 存储帐户的专用终结点连接。 此图还显示阻止创建专用终结点。 已阻止创建此专用终结点，因为它针对 Fabrikam Azure AD 租户中的 Azure 存储帐户，该帐户不是 Contoso 工作区的获得批准的 Azure AD 租户。

:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="此图显示了如何为 Synapse 工作区实现数据外泄保护":::

>[!IMPORTANT]
>
> 非工作区租户中的资源不能具有阻止防火墙规则，这样 SQL 池才能连接这些资源。 工作区的托管虚拟网络中的资源（例如 Spark 群集）可以通过托管的专用链接连接到受防火墙保护的资源。
> >

## <a name="next-steps"></a>后续步骤

了解如何[创建启用了数据外泄保护功能的工作区](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

详细了解[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)

[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)
