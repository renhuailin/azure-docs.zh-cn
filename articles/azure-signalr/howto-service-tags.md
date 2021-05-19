---
title: 使用服务标记
titleSuffix: Azure SignalR Service
description: 使用服务标记来允许流向 Azure SignalR 服务的出站流量
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152316"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>对 Azure SignalR 服务使用服务标记

配置[网络安全组](../virtual-network/network-security-groups-overview.md#network-security-groups)时，可以对 Azure SignalR 服务使用[服务标记](../virtual-network/network-security-groups-overview.md#service-tags)。 它允许你定义 Azure SignalR 服务终结点的出站网络安全规则，而无需对 IP 地址进行硬编码。

Azure SignalR 服务管理这些服务标记。 你无法创建自己的服务标记，也无法修改现有标记。 Microsoft 会管理与服务标记匹配的这些地址前缀，并会在地址发生更改时自动更新服务标记。

## <a name="use-service-tag-on-portal"></a>在门户上使用服务标记

可以通过添加新的出站网络安全规则来允许 Azure SignalR 服务的出站流量：

1. 转到网络安全组。

1. 单击名为“出站安全规则”的设置菜单。

1. 单击顶部的“+ 添加”按钮。

1. 选择“目标”下的“服务标记” 。

1. 选择“目标服务标记”下的“AzureSignalR” 。

1. 在“目标端口范围”中填写“443“ 。

    ![创建出站安全规则](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. 根据需要调整其他字段。

1. 单击“添加”。


## <a name="next-steps"></a>后续步骤

- [网络安全组：服务标记](../virtual-network/network-security-groups-overview.md#security-rules)