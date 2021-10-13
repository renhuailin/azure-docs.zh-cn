---
title: VMware 软件更新频率
description: Azure VMware 解决方案支持的 VMware 软件更新频率。
ms.topic: include
ms.date: 08/24/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 297fc81559adc9bb3f9c22091ca568da36cffb8f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638219"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Azure VMware 解决方案私有云的一个优点是可以为你维护平台。  Microsoft 负责管理 VMware 软件（ESXi、vCenter 和 vSAN）的生命周期。 Microsoft 还负责管理 NSX-T 设备的生命周期及启动网络配置，如创建第 0 层网关和启用北-南路由。 你负责配置 NSX-T SDN：网段、分布式防火墙规则、第 1 层网关和负载均衡器。 

Microsoft 负责将任何补丁、更新或升级应用于私有云中的 ESXi、vCenter、vSAN 和 NSX-T。 补丁、更新和升级对 ESXi、vCenter 和 NSX-T 的影响各不相同。 

- ESXi - 私有云中运行的工作负载不会受到影响。 在此期间，不会阻止对 vCenter 和 NSX-T 的访问。  建议在这段时间内不要在私有云中计划任何其他活动，例如纵向扩展私有云、创建新网络等。

- vCenter - 私有云中运行的工作负载不会受到影响。 在此期间，vCenter 将不可用，你将无法管理 VM（停止、启动、创建或删除）。 建议在这段时间内不要规划任何其他活动，例如纵向扩展私有云、创建新网络，等等。

- NSX-T - 升级特定主机时对工作负载有影响，该主机上的 VM 可能会因下列任何症状或所有症状而失去连接，时间从 2 秒到最长 1 分钟不等：

   - Ping 错误

   - 数据包丢失

   - 错误消息（例如，目标主机无法访问以及 Net 无法访问） 

   在此升级时段内，将阻止对 NSX-T 管理平面的所有访问。 在该时段内，无法对 NSX-T 环境进行配置更改。  但是，工作负载将继续正常运行，具体取决于上文详细介绍的升级影响。
 
   建议在升级时间段内不要在私有云中计划任何其他活动，例如纵向扩展私有云等。 这可能会阻止启动升级，或者可能会对升级和环境产生不利影响。
 
将补丁/更新或升级应用到私有云之前，你会收到通知。 在将更新或升级应用到私有云之前，我们还将与你共同计划维护时段。


软件更新包括：

- 补丁 - VMware 发布的安全补丁或 bug 修补程序

- 更新 - VMware 堆栈组件的次要版本更改

- 升级 - VMware 堆栈组件的主要版本更改

>[!NOTE]
>Microsoft 可以在 VMware 发布关键安全补丁后立即对其进行测试。

在部署下一个计划的更新之前，将会实施有文档记录的 VMware 解决方法，而不是安装相应的补丁。
