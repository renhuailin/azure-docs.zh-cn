---
title: 针对 Service Fabric 托管群集节点选择托管磁盘类型
description: 了解如何为 Service Fabric 托管群集节点选择托管磁盘类型，并在 ARM 模板中进行配置。
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: c9daef02b9249d44d5d202f5066bac40f807ec58
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546323"
---
# <a name="select-managed-disk-types-for-service-fabric-managed-cluster-nodes"></a>针对 Service Fabric 托管群集节点选择托管磁盘类型

Azure Service Fabric 托管群集使用托管磁盘满足所有存储需求（包括应用程序数据），以实现可靠集合和参与者等方案。 Azure 托管磁盘是由 Azure 托管并与 Azure 虚拟机配合使用的块级存储卷。 托管磁盘类似于本地服务器中的物理磁盘，但它是虚拟化的。 使用托管磁盘时，只需指定磁盘大小、磁盘类型并预配磁盘即可。 预配此磁盘后，Azure 会处理剩余磁盘。 有关托管磁盘的详细信息，请参阅 [Azure 托管磁盘简介](../virtual-machines/managed-disks-overview.md)。

## <a name="managed-disk-types"></a>托管磁盘类型

Azure Service Fabric 托管群集支持以下托管磁盘类型：
* 标准 HDD
    * 标准 HDD，本地冗余存储。 最适合备份、非关键和不频繁的访问。 
* 标准 SSD 默认
    * 标准 SSD，本地冗余存储。 最适合 Web 服务器、很少使用的企业应用程序和开发/测试。
* 高级 SSD 与特定 VM 大小兼容，请参阅[高级 SSD](../virtual-machines/disks-types.md#premium-ssd) 了解详细信息
    * 高级 SSD，本地冗余存储。 最适合生产和性能敏感的工作负载。

>[!NOTE]
> 与 VM 大小关联的任何临时磁盘都不会用于存储任何 Service Fabric 或与应用程序相关的数据

## <a name="specifying-a-service-fabric-managed-cluster-disk-type"></a>指定 Service Fabric 托管群集磁盘类型

若要指定 Service Fabric 托管群集磁盘类型，必须在托管群集资源定义中包括以下值。

* dataDiskType 值属性，指定用于节点的托管磁盘类型。

可能的值包括：
* “Standard_LRS”
* “StandardSSD_LRS”
* “Premium_LRS”
>[!NOTE]
> 并非所有托管磁盘类型都适用于所有 VM 大小，有关详细信息，请参阅 [Azure 有哪些可用的磁盘类型？](../virtual-machines/disks-types.md)

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "dataDiskType": "StandardSSD_LRS"
}
```

包含此规范的可用示例模板如下：[Service Fabric 托管群集模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)
