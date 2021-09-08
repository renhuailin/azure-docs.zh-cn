---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 04/07/2021
ms.topic: include
ms.openlocfilehash: 24f466e7c9a9e5630510f73652fcf515540bee10
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646567"
---
Azure Defender 为容器化环境提供实时威胁防护，并针对可疑活动发出警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

Azure Defender 可提供不同级别的威胁防护： 

* **主机级别（由适用于服务器的 Azure Defender 提供）** - Azure Defender 利用安全中心在其他 VM 上使用的同一 Log Analytics 代理监视你的 Linux Kubernetes 节点中是否存在可疑活动，例如 Web 外壳检测和与已知的可疑 IP 地址进行连接。 该代理还会监视特定于容器的分析，例如创建特权容器、以可疑方式访问 API 服务器以及在 Docker 容器内部运行安全外壳 (SSH) 服务器。

    如果你选择不在主机上安装代理，则只能收到一部分威胁防护权益和安全警报。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。

    >[!IMPORTANT]
    > 目前，我们不支持在虚拟机规模集上运行的 Azure Kubernetes 服务群集上安装 Log Analytics 代理。

    有关主机级别的警报列表，请参阅[警报参考表](../articles/security-center/alerts-reference.md#alerts-containerhost)。


* **群集级别（由 Azure Defender for Kubernetes 提供）** - 在群集级别，威胁防护基于对 Kubernetes 审核日志的分析。 要启用此无代理监视，请启用 Azure Defender。 如果群集位于本地或其他云提供商上，请启用[已启用 Arc 的 Kubernetes 和 Azure Defender 扩展](../articles/security-center/defender-for-kubernetes-azure-arc.md)。

    若要在此级别生成警报，Azure Defender 会监视群集的日志。 此级别的事件示例包括公开 Kubernetes 仪表板、创建高特权角色，以及创建敏感的装入点。

    >[!NOTE]
    > Azure Defender 会针对在订阅上启用 Azure Defender for Kubernetes 计划后发生的操作和部署生成安全警报。 

    有关群集级别的警报列表，请参阅[警报参考表](../articles/security-center/alerts-reference.md#alerts-k8scluster)。

此外，我们的全球安全研究团队会不断监视威胁态势。 一旦发现威胁，他们就会添加容器特定的警报和漏洞。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。
