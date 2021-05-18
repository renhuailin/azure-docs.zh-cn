---
title: 参考体系结构示意图 | Azure 市场
description: 如何在 Microsoft 商业市场中为产品/服务创建参考体系结构示意图。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604485"
---
# <a name="reference-architecture-diagram"></a>参考体系结构示意图

参考体系结构示意图是一个表示你的产品所依赖的基础结构的模型。 对于 Azure IP 解决方案，该图还应显示产品/服务如何按照 IP 联合销售的技术需求使用 Microsoft 的云服务。 它并非旨在评估体系结构的质量。 它旨在演示解决方案如何使用 Microsoft 服务。

可以通过多种工具创建参考体系结构示意图。 我们建议使用 Microsoft Visio，因为它具有描绘 Azure 体系结构模型的多个模具。

构建体系结构示意图的一个很有帮助的起点是利用 [Azure 体系结构模型](/azure/architecture/browse/)。

## <a name="typical-components-of-a-reference-architecture-diagram"></a>参考体系结构示意图的典型组件

- 托管和与产品/服务交互的云服务，包括使用 Azure 资源的服务
- 你的产品/服务使用的数据连接、数据层和数据服务
- 用于控制产品/服务的安全性、身份验证和用户的云服务
- 向用户公开产品/服务的用户界面和其他服务
- 混合或本地连接和集成，或两者的组合

此屏幕截图显示了一个参考体系结构示意图的示例。

[![此图是一个示例联合销售体系结构示意图。](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

此示例参考体系结构示意图适用于垂直行业聊天机器人，该机器人可以与 Intranet 站点集成，以通过机器学习算法帮助预测需求情况。 此解决方案使用来自不同 ERP 系统的供应链和制造计划数据。 此机器人旨在解决有关销售人员何时可以给出订单的预计交付日期的问题。

## <a name="next-steps"></a>后续步骤

- [配置商业市场产品/服务的联合销售](commercial-marketplace-co-sell.md)
