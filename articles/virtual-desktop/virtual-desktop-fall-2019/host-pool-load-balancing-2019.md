---
title: Windows 虚拟桌面（经典）主机池负载均衡 - Azure
description: Windows 虚拟桌面环境的主机池负载均衡方法。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 940863b983b00dbb3c4af590d75868665372f818
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "88002299"
---
# <a name="host-pool-load-balancing-methods-in-windows-virtual-desktop-classic"></a>Windows 虚拟桌面（经典）中的主机池负载均衡方法

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../host-pool-load-balancing.md)。

Windows 虚拟桌面支持两种负载均衡方法。 每种方法将确定在用户连接到主机池中的资源时，哪些会话主机将托管用户会话。

Windows 虚拟桌面提供以下负载均衡方法：

- 广度优先负载均衡允许你在主机池中的会话主机之间均匀分布用户会话。
- 深度优先负载均衡可使用主机池中的用户会话让会话主机饱和。 第一个会话达到其会话限制阈值后，负载均衡器会将任何新用户连接定向到主机池中的下一个会话，直至会话主机达到限制，以此类推。

每个主机池只能配置一种特定的负载均衡。 但是，无论它们位于哪个主机池，这两种负载均衡方法均具有以下行为：

- 如果用户已在主机池中有会话，并重新连接到该会话，则负载均衡器会将其成功地重定向到其现有会话所在的会话主机。 即使会话主机的 AllowNewConnections 属性设置为 False，此行为依然适用。
- 如果用户还没有在主机池中建立会话，则负载均衡器在负载均衡期间不会考虑 AllowNewConnections 属性设置为 False 的会话主机。

## <a name="breadth-first-load-balancing-method"></a>广度优先负载均衡方法

通过广度优先的负载均衡方法，你可以分布用户连接以优化此方案。 此方法非常适用于组织，帮助用户在连接到组织共用虚拟桌面环境时获得最佳体验。

广度优先方法首先查询允许新连接的会话主机。 然后，方法选择会话数最少的会话主机。 如果存在关联，则该方法将选择查询中的第一个会话主机。

## <a name="depth-first-load-balancing-method"></a>深度优先负载均衡方法

深度优先负载均衡方法可让你一次使一台会话主机实现饱和，以便针对此方案进行优化。 此方法非常适合注重成本的组织，这些组织需要更精细地控制其为主机池分配的虚拟机数量。

深度优先方法首先查询允许新连接且尚未超出其最大会话限制的会话主机。 然后，该方法选择具有最多会话数量的会话主机。 如果存在关联，则该方法将选择查询中的第一个会话主机。