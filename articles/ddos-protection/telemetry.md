---
title: 查看和配置适用于 Azure DDoS 防护标准的 DDoS 防护遥测
description: 了解如何查看和配置适用于 Azure DDoS 防护标准的 DDoS 防护遥测。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 6dc086aae55f3b35dbb7dc787df6a65b7ade108f
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674194"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>查看和配置 DDoS 防护遥测

Azure DDoS 防护标准通过 DDoS 攻击分析提供详细的攻击见解和可视化效果。 保护其虚拟网络免受 DDoS 攻击的客户可通过攻击缓解报告和缓解流日志来详细了解攻击流量以及缓解攻击的操作。 在 DDoS 攻击期间通过 Azure Monitor 公开丰富的遥测数据（包括详细的指标）。 可以针对 DDoS 防护公开的任何 Azure Monitor 指标配置警报。 可将记录与 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk（Azure 事件中心）、OMS Log Analytics 和 Azure 存储进一步集成，以通过 Azure Monitor 诊断界面进行高级分析。

本教程介绍以下操作：

> [!div class="checklist"]
> * 查看 DDoS 防护遥测
> * 查看 DDoS 缓解策略
> * 验证和测试 DDoS 防护遥测

### <a name="metrics"></a>指标

指标名称提供不同的数据包类型和字节数与数据包数，每个指标都有如下所示的标记名称基本构造：

- **丢弃的标记名称（例如，丢弃的入站数据包 DDoS）**：由 DDoS 防护系统丢弃/清理的数据包数。
- **转发的标记名称（例如，转发的入站数据包 DDoS）**：由 DDoS 系统转发到目标 VIP 的数据包数 - 未筛选的流量。
- **无标记名称（例如，入站数据包 DDoS）**：进入到清理系统的总数据包数 – 表示丢弃的和转发的数据包的和。

> [!NOTE]
> 尽管“聚合”的多个选项显示在 Azure 门户上，但每个指标仅支持下表中列出的聚合类型。 我们对于这给你造成的困惑表示歉意，我们正在努力解决此问题。

以下[指标](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)适用于 Azure DDoS 防护标准。 这些指标也可以通过诊断设置导出（请参阅[查看和配置 DDoS 诊断日志记录](diagnostic-logging.md)）。


| 指标 | 指标显示名称 | 单位 | 聚合类型 | 说明 |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | 丢弃的入站字节 DDoS | 每秒字节数 | 最大值 | 丢弃的入站字节 DDoS| 
| BytesForwardedDDoS | 转发的入站字节 DDoS | 每秒字节数 | 最大值 | 转发的入站字节 DDoS |
| BytesInDDoS | 入站字节 DDoS | 每秒字节数 | 最大值 | 入站字节 DDoS |
| DDoSTriggerSYNPackets | 触发 DDoS 缓解的入站 SYN 数据包 | 每秒计数 | 最大值 | 触发 DDoS 缓解的入站 SYN 数据包 |
| DDoSTriggerTCPPackets | 触发 DDoS 缓解的入站 TCP 数据包 | 每秒计数 | 最大值 | 触发 DDoS 缓解的入站 TCP 数据包 |
| DDoSTriggerUDPPackets | 触发 DDoS 缓解的入站 UDP 数据包 | 每秒计数 | 最大值 | 触发 DDoS 缓解的入站 UDP 数据包 |
| IfUnderDDoSAttack | 是否遭到 DDoS 攻击 | 计数 | 最大值 | 是否遭到 DDoS 攻击 |
| PacketsDroppedDDoS | 丢弃的入站数据包 DDoS | 每秒计数 | 最大值 | 丢弃的入站数据包 DDoS |
| PacketsForwardedDDoS | 转发的入站数据包 DDoS | 每秒计数 | 最大值 | 转发的入站数据包 DDoS |
| PacketsInDDoS | 入站数据包 DDoS | 每秒计数 | 最大值 | 入站数据包 DDoS |
| TCPBytesDroppedDDoS | 丢弃的入站 TCP 字节 DDoS | 每秒字节数 | 最大值 | 丢弃的入站 TCP 字节 DDoS |
| TCPBytesForwardedDDoS | 转发的入站 TCP 字节 DDoS | 每秒字节数 | 最大值 | 转发的入站 TCP 字节 DDoS |
| TCPBytesInDDoS | 入站 TCP 字节 DDoS | 每秒字节数 | 最大值 | 入站 TCP 字节 DDoS |
| TCPPacketsDroppedDDoS | 丢弃的入站 TCP 数据包 DDoS | 每秒计数 | 最大值 | 丢弃的入站 TCP 数据包 DDoS |
| TCPPacketsForwardedDDoS | 转发的入站 TCP 数据包 DDoS | 每秒计数 | 最大值 | 转发的入站 TCP 数据包 DDoS |
| TCPPacketsInDDoS | 入站 TCP 数据包 DDoS | 每秒计数 | 最大值 | 入站 TCP 数据包 DDoS |
| UDPBytesDroppedDDoS | 丢弃的入站 UDP 字节 DDoS | 每秒字节数 | 最大值 | 丢弃的入站 UDP 字节 DDoS |
| UDPBytesForwardedDDoS | 转发的入站 UDP 字节 DDoS | 每秒字节数 | 最大值 | 转发的入站 UDP 字节 DDoS |
| UDPBytesInDDoS | 入站 UDP 字节 DDoS | 每秒字节数 | 最大值 | 入站 UDP 字节 DDoS |
| UDPPacketsDroppedDDoS | 丢弃的入站 UDP 数据包 DDoS | 每秒计数 | 最大值 | 丢弃的入站 UDP 数据包 DDoS |
| UDPPacketsForwardedDDoS | 转发的入站 UDP 数据包 DDoS | 每秒计数 | 最大值 | 转发的入站 UDP 数据包 DDoS |
| UDPPacketsInDDoS | 入站 UDP 数据包 DDoS | 每秒计数 | 最大值 | 入站 UDP 数据包 DDoS |

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准防护计划](manage-ddos-protection.md)，并且必须在虚拟网络上启用 DDoS 防护标准。
- DDoS 监视分配给虚拟网络中的资源的公共 IP 地址。 如果虚拟网络中没有任何具有公共 IP 地址的资源，必须首先创建具有公共 IP 地址的资源。 你可以监视 [Azure 服务的虚拟网络](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)中列出的通过资源管理器（非经典）部署的所有资源（包括后端虚拟机位于虚拟网络中的 Azure 负载均衡器）的公共 IP，但 Azure 应用服务环境除外。 可快速创建 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机继续本教程的内容。  

