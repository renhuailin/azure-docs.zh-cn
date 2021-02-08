---
title: 配置 IP 防火墙规则
description: 一篇介绍如何在 Azure Synapse Analytics 中配置 IP 防火墙规则的文章
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b937dad6c3c8f5a5773ca7779493b41c905307b1
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226499"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Azure Synapse Analytics IP 防火墙规则

本文将介绍 IP 防火墙规则，并介绍如何在 Azure Synapse Analytics 中配置它们。

## <a name="ip-firewall-rules"></a>IP 防火墙规则

IP 防火墙规则基于每个请求的来源 IP 地址授予或拒绝对 Synapse 工作区的访问权限。 你可以为工作区配置 IP 防火墙规则。 在工作区级别配置的 IP 防火墙规则应用于工作区的所有公共终结点（专用 SQL 池、无服务器 SQL 池以及开发）。

## <a name="create-and-manage-ip-firewall-rules"></a>创建和管理 IP 防火墙规则

可通过两种方式向 Synapse 工作区添加 IP 防火墙规则。 若要向工作区添加 IP 防火墙，请在创建工作区期间选择“安全 + 网络”，并选中“允许来自所有 IP 地址的连接”。

![屏幕截图突出显示了“安全 + 网络”按钮。](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Azure 门户 Synapse 工作区 IP 配置。](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

还可以在创建工作区后向 Synapse 工作区添加 IP 防火墙规则。 在 Azure 门户中，从“安全性”下选择“防火墙”。 若要添加新的 IP 防火墙规则，请为其指定名称、起始 IP 和结束 IP。 完成后，选择“保存”。

![Azure 门户中的 Azure Synapse 工作区 IP 配置。](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>从你自己的网络连接到 Synapse

可以使用 Synapse Studio 连接到 Synapse 工作区。 还可以使用 SQL Server Management Studio (SSMS) 连接到工作区中的 SQL 资源（专用 SQL 池和无服务器 SQL 池）。

请确保你的网络和本地计算机上的防火墙允许 Synapse Studio 在 TCP 端口 80、443 和 1443 上的传出通信。

此外，还需要允许 Synapse Studio 在 UDP 端口 53 上的传出通信。 若要使用 SSMS 和 Power BI 等工具进行连接，必须允许 TCP 端口 1433 上的传出通信。

工作区的 SQL 连接策略设置为“默认”。 可在[此处](../../azure-sql/database/connectivity-architecture.md#connection-policy)详细了解客户端应允许与之进行出站通信的 IP 地址和端口。




## <a name="next-steps"></a>后续步骤

创建 [Azure Synapse 工作区](../quickstart-create-workspace.md)

创建包含[托管工作区虚拟网络的 Azure Synapse 工作区](./synapse-workspace-managed-vnet.md)