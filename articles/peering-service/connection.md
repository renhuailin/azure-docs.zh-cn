---
title: Azure 对等互连服务连接
description: 了解 Microsoft Azure 对等互连服务连接
services: peering-service
author: gthareja
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 04/07/2021
ms.author: gatharej
ms.openlocfilehash: 9463a0967b9cfc25f189dd3efac80e26428866fa
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202652"
---
# <a name="peering-service-connection"></a>对等互连服务连接

连接通常是指标识对等互连服务的逻辑信息集。 它通过指定以下属性进行定义：

- 逻辑名称
- 连接合作伙伴
- 连接合伙伙伴主要服务位置
- 连接合作伙伴备份服务位置
- 客户的物理位置
- IP 前缀

客户可根据需要建立一个或多个连接。 连接也用作遥测收集的单元。 例如，若要选择启用遥测警报，客户必须定义要监视的连接。

> [!Note]
> 当你注册对等互连服务时，我们会分析你的 Windows 和 Microsoft 365 遥测，以便为你提供所选前缀的延迟度量。目前，仅支持 /24 或更大前缀的遥测数据。
>有关连接遥测的详细信息，请参阅[对等互连服务连接遥测](connection-telemetry.md)。
>

## <a name="how-to-create-a-peering-service-connection"></a>如何创建对等互连服务连接？

方案 - 假设分支机构分布在不同的地理位置，如下图所示。 在这里，客户需要提供与单个连接关联的逻辑名称、服务提供商 (SP) 名称、客户的物理位置和 IP 前缀（由客户拥有或由服务提供商分配）。  合作伙伴的主要和备份服务位置有助于为客户确定首选服务位置。 必须重复执行此过程，才能为其他位置创建对等互连服务。

![异地冗余连接](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> 当连接的地理位置位于美国时，将考虑对客户的物理位置进行州级筛选。
>

## <a name="next-steps"></a>后续步骤

若要了解注册对等互连服务连接的分步过程，请参阅[使用 Azure 门户创建对等互连服务](azure-portal.md)。

若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。

若要访问遥测，请参阅[访问连接遥测](measure-connection-telemetry.md)。
