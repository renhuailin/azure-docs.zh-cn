---
title: 为 Azure NetApp 文件创建 SMB 卷 | Microsoft Docs
description: 本文介绍如何在 Azure NetApp 文件中创建 SMB3 卷。 了解 Active Directory 连接和域服务的要求。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 6bb787084d4396737d3002bb98fe50e283c5764a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582902"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>创建用于 Azure NetApp 文件的 SMB 卷

Azure NetApp 文件支持使用 NFS (NFSv3 和 NFSv 4.1) 、SMB3 或双重协议 (NFSv3 和 SMB) 创建卷。 卷的容量消耗是依据其池的预配容量计数的。 本文介绍如何创建 SMB3 卷。

## <a name="before-you-begin"></a>开始之前 
必须已设置容量池。   
[设置容量池](azure-netapp-files-set-up-capacity-pool.md)   
子网必须委派给 Azure NetApp 文件。  
[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)

## <a name="configure-active-directory-connections"></a>配置 Active Directory 连接 

创建 SMB 卷之前，需要创建 Active Directory 连接。 如果尚未配置 Azure NetApp 文件 Active Directory 连接，请按照 [创建和管理 Active Directory 连接](create-active-directory-connections.md)中所述的说明进行操作。

## <a name="add-an-smb-volume"></a>添加 SMB 卷

1. 从“容量池”边栏选项卡中单击“卷”边栏选项卡。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. 单击“+ 添加卷”以创建卷。  
    此时将显示“创建卷”窗口。

3. 在 "创建卷" 窗口中，单击 " **创建** "，并在 "基本信息" 选项卡下提供以下字段的信息：   
    * **卷名称**      
        指定要创建的卷的名称。   

        卷名称在每个容量池中必须是唯一的。 它的长度必须至少为三个字符。 你可以使用任何字母数字字符。   

        不能将 `default` 或 `bin` 用作卷名称。

    * **容量池**  
        指定要在其中创建卷的容量池。

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **吞吐量 (MiB/秒)**   
        如果在手动 QoS 容量池中创建了卷，请指定要用于卷的吞吐量。   

        如果卷是在自动 QoS 容量池中创建的，则此字段中显示的值为 (配额 x 服务级别吞吐量) 。   

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (VNet)。  

        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 VNet 或者从与卷位于同一区域的 VNet 通过 VNet 对等互连来访问 Azure NetApp 文件服务。 还可以从本地网络通过 Express Route 来访问卷。   

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。 然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”来为 Azure NetApp 文件委派子网。 在每个 VNet 中，只能将一个子网委派给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 如果要将现有的快照策略应用到卷，请单击 " **显示高级" 部分** 将其展开，指定是否要隐藏快照路径，并在下拉菜单中选择快照策略。 

        有关创建快照策略的信息，请参阅 [管理快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)。

        ![显示高级选择](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. 单击“协议”，并完成以下信息：  
    * 选择“SMB”作为卷的协议类型。 
    * 从下拉列表中选择“Active Directory”连接。
    * 在“共享名”中指定共享卷的名称。

    ![指定 SMB 协议](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 单击“查看 + 创建”以查看卷详细信息。  然后单击“创建”以创建 SMB 卷。

    创建的卷将显示在“卷”页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="control-access-to-an-smb-volume"></a>控制对 SMB 卷的访问  

通过权限管理对 SMB 卷的访问。  

### <a name="share-permissions"></a>共享权限  

默认情况下，新卷具有“所有人/完全控制”共享权限。 域管理员组的成员可以通过对用于 Azure NetApp 文件卷的计算机帐户使用计算机管理来更改共享权限。

![SMB 装载路径](../media/azure-netapp-files/smb-mount-path.png) 
![设置共享权限](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS 文件和文件夹权限  

可以使用 Windows SMB 客户端中对象属性的“安全”选项卡设置文件或文件夹的权限。
 
![设置文件和文件夹权限](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>后续步骤  

* [为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [SMB 常见问题解答](./azure-netapp-files-faqs.md#smb-faqs)
* [SMB 或双重协议卷故障排除](troubleshoot-dual-protocol-volumes.md)
* [了解 Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
* [使用 Azure CLI 安装新的 Active Directory 林](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
