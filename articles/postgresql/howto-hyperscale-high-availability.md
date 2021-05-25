---
title: 配置高可用性 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 如何启用或禁用高可用性
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "90907398"
---
# <a name="configure-hyperscale-citus-high-availability"></a>配置超大规模 (Citus) 高可用性

Azure Database for PostgreSQL - 超大规模 (Citus) 可提供高可用性 (HA)，从而避免数据库停机。 启用高可用性时，服务器组中的每个节点都会获得一个备用节点。 如果原始节点无法正常运行，它的备用节点将获得提升并替代它。

> [!IMPORTANT]
> 由于高可用性将组中服务器的数量增加了一倍，因此它也会将成本增加一倍。

可在创建服务器组时启用高可用性，也可在创建服务器组后从 Azure 门户中相应服务器组的“计算 + 存储”选项卡启用它。 这两种情况下用户界面的外观相似。 将“高可用性”的滑块从“否”拖到“是”：

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="高可用性滑块":::

单击“保存”按钮，应用所选设置。 启用高可用性可能需要一些时间，因为服务器组需要预配备用节点并将数据流式传输给它们。

服务器组的“概览”选项卡将列出所有节点及其备用节点，同时还会显示一个“高可用性”列来指示是否为每个节点成功启用了高可用性 。

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="服务器组“概览”中的“高可用性”列":::

### <a name="next-steps"></a>后续步骤

深入了解[高可用性](concepts-hyperscale-high-availability.md)。
