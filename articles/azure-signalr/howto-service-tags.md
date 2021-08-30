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
ms.openlocfilehash: fdb87dab5f2dc105da28ca097f81ff83c0dce3b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735638"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>对 Azure SignalR 服务使用服务标记

配置[网络安全组](../virtual-network/network-security-groups-overview.md#network-security-groups)时，可以对 Azure SignalR 服务使用[服务标记](../virtual-network/network-security-groups-overview.md#service-tags)。 它允许你定义 Azure SignalR 服务终结点的入站/出站网络安全规则，而无需对 IP 地址进行硬编码。

Azure SignalR 服务管理这些服务标记。 你无法创建自己的服务标记，也无法修改现有标记。 Microsoft 会管理与服务标记匹配的这些地址前缀，并会在地址发生更改时自动更新服务标记。

> [!Note]
> 自 2021 年 8 月 15 日起，Azure SignalR 服务支持对入站和出站流量使用双向服务标记。

## <a name="use-service-tag-on-portal"></a>在门户上使用服务标记

### <a name="configure-outbound-traffic"></a>配置出站流量

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

### <a name="configure-inbound-traffic"></a>配置入站流量

如果你有上游，还可通过添加新的入站网络安全规则，允许来自 Azure SignalR 服务的入站流量：

1. 转到网络安全组。

1. 单击名为“入站安全规则”的设置菜单。

1. 单击顶部的“+ 添加”按钮。

1. 选择“源”下的“服务标记” 。

1. 选择“源服务标记”下的“AzureSignalR” 。

1. 在“源端口范围”中填写“\*”。

   :::image type="content" alt-text="创建入站安全规则" source="media/howto-service-tags/portal-add-inbound-security-rule.png" :::


1. 根据需要调整其他字段。

1. 单击“添加”。

## <a name="next-steps"></a>后续步骤

- [网络安全组：服务标记](../virtual-network/network-security-groups-overview.md#security-rules)