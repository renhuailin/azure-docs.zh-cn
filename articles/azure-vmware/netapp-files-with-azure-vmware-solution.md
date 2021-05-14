---
title: 将 Azure NetApp 文件与 Azure VMware 解决方案配合使用
description: 将 Azure NetApp 文件与 Azure VMware 解决方案 VM 配合使用，以在本地服务器、Azure VMware 解决方案 VM 与云基础结构之间迁移和同步数据。
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 2f2e8fdeb777e7e4b2b4e89c1bb36b51c3083257
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575431"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>将 Azure NetApp 文件与 Azure VMware 解决方案配合使用

在本文中，我们将介绍如何将 Azure NetApp 文件与基于 Azure VMware 解决方案的工作负荷集成。 来宾操作系统将在访问 Azure NetApp 文件卷的虚拟机 (VM) 中运行。 

## <a name="azure-netapp-files-overview"></a>Azure NetApp 文件概述

[Azure NetApp 文件](../azure-netapp-files/azure-netapp-files-introduction.md)是一项 Azure 服务，用于迁移和运行云中要求最苛刻的企业文件工作负荷（数据库、SAP 和高性能计算应用程序），无需更改代码。

### <a name="features"></a>功能
（使用了 Azure NetApp 文件的服务。）

- Active Directory 连接：Azure NetApp 文件支持 [Active Directory 域服务和 Azure Active Directory 域服务](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use)。

- 共享协议：Azure NetApp 文件支持服务器消息块 (SMB) 和网络文件系统 (NFS) 协议。 此支持意味着可以在 Linux 客户端上装载卷，并且可以在 Windows 客户端上映射这些卷。

- Azure VMware 解决方案：可以从在 Azure VMware 解决方案环境中创建的 VM 装载 Azure NetApp 文件共享。

Azure NetApp 文件在许多 Azure 区域中可用，并支持跨区域复制。 有关 Azure NetApp 文件配置方法的信息，请参阅 [Azure Netapp 文件的存储层次结构](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)。

## <a name="reference-architecture"></a>参考体系结构

下图展示了通过 Azure ExpressRoute 建立的与 Azure VMware 解决方案私有云的连接。 Azure VMware 解决方案环境访问 Azure VMware 解决方案 VM 上装载的 Azure NetApp 文件共享。

![显示了 Azure VMware 解决方案体系结构的 NetApp 文件的示意图。](media/net-app-files/net-app-files-topology.png)

本文介绍了如何将 Azure NetApp 文件卷设置为 Azure VMware 解决方案 VM 的文件共享，以及如何对其进行测试和验证。 在此方案中，我们使用了 NFS 协议。 Azure NetApp 文件和 Azure VMware 解决方案在同一 Azure 区域中创建。

## <a name="prerequisites"></a>先决条件 

> [!div class="checklist"]
> * 启用了 Azure NetApp 文件的 Azure 订阅
> * Azure NetApp 文件的子网
> * Azure VMware 解决方案上的 Linux VM
> * Azure VMware 解决方案上的 Windows VM

## <a name="regions-supported"></a>支持的区域

可以在 [Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)中找到支持的区域的列表。

## <a name="verify-pre-configured-azure-netapp-files"></a>验证预配置的 Azure NetApp 文件 

请遵循以下文章中的分步说明，在 Azure VMware 解决方案 VM 上创建和装载 Azure NetApp 文件卷。

- [创建 NetApp 帐户](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [设置容量池](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [创建用于 Azure NetApp 文件的 SMB 卷](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [创建用于 Azure NetApp 文件的 NFS 卷](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [将子网委派给 Azure NetApp 文件](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

以下步骤包括验证在 Azure 中的 Azure NetApp 文件高级服务级别创建的预配置 Azure NetApp 文件。

1. 在 Azure 门户中的“存储”下，选择“Azure NetApp 文件”。 此时会显示已配置的 Azure NetApp 文件的列表。 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="屏幕截图，显示了预配置的 Azure NetApp 文件的列表。"::: 

2. 选择一个已配置的 NetApp 文件帐户来查看其设置。 例如，选择“Contoso-anf2”。 

3. 选择“容量池”来验证已配置的池。 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="屏幕截图显示了用于查看已配置的 NetApp 文件帐户的容量池和卷的选项。":::

    “容量池”页面随即打开，显示容量和服务级别。 在此示例中，存储池配置为高级服务级别且容量为 4 TiB。

4. 选择“卷”以查看在容量池下创建的卷。 （请参阅前面的屏幕截图。）

5. 选择一个卷来查看其配置。  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="屏幕截图显示了在容量池下创建的卷。":::

    此时会打开一个窗口，其中显示卷的配置详细信息。

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="屏幕截图显示了卷的配置详细信息。":::

    你可以看到，anfvolume 的大小为 200 GiB，位于容量池 anfpool1 中。 它通过 10.22.3.4:/ANFVOLUME 导出为 NFS 文件共享。 从 Azure 虚拟网络 (VNet) 为 Azure NetApp 文件和用于在 VM 上进行装载的 NFS 路径创建了一个专用 IP。

    若要了解 Azure NetApp 文件按大小或“配额”划分的卷性能，请参阅 [Azure Netapp 文件的性能注意事项](../azure-netapp-files/azure-netapp-files-performance-considerations.md)。 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>验证预配置的 Azure VMware 解决方案 VM 共享映射

若要使 Azure NetApp 文件共享可供 Azure VMware 解决方案 VM 访问，你需要了解 SMB 和 NFS 共享映射。 只有在配置 SMB 或 NFS 卷后，才能如此处所述装载它们。

- SMB 共享：在部署 SMB 卷之前创建 Active Directory 连接。 若要成功进行连接，Azure NetApp 文件的委托子网必须可以访问指定的域控制器。 在 Azure NetApp 文件帐户内配置 Active Directory 后，在创建 SMB 卷时，它将显示为可选择的项。

- NFS 共享：Azure NetApp 文件有助于使用 NFS 或双重协议（NFS 和 SMB）创建卷。 卷的容量消耗是依据其池的预配容量计数的。 可以通过使用命令行或 /etc/fstab 条目将 NFS 装载到 Linux 服务器。

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>将 Azure NetApp 文件与 Azure VMware 解决方案配合使用的用例

下面只是几个非常引人注目的 Azure NetApp 文件用例。 
- Horizon 配置文件管理
- Citrix 配置文件管理
- 远程桌面服务配置文件管理
- Azure VMware 解决方案上的文件共享

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 Azure NetApp 文件与 Azure VMware 解决方案工作负荷集成，可能还需要了解以下内容：

- [Azure NetApp 文件的资源限制](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)。
- [Azure NetApp 文件网络规划指南](../azure-netapp-files/azure-netapp-files-network-topologies.md)。
- [Azure NetApp 文件卷的跨区域复制](../azure-netapp-files/cross-region-replication-introduction.md)。 
- [有关 Azure NetApp 文件的常见问题解答](../azure-netapp-files/azure-netapp-files-faqs.md)。
