---
title: 概念 - 存储
description: 了解 Azure VMware 解决方案私有云中的存储容量、存储策略、容错和存储集成。
ms.topic: conceptual
ms.custom: contperf-fy21q4
ms.date: 07/28/2021
ms.openlocfilehash: ae37e0147ea03f91c2af68b8733a1702a02f81f3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729012"
---
# <a name="azure-vmware-solution-storage-concepts"></a>Azure VMware 解决方案存储概念

Azure VMware 解决方案私有云提供具有 VMware vSAN 的本机、群集范围内的存储。 群集中每个主机的本地存储都用于 vSAN 数据存储，并且静态数据加密在默认情况下可用且已启用。 可以使用 Azure 存储资源扩展私有云的存储功能。

## <a name="vsan-clusters"></a>vSAN 群集

每个群集主机中的本地存储用作 vSAN 数据存储的一部分。 所有磁盘组都使用 1.6 TB 的 NVMe 缓存层和 15.4 TB 的原始每主机 SSD 容量层。 群集的原始容量层大小是每个主机容量乘以主机数。 例如，四主机群集在 vSAN 容量层中提供 61.6 TB 的原始容量。

群集主机中的本地存储用于群集范围内的 vSAN 数据存储。 所有数据存储都创建为私有云部署的一部分，可立即使用。 cloudadmin 用户和分配到了 CloudAdmin 角色的所有用户都可以通过以下 vSAN 权限管理数据存储：

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

>[!IMPORTANT]
>不能更改数据存储或群集的名称。 如果从门户以外的地方（AzureCLI 或 PowerShell）进行预配时，可以选择“Cluster-n ”以外的群集名称，其中 n > 1。

## <a name="storage-policies-and-fault-tolerance"></a>存储策略和容错

该默认存储策略设置为 RAID-1（镜像）、FTT-1 和厚预配。  除非调整存储策略或应用新策略，否则群集将继续随此配置增长。 在三主机群集中，FTT-1 用于容纳单个主机的失败。 Microsoft 会定期控制失败，并在从体系结构角度检测到事件时替换硬件。

:::image type="content" source="media/concepts/vsphere-vm-storage-policies.png" alt-text="显示 vSphere 客户端 VM 存储策略的屏幕截图。":::


|预配类型  |说明  |
|---------|---------|
|**厚**      | 属于预留或预分配的存储空间。 因为空间已预留，该类型通过允许系统在 vSAN 数据存储已满的情况下仍能运行来保护系统。 例如，如果使用厚预配创建 10 GB 虚拟磁盘，则会在虚拟磁盘的物理存储上预分配虚拟磁盘的全部存储容量，并使用数据存储中为其分配的所有空间。 它不会允许其他虚拟机 (VM) 共用数据存储中的空间。         |
|“精简”      | 使用最初所需的空间，并增长到满足数据存储中使用的数据空间需求。 在默认（厚预配）之外，可以使用 FTT-1 精简预配创建 VM。 对于重复数据设置，请将精简预配用于 VM 模板。         |

>[!TIP]
>如果不确定群集是否会增长到 4 或更大，请使用默认策略进行部署。  如果确定群集会增长，建议在部署过程中部署额外的主机，而不是在初始部署后再扩展群集。 在 VM 部署到群集后，将 VM 设置中的磁盘存储策略更改为 RAID-5 FTT-1 或 RAID-6 FTT-2。 
>
>:::image type="content" source="media/concepts/vsphere-vm-storage-policies-2.png" alt-text="显示 RAID-5 FTT-1 和 RAID-6 Ftt-2 选项的屏幕截图。":::


## <a name="data-at-rest-encryption"></a>静态数据加密

vSAN 数据存储默认使用 Azure Key Vault 中存储的密钥进行静态数据加密。 加密解决方案基于 KMS，并支持用于密钥管理的 vCenter 操作。  从群集中删除主机时，SSD 上的数据将立即失效。

## <a name="azure-storage-integration"></a>Azure 存储集成

可以在私有云中运行的工作负载中使用 Azure 存储服务。 Azure 存储服务包括存储帐户、表存储和 Blob 存储。 将工作负载连接到 Azure 存储服务不会遍历 Internet。 此连接提供更高的安全性，使你能够在私有云工作负载中使用基于 SLA 的 Azure 存储服务。

## <a name="alerts-and-monitoring"></a>警报和监视

容量消耗超过 75% 时，Microsoft 将提供警报。  可以监视集成到 Azure Monitor 中的容量消耗指标。 有关详细信息，请参阅[在 Azure VMware 解决方案中配置 Azure 警报](configure-alerts-for-azure-vmware-solution.md)。

## <a name="next-steps"></a>后续步骤

现在，你已了解 Azure VMware 解决方案存储概念，建议你了解以下内容：

- [将磁盘池附加到 Azure VMware 解决方案（预览版）](attach-disk-pools-to-azure-vmware-solution-hosts.md) - 可以使用磁盘作为 Azure VMware 解决方案的持久性存储以实现最佳成本和性能。
- [缩放私有云中的群集][tutorial-scale-private-cloud] - 可以根据应用程序工作负载的需求，缩放私有云中的群集和主机。 应该逐一解决特定服务的性能和可用性限制。
- [Azure NetApp 文件与 Azure VMware 解决方案](netapp-files-with-azure-vmware-solution.md) - 可使用 Azure NetApp 迁移和运行云中要求最苛刻的企业文件工作负荷：数据库、SAP 和高性能计算应用程序，而无需更改代码。 
- [适用于 Azure VMware 解决方案的 vSphere 基于角色的访问控制](concepts-identity.md) - 可使用 vCenter 管理 VM 工作负载以及使用 NSX-T Manager 管理和扩展私有云。 访问和身份管理对 vCenter 使用 CloudAdmin 角色，对 NSX-T Manager 使用受限的管理员权限。


<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
