---
title: 管理区域冗余高可用性 - Azure 门户 - Azure Database for MySQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for MySQL 灵活服务器中启用或禁用区域冗余高可用性。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: d65b074385311e74444929ef74901e402e29ec03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241729"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>在 Azure Database for MySQL 灵活服务器（预览版）中管理区域冗余高可用性

本文介绍在灵活服务器中如何启用或禁用区域冗余高可用性配置。

高可用性功能预配在不同区域中物理分隔主副本和备用副本。 有关详细信息，请参阅[高可用性概念文档](./concepts/../concepts-high-availability.md)。 

> [!IMPORTANT]
> 你只能在创建灵活服务器期间启用区域冗余高可用性。

本页提供了如何启用或禁用高可用性的指南。 此操作不会更改其他设置，包括 VNET 配置、防火墙设置和备份保留。 同样，禁用高可用性是一个联机操作，并且不会影响应用程序的连接和操作。

> [!IMPORTANT]
> 区域冗余高可用性仅限在以下区域内可用：东南亚、美国西部 2、欧洲西部和美国东部。  

## <a name="enable-high-availability-during-server-creation"></a>在服务器创建过程中启用高可用性

本部分详细介绍了 HA 相关的字段。 创建灵活服务器时，可以按照以下步骤部署高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择“灵活服务器”，然后单击“创建”。  有关如何填写“订阅”、“资源组”、“服务器名称”、“区域”和其他字段等详细信息的详细信息，请参阅服务器创建的操作方法文档。

2.  在“可用性”选项中，单击“区域冗余高可用性”复选框。

3.  如果要更改默认计算和存储，请单击“配置服务器”。

4.  如果选中“高可用性”选项，将无法选择可突发层。 你可以选择“常规用途”或“内存优化”计算层。

    > [!IMPORTANT]
    > 对于“常规用途”和“内存优化”定价层，我们仅支持区域冗余高可用性。

5.  从下拉列表中选择所选的计算大小。

6.  使用滑动条选择“存储大小”(单位为 GiB)，然后在 7 天和 35 天之间选择“备份保持期”。   

## <a name="disable-high-availability"></a>禁用高可用性

请按照以下步骤为配置有区域冗余的灵活服务器禁用高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MySQL 灵活服务器。

2.  在“灵活服务器”页面上，单击前面板中的“高可用性”以打开“高可用性”页面。

3.  单击“区域冗余高可用性”复选框以禁用该选项，然后单击“保存”以保存更改。

4.  此时将显示一个确认对话框，你可以在其中确认禁用 HA。

5.  单击“禁用 HA”按钮，以禁用高可用性。

6.  此时将显示一条通知，指出正在解除高可用性部署。

## <a name="next-steps"></a>后续步骤

-   了解[业务连续性](./concepts-business-continuity.md)
-   了解[区域冗余高可用性](./concepts-high-availability.md)
