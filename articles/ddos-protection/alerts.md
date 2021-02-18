---
title: 查看和配置 Azure DDoS 保护标准的 DDoS 保护警报
description: 了解如何查看和配置 Azure DDoS 保护标准的 DDoS 防护警报。
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
ms.openlocfilehash: a5639d583d9b98f6527e47bf5db213cb191ebeb7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575293"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>查看和配置 DDoS 防护警报

Azure DDoS 防护标准通过 DDoS 攻击分析提供详细的攻击见解和可视化效果。 保护其虚拟网络免受 DDoS 攻击的客户可通过攻击缓解报告和缓解流日志来详细了解攻击流量以及缓解攻击的操作。 丰富的遥测通过 Azure Monitor 公开，包括在 DDoS 攻击期间的详细指标。 可以针对 DDoS 防护公开的任何 Azure Monitor 指标配置警报。 可以通过 Azure Monitor 诊断接口，将日志记录与 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure 事件中心) 、OMS Log Analytics 和 azure 存储进行进一步集成，以实现高级分析。

本教程介绍以下操作：

> [!div class="checklist"]
> * 通过 Azure Monitor 配置警报
> * 通过门户配置警报
> * 查看 Azure 安全中心的警报
> * 验证并测试警报

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准保护计划](manage-ddos-protection.md) ，并且必须在虚拟网络上启用 DDoS 保护标准。
- DDoS 监视分配给虚拟网络中的资源的公共 IP 地址。 如果虚拟网络中没有任何具有公共 IP 地址的资源，必须首先创建具有公共 IP 地址的资源。 你可以通过 [azure 服务的虚拟网络](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) 中列出的资源管理器 (非经典) 来监视部署的所有资源的公共 IP 地址 (包括) 环境除外的虚拟网络中的后端虚拟机所在的 Azure 负载平衡器。 可快速创建 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机继续本教程的内容。     

## <a name="configure-alerts-through-azure-monitor"></a>通过 Azure Monitor 配置警报

使用这些模板，可以为已启用诊断日志记录的所有公共 IP 地址配置警报。 因此，若要使用这些警报模板，首先需要一个启用了诊断设置的 Log Analytics 工作区。 请参阅 [查看和配置 DDoS 诊断日志记录](diagnostic-logging.md)。

### <a name="azure-monitor-alert-rule"></a>Azure Monitor 警报规则

此 [Azure Monitor 警报规则](https://aka.ms/DDOSmitigationstatus) 将运行一个简单的查询，以检测发生活动 DDoS 缓解的时间。 这表明可能存在攻击。 操作组可用于作为警报的结果调用操作。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>逻辑应用 Azure Monitor 警报规则

此 [模板](https://aka.ms/ddosalert) 可部署已扩充的 DDoS 缓解警报的必要组件： Azure Monitor 警报规则、操作组和逻辑应用。 此过程的结果是一封电子邮件警报，其中包含有关受攻击的 IP 地址的详细信息，包括与 IP 关联的资源的相关信息。 资源的所有者将作为电子邮件的收件人以及安全团队添加。 还会执行基本应用程序可用性测试，并将结果包含在电子邮件警报中。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>通过门户配置警报

利用 Azure Monitor 警报配置，可以选择任何可用的 DDoS 防护指标，以在攻击期间发生活跃的风险缓解时发出警报。 

1. 登录到 [Azure 门户](https://portal.azure.com/) 并浏览到 DDoS 保护计划。
2. 在“监视”下，选择“指标”。
3. 在灰色导航栏中，选择 " **新建警报规则**"。 
4. 输入或选择自己的值，或输入以下示例值，接受其余默认值，然后选择 " **创建警报规则**"：

    |设置                  |值                                                                                               |
    |---------                |---------                                                                                           |
    | 范围                   | 选中“选择资源”。 </br> 选择包含要记录的公共 IP 地址的 **订阅**，选择 "**资源类型** 的 **公共 ip 地址**"，然后选择要为其记录指标的特定公共 ip 地址。 </br> 选择“完成”。 | 
    | 条件 | 选择 " **选择条件**"。 </br> 在 "信号名称" 下，选择 **"DDoS 攻击"**。 </br> 在 " **运算符**" 下，选择 " **大于或等于**"。 </br> 在 " **聚合类型**" 下，选择 " **最大值**"。 </br> 在 " **阈值**" 下，输入 *1*。 对于 " **DDoS 攻击" 或 "非** 指标"， **0** 表示未受到攻击，而 **1** 表示受到攻击。 </br> 选择“完成”。 | 
    | 操作 | 选择 " **添加操作组**"。 </br> 选择“创建操作组”。 </br> 在 " **通知**" 下的 " **通知类型**" 下，选择 " **电子邮件/短信/推送/语音**"。 </br> 在 " **名称**" 下输入 _MyUnderAttackEmailAlert_。 </br> 单击 "编辑" 按钮，选择 " **电子邮件** "，并选择所需的以下任意选项，然后选择 **"确定"**。 </br> 选择“查看 + 创建”。 | 
    | 警报规则详细信息 | 在 " **警报规则名称**" 下输入 _MyDdosAlert_。 |

在检测到几分钟后，你应该会收到一封来自 Azure Monitor 度量值的电子邮件，如下图所示：

![攻击警报](./media/manage-ddos-protection/ddos-alert.png)

还可以了解有关为创建警报而[配置 webhook](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和[逻辑应用](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的详细信息。

## <a name="view-alerts-in-azure-security-center"></a>查看 Azure 安全中心的警报

Azure 安全中心提供 [安全警报](../security-center/security-center-managing-and-responding-alerts.md)的列表，其中包含有助于调查和修正问题的信息。 利用此功能，可以获得警报的统一视图，包括与 DDoS 攻击相关的警报，以及在短期内缓解攻击所采取的措施。
你将看到两个特定警报，你可以看到这些警报是为了进行 DDoS 攻击检测和缓解：

- **检测到公共 ip 的 DDoS 攻击**：当 ddos 防护服务检测到某个公共 ip 地址是 DDoS 攻击的目标时，会生成此警报。
- **公共 ip 的 DDoS 攻击缓解**：当已缓解对公共 ip 地址的攻击时，将生成此警报。
若要查看警报，请在 Azure 门户中打开 " **安全中心** "。 在 " **威胁防护**" 下，选择 " **安全警报**"。 以下屏幕截图显示了 DDoS 攻击警报的示例。

![Azure 安全中心中的 DDoS 警报](./media/manage-ddos-protection/ddos-alert-asc.png)

这些警报包含有关受攻击、异地和威胁智能信息以及修正步骤的公共 IP 地址的一般信息。

## <a name="validate-and-test"></a>验证并测试

若要模拟 DDoS 攻击来验证警报，请参阅 [验证 ddos 检测](test-through-simulations.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

- 通过 Azure Monitor 配置警报
- 通过门户配置警报
- 查看 Azure 安全中心的警报
- 验证并测试警报

若要了解如何测试和模拟 DDoS 攻击，请参阅模拟测试指南：

> [!div class="nextstepaction"]
> [通过模拟进行测试](test-through-simulations.md)
