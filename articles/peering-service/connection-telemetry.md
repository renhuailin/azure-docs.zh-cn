---
title: Azure 对等互连服务连接遥测
description: 了解 Microsoft Azure 对等互连服务连接遥测
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "84871297"
---
# <a name="peering-service-connection-telemetry"></a>对等互连服务连接遥测

连接遥测数据就客户位置与 Microsoft 网络之间的连接收集了相关见解。 客户可以通过在 Azure 门户中注册连接来获得 Azure 对等互连服务连接的遥测。 此功能可提供前缀安全性和对网络性能的见解。


连接遥测由以下范围组成：

### <a name="latency-measurement"></a>延迟度量

 对于在对等互连服务中注册的前缀，延迟是从客户端到 Microsoft Edge PoP 进行度量的。

### <a name="route-prefix-monitoring-and-protection"></a>路由前缀监视和保护

监视路由路径中是否存在任何可疑的活动，然后将其捕获到事件日志中。 例如，针对以下某些因素创建事件日志：

- 前缀劫持
- 前缀撤销
- 路由泄漏

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务连接，请参阅[对等互连服务连接](connection.md)。
- 若要加入对等互连服务连接，请参阅[加入对等互连服务模型](onboarding-model.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
