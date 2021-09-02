---
title: 为 Azure 市场上的 IoT Edge 模块产品/服务创建和编辑计划
description: 为 Azure 市场上的 IoT Edge 模块产品/服务创建和编辑计划。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 07/08/2021
ms.openlocfilehash: 238be0168be689b9bfa179a4e13e08c2c427b8a2
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598056"
---
# <a name="create-and-edit-plans-for-an-iot-edge-module-offer"></a>为 IoT Edge 模块产品/服务创建和编辑计划

此概述页面可让你在相同产品/服务中创建不同的计划选项。 计划（以前称为 SKU）在可获取的位置（Azure 全球或 Azure 政府）和计划引用的映像方面可能有所不同。 产品/服务必须包含至少一个计划。

最多可为每个产品/服务创建 100 个计划：其中专用计划最多 45 个。 详细了解[Microsoft 商业市场中的专用产品/服务](private-offers.md)中的专用计划。

创建计划后，“计划概述”页面将显示：

- 计划名称
- 定价模型
- Azure 区域（全球或政府）
- 当前发布状态
- 任何可用操作

计划中的可用操作因计划的当前状态而异。 它们包括：

- “删除草稿”- 如果计划状态为“草稿”。
- “停止销售计划”- 如果计划状态为“已发布上线”。

## <a name="edit-a-plan"></a>编辑计划

选择计划名称以编辑其详细信息。

## <a name="create-a-plan"></a>创建计划

若要设置新计划，请选择“+ 新建计划”。

为每个计划输入唯一的“计划 ID”。 客户可以在产品的 Web 地址中看到此 ID。 仅可使用小写字母和数字、破折号或下划线字符，最多包含 50 个字符。 选择“创建”后，“计划 ID”将无法修改。

输入“计划名称”。 客户在确定要在产品/服务中选择哪个计划时会看到此名称。 此产品/服务中的每个计划都必须有唯一名称。 例如，可以将 Windows Server 的产品/服务名称与 Windows Server 2016 和 Windows Server 2019 计划一起使用。

选择下方的“创建”并继续。

## <a name="next-steps"></a>后续步骤

- [+ 新建计划](iot-edge-plan-setup.md)或者
- 退出计划设置，然后继续执行（可选）[与 Microsoft 联合销售](./co-sell-overview.md)或者
- [查看并发布产品/服务](review-publish-offer.md)