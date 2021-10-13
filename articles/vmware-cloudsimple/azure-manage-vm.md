---
title: Azure VMware Solution by CloudSimple - 在 Azure 中管理私有云 VM
description: 介绍如何在 Azure 门户中管理 CloudSimple 私有云 VM，包括添加磁盘、更改 VM 容量和添加网络接口
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 26f35e9b34278a5fe7aaacbfef5672cd5b5c8d80
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618150"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>在 Azure 中管理 CloudSimple 私有云虚拟机

若要管理[为 CloudSimple 私有云创建](azure-create-vm.md)的虚拟机，请登录到 [Azure 门户](https://portal.azure.com)。 搜索并选择虚拟机（在边侧菜单中的“所有服务”或“虚拟机”下进行搜索） 。

## <a name="control-virtual-machine-operation"></a>控制虚拟机操作

所选虚拟机的“概述”页中提供了以下控件。

| 控制 | 说明 |
| ------------ | ------------- |
| 连接 | 连接到指定的 VM。  |
| 开始 | 启动指定的 VM。  |
| 重启 | 关闭然后打开指定的 VM。  |
| 停止 | 关闭特定的 VM。  |
| 捕获 | 捕获指定 VM 的映像，以便可以使用该映像创建其他 VM。 请参阅[在 Azure 中创建通用 VM 的托管映像](../virtual-machines/windows/capture-image-resource.md)。   |
| 移动 | 移到指定的 VM。  |
| 删除 | 删除指定的 VM。  |
| 刷新 | 刷新显示画面中的数据。  |

### <a name="view-performance-information"></a>查看性能信息

“概述”页下半区域中的图表显示所选时间间隔（过去一小时至过去 30 天；默认值为过去一小时）的性能数据。 在每个图表中，可以通过在图表上方来回移动光标，显示该时间间隔内任何时间的数值。

将显示以下图表。

| 项 | 说明 |
| ------------ | ------------- |
| CPU (平均值) | 所选时间间隔内的平均 CPU 利用率（以百分比表示）。   |
| 网络 | 所选时间间隔内传入和传出网络的流量 (MB)。  |
| 磁盘字节数 | 所选时间间隔内从磁盘读取和写入磁盘的总数据量 (MB)。  |
| 磁盘操作 | 所选时间间隔内的平均磁盘操作速率（操作次数/秒）。 |

## <a name="manage-vm-disks"></a>管理 VM 磁盘

若要添加 VM 磁盘，请打开所选 VM 的“磁盘”页。 若要添加磁盘，请单击“添加磁盘”。 通过输入或选择内联选项来配置以下每个设置。 单击“保存”  。

   | 项 | 说明 |
   | ------------ | ------------- |
   | 名称 | 输入一个名称用于标识磁盘。  |
   | 大小 | 选择一种可用大小。  |
   | SCSI 控制器 | 选择一个 SCSI 控制器。 可用的控制器根据支持的操作系统的不同而异。  |
   | “模式” | 确定磁盘如何参与快照。 选择以下选项之一： <br> - 独立持久性：永久写入已写入到磁盘的所有数据。<br> - 独立非持久性：关闭或重置虚拟机时，会丢弃已写入到磁盘的更改。  使用此模式可始终以相同的状态重启 VM。 有关详细信息，请参阅 [VMware 文档](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)。 |

若要删除某个磁盘，请将其选中并单击“删除”。

## <a name="change-the-capacity-of-the-vm"></a>更改 VM 的容量

若要更改 VM 的容量，请打开所选 VM 的“大小”页。 指定以下任何项，然后单击“保存”。

| 项 | 说明 |
| ------------ | ------------- |
| 内核数 | 分配给 VM 的核心数。  |
| 硬件虚拟化 | 选中此复选框可向来宾 OS 公开硬件虚拟化。 请参阅 VMware 文章 [Expose VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)（公开 VMware 硬件辅助虚拟化）。 |
| 内存大小 | 选择要分配给 VM 的内存量。  

## <a name="manage-network-interfaces"></a>管理网络接口

若要添加接口，请单击“添加网络接口”。 通过输入或选择内联选项来配置以下每个设置。 单击“保存”  。

   | 控制 | 说明 |
   | ------------ | ------------- |
   | 名称 | 输入一个名称用于标识接口。  |
   | 网络 | 从私有云 vSphere 中配置的网络列表中进行选择。  |
   | 适配器 | 从为 VM 配置的可用类型列表中选择 vSphere 适配器。 有关详细信息，请参阅 VMware 知识库文章 [Choosing a network adapter for your virtual machine](https://kb.vmware.com/s/article/1001805)（选择虚拟机的网络适配器）。 |
   | 启动时开机 | 选择是否要在启动 VM 时启用 NIC 硬件。 默认设置为“启用”。 |

若要删除某个网络接口，请将其选中并单击“删除”。
