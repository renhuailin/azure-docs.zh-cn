---
title: Azure 复原能力术语
description: 了解术语
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 5b095f445cc024fd0e643785a231e9ddf3a58a6a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620810"
---
# <a name="terminology"></a>术语

为更好地了解 Azure 中的区域和可用性区域，了解关键术语或概念很有帮助。

| 术语或概念 | 说明 |
| --- | --- |
| region | 在定义了延迟的外围中部署的一系列数据中心，通过专用的区域性低延迟网络互相连接。 |
| geography | 世界上至少包含一个 Azure 区域的地区。 地域定义一个独立的市场，具有数据驻留和符合性边界。 地域允许具有特定数据驻留和符合性要求的客户保持他们的数据和应用程序相邻近。 地域具有容错能力，通过与专用的高容量网络基础设施相连，可承受整个区域的故障。 |
| 可用性区域 | 某个区域内的唯一物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 |
| 建议的区域 | 提供最广泛服务功能的区域，旨在为可用性区域现在或将来使用提供支持。 这些区域在 Azure 门户中被指定为“建议”。 |
| 备用（其他）区域 | 在数据驻留边界内（建议区域也位于其中）扩展 Azure 占用空间的区域。 备用区域有助于优化延迟，并提供辅助区域来应对灾难恢复需求。 它们并非用来支持可用性区域，但 Azure 会定期评估这些区域，以确定它们是否应该成为建议区域。 这些区域在 Azure 门户中被指定为“其他”。 |
| 跨区域复制（以前称为配对区域） | 一种可靠性策略和实现，将可用性区域的高可用性与区域范围事件的保护机制相结合，以满足灾难恢复和业务连续性需求。 |
| 基础服务 | 在区域正式发布后，所有区域均可用的核心 Azure 服务。 |
| 主要服务 | 在区域正式发布后 90 天内，所有建议区域均可用的 Azure 服务，或在备用区域根据需求进行发布的 Azure 服务。 |
| 战略服务 | 在各区域根据需求发布的 Azure 服务，由自定义/专用硬件提供支持。 |
| 区域服务 | 在区域内部署的 Azure 服务，允许客户指定要将该服务部署到的区域。 如需完整列表，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=all)。 |
| 非区域服务 | 不依赖于特定 Azure 区域的 Azure 服务。 非区域服务部署到两个或更多区域。 如果出现区域故障，另一个区域中的服务实例将继续为客户提供服务。 如需完整列表，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=all)。 |
| 区域性服务 | 一种支持可用性区域的 Azure 服务，它允许将资源部署到特定的、自选的可用性区域，以满足更严格的延迟或性能要求。 |
| 区域冗余服务 | 一种支持可用性区域的 Azure 服务，它允许自动跨区域复制或分发资源。 |
| 始终可用的服务 | 一种支持可用性区域的 Azure 服务，它使资源在所有 Azure 地域始终可用并且能够从区域范围的中断中复原。 |
