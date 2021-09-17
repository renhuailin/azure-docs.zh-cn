---
title: Azure Synapse 连接设置
description: 本文介绍如何在 Azure Synapse Analytics 中配置连接设置
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/05/2021
author: ashinMSFT
ms.author: seshin
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: 58086bcbe321b7d50d6f5d0e41a9723dfbe512ed
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861618"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Azure Synapse Analytics 连接设置

本文将介绍 Azure Synapse Analytics 中的连接设置以及如何根据情况对其进行配置。

## <a name="public-network-access"></a>公用网络访问 

可以使用公用网络访问功能允许与 Azure Synapse 工作区建立传入公用网络连接。 

- 公用网络访问处于禁用状态时，只能使用[专用终结点](synapse-workspace-managed-private-endpoints.md)连接到工作区。 
- 公用网络访问处于启用状态时，还可以从公用网络连接到工作区。 可以在创建工作区期间和之后管理此功能。 

> [!IMPORTANT]
> 此功能仅适用于与 [Azure Synapse Analytics 托管虚拟网络](synapse-workspace-managed-vnet.md)关联的 Azure Synapse 工作区。 但是，仍然可以向公用网络打开 Synapse 工作区，而不考虑其与托管 VNet 的关联。 

选择“禁用”选项不会应用可能配置的任何防火墙规则。 此外，防火墙规则会在 Synapse 门户的网络设置中灰显。 再次启用公用网络访问时，会重新应用防火墙配置。 

> [!TIP]
> 当还原为启用状态时，请在编辑防火墙规则前等待一段时间。

### <a name="configure-public-network-access-when-you-create-your-workspace"></a>创建工作区时配置公用网络访问

1.    在 [Azure 门户](https://aka.ms/azureportal)中创建工作区时，请选择“网络”选项卡。
2.    在“托管虚拟网络”下，选择“启用”以将工作区与托管虚拟网络关联，并允许进行公用网络访问。 

       :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png" alt-text="创建 Synapse 工作区，网络选项卡，托管虚拟网络设置" lightbox="media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png":::

3. 在“公用网络访问”下，选择“禁用”以拒绝对工作区的公用访问 。 如果要允许对工作区进行公用访问，则选择“启用”。

   :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-public-network-access-2.png" alt-text="创建 Synapse 工作区，网络选项卡，公用网络访问设置" lightbox="media/connectivity-settings/create-synapse-workspace-public-network-access-2.png"::: 

4.    完成工作区创建流程的其余部分。

### <a name="configure-public-network-access-after-you-create-your-workspace"></a>创建工作区之后配置公用网络访问

1.    在 [Azure 门户](https://aka.ms/azureportal)中选择 Synapse 工作区。
2.    从左侧导航栏中选择“网络”。
3.    选择“禁用”以拒绝对工作区的公用访问。 如果要允许对工作区进行公用访问，则选择“已启用”。

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png" alt-text="在现有 Synapse 工作区中，网络选项卡，公用网络访问设置为启用状态" lightbox="media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png"::: 

4.    处于禁用状态时，“防火墙规则”会灰显以指示防火墙规则未生效。 防火墙规则配置会保留。 

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png" alt-text="在现有 Synapse 工作区中，网络选项卡，公用网络访问设置为禁用状态，注意防火墙规则" lightbox="media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png"::: 
 
5.    选择“保存”以保存更改。 通知会确认已成功保存网络设置。

## <a name="connection-policy"></a>连接策略
Azure Synapse Analytics 中 Synapse SQL 的连接策略设置为“Default”。 无法在 Azure Synapse Analytics 中更改此项。 若要详细了解如何影响 Azure Synapse Analytics 中 Synapse SQL 的连接，请参阅[此文](../../azure-sql/database/connectivity-architecture.md#connection-policy)。 

## <a name="minimal-tls-version"></a>最低 TLS 版本
Azure Synapse Analytics 中的 Synapse SQL 允许使用所有 TLS 版本进行连接。 无法在 Azure Synapse Analytics 中设置 Synapse SQL 的最低 TLS 版本。

## <a name="next-steps"></a>后续步骤

 - 创建 [Azure Synapse 工作区](./synapse-workspace-ip-firewall.md)。