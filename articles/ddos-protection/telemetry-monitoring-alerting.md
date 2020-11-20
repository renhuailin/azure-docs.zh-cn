---
title: 查看和配置 DDoS 防护遥测
description: 了解如何查看和配置 DDoS 保护遥测。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c80e5e611c275c2a2262963aa0759075fca836b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989388"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>查看和配置 DDoS 防护遥测

Azure DDoS 防护标准通过 DDoS 攻击分析提供详细的攻击见解和可视化效果。 保护其虚拟网络免受 DDoS 攻击的客户可通过攻击缓解报告和缓解流日志来详细了解攻击流量以及缓解攻击的操作。 丰富的遥测通过 Azure Monitor 公开，包括在 DDoS 攻击期间的详细指标。 可以针对 DDoS 防护公开的任何 Azure Monitor 指标配置警报。 可以通过 Azure Monitor 诊断接口，将日志记录与 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure 事件中心) 、OMS Log Analytics 和 azure 存储进行进一步集成，以实现高级分析。

本教程介绍以下操作：

> [!div class="checklist"]
> * 配置针对 DDoS 防护指标的警报
> * 使用 DDoS 防护遥测
> * 查看 DDoS 缓解策略
> * 在 Azure 安全中心查看 DDoS 保护警报

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准保护计划](manage-ddos-protection.md)。

## <a name="configure-alerts-for-ddos-protection-metrics"></a>配置针对 DDoS 防护指标的警报

利用 Azure Monitor 警报配置，可以选择任何可用的 DDoS 防护指标，以在攻击期间发生活跃的风险缓解时发出警报。 当满足条件时，指定的地址会收到警报电子邮件：

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 Monitor。 当“Monitor”出现在结果中时，将其选中。
3. 选择“共享服务”下的“指标”。
4. 输入或选择自己的值或输入以下示例值，接受其余默认值，然后选择“确定”：

    |设置                  |值                                                                                               |
    |---------                |---------                                                                                           |
    |名称                     | 输入 _MyDdosAlert_。                                                                                |
    |订阅             | 选择包含希望用于接收警报的公共 IP 地址的订阅。        |
    |资源组           | 选择包含希望用于接收警报的公共 IP 地址的资源组。      |
    |资源                 | 选择包含希望用于接收警报的公共 IP 地址的公共 IP 地址。 DDoS 监视分配给虚拟网络中的资源的公共 IP 地址。 如果虚拟网络中没有任何具有公共 IP 地址的资源，必须首先创建具有公共 IP 地址的资源。 可监视通过[适用于 Azure 服务的虚拟网络](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)中列出的资源管理器部署的所有资源的公共 IP 地址，Azure App Service 环境和 Azure VPN 网关除外。 可快速创建 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机继续本教程的内容。                   |
    |指标                   | 选择 **"DDoS 攻击"**。                                                                |
    |阈值                | 1 - 1 表示遭到攻击。 0 表示未遭到攻击。                         |
    |周期                   | 选择所选的任何值。                                                                   |
    |通过电子邮件通知         | 选中复选框。                                                                                 |
    |其他管理员 | 如果不是电子邮件所有者、参与者或订阅的阅读者，请输入你的电子邮件地址。 |

    检测到攻击几分钟后，将从 Azure Monitor 指标收到一封电子邮件，如下图所示：

    ![攻击警报](./media/manage-ddos-protection/ddos-alert.png)


若要模拟 DDoS 攻击来验证警报，请参阅[验证 DDoS 检测](test-through-simulations.md)。

还可以了解有关为创建警报而[配置 webhook](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和[逻辑应用](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的详细信息。

## <a name="use-ddos-protection-telemetry"></a>使用 DDoS 防护遥测

对攻击的遥测是通过 Azure Monitor 实时提供的。 遥测仅在公共 IP 地址处于风险缓解期间可用。 在攻击缓解之前或之后，都不会看到遥测。

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 Monitor。 当“Monitor”出现在结果中时，将其选中。
3. 选择 "**共享服务**" 下的 "**指标**"。
4. 选择包含要为其遥测的公共 IP 地址的订阅和资源组。
5. 为“资源类型”选择“公共 IP 地址”，然后选择要为其遥测的特定公共 IP 地址。
6. 屏幕的左侧将显示一系列可用指标。 选中这些指标时，会在概述屏幕上的 **Azure Monitor 指标图表** 中绘制这些指标。
7. 选择 **聚合** 类型为 **Max**

指标名称提供不同的数据包类型和字节数与数据包数，每个指标都有如下所示的标记名称基本构造：

- **丢弃的标记名称（例如，丢弃的入站数据包 DDoS）**：由 DDoS 防护系统丢弃/清理的数据包数。
- **转发的标记名称（例如，转发的入站数据包 DDoS）**：由 DDoS 系统转发到目标 VIP 的数据包数 - 未筛选的流量。
- **无标记名称（例如，入站数据包 DDoS）**：进入到清理系统的总数据包数 – 表示丢弃的和转发的数据包的和。

此 [Azure Monitor 警报规则](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) 将运行一个简单的查询，以检测发生活动 DDoS 缓解的时间。 若要模拟 DDoS 攻击来验证遥测，请参阅[验证 DDoS 检测](test-through-simulations.md)。 

## <a name="view-ddos-mitigation-policies"></a>查看 DDoS 缓解策略

标准 DDoS 防护针对已启用 DDoS 的虚拟网络中受保护资源的每个公共 IP 地址，应用三个自动优化的缓解策略（TCP SYN、TCP 和 UDP）。 可以通过选择 "**入站 TCP 数据包" 来** 查看策略阈值，以便触发 ddos 缓解和 **入站 UDP 数据包，以触发****聚合** 类型为 "Max" 的 ddos 缓解指标，如下图所示：

![查看缓解策略](./media/manage-ddos-protection/view-mitigation-policies.png)

策略阈值是通过基于 Azure 机器学习的网络流量探查自动配置的。 仅当超过策略阈值时，才会对受攻击的 IP 地址进行 DDoS 缓解。

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>在 Azure 安全中心查看 DDoS 保护警报

Azure 安全中心提供 [安全警报](../security-center/security-center-managing-and-responding-alerts.md)的列表，其中包含有助于调查和修正问题的信息。 利用此功能，可以获得警报的统一视图，包括与 DDoS 攻击相关的警报，以及在短期内缓解攻击所采取的措施。
你将看到两个特定警报，你可以看到这些警报是为了进行 DDoS 攻击检测和缓解：

- **检测到公共 ip 的 DDoS 攻击**：当 ddos 防护服务检测到某个公共 ip 地址是 DDoS 攻击的目标时，会生成此警报。
- **公共 ip 的 DDoS 攻击缓解**：当已缓解对公共 ip 地址的攻击时，将生成此警报。
若要查看警报，请在 Azure 门户中打开 " **安全中心** "。 在 " **威胁防护**" 下，选择 " **安全警报**"。 以下屏幕截图显示了 DDoS 攻击警报的示例。

![Azure 安全中心中的 DDoS 警报](./media/manage-ddos-protection/ddos-alert-asc.png)

这些警报包含有关受攻击、异地和威胁智能信息以及修正步骤的公共 IP 地址的一般信息。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

- 配置针对 DDoS 防护指标的警报
- 使用 DDoS 防护遥测
- 查看 DDoS 缓解策略
- 在 Azure 安全中心查看 DDoS 保护警报

若要了解如何配置攻击缓解报告和流日志，请继续学习下一教程。

> [!div class="nextstepaction"]
> [配置 DDoS 攻击缓解报表和流日志](reports-and-flow-logs.md)