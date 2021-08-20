---
title: 简介
description: 了解使用 Azure VMware 解决方案在 Azure 中部署和管理基于 VMware 的工作负载的功能和优势。 Azure VMware 解决方案 SLA 保证 Azure VMware 管理工具（vCenter Server 和 NSX 管理器）将在至少 99.9% 的时间内可用。
ms.topic: overview
ms.date: 04/20/2021
ms.openlocfilehash: 79739f230b057b030da7a90501e9fb3fb2630eba
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113433573"
---
# <a name="what-is-azure-vmware-solution"></a>什么是 Azure VMware 解决方案？

Azure VMware 解决方案为你提供了私有云，这些私有云包含基于专用裸机 Azure 基础结构构建的 vSphere 群集。 最低初始部署为三个主机，但可以逐个添加更多的主机，每个群集最多可以包含 16 个主机。  所有预配的私有云都具有 vCenter Server、vSAN、vSphere 和 NSX-T。 可以从本地环境迁移工作负载、部署新的虚拟机 (VM)，并从私有云使用 Azure 服务。  Azure VMware 管理工具（vCenter Server 和 NSX 管理器）将在至少 99.9% 的时间内可用。 有关详细信息，请参阅 [Azure VMware 解决方案 SLA](https://aka.ms/avs/sla)。

Azure VMware 解决方案是经过 VMware 验证的解决方案，我们会持续对增强和升级进行验证和测试。 Microsoft 管理和维护私有云基础结构和软件。 因此，你可以专注于在私有云中开发和运行工作负载。 

此图显示了 Azure、Azure 服务和本地环境中的私有云与 VNet 之间的邻近性。 从私有云对 Azure 服务或 VNet 进行网络访问可以提供 SLA 驱动的 Azure 服务终结点集成。 ExpressRoute Global Reach 将本地环境连接到 Azure VMware 解决方案私有云。 

:::image type="content" source="media/adjacency-overview-drawing-final.png" alt-text="Azure VMware 解决方案私有云与 Azure 和本地的邻近性图表。" border="false":::

## <a name="hosts-clusters-and-private-clouds"></a>主机、群集和私有云

Azure VMware 解决方案私有云和群集是从裸机超融合 Azure 基础设施主机构建的。 高端主机具有 576 GB RAM 和 Intel 18 核 2.3 GHz 双处理器。 HE 主机具有两个 vSAN 磁盘组，其中采用 15.36 TB (SSD) 的原始 vSAN 容量层和 3.2 TB (NVMe) vSAN 缓存层。

通过 Azure 门户或 Azure CLI 部署新的私有云。

## <a name="networking"></a>网络

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

有关详细信息，请参阅[网络概念](concepts-networking.md)。

## <a name="access-and-security"></a>访问和安全性

为了增强安全性，Azure VMware 解决方案私有云使用 vSphere 基于角色的访问控制。 你可以将 vSphere SSO LDAP 功能与 Azure Active Directory 集成。 有关详细信息，请参阅[访问权限和标识的概念](concepts-identity.md)。  

默认已启用 vSAN 静态数据加密，此功能用于提供 vSAN 数据存储安全性。 有关详细信息，请参阅[存储概念](concepts-storage.md)。

## <a name="host-and-software-lifecycle-maintenance"></a>主机和软件生命周期维护

定期升级 Azure VMware 解决方案私有云和 VMware 软件可确保在私有云中运行的安全性、稳定性和功能集是最新的。 有关详细信息，请参阅[主机维护和生命周期管理](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)。

## <a name="monitoring-your-private-cloud"></a>监视私有云

将 Azure VMware 解决方案部署到订阅后，系统会自动生成 [Azure Monitor 日志](../azure-monitor/overview.md)。 

在私有云中，你可以：
- 收集每个 VM 上的日志。
- 在 Linux 和 Windows VM 上[下载并安装 MMA 代理](../azure-monitor/agents/log-analytics-agent.md#installation-options)。
- 启用 [Azure 诊断扩展](../azure-monitor/agents/diagnostics-extension-overview.md)。
- [创建并运行新查询](../azure-monitor/logs/data-platform-logs.md#log-queries)。
- 运行通常在 VM 上运行的相同查询。

Azure VMware 解决方案中的监视模式类似于 IaaS 平台中的 Azure VM。 有关详细信息和操作说明，请参阅[使用 Azure Monitor 监视 Azure VM](../azure-monitor/vm/monitor-vm-azure.md)。

## <a name="customer-communication"></a>客户通信
[!INCLUDE [customer-communications](includes/customer-communications.md)]

## <a name="next-steps"></a>后续步骤

下一步是了解关键的[私有云和群集概念](concepts-private-clouds-clusters.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md

