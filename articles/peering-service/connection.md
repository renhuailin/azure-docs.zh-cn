---
title: Azure 对等互连服务连接
description: 了解 Microsoft Azure 对等互连服务连接
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0540fa9c7a29456aaf343adce83509b2d0e390e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91398914"
---
# <a name="peering-service-connection"></a>对等互连服务连接

连接通常是指标识对等互连服务的逻辑信息集。 它通过指定以下属性进行定义：

- 逻辑名称
- 连接合作伙伴
- 客户的物理位置
- IP 前缀

客户可根据需要建立一个或多个连接。 连接也用作遥测收集的单元。 例如，若要选择启用遥测警报，客户必须定义要监视的连接。

> [!Note]
> 当你注册对等互连服务时，我们会分析你的 Windows 和 Microsoft 365 遥测，以便为你提供所选前缀的延迟度量。
>有关连接遥测的详细信息，请参阅[对等互连服务连接遥测](connection-telemetry.md)。
>

>## <a name="how-to-register-a-connection"></a>如何注册连接？

方案 - 假设分支机构分布在不同的地理位置，如下图所示。 在这里，客户需要提供与单个连接关联的逻辑名称、服务提供商 (SP) 名称、客户的物理位置和 IP 前缀（由客户拥有或由服务提供商分配）。 必须重复此过程才能为单独的异地冗余连接注册对等互连服务。

![异地冗余连接](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> 当连接的地理位置位于美国时，将考虑对客户的物理位置进行州级筛选。
>

## <a name="next-steps"></a>后续步骤

若要了解注册对等互连服务连接的分步过程，请参阅[使用 Azure 门户注册对等互连服务](azure-portal.md)。

若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。

若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
