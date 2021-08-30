---
title: 将 Azure 防火墙数据连接到 Azure Sentinel
description: 了解如何将 Azure 防火墙数据连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f4683708f8fdd4eda2f483cc4112fe9fe9939ce6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778747"
---
# <a name="connect-data-from-azure-firewall"></a>连接 Azure 防火墙数据

Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 

可以将 Azure 防火墙日志连接到 Azure Sentinel，以便查看工作簿中的日志数据，使用这些数据创建自定义警报，并通过整合这些数据来改进调查。

详细了解[监视 Azure 防火墙日志](../firewall/firewall-diagnostics.md)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

## <a name="connect-to-azure-firewall"></a>连接到 Azure 防火墙
    
1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Azure 防火墙”，然后在预览窗格中选择“打开连接器页面”。

1. 在要连接其日志的所有防火墙上启用“诊断日志”：

    1. 选择“打开 Azure 防火墙资源 >”链接。

    1. 从“防火墙”导航菜单中选择“诊断设置”。

    1. 选择列表底部的“+ 添加诊断设置”。

    1. 在“诊断设置”屏幕的“诊断设置名称”字段中输入一个名称。
    
    1. 选中“发送到 Log Analytics”复选框。 其下将显示两个新字段。 选择相关“订阅”和“Log Analytics 工作区”（Azure Sentinel 驻留的位置）。

    1. 选中要引入其日志的规则类型的复选框。 建议选中“AzureFirewallApplicationRule”、“AzureFirewallNetworkRule”和“AzureFirewallDNSProxy”  。

    1. 选择屏幕顶部的“保存”。

1. 若要将 Log Analytics 中的相关架构用于 Azure 防火墙警报，请搜索“AzureDiagnostics”。

> [!NOTE]
>
> 对于这个特定的数据连接器，只有当数据在过去两周内的某个时间点被引入的情况下，连接状态指示器（数据连接器库中的色条和数据类型名称旁边的连接图标）才会显示为“已连接”（绿色）。 一旦两周过去但没有数据引入，连接器将显示为已断开。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解如何将 Azure 防火墙日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
