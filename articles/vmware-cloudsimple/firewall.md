---
title: Azure VMware Solution by CloudSimple - 设置防火墙表和规则
description: 介绍如何设置私有云防火墙表和规则，以限制子网和 VLAN 上的流量。
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2b1b8b9f5388dc2cf6790a94c450ab79d1862197
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612800"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>为私有云设置防火墙表和规则

防火墙表和关联的规则允许你指定要应用于特定子网和 VLAN 的流量限制。

* 一个子网可与一个防火墙表相关联。
* 一个防火墙表可与多个子网相关联。

## <a name="add-a-new-firewall-table"></a>添加新的防火墙表

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)，并在侧边菜单中选择“网络”。
2. 选择“防火墙表”。
3. 选择“创建防火墙表”。

    ![VLAN/子网页](media/firewall-tables-page.png)

4. 输入表的名称。
5. 将列出该表的默认规则。 单击“新建规则”以创建其他规则。 有关详细信息，请参阅以下过程。
6. 单击“完成”保存防火墙表。

> [!IMPORTANT]
> 每个私有云最多可以创建两个防火墙表。

## <a name="firewall-rules"></a>防火墙规则

防火墙规则确定防火墙如何处理特定类型的流量。 选定防火墙表的“规则”选项卡列出了所有关联的规则。

![防火墙规则表](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>创建防火墙规则

1. 通过以下方式之一显示用于创建防火墙规则的设置：
    * 创建防火墙表时，单击“添加规则”。
    * 在“网络 > 防火墙表”页上选择特定的防火墙表，然后单击“新建防火墙规则”。
2. 设置规则，如下所示：
    * **Name**。 为规则指定名称。
    * **优先级**。 向规则分配优先级。 首先执行数字较小的规则。
    * 流量类型。 选择规则是针对私有云、Internet 或 VPN 流量（无状态）还是针对公共 IP 地址（有状态）。
    * **协议**。 选择规则涵盖的协议（TCP、UDP 或任何协议）。
    * 方向。 选择规则是用于入站流量还是出站流量。 必须为入站和出站流量定义单独的规则。
    * **操作**。 选择规则匹配（允许或拒绝）时要采取的措施。
    * **源**。 指定规则覆盖的源（CIDR 块、内部或任何源）。
    * 源端口范围。 指定受规则限制的端口范围。
    * 方向。 选择入站或出站。
    * 目标。 指定规则覆盖的目标（CIDR 块、内部或任何源）。
    * 源端口范围。 指定受规则限制的端口范围。

    ![防火墙表添加规则](media/firewall-rule-create.png)

3. 单击“完成”保存规则并将其添加到防火墙表的规则列表。

> [!IMPORTANT]
> 每个防火墙表最多可以有 10 个入站规则和 20 个出站规则。 如需提高限额，可[联系支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>附加 VLAN/子网

定义防火墙表后，可以指定受表中规则制约的子网。

1. 在“网络” > “防火墙表”页上，选择一个防火墙表。
2. 打开“附加的 VLAN/子网”选项卡。
3. 单击“附加到 VLAN/子网”。
4. 选择私有云和 VLAN。 将显示关联的子网名称和 CIDR 块。
5. 单击“提交”  。
