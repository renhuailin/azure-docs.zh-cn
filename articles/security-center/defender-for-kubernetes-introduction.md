---
title: 适用于 Kubernetes 的 Azure Defender - 优点和功能
description: 了解适用于 Kubernetes 的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 07/20/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 53c5ef179a3df860d97cbe31287f2defcec2411b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750713"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>适用于 Kubernetes 的 Azure Defender 简介

Azure Defender for Kubernetes 是 Azure Defender 计划，无论 Kubernetes 群集在哪里运行，都可为其提供保护。 

可以保护：

- **Azure Kubernetes 服务 (AKS)** 中的群集；AKS 是 Microsoft 的托管服务，用于开发、部署和管理容器化应用程序

- **本地和多云环境** 中的群集；可 [为已启用 Arc 的 Kubernetes 使用扩展](defender-for-kubernetes-azure-arc.md)

Azure 安全中心和 AKS 构成了一种云原生 Kubernetes 安全产品/服务，同时提供环境强化功能、工作负载保护和运行时间保护，如[安全中心中的容器安全](container-security.md)所述。

如果启用[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 及其 Log Analytics 代理，则可以使用适用于 Linux AKS 节点的主机级威胁检测。 但如果在 Azure Kubernetes 服务虚拟机规模集 (VMSS) 上部署群集，则当前不支持 Log Analytics 代理。



## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|用于 Kubernetes 的 Azure Defender 按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中显示的定价计费|
|所需角色和权限：|**安全管理员** 可以消除警报。<br>**安全读取者** 可以查看结果。|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 国家/地区/主权云（Azure 政府、Azure 中国世纪互联）|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>适用于 Kubernetes 的 Azure Defender 有哪些优点？

Azure Defender for Kubernetes 通过监视群集日志来提供群集级别的威胁防护。

Azure Defender for Kubernetes 监视的安全事件示例包括公开 Kubernetes 仪表板、创建高特权角色，以及创建敏感的装入点。 如需群集级警报的完整列表，请参阅[警报的引用表](alerts-reference.md#alerts-k8scluster)。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。

此外，我们的全球安全研究团队会不断监视威胁态势。 一旦发现威胁，他们就会添加容器特定的警报和漏洞。

>[!NOTE]
> Azure Defender 会针对在订阅上启用 Defender for Kubernetes 计划后发生的操作和部署生成安全警报。




## <a name="faq---azure-defender-for-kubernetes"></a>常见问题解答 - Azure Defender for Kubernetes

- [如果没有 Log Analytics 代理，是否仍可以获得群集保护？](#can-i-still-get-cluster-protections-without-the-log-analytics-agent)
- [AKS 是否允许我在 AKS 节点上安装自定义 VM 扩展？](#does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes)
- [如果我的群集已经在运行用于容器的 Azure Monitor 代理，我是否也需要 Log Analytics 代理？](#if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too)
- [Azure Defender for Kubernetes 是否支持包含 VMSS 节点的 AKS？](#does-azure-defender-for-kubernetes-support-aks-with-vmss-nodes)

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>如果没有 Log Analytics 代理，是否仍可以获得群集保护？

Azure Defender for Kubernetes 计划在群集级别提供保护。 如果还部署适用于服务器的 Azure Defender 的 Log Analytics 代理，则将获得该计划随附的用于节点的威胁防护功能。 有关详细信息，请参阅[适用于服务器的 Azure Defender 简介](defender-for-servers-introduction.md)。

建议两者同时部署，以实现最完整的保护。

如果你选择不在主机上安装代理，则只能收到一部分威胁防护权益和安全警报。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS 是否允许我在 AKS 节点上安装自定义 VM 扩展？
为使 Azure Defender 能够监视 AKS 节点，它们必须运行 Log Analytics 代理。

AKS 是一项托管服务，由于 Log Analytics 代理是 Microsoft 托管的扩展，因此也受 AKS 群集支持。

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>如果我的群集已经在运行用于容器的 Azure Monitor 代理，我是否也需要 Log Analytics 代理？
为使 Azure Defender 能够监视节点，它们必须运行 Log Analytics 代理。

如果群集已经在运行用于容器的 Azure Monitor 代理，则也可以安装 Log Analytics 代理，这两个代理可以彼此协同工作，而不会出现任何问题。

[详细了解用于容器的 Azure Monitor 代理](../azure-monitor/containers/container-insights-manage-agent.md)。


### <a name="does-azure-defender-for-kubernetes-support-aks-with-vmss-nodes"></a>Azure Defender for Kubernetes 是否支持包含 VMSS 节点的 AKS？
如果在 Azure Kubernetes 服务虚拟机规模集 (VMSS) 上部署群集，则当前不支持 Log Analytics 代理。



## <a name="next-steps"></a>后续步骤

本文介绍了安全中心的 Kubernetes 保护，包括适用于 Kubernetes 的 Azure Defender。 

> [!div class="nextstepaction"]
> [启用 Azure Defender](enable-azure-defender.md)

如需相关材料，请参阅以下文章： 

- [将警报流式传输到 SIEM、SOAR 或 IT 服务管理解决方案](export-to-siem.md)
- [警报的引用表](alerts-reference.md)
