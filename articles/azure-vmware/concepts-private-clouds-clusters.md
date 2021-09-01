---
title: 概念 - 私有云和群集
description: 了解 Azure VMware 解决方案软件定义的数据中心和 vSphere 群集的主要功能。
ms.topic: conceptual
ms.date: 08/25/2021
ms.openlocfilehash: 6e068462cada0a324d646766574e05414ad92772
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968504"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware 解决方案私有云和群集概念

Azure VMware 解决方案可在 Azure 中提供基于 VMware 的私有云。 私有云硬件和软件部署可在 Azure 中实现完全集成和自动化。 通过 Azure 门户、CLI 或 PowerShell 部署和管理私有云。  

私有云包括具有以下内容的群集：

- 使用 VMware ESXi 虚拟机监控程序预配的专用裸机服务器主机 
- 用于管理 ESXi 和 vSAN 的 vCenter Server 
- 软件定义的 VMware NSX-T 网络，适用于 vSphere 工作负载 VM  
- VMware vSAN 数据存储，适用于 vSphere 工作负荷 VM  
- 用于工作负载移动性的 VMware HCX  
- Azure 底层中的资源（连接和操作私有云所需）

与其他资源一样，私有云在 Azure 订阅中进行安装和管理。 订阅中的私有云数量是可缩放的。 最初，每个订阅的私有云数限制为一个。  Azure 订阅、Azure VMware 解决方案私有云、vSAN 群集和主机之间存在逻辑关系。 

此关系图显示的是一项 Azure 订阅，其中包含两个分别代表开发和生产环境的私有云。 其中每个私有云都是两个群集。 

:::image type="content" source="media/concepts/hosts-clusters-private-clouds-final.png" alt-text="显示的图显示的是一项 Azure 订阅，其中包含两个分别代表开发和生产环境的私有云。" border="false":::

## <a name="hosts"></a>主机

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="clusters"></a>群集

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]

[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="vmware-software-versions"></a>VMware 软件版本

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>主机维护和生命周期管理



[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-monitoring-and-remediation"></a>主机监视和修正

Azure VMware 解决方案可持续监视底层和 VMware 组件的运行状况。 当 Azure VMware 解决方案检测到故障时，其将采取措施来修复出现故障的组件。 当 Azure VMware 解决方案在 Azure VMware 解决方案节点上检测到性能下降或故障时，其将触发主机修正进程。 

主机修正涉及到将错误节点替换为群集中新的健康节点。 然后，如有可能，故障主机会处于 VMware vSphere 维护模式。 VMware vMotion 会将 VM 从故障主机移到群集中的其他可用服务器上，这可能会导致工作负载的实时迁移出现零停机时间这一情况。 如果故障主机无法处于维护模式，则主机将从群集中删除。

Azure VMware 解决方案将监视主机上的以下情况：  

- 处理器状态 
- 内存状态 
- 连接和电源状态 
- 硬件风扇状态 
- 网络连接丢失 
- 硬件系统板状态 
- vSAN 主机上的磁盘出现错误 
- 硬件电压 
- 硬件温度状态 
- 硬件电源状态 
- 存储状态 
- 连接失败 

> [!NOTE]
> Azure VMware 解决方案租户管理员不得编辑或删除上文定义的 VMware vCenter 警报，因为这些警报由 vCenter 上的 Azure VMware 解决方案控制平面进行管理。 Azure VMware 解决方案监视可使用这些警报来触发 Azure VMware 解决方案主机修正进程。

## <a name="backup-and-restoration"></a>备份和还原

私有云 vCenter 和 NSX-T 配置采用每小时备份计划。  备份会保留三天。 如果需要从备份恢复，请在 Azure 门户中打开[支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)以请求恢复。

Azure VMware 解决方案可持续监视底层和 VMware 组件的运行状况。 当 Azure VMware 解决方案检测到故障时，其将采取措施来修复出现故障的组件。

## <a name="next-steps"></a>后续步骤

现在，你已了解 Azure VMware 解决方案私有云概念，建议你了解以下内容： 

- [Azure VMware 解决方案网络和互连概念](concepts-networking.md)
- [Azure VMware 解决方案存储概念](concepts-storage.md)
- [如何启用 Azure VMware 解决方案资源](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

