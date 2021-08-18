---
title: 将 Azure NetApp 文件与 Azure VMware 解决方案配合使用
description: 将 Azure NetApp 文件与 Azure VMware 解决方案 VM 配合使用，以在本地服务器、Azure VMware 解决方案 VM 与云基础结构之间迁移和同步数据。
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 4c07765bd54e59109f15f8a0cdfd067f48dd14b4
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443762"
---
# <a name="integrate-azure-netapp-files-with-azure-vmware-solution"></a>将 Azure NetApp 文件与 Azure VMware 解决方案配合使用

[Azure NetApp 文件](../azure-netapp-files/azure-netapp-files-introduction.md)是一项 Azure 服务，用于迁移和运行云中要求最苛刻的企业文件工作负荷（数据库、SAP 和高性能计算应用程序），无需更改代码。 本文介绍了如何使用网络文件系统 (NFS) 协议，将 Azure NetApp 文件卷设置为 Azure VMware 解决方案工作负载的文件共享，以及如何对其进行测试和验证。 来宾操作系统在访问 Azure NetApp 文件卷的虚拟机 (VM) 中运行。 

Azure NetApp 文件和 Azure VMware 解决方案在同一 Azure 区域中创建。 Azure NetApp 文件可在许多 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)中使用，并支持跨区域复制。 有关 Azure NetApp 文件配置方法的信息，请参阅 [Azure Netapp 文件的存储层次结构](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)。

使用 Azure NetApp 文件的服务：

- Active Directory 连接：Azure NetApp 文件支持 [Active Directory 域服务和 Azure Active Directory 域服务](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use)。

- 共享协议：Azure NetApp 文件支持服务器消息块 (SMB) 和网络文件系统 (NFS) 协议。 此支持意味着可以在 Linux 客户端上装载卷，并且可以在 Windows 客户端上映射这些卷。

- Azure VMware 解决方案：可以从在 Azure VMware 解决方案环境中创建的 VM 装载 Azure NetApp 文件共享。


下图展示了与 Azure VMware 解决方案私有云的连接，该连接通过 Azure ExpressRoute 建立而成。 Azure VMware 解决方案环境访问 Azure VMware 解决方案 VM 上装载的 Azure NetApp 文件共享。

:::image type="content" source="media/netapp-files/netapp-files-topology.png" alt-text="显示了 Azure VMware 解决方案体系结构的 NetApp 文件的示意图。" border="false":::


## <a name="prerequisites"></a>先决条件 

> [!div class="checklist"]
> * 启用了 Azure NetApp 文件的 Azure 订阅
> * Azure NetApp 文件的子网
> * Azure VMware 解决方案上的 Linux VM
> * Azure VMware 解决方案上的 Windows VM


## <a name="create-and-mount-azure-netapp-files-volumes"></a>创建并装载 Azure NetApp 文件卷

你将创建 Azure NetApp 文件卷并将其装载到 Azure VMware 解决方案虚拟机上。

1. [创建 NetApp 帐户](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)。

1. [设置容量池](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)。

1. [创建用于 Azure NetApp 文件的 SMB 卷](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)。

1. [创建用于 Azure NetApp 文件的 NFS 卷](../azure-netapp-files/azure-netapp-files-create-volumes.md)。

1. [将子网委托给 Azure NetApp 文件](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)。


## <a name="verify-pre-configured-azure-netapp-files"></a>验证预配置的 Azure NetApp 文件 

你将验证在 Azure 中的 Azure NetApp 文件高级服务级别创建的预配置 Azure NetApp 文件。

1. 在 Azure 门户中的“存储”下，选择“Azure NetApp 文件”。 此时会显示已配置的 Azure NetApp 文件的列表。 

   :::image type="content" source="media/netapp-files/azure-netapp-files-list.png" alt-text="屏幕截图，显示了预配置的 Azure NetApp 文件的列表。"::: 

2. 选择一个已配置的 NetApp 文件帐户来查看其设置。 例如，选择“Contoso-anf2”。 

3. 选择“容量池”来验证已配置的池。 

   :::image type="content" source="media/netapp-files/netapp-settings.png" alt-text="屏幕截图显示了用于查看已配置的 NetApp 文件帐户的容量池和卷的选项。":::

   “容量池”页面随即打开，显示容量和服务级别。 在此示例中，存储池配置为高级服务级别且容量为 4 TiB。

4. 选择“卷”以查看在容量池下创建的卷。 （请参阅前面的屏幕截图。）

5. 选择一个卷来查看其配置。  

   :::image type="content" source="media/netapp-files/azure-netapp-volumes.png" alt-text="屏幕截图显示了在容量池下创建的卷。":::

   此时会打开一个窗口，其中显示卷的配置详细信息。

   :::image type="content" source="media/netapp-files/configuration-of-volume.png" alt-text="屏幕截图显示了卷的配置详细信息。":::

   你可以看到，anfvolume 的大小为 200 GiB，位于容量池 anfpool1 中。 它通过 10.22.3.4:/ANFVOLUME 导出为 NFS 文件共享。 从 Azure 虚拟网络 (VNet) 为 Azure NetApp 文件和用于在 VM 上进行装载的 NFS 路径创建了一个专用 IP。

   若要了解 Azure NetApp 文件按大小或“配额”划分的卷性能，请参阅 [Azure Netapp 文件的性能注意事项](../azure-netapp-files/azure-netapp-files-performance-considerations.md)。 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>验证预配置的 Azure VMware 解决方案 VM 共享映射

若要使 Azure NetApp 文件共享可供 Azure VMware 解决方案 VM 访问，你需要了解 SMB 和 NFS 共享映射。 只有在配置 SMB 或 NFS 卷后，才能如此处所述装载它们。

- SMB 共享：在部署 SMB 卷之前创建 Active Directory 连接。 若要成功进行连接，Azure NetApp 文件的委托子网必须可以访问指定的域控制器。 在 Azure NetApp 文件帐户内配置 Active Directory 后，在创建 SMB 卷时，它将显示为可选择的项。

- NFS 共享：Azure NetApp 文件有助于使用 NFS 或双重协议（NFS 和 SMB）创建卷。 卷的容量消耗是依据其池的预配容量计数的。 可以通过使用命令行或 /etc/fstab 条目将 NFS 装载到 Linux 服务器。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 Azure NetApp 文件与 Azure VMware 解决方案工作负荷集成，可能还需要了解以下内容：

- [Azure NetApp 文件的资源限制](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Azure NetApp 文件网络规划指南](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Azure NetApp 文件卷的跨区域复制](../azure-netapp-files/cross-region-replication-introduction.md) 
- [有关 Azure NetApp 文件的常见问题解答](../azure-netapp-files/azure-netapp-files-faqs.md)
