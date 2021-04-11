---
title: 概念 - 存储
description: 了解 Azure VMware 解决方案私有云中的密钥存储功能。
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: 83a4a52f8414e656b5bc688796db6e93a53d4d76
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801615"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Azure VMware 解决方案存储概念

Azure VMware 解决方案私有云提供具有 VMware vSAN 的本机、群集范围内的存储。 群集中每个主机的所有本地存储都用于 vSAN 数据存储，并且静态数据加密在默认情况下可用且已启用。 可以使用 Azure 存储资源扩展私有云的存储功能。

## <a name="vsan-clusters"></a>vSAN 群集

每个群集主机中的本地存储用作 vSAN 数据存储的一部分。 所有磁盘组都使用 1.6 TB 的 NVMe 缓存层和 15.4 TB 的原始每主机 SSD 容量层。 群集的原始容量层大小是每个主机容量乘以主机数。 例如，四主机群集会在 vSAN 容量层中提供 61.6 TB 的原始容量。

群集主机中的本地存储用于群集范围内的 vSAN 数据存储。 所有数据存储都创建为私有云部署的一部分，可立即使用。 cloudadmin 用户和 CloudAdmin 组中的所有用户都可以管理具有以下 vSAN 权限的数据存储：

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>静态数据加密

默认情况下，vSAN 数据存储使用静态数据加密。 加密解决方案基于 KMS，并支持用于密钥管理的 vCenter 操作。 密钥由 Azure Key Vault 主密钥存储、加密和包装。 从群集中删除主机时，SSD 上的数据将立即失效。

## <a name="scaling"></a>扩展

通过将主机添加到群集来扩展本机群集存储容量。 对于使用 AVS36 主机的群集，将为每个添加的主机增加 15.4 TB 的原始群集范围内的容量。 主机大约需要 10 分钟才能添加到群集。  有关扩展群集的说明，请参阅[扩展私有云教程][tutorial-scale-private-cloud]。

## <a name="azure-storage-integration"></a>Azure 存储集成

可以在私有云中运行的工作负载中使用 Azure 存储服务。 Azure 存储服务包括存储帐户、表存储和 Blob 存储。 将工作负载连接到 Azure 存储服务不会遍历 Internet。 此连接提供更高的安全性，使你能够在私有云工作负载中使用基于 SLA 的 Azure 存储服务。

## <a name="next-steps"></a>后续步骤

现在，你已了解 Azure VMware 解决方案存储概念，建议你了解以下内容：

- [私有云标识概念](concepts-identity.md)。
- [适用于 Azure VMware 解决方案的 vSphere 基于角色的访问控制](concepts-identity.md)。
- [如何启用 Azure VMware 解决方案资源](enable-azure-vmware-solution.md)。
- [将 Azure NetApp 文件与 Azure VMware 解决方案配合使用](netapp-files-with-azure-vmware-solution.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
