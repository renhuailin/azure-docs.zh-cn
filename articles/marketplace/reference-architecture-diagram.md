---
title: 参考体系结构关系图 |Azure Marketplace
description: 如何在 Microsoft 商业应用商店中为产品/服务创建参考体系结构图表。
author: mingshen-ms
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 0331bdd8f928b52e56a709fdadbc95892d437cf4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744371"
---
# <a name="reference-architecture-diagram"></a>参考体系结构示意图

参考体系结构关系图是一个表示产品/服务所依赖的基础结构的模型。 对于 Azure IP 解决方案，该图还应显示你的产品/服务如何根据 IP 共同销售的技术要求使用 Microsoft 的云服务。 它不用于评估体系结构的质量。 它旨在演示解决方案如何使用 Microsoft 服务。

可以通过多种工具创建参考体系结构关系图。 建议 Microsoft Visio，因为它有多个描述 Azure 体系结构模型的模具。

构建参考体系结构关系图的一个有用起点是利用 [Azure 体系结构模型](/azure/architecture/browse/)。

## <a name="typical-components-of-a-reference-architecture-diagram"></a>参考体系结构示意图的典型组件

- 托管和与产品/服务交互的云服务，包括使用 Azure 资源的服务
- 你的产品/服务使用的数据连接、数据层和数据服务
- 用于控制产品/服务的安全性、身份验证和用户的云服务
- 向用户提供产品/服务的用户界面和其他服务
- 混合或本地连接和集成，或两者的组合

此屏幕截图显示了参考体系结构关系图的示例。

[![此图是一个示例共同销售体系结构关系图。](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

此示例参考体系结构示意图适用于可与 intranet 站点集成的垂直行业聊天机器人，可通过机器学习算法帮助预测需求方案。 此解决方案使用来自不同 ERP 系统的供应链和制造计划数据。 机器人旨在解决有关销售人员可以在订单的可能交付日期上提交的问题。

## <a name="next-steps"></a>后续步骤

- [配置商业 marketplace 产品/服务的共同销售](commercial-marketplace-co-sell.md)