## <a name="view-ddos-protection-telemetry"></a>查看 DDoS 防护遥测

对攻击的遥测是通过 Azure Monitor 实时提供的。 虽然 TCP SYN、TCP 和 UDP 的[缓解触发器](#view-ddos-mitigation-policies)在和平期内可用，但其他遥测只能在公共 IP 地址被缓解后可用。 

可以通过以下三种不同的资源类型查看受保护的公共 IP 地址的 DDoS 遥测：DDoS 防护计划、虚拟网络和公共 IP 地址。

### <a name="ddos-protection-plan"></a>DDoS 防护计划
1. 登录到 [Azure 门户](https://portal.azure.com/)，然后浏览到 DDoS 防护计划。
2. 在“监视”下，选择“指标”。
3. 选择“范围”。 选择包含要记录的公共 IP 地址的“订阅”，为“资源类型”选择“公共 IP 地址”，然后选择要记录指标的特定公共 IP 地址，最后选择“应用”   。
4. 选择“最大值”作为“聚合”类型 。 

### <a name="virtual-network"></a>虚拟网络
1. 登录到 [Azure 门户](https://portal.azure.com/)，然后浏览到已启用 DDoS 防护功能的虚拟网络。
2. 在“监视”下，选择“指标”。
3. 选择“范围”。 选择包含要记录的公共 IP 地址的“订阅”，为“资源类型”选择“公共 IP 地址”，然后选择要记录指标的特定公共 IP 地址，最后选择“应用”   。
4. 选择“最大值”作为“聚合”类型 。
5. 选择“添加筛选器”。 在“属性”下，选择“受保护的 IP 地址”，运算符应设置为 =。 你将在“值”下看到一个下拉列表，其中包含已启用 DDoS 防护保护功能的公共 IP 地址，且这些地址与虚拟网络关联。 

![DDoS 诊断设置](./media/ddos-attack-telemetry/vnet-ddos-metrics.png)

### <a name="public-ip-address"></a>公共 IP 地址
1. 登录到 [Azure 门户](https://portal.azure.com/)，并浏览到公共 IP 地址。
2. 在“监视”下，选择“指标”。 
3. 选择“最大值”作为“聚合”类型 。

## <a name="view-ddos-mitigation-policies"></a>查看 DDoS 缓解策略

标准 DDoS 防护针对已启用 DDoS 防护功能的虚拟网络中受保护资源的每个公共 IP 地址，应用三个自动优化的缓解策略（TCP SYN、TCP 和 UDP）。 通过选择“聚合”类型为“最大值”的“触发 DDoS 缓解的入站 TCP 数据包”和“触发 DDoS 缓解的入站 UDP 数据包”指标，可查看策略阈值，如下图中所示  ：

![查看缓解策略](./media/manage-ddos-protection/view-mitigation-policies.png)

## <a name="validate-and-test"></a>验证并测试

要模拟 DDoS 攻击以验证 DDoS 防护遥测，请参阅[验证 DDoS 检测](test-through-simulations.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

- 配置针对 DDoS 防护指标的警报
- 查看 DDoS 防护遥测
- 查看 DDoS 缓解策略
- 验证和测试 DDoS 防护遥测

要了解如何配置攻击缓解报告和流日志，请继续学习下一个教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 诊断日志记录](diagnostic-logging.md)