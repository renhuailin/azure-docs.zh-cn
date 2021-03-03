---
title: 查看和配置适用于 Azure DDoS 保护标准的 DDoS 保护遥测
description: 了解如何查看和配置适用于 Azure DDoS 保护标准的 DDoS 保护遥测。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 0be184921ff0bd6b98dd2975acb4e0d5c8b26ba0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716187"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>查看和配置 DDoS 防护遥测

Azure DDoS 防护标准通过 DDoS 攻击分析提供详细的攻击见解和可视化效果。 保护其虚拟网络免受 DDoS 攻击的客户可通过攻击缓解报告和缓解流日志来详细了解攻击流量以及缓解攻击的操作。 丰富的遥测通过 Azure Monitor 公开，包括在 DDoS 攻击期间的详细指标。 可以针对 DDoS 防护公开的任何 Azure Monitor 指标配置警报。 可以通过 Azure Monitor 诊断接口，将日志记录与 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure 事件中心) 、OMS Log Analytics 和 azure 存储进行进一步集成，以实现高级分析。

本教程介绍以下操作：

> [!div class="checklist"]
> * 查看 DDoS 保护遥测
> * 查看 DDoS 缓解策略
> * 验证并测试 DDoS 保护遥测

### <a name="metrics"></a>指标

> [!NOTE]
> 在 Azure 门户上显示 **聚合** 的多个选项时，每个度量值仅支持下表中列出的聚合类型。 对于这种混乱，我们深表歉意，我们正在努力解决此问题。

以下 [指标](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) 适用于 Azure DDoS 保护标准。 这些指标也可通过诊断设置导出 (参阅 [查看和配置 DDoS 诊断日志记录](diagnostic-logging.md)) 。


| 指标 | 指标显示名称 | 计价单位 | 聚合类型 | 说明 |
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
- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准保护计划](manage-ddos-protection.md) ，并且必须在虚拟网络上启用 DDoS 保护标准。
- DDoS 监视分配给虚拟网络中的资源的公共 IP 地址。 如果虚拟网络中没有任何具有公共 IP 地址的资源，必须首先创建具有公共 IP 地址的资源。 你可以通过 [azure 服务的虚拟网络](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) 中列出的资源管理器 (非经典) 来监视部署的所有资源的公共 IP 地址 (包括) 环境除外的虚拟网络中的后端虚拟机所在的 Azure 负载平衡器。 可快速创建 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机继续本教程的内容。  

## <a name="view-ddos-protection-telemetry"></a>查看 DDoS 保护遥测

对攻击的遥测是通过 Azure Monitor 实时提供的。 遥测仅在公共 IP 地址被缓解时可用。 

1. 登录到 [Azure 门户](https://portal.azure.com/) 并浏览到 DDoS 保护计划。
2. 在“监视”下，选择“指标”。
3. 选择“范围”。 选择包含要记录的公共 IP 地址的 **订阅**，选择 "**资源类型** 的 **公共 ip 地址**"，然后选择要为其记录指标的特定公共 Ip 地址，然后选择 "**应用**"。
4. 选择 "**最大值**"**聚合** 类型。

指标名称提供不同的数据包类型和字节数与数据包数，每个指标都有如下所示的标记名称基本构造：

- **丢弃的标记名称（例如，丢弃的入站数据包 DDoS）**：由 DDoS 防护系统丢弃/清理的数据包数。
- **转发的标记名称（例如，转发的入站数据包 DDoS）**：由 DDoS 系统转发到目标 VIP 的数据包数 - 未筛选的流量。
- **无标记名称（例如，入站数据包 DDoS）**：进入到清理系统的总数据包数 – 表示丢弃的和转发的数据包的和。

## <a name="view-ddos-mitigation-policies"></a>查看 DDoS 缓解策略

标准 DDoS 防护针对已启用 DDoS 的虚拟网络中受保护资源的每个公共 IP 地址，应用三个自动优化的缓解策略（TCP SYN、TCP 和 UDP）。 可以通过选择 "**入站 TCP 数据包" 来** 查看策略阈值，以便触发 ddos 缓解和 **入站 UDP 数据包，以触发****聚合** 类型为 "Max" 的 ddos 缓解指标，如下图所示：

![查看缓解策略](./media/manage-ddos-protection/view-mitigation-policies.png)

策略阈值是通过基于 Azure 机器学习的网络流量探查自动配置的。 仅当超过策略阈值时，才会对受攻击的 IP 地址进行 DDoS 缓解。

## <a name="validate-and-test"></a>验证并测试

若要模拟 DDoS 攻击来验证 DDoS 保护遥测，请参阅 [验证 ddos 检测](test-through-simulations.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

- 配置针对 DDoS 防护指标的警报
- 查看 DDoS 保护遥测
- 查看 DDoS 缓解策略
- 验证并测试 DDoS 保护遥测

若要了解如何配置攻击缓解报告和流日志，请继续学习下一教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 诊断日志记录](diagnostic-logging.md)