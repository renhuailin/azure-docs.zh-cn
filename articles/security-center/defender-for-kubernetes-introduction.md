---
title: 适用于 Kubernetes 的 Azure Defender - 优点和功能
description: 了解适用于 Kubernetes 的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1f013f22b482c1e1d093f106bd786be870103f3d
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008496"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>适用于 Kubernetes 的 Azure Defender 简介

Azure Kubernetes 服务 (AKS) 是 Microsoft 的托管服务，用于开发、部署和管理容器化应用程序。

Azure 安全中心和 AKS 构成了一种云原生 Kubernetes 安全产品/服务，同时提供环境强化功能、工作负载保护和运行时间保护，如[安全中心中的容器安全](container-security.md)所述。

若要对 Kubernetes 群集进行威胁检测，请启用适用于 Kubernetes 的 Azure Defender。

如果启用[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 及其 Log Analytics 代理，则可以使用适用于 Linux AKS 节点的主机级威胁检测。 但是，如果在虚拟机规模集上部署 AKS 群集，则当前不支持 Log Analytics 代理。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|适用于 Kubernetes 的 Azure Defender 按[定价页](security-center-pricing.md)中的定价计费|
|所需角色和权限：|**安全管理员** 可以消除警报。<br>**安全读取者** 可以查看结果。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>适用于 Kubernetes 的 Azure Defender 有哪些优点？

Azure Defender for Kubernetes 通过 Azure Kubernetes 服务 (AKS) 检索到的日志来监视 AKS 托管服务，从而提供群集级别的威胁防护。

Azure Defender for Kubernetes 监视的安全事件示例包括公开 Kubernetes 仪表板、创建高特权角色，以及创建敏感的装入点。 有关 AKS 群集级警报的完整列表，请参阅[警报引用表](alerts-reference.md#alerts-akscluster)。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。

此外，我们的全球安全研究团队会不断监视威胁态势。 一旦发现威胁，他们就会添加容器特定的警报和漏洞。

>[!NOTE]
> 安全中心针对在启用 Azure Defender for Kubernetes 后发生的 Azure Kubernetes 服务操作和部署生成安全警报。




## <a name="azure-defender-for-kubernetes---faq"></a>适用于 Kubernetes 的 Azure Defender - 常见问题解答

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>如果没有 Log Analytics 代理，是否仍可获得 AKS 保护？

Azure Defender for Kubernetes 计划在群集级别提供保护。 如果还部署适用于服务器的 Azure Defender 的 Log Analytics 代理，则将获得该计划随附的用于节点的威胁防护功能。 有关详细信息，请参阅[适用于服务器的 Azure Defender 简介](defender-for-servers-introduction.md)。

建议两者同时部署，以实现最完整的保护。

如果你选择不在主机上安装代理，则只能收到一部分威胁防护权益和安全警报。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS 是否允许我在 AKS 节点上安装自定义 VM 扩展？
为使 Azure Defender 能够监视 AKS 节点，它们必须运行 Log Analytics 代理。 

AKS 是一项托管服务，由于 Log Analytics 代理是 Microsoft 托管的扩展，因此也受 AKS 群集支持。

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>如果我的群集已经在运行用于容器的 Azure Monitor 代理，我是否也需要 Log Analytics 代理？
为使 Azure Defender 能够监视 AKS 节点，它们必须运行 Log Analytics 代理。

如果群集已经在运行用于容器的 Azure Monitor 代理，则也可以安装 Log Analytics 代理，这两个代理可以彼此协同工作，而不会出现任何问题。

[详细了解用于容器的 Azure Monitor 代理](../azure-monitor/insights/container-insights-manage-agent.md)。


## <a name="next-steps"></a>后续步骤

本文介绍了安全中心的 Kubernetes 保护，包括适用于 Kubernetes 的 Azure Defender。 

> [!div class="nextstepaction"]
> [启用 Azure Defender](security-center-pricing.md#enable-azure-defender)

如需相关材料，请参阅以下文章： 

- [将警报流式传输到 SIEM、SOAR 或 IT 服务管理解决方案](export-to-siem.md)
- [警报的引用表](alerts-reference.md)
