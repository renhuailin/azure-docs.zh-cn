---
title: Azure NetApp 文件与 Azure VMware 解决方案
description: 结合使用 Azure NetApp 文件和 Azure VMware 解决方案 Vm，在本地服务器、Azure VMware 解决方案 Vm 和云基础结构之间迁移和同步数据。
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: 8c101b652ffcefe05e9b6c11f166c1da3df2ede1
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539360"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp 文件与 Azure VMware 解决方案

本文介绍如何将 Azure NetApp 文件与基于 Azure VMware 解决方案的工作负荷集成。 来宾操作系统将在 (Vm) 访问 Azure NetApp 文件卷的虚拟机中运行。 

## <a name="azure-netapp-files-overview"></a>Azure NetApp 文件概述

[Azure NetApp 文件](../azure-netapp-files/azure-netapp-files-introduction.md) 是一项 azure 第一方服务，用于迁移和运行云中最苛刻的企业文件工作负荷，包括数据库、SAP 和高性能计算应用程序，无代码更改。

### <a name="features"></a>功能
 (使用 Azure NetApp 文件的服务。 ) 

- **Active Directory 连接**： Azure NetApp 文件支持 [Active Directory 域服务和 Azure Active Directory 域服务](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use)。

- **共享协议**： Azure NetApp 文件支持 (SMB) 和网络文件系统 (NFS) 协议的服务器消息块。 此支持意味着可以在 Linux 客户端上装载卷，并可以在 Windows 客户端上映射这些卷。

- **Azure Vmware 解决方案**：可以从在 Azure vmware 解决方案环境中创建的 vm 装载 Azure NetApp 文件共享。

Azure NetApp 文件在许多 Azure 区域中提供，并支持跨区域复制。 有关 Azure NetApp 文件配置方法的信息，请参阅 [Azure Netapp 文件的存储层次结构](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)。

## <a name="reference-architecture"></a>参考体系结构

下图说明了通过 Azure ExpressRoute 连接到 Azure VMware 解决方案私有云的连接。 其中显示了 azure NetApp 解决方案虚拟机上的 azure NetApp 文件共享的使用情况，该共享是在 azure VMware 解决方案环境下进行访问的。

![显示 Azure VMware 解决方案体系结构的 NetApp 文件的关系图。](media/net-app-files/net-app-files-topology.png)

本文介绍如何针对 Azure VMware 解决方案 Vm 设置、测试和验证 Azure NetApp 文件卷，作为文件共享。 在此方案中，我们使用了 NFS 协议。 Azure NetApp 文件和 Azure VMware 解决方案在同一 Azure 区域中创建。

## <a name="prerequisites"></a>必备条件 

> [!div class="checklist"]
> * Azure 订阅已启用 Azure NetApp 文件
> * Azure NetApp 文件的子网
> * Azure VMware 解决方案上的 Linux VM
> * Azure VMware 解决方案上的 Windows Vm

## <a name="regions-supported"></a>支持的区域

受支持区域的列表可以在 [Azure 产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)）中找到。

## <a name="verify-pre-configured-azure-netapp-files"></a>验证预配置的 Azure NetApp 文件 

遵循以下文章中的分步说明，在 Azure VMware 解决方案 Vm 上创建和装载 Azure NetApp 文件卷。

- [创建 NetApp 帐户](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [设置容量池](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [创建用于 Azure NetApp 文件的 SMB 卷](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [创建用于 Azure NetApp 文件的 NFS 卷](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [将子网委派给 Azure NetApp 文件](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

以下步骤包括验证在 azure 上的 azure NetApp 文件高级服务级别中创建的预配置 Azure NetApp 文件。

1. 在 Azure 门户的 " **存储**" 下，选择 " **Azure NetApp 文件**"。 将显示已配置的 Azure NetApp 文件的列表。 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="显示预配置的 Azure NetApp 文件列表的屏幕截图。"::: 

2. 选择已配置的 NetApp 文件帐户以查看其设置。 例如，选择 " **Contoso-anf2**"。 

3. 选择 **容量池** 以验证配置的池。 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="显示用于查看配置的 NetApp 文件帐户的容量池和卷的选项的屏幕截图。":::

    此时会打开 "容量池" 页，其中显示了容量和服务级别。 在此示例中，将存储池配置为具有高级服务级别的 4 TiB。

4. 选择 **卷** 以查看在容量池下创建的卷。  (查看前面的屏幕截图。 ) 

5. 选择要查看其配置的卷。  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="显示容量池下创建的卷的屏幕截图。":::

    此时会打开一个窗口，其中显示了卷的配置详细信息。

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="显示卷的配置详细信息的屏幕截图。":::

    你可以看到，容量池 anfpool1 中创建了卷 anfvolume，大小为 200 GiB，并通过10.22.3.4：/ANFVOLUME. 将其作为 NFS 文件共享导出。 Azure 虚拟网络中的一个专用 IP (VNet) 是为 Azure NetApp 文件创建的，另一个是在 VM 上装载的 NFS 路径。 有关 Azure NetApp 文件相对于大小 ( "配额" ) 的容量性能的信息，请参阅 [Azure Netapp 文件的性能注意事项](../azure-netapp-files/azure-netapp-files-performance-considerations.md)。 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>验证预配置的 Azure VMware 解决方案 VM 共享映射

在展示 azure NetApp 文件共享到 Azure VMware 解决方案 VM 的可访问性之前，了解 SMB 和 NFS 共享映射很重要。 仅在配置 SMB 或 NFS 卷之后，才能按此处所述装载它们。

- SMB 共享：在部署 SMB 卷之前创建 Active Directory 连接。 若要成功连接，Azure NetApp 文件的委托子网必须可以访问指定的域控制器。 在 Azure NetApp 文件帐户内配置 Active Directory 后，在创建 SMB 卷时，它将显示为可选择的项目。

- NFS 共享： Azure NetApp 文件有助于使用 NFS 或双重协议 (NFS 和 SMB) 创建卷。 卷的容量消耗是依据其池的预配容量计数的。 可以通过使用命令行或/etc/fstab 条目将 NFS 装载到 Linux 服务器。

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>使用 Azure VMware 解决方案的 Azure NetApp 文件的用例

下面只是几个引人注目的 Azure NetApp 文件用例。 
- 范围配置文件管理
- Citrix 配置文件管理
- 远程桌面服务配置文件管理
- Azure VMware 解决方案上的文件共享

## <a name="next-steps"></a>后续步骤

将 Azure NetApp 文件与 Azure VMware 解决方案工作负荷集成后，你可能想要详细了解以下内容：

- [Azure NetApp 文件的资源限制](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)。
- [Azure NetApp 文件网络规划指南](../azure-netapp-files/azure-netapp-files-network-topologies.md)。
- [Azure NetApp 文件卷的跨区域复制](../azure-netapp-files/cross-region-replication-introduction.md)。 
- [有关 Azure NetApp 文件的常见问题解答](../azure-netapp-files/azure-netapp-files-faqs.md)。
