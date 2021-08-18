---
title: 将 Azure DDoS 防护数据连接到 Azure Sentinel
description: 了解如何将 Azure DDoS 保护数据引入 Azure Sentinel，以便查看、分析和调查这些数据。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 999555624ad4e88058a575dce3fb9cd606228b18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778328"
---
# <a name="connect-data-from-azure-ddos-protection"></a>连接 Azure DDoS 保护数据

分布式拒绝服务 (DDoS) 攻击试图耗尽应用程序的资源，使合法用户无法使用应用程序。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。 [Azure DDoS 保护](../ddos-protection/ddos-protection-overview.md)与应用程序设计最佳做法相结合，可提供针对 DDoS 攻击的可靠防御。 可以将 Azure DDoS 防护日志连接到 Azure Sentinel，以便查看工作簿中的日志数据，使用这些数据创建自定义警报，并通过整合这些数据来改进调查。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 你必须具有已配置的 [Azure DDoS 标准防护计划](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan)。

- 你必须具有已配置的[启用了 Azure DDoS 标准的虚拟网络](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network)。

## <a name="connect-to-azure-ddos-protection"></a>连接到 Azure DDoS 防护
    
1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Azure DDoS 防护”，然后在预览窗格中选择“打开连接器页面”。 

1. 在要连接其日志的所有公共 IP 地址上启用“诊断日志”：

    1. 选择“打开诊断设置 >”链接，然后从列表中选择“公共 IP 地址”资源。

    1. 选择“+ 添加诊断设置”。 

    1. 在“诊断设置”屏幕中：
       - 在“诊断设置名称”字段中输入名称。

       - 选中“发送到 Log Analytics”复选框。 其下将显示两个新字段。 选择相关“订阅”和“Log Analytics 工作区”（Azure Sentinel 驻留的位置）。

       - 选中要引入其日志的规则类型的复选框。 建议选择“DDoSProtectionNotifications”、“DDoSMitigationFlowLogs”和“DDoSMitigationReports”。

    1. 单击屏幕顶部的“保存”。  针对已启用 DDoS 防护的任何其他防火墙（公共 IP 地址）重复此过程。

1. 若要将 Log Analytics 中的相关架构用于 Azure DDoS 防护警报，请搜索 **AzureDiagnostics**。

> [!NOTE]
>
> 对于这个特定的数据连接器，只有当数据在过去两周内的某个时间点被引入的情况下，连接状态指示器（数据连接器库中的色条和数据类型名称旁边的连接图标）才会显示为“已连接”（绿色）。 一旦两周过去但没有数据引入，连接器将显示为已断开。 当有更多数据引入的那一刻，连接器将重新变为“已连接”状态。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Azure DDoS 防护日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
