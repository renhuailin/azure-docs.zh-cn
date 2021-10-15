---
title: SQL Server HADR 和范围级别 Azure 混合权益共存
description: 本文介绍 SQL Server HADR 软件保障权益和范围级别 Azure 混合权益如何共存。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 97c338134430156fefa5d874d44e27c8dc34a496
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547294"
---
# <a name="sql-server-hadr-and-scope-level-azure-hybrid-benefit-coexistence"></a>SQL Server HADR 和范围级别 Azure 混合权益共存

软件保障 (SA) 的好处之一是，它使 Azure 客户能够安装和运行被动 SQL Server 实例，以在发生故障转移事件时进行灾难恢复。 范围级别的 Azure 混合权益管理通过确保限定的灾难恢复副本不会使用分配的 SQL Server 许可证，来支持 SQL Server HADR 权益。 因此，无需单独管理副本。 

## <a name="hadr-benefit-selection"></a>HADR 权益选择

若要使用 HADR 权益，请使用“SQL 虚拟机”资源的“配置”窗格选择“HADR”  ，如下图所示。

:::image type="content" source="./media/sql-server-hadr-licenses/select-hadr-benefit.png" alt-text="显示 SQL 虚拟机配置中的 HADR 选项的屏幕截图。" lightbox="./media/sql-server-hadr-licenses/select-hadr-benefit.png" :::

如果选择了 HADR 属性，则集中分配给范围的许可证不会用于对该资源的 SQL 软件成本进行折扣。 相反，即用即付计量会自动切换为 $0 HADR 计量。 此方法可确保分配的 SQL 许可证仅用于需要许可的资源。 无需扩充 SQL 许可证分配以说明 HADR 权益。 下图演示了此概念。

下图所示的价格仅用作示例。

:::image type="content" source="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg" alt-text="显示完全折扣 vCore 使用量的关系图，其中选择了 HADR。" border="false" lightbox="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg":::

> [!NOTE]
> HADR 选项反映了此 SQL Server 实例在 AlwaysOn 可用性组中的特定角色。 选择它由服务所有者或 DBA 负责，且至少需要一个 [SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)角色。 此任务与范围级许可证分配无关。

## <a name="next-steps"></a>后续步骤

- 查看[范围级别 Azure 混合权益管理常见问题解答](faq-azure-hybrid-benefit-scope.yml)。
- 若要了解如何应用折扣，请参阅[什么是 Azure 混合权益的应用范围级别管理？](sql-server-hadr-licenses.md)
- 了解如何[从现有 Azure 混合权益体验进行过渡](transition-existing.md)。