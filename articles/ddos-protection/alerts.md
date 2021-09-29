---
title: 查看和配置适用于 Azure DDoS 防护标准的 DDoS 防护警报
description: 了解如何查看和配置适用于 Azure DDoS 防护标准的 DDoS 防护警报。
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
ms.openlocfilehash: 1ea079802a2628b8a698e108f6f689ff5e58f08b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750301"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>查看和配置 DDoS 防护警报

Azure DDoS 防护标准通过 DDoS 攻击分析提供详细的攻击见解和可视化效果。 保护其虚拟网络免受 DDoS 攻击的客户可通过攻击缓解报告和缓解流日志来详细了解攻击流量以及缓解攻击的操作。 在 DDoS 攻击期间通过 Azure Monitor 公开丰富的遥测数据（包括详细的指标）。 可以针对 DDoS 防护公开的任何 Azure Monitor 指标配置警报。 可将记录与 [Azure Sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection)、Splunk（Azure 事件中心）、OMS Log Analytics 和 Azure 存储进一步集成，以通过 Azure Monitor 诊断界面进行高级分析。

本教程介绍以下操作：

> [!div class="checklist"]
> * 通过 Azure Monitor 配置警报
> * 通过门户配置警报
> * 在 Azure 安全中心查看警报
> * 验证和测试警报

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准防护计划](manage-ddos-protection.md)，并且必须在虚拟网络上启用 DDoS 防护标准。
- DDoS 监视分配给虚拟网络中的资源的公共 IP 地址。 如果虚拟网络中没有任何具有公共 IP 地址的资源，必须首先创建具有公共 IP 地址的资源。 你可以监视 [Azure 服务的虚拟网络](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)中列出的通过资源管理器（非经典）部署的所有资源（包括后端虚拟机位于虚拟网络中的 Azure 负载均衡器）的公共 IP，但 Azure 应用服务环境除外。 可快速创建 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机继续本教程的内容。     

## <a name="configure-alerts-through-azure-monitor"></a>通过 Azure Monitor 配置警报

使用这些模板，可以为已启用诊断日志记录的所有公共 IP 地址配置警报。 因此，若要使用这些警报模板，首先需要一个已启用诊断设置的 Log Analytics 工作区。 请参阅[查看和配置 DDoS 诊断日志记录](diagnostic-logging.md)。

### <a name="azure-monitor-alert-rule"></a>Azure Monitor 警报规则

此 [Azure Monitor 警报规则](https://aka.ms/DDOSmitigationstatus)将运行一个简单的查询，以检测何时发生主动的 DDoS 缓解措施。 这指示存在潜在攻击。 操作组可用于作为警报结果调用操作。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>具有逻辑应用的 Azure Monitor 警报规则

此[模板](https://aka.ms/ddosalert)部署扩充的 DDoS 缓解警报的必要组件：Azure Monitor 警报规则、操作组和逻辑应用。 此过程的结果是一封电子邮件警报，其中包含有关受攻击的 IP 地址的详细信息，包括与 IP 关联的资源的相关信息。 资源的所有者将与安全团队一起添加为电子邮件的收件人。 还会执行基本应用程序可用性测试，并将结果包含在电子邮件警报中。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>通过门户配置警报

利用 Azure Monitor 警报配置，可以选择任何可用的 DDoS 防护指标，以在攻击期间发生活跃的风险缓解时发出警报。 

1. 登录到 [Azure 门户](https://portal.azure.com/)，然后浏览到 DDoS 防护计划。
2. 在“监视”下，选择“指标”。
3. 在灰色导航栏中，选择“新建警报规则”。 
4. 输入或选择自己的值或输入以下示例值，接受其余默认值，然后选择“创建警报规则”：

    |设置                  |值                                                                                               |
    |---------                |---------                                                                                           |
    | 范围                   | 选中“选择资源”。 </br> 选择包含要记录的公共 IP 地址的“订阅”，为“资源类型”选择“公共 IP 地址”，然后选择要记录指标的特定公共 IP 地址  。 </br> 选择“完成”。 | 
    | 条件 | 选择“选择条件”。 </br> 在信号名称下，选择“是否受到 DDoS 攻击”。 </br> 在“运算符”下，选择“大于或等于” 。 </br> 在“聚合类型”下，选择“最大” 。 </br> 在“阈值”下，输入 1。 对于“是否受到 DDoS 攻击”指标，“0”表示未受到攻击，而“1”表示受到攻击  。 </br> 选择“完成”。 | 
    | Actions | 选择“添加操作组”。 </br> 选择“创建操作组”。 </br> 在“通知”下的“通知类型”下，选择“电子邮件/短信/推送/语音”  。 </br> 在“名称”下，输入 MyUnderAttackEmailAlert。 </br> 单击“编辑”按钮，选择“电子邮件”和所需的以下任意选项，然后选择“确定” 。 </br> 选择“查看 + 创建”。 | 
    | 警报规则详细信息 | 在“警报规则名称”下，输入 MyDdosAlert。 |

检测到攻击几分钟后，应从 Azure Monitor 指标收到一封电子邮件，如下图所示：

![攻击警报](./media/manage-ddos-protection/ddos-alert.png)

还可以了解有关为创建警报而[配置 webhook](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和[逻辑应用](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的详细信息。

## <a name="view-alerts-in-azure-security-center"></a>在 Azure 安全中心查看警报

Azure 安全中心提供[安全警报](../security-center/security-center-managing-and-responding-alerts.md)列表，其中包含有助于调查和修正问题的信息。 通过此功能，你可以获得警报的统一视图，其中包括与 DDoS 攻击有关的警报以及为近实时缓解攻击而采取的措施。
对于任意 DDoS 攻击检测和缓解，你都会看到两种特定的警报：

- **检测到针对公共 IP 的 DDoS 攻击**：当 DDoS 防护服务检测到你的某个公共 IP 地址是 DDoS 攻击的目标时，将生成此警报。
- **针对公共 IP 的 DDoS 攻击得到缓解**：当针对公共 IP 地址的攻击得到缓解时，将生成此警报。
若要查看警报，请在 Azure 门户中打开“安全中心”。 在“威胁防护”下，选择“安全警报” 。 以下屏幕截图显示了 DDoS 攻击警报的示例。

![Azure 安全中心中的 DDoS 警报](./media/manage-ddos-protection/ddos-alert-asc.png)

这些警报包含有关受到攻击的公共 IP 地址、异地和威胁情报信息以及修正步骤的一般信息。

## <a name="validate-and-test"></a>验证并测试

若要模拟 DDoS 攻击来验证警报，请参阅[验证 DDoS 检测](test-through-simulations.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

- 通过 Azure Monitor 配置警报
- 通过门户配置警报
- 在 Azure 安全中心查看警报
- 验证和测试警报

若要了解如何测试和模拟 DDoS 攻击，请参阅模拟测试指南：

> [!div class="nextstepaction"]
> [通过模拟进行测试](test-through-simulations.md)